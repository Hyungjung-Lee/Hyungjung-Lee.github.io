---
title: "Airflow 에서 Mysql 사용하기"
date: 2022-1-9 21:53:00 +0900
excerpt: "python mysql"
header:
  overlay_image: /assets/images/wallpaper_2.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study flask mysql
---
Airflow 에서 Mysql 사용하기
=============

워크플로우 스케쥴링 툴인 Airflow를 이용하여 주기적인 ETL 배치를 작성하는 방법을 알아보겠습니다.

우선 Mysql에 접근하기 위한 Operator를 생성해야합니다. Custom Operator는 Airflow에서 제공하는 `BaseOperator` 를 상속 받은 Class를 생성합니다.

이때 Class 생성 위치는 Airflow가 제공하는 경로에 위치해야 합니다. [가이드문서](https://airflow.apache.org/docs/apache-airflow/stable/howto/custom-operator.html) 를 보면 Airflow에서 python file을 인식하는 경로는 아래와 같습니다.

![이미지](/assets/images/custom_airflow.png) 

- dags : dag 파일을 저장하는 기본 경로입니다.
- plugins : 외부 Plugin 을 저장하는 경로입니다.
- config : 설정파일을 저장하는 경로입니다.
- PYTHONPATH : 시스템에서 PYTHON PATH로 지정한 경로입니다.

이번 포스팅에서는 `plugins/operators/` 경로 하위에 작성하겠습니다.

Operator의 구조는 2가지로 구성 됩니다.

### `__init__`

Custom Operator의 생성자입니다. Dag에서 생성자를 이용해서 Operator를 생성하기 때문에 작업에 필요한 parameter를 생성자 기록합니다. 이 포스팅에서는 mysql 접속을 위한 parameter를 넣어줍니다.

- host : mysql 이 설치되어있는 경로입니다.
- user : 접속할 유저 이름입니다.
- password : mysql password 입니다.
- database : 접근할 데이터베이스 이름입니다.
- query : 사용할 쿼리입니다.
- needCommit : insert, update, delete 를 사용할 경우 commit이 필요합니다. 그 여부입니다.
- *args : 기본 parameter를 담기위한 함수 파라미터입니다.
- **kwargs : 기본 parameter를 담기위한 함수 파라미터입니다.

위 파라미터를 바탕으로 mysql 을 위한 custom operator의 생성자는 아래와 같습니다.
```
def __init__(self, host, user, password, database, query, needCommit, *args, **kwargs): 
```

### `execute` 

실제 로직이 위치하는 함수입니다. `BaseOperator` 에서 정의 되어있는 함수를 그대로 Override 하여 사용합니다.

```
def execute(self, context):
```

이렇게 해서 작성 된 Mysql Operator의 모습은 아래와 같습니다.

```
from airflow.models import BaseOperator
import pymysql

class MySqlOperator(BaseOperator):
    def __init__(self, host, user, password, database, query, needCommit, *args, **kwargs): 
        self.host = host
        self.user = user
        self.password = password
        self.database = database
        self.query = query
        self.needCommit = needCommit
        super().__init__(*args, **kwargs)

    def execute(self, context):
        remoteDB = pymysql.connect(user=self.user, passwd=self.password, host=self.host, db=self.database, charset='utf8')
        cursor = remoteDB.cursor(pymysql.cursors.DictCursor)
        cursor.execute(self.query)
        if(self.needCommit):
            remoteDB.commit()
```
생성자에서 parameter들을 할당 받고 execute에서 database 관련 된 로직을 실행합니다.
connect를 통해 remote db를 연결하고 cursor를 생성합니다. 이후 이 cursor에 생성자에서 입력받은 query를 input 합니다. 
insert, update, delete의 경우 commit를 실행합니다.


### Dag 에서 사용하기

위에서 작성한 Mysql Operator를 Dag에서 직접 사용해봅니다.
신규 생성하는 Dag는 가이드대로 `dags` 폴더에 생성합니다.

`from operators.mysql_operator import MySqlOperator` 을 이용하여 생성한 Operator를 불러옵니다.

`With` 키워드로 Dag를 생성합니다.

dag id 를 입력하고 schedule을 chrontab으로 입력합니다. 예제 코드는 매 분마다 호출하는 `* * * * *` 을 사용합니다.

start_date 를 원하는 날짜로 입력합니다.

`MySqlOperator` 객체를 직접 생성하여 Task를 생성합니다.

```
from datetime import datetime, timedelta

from airflow import DAG
from operators.mysql_operator import MySqlOperator

with DAG(
    dag_id='mysql_test_operator',
    schedule_interval='* * * * *',
    start_date=datetime(2021, 1, 1),
    catchup=False,
    dagrun_timeout=timedelta(minutes=60),
    tags=['mysql'],
    params={"example_key": "example_value"},
) as dag:
    t1 = MySqlOperator(task_id='insert_data',
                  host='127.0.0.1',
                  user='root',
                  password='1234',
                  database='test_db',
                  query='''INSERT INTO `new_table` (name) VALUES ('testName');''',
                  needCommit=True,
                  dag=dag)

    t1

if __name__ == "__main__":
    dag.cli()

```