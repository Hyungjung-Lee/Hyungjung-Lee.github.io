---
title: "python airflow"
date: 2021-1-9 21:53:00 +0900
excerpt: "python mysql"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study flask mysql
---
Airflow 
=============

# Airflow 란

Airflow는 Airbnb에서 개발한 워크플로우 스케쥴링, 모니터링 도구로 현재는 Apache 에서 관리되는 프로젝트입니다. 파이썬을 바탕으로하여 여러 IT회사에서 적용사례들이 많습니다. 간단한 배치부터, 복잡한 배치 작업까지 손쉽게 작업할 수 있습니다. 또한 배치가 동작하는 과정을 Web UI를 통해 파악 할 수 있습니다.

[airflow document](https://airflow.apache.org) 에서 전체적인 내용을 확인 할 수 있습니다.


[airflow git hub](http://github.com/apache/airflow) 에서 현재 개발 중인 내용을 open source로 확인 할 수 있습니다.

# 특징

- Dynamic : 에어플로우 파이프라인(동작순서, 방식)을 파이썬 코드를 이용하여 구성하기 때문에 동적인 구성이 가능
- Extensible : python 이용하여 Operator, executor를 사용하여 사용자의 환경에 맞게 확장하여 구성하는 것이 가능함
- Elegant : 에어플로우 파이프라인은 간결하고 명시적이며, 진자 템플릿(jinja template)을 이용하여 파라미터화 된 데이터를 전달하고 자동으로 파이프라인을 생성하는 것이 가능
- Scalable : 분산구조와 메시지큐를 이용하여 많은 수의 워커간의 협업을 지원하고, 스케일 아웃이 가능함


## Basic Concept

기본적인 개념은 아래 이미지 처럼 스케쥴러가 Meta DB를 통해서 Task 들의 조건을 관리합니다. 

스케쥴러가 Task의 실행 조건을 확인하면 Broker를 통해 Task를 요청합니다.

이때 요청 된 Task 각각의 Worker 에 배정되어 실행되고, 결과값을 Broker를 통해 전달합니다. Broker는 이 내용을 바탕으로 연관되어 있는 Task를 실행하게 됩니다.

![Architecture](/assets/images/airflow1.png)

이 과정을 통해 아래와 같은 복잡한 Task 간의 관계를 구현할 수 있습니다.

![Architecture](/assets/images/airflow_3.png)



## Arcithecture

![Architecture](/assets/images/airflow2.png)

전체적인 파이프라인은 위 이미지와 같습니다. 

Data Engineer 는 configuration 정보를 airflow.cfg에 기록하고 이것을 바탕으로 

Scheduler, Metadata DB, Web Server, Worker, Executor (Broker) 가 실행 됩니다.

이후 Data Engineer는 `DAG`라고 하는 Python 파일을 작성하여 batch를 실행하게 됩니다.

## Dag

DAG(Directed Acyclic Graph, 비순환 방향 그래프)는 Python으로 작성되며 각 배치 스케쥴을 관리합니다.
DAG하위에는 고유한 여러 Task가 존재하며 순서를 갖습니다.
Task는 BashOperator, PythonOperator 등 다양한 Operator를 지원합니다. Data Engineer는 용도에 맞는 Operator를 작성하여 Task에 재사용할 수 있습니다.

아래는 Dag의 예시입니다.

```
from airflow.models import DAG
from airflow.utils.dates import days_ago
from airflow.operators.bash_operator import BashOperator

args = {'owner': 'jovyan', 'start_date': days_ago(n=1)}
dag  = DAG(dag_id='my_first_dag',
           default_args=args,
           schedule_interval='@daily')

t1 = BashOperator(task_id='print_date',
                  bash_command='date',
                  dag=dag)

t2 = BashOperator(task_id='sleep',
                  bash_command='sleep 3',
                  dag=dag)

t3 = BashOperator(task_id='print_whoami',
                  bash_command='whoami',
                  dag=dag)

t1 >> t2 >> t3
```

먼저 Dag객체를 생성합니다. dag에는 고유의 id가 들어가고 스케쥴링을 위한 변수가 들어갑니다. 현재는 `@daily`가 들어간 것을 볼 수 있습니다. 이 값은 `0 0 0 0` 과 같이 `cron tap`으로 관리 될 수 있습니다.

각각의 Operator를 변수로 만들고 변수 간의 순서를 정하는 것을 볼 수 있습니다.
Operator들은 고유의 Task id를 갖게되고 task 별로 parameter를 받아 처리합니다.

custom operator의 예시는 아래와 같습니다.

```
from airflow.models import BaseOperator


class SimpleExampleOperator(BaseOperator):
    def execute(self, context):
        print("Run simple example operator")
```

BaseOperator를 class로 상속받아서 내부 구현을 합니다.

이때 Operator 내부에 원하는 Custom Library를 설치하여 복잡한 작업도 구현할 수 있습니다.

# Web UI

airflow는 이렇게 작성 되어있는 Dag들을 Web에서 확인 할 수 있습니다.

![web](/assets/images/airflow_5.png)


# 설치 방법

로컬에 PIP를 이용하여 설치하는 방법이 있고 Docker 를 이용하여 설치하는 방법이 있습니다.

우선은 PIP를 이용하여 설치하는 방법을 소개합니다.

```
# Airflow needs a home. `~/airflow` is the default, but you can put it
# somewhere else if you prefer (optional)
export AIRFLOW_HOME=~/airflow

# Install Airflow using the constraints file
AIRFLOW_VERSION=2.2.3
PYTHON_VERSION="$(python --version | cut -d " " -f 2 | cut -d "." -f 1-2)"
# For example: 3.6
CONSTRAINT_URL="https://raw.githubusercontent.com/apache/airflow/constraints-${AIRFLOW_VERSION}/constraints-${PYTHON_VERSION}.txt"
# For example: https://raw.githubusercontent.com/apache/airflow/constraints-2.2.3/constraints-3.6.txt
pip install "apache-airflow==${AIRFLOW_VERSION}" --constraint "${CONSTRAINT_URL}"

# The Standalone command will initialise the database, make a user,
# and start all components for you.
airflow standalone

# Visit localhost:8080 in the browser and use the admin account details
# shown on the terminal to login.
# Enable the example_bash_operator dag in the home page
```