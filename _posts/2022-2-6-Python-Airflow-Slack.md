---
title: "Airflow 에서 Slack 사용하기"
date: 2022-2-6 12:15:00 +0900
excerpt: "python slack"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study slack airflow
---
# Airflow에서 Slack 사용하기

Airflow를 통해 Mysql, MongoDB를 사용하는 법을 배웠습니다. 지금까지는 작성한 Dag들이 정상적으로 동작했는지, 실패했는지 확인을 하려면 Airflow Web에서 확인을 해야했습니다. 
이러한 내용을 Slack을 통해 손쉽게 확인하는 방법이 있습니다. 이번 포스팅에서는 Slack을 이용해서 Airflow 결과를 받는 방법을 소개합니다.

먼저 슬랙에서 사용할 앱을 만들어야합니다. 슬랙 앱의 구조는 아래와 같습니다.

![git](/assets/images/slack-struct.png)

- 슬랙 워크스페이스는 우리가 속해있는 공간을 뜻합니다. 
슬랙 채널은 워크스페이스 내부에 우리가 대화하는 채팅방을 뜻합니다.
- 슬랙 앱은 특정 기능을하는 어플리케이션을 뜻합니다. 우리가 직접 생성할 수도 있고 외부에서 만든 것을 사용할 수도 있습니다.
- 슬랙 봇은 앱에서 생성한 특정기능 집합입니다. 채널에 메세지를 보낼 수도 있고 채널 내부에서 누군가가 명령어를 입력하면 그에따른 동작을 할 수 있습니다.
- 외부서비스는 슬랙 봇에 메세지를 보내고 슬랙 봇은 그 메세지를 특정 채널에 올릴 수 있습니다. 

## 앱 생성하기

슬랙앱 생성은 [슬랙 홈페이지](https://api.slack.com/apps)에서 진행할 수 있습니다.

![git](/assets/images/slack-app.png)

## 봇 생성하기

앱을 생성하면 앱안에 봇을 생성할 수 있습니다.

![git](/assets/images/slack-bot.png)

### 퍼미션 추가하기
봇이 사용할 퍼미션을 추가합니다.

![git](/assets/images/slack-permission.png)

### 토큰 값 확인하기 

이제 봇의 토큰 값을 복사합니다. 이 값을 통해 우리가 정상적인 사용자인 것을 인증 할 수 있습니다. 그렇기 때문에 이 값은 외부에 노출되어서는 안됩니다.

![git](/assets/images/slack-token.png)

### 워크스페이스에 추가하기

/Invite @airflow 를 통해 봇을 채널에 추가할 수 있습니다.

슬랙에서의 준비는 이제 완료 되었습니다. 

## 라이브러리 다운로드 받기

이제 airflow 에서 사용할 파이썬 라이브러리를 추가합니다.
```
pip install slack_sdk
```

## 오퍼레이터 생성하기

dag에서 사용할 것이므로 operator가 필요합니다.  operator 내용은 아래와 같습니다

```
from airflow.models import BaseOperator
from slack_sdk import WebClient


class SlackBotOperator(BaseOperator):
    def __init__(self, message, *args, **kwargs):
        self.message = message
        super().__init__(*args, **kwargs)

    def execute(self, context):
        client = WebClient(token='토큰값')
        response = client.chat_postMessage(channel='airflow-message', text=self.message)
```
생성자를 통해 받은 메세지를 추가한 슬랙 라이브러리에 넣어주는 내용입니다.


## Util 함수 만들기 

우리는 이 메세지가 Dag가 성공했을때, 실패했을때 전달 받고 싶습니다. airflow는 각각의 Task가 성공할때 호출해주는 함수가 존재합니다.

각각 `on_success_callback` 과 `on_failure_callback`이라고 합니다. 이것은 `default_args` 에 넣어서 사용할 수 있습니다.

이것을 설정하기 위해 Operator를 함수로 호출 할 수 있도록 유틸함수를 생성해 봅시다.

`plugins/utils/slack.py` 파일을 생성하고 내용은 아래와 같이 설정합니다. 

파라미터로 전달 받는 context는 Airflow를 통해 전달받은 Task의 현재 상태값입니다.

방금 만든 Slack Operator를 사용하여 슬랙에 메세지를 보냅니다.

```
from slack_sdk import WebClient
from operators.slack_bot_operator import SlackBotOperator


def slack_success_alert(context):
    alert = SlackBotOperator(
        task_id='slack_success',
        message="""
        :white_check_mark: Task success.
        *Task*: {task}  
        *Dag*: {dag}
        *Execution Time*: {exec_date}  
        *Log Url*: {log_url}
        """.format(
            task=context.get('task_instance').task_id,
            dag=context.get('task_instance').dag_id,
            exec_date=context.get('execution_date'),
            log_url=context.get('task_instance').log_url,
        )
    )
    return alert.execute(context=context)


def slack_fail_alert(context):
    alert = SlackBotOperator(
        task_id='slack_failed',
        message="""
            :red_circle: Task Failed.
            *Task*: {task}  
            *Dag*: {dag}
            *Execution Time*: {exec_date}  
            *Log Url*: {log_url}
            """.format(
            task=context.get('task_instance').task_id,
            dag=context.get('task_instance').dag_id,
            exec_date=context.get('execution_date'),
            log_url=context.get('task_instance').log_url,
        )
    )
    return alert.execute(context=context)


```

default_args={
    'on_success_callback': slack_success_alert,
    'on_failure_callback': slack_fail_alert
},


## Dag에서 사용하기

이제 Dag에서 사용해봅시다.


```
from datetime import datetime, timedelta

from airflow import DAG
from operators.mongo_operator import MongoCreateDocumentOperator
from operators.slack_webhook_operator import SlackWebhookOperator
from utils.slack import slack_success_alert
from utils.slack import slack_fail_alert

with DAG(
        dag_id='mongo_test_operator',
        schedule_interval='* * * * *',
        start_date=datetime(2021, 1, 1),
        catchup=False,
        dagrun_timeout=timedelta(minutes=60),
        tags=['mongo'],
        default_args={
            'on_success_callback': slack_success_alert,
            'on_failure_callback': slack_fail_alert
        },
        params={
        },
) as dag:
    t1 = MongoCreateDocumentOperator(task_id='insert_data',
                                     host='127.0.0.1',
                                     database='test',
                                     collection='test',
                                     data={'test': 'test'},
                                     dag=dag)

    t1

if __name__ == "__main__":
    dag.cli()


```

## 확인하기 

![git](/assets/images/slack-success.png)

이것으로 슬랙을 이용하여 작업환경에 이상이 없는지 손쉽게 확인 할 수 있습니다. 지속적인 작업환경을 만드는 것을 크게 봤을때 DevOps라고합니다.