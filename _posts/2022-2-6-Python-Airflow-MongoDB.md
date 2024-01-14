---
title: "Airflow 에서 Mongodb 사용하기"
date: 2022-2-6 11:15:00 +0900
excerpt: "python mongo"
header:
  overlay_image: /assets/images/wallpaper.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study mongo airflow
---
# Airflow에서 MongoDB 사용하기

Airflow 에서 MongoDB를 사용하기 위한 방법에 대해서 알아봅니다.

MongoDB 와 Airflow 에 대한 것은 아래 블로그를 확인해 주세요.

https://hyungjung-lee.github.io/python/Python-Airflow/

https://hyungjung-lee.github.io/study/database/mongo-db/

## 사전 준비

Mysql을 Airflow에서 사용했던 것 처럼 MongoDB도 같은 방식으로 사용합니다.

그렇다면 MongoDB를 사용하는 Operator 클래스를 생성하고 Dag에서 사용하는 형태가 됩니다.

우선 Operator에서 사용할 MongoDB 라이브러리를 설치합니다.

```
pip install pymongo
```

라이브러리를 설치했으니 Operator를 작성합니다.

Plugins/operators 하위에 `mongo_operator.py`를 생성합니다.

## MongoDB Operator 작성

MongoDB 에 접근하려면 host정보, database 정보, collection 정보가 필수입니다. (포트를 변경한 경우에는 포트값도 필요합니다.)

예시로 생성할 Operator는 생성을 위한 Operator 라 입력할 Data도 추가하겠습니다.

이를 바탕으로 생성한 생성자는 아래와 같습니다

```
class MongoCreateDocumentOperator(BaseOperator):
    def __init__(self, host, database, collection, data, *args, **kwargs):
        self.host = host
        self.database = database
        self.collection = collection
        self.data = data
        super().__init__(*args, **kwargs)
```

이후 execute 에서는 mongodb 에 접근하고 사용자가 입력한 database, collection에 입력받은 data를 넣어야합니다.

```
def execute(self, context):
    mongo = MongoClient(host=self.host, port=27017)
    mongo[self.database][self.collection].insert_one(self.data)
```

전체 완성된 Operator의 모습은 아래와 같습니다.

```
from airflow.models import BaseOperator
from pymongo import MongoClient


class MongoCreateDocumentOperator(BaseOperator):
    def __init__(self, host, database, collection, data, *args, **kwargs):
        self.host = host
        self.database = database
        self.collection = collection
        self.data = data
        super().__init__(*args, **kwargs)

    def execute(self, context):
        mongo = MongoClient(host=self.host, port=27017)
        mongo[self.database][self.collection].insert_one(self.data)
```

Operator를 생성했으니 Dag에 사용해 봅니다.

dags 하위에 dag로 사용할 python 파일을 생성합니다.

Operator에 넣을 파라미터를 확인합니다. 최종 Dag의 값은 아래와 같습니다.

```
from datetime import datetime, timedelta

from airflow import DAG
from operators.mongo_operator import MongoCreateDocumentOperator

with DAG(
        dag_id='mongo_test_operator',
        schedule_interval='* * * * *',
        start_date=datetime(2022, 1, 1),
        catchup=False,
        dagrun_timeout=timedelta(minutes=60),
        tags=['mongo'],
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

Dag의 내용은 매 분마다 127.0.0.1 호스트의 test db의 test collection에 `{"test":"test"}` 데이터를 집어넣는 것입니다.

Aggregation, Delete 등을 위해서는 별도의 Operator 생성이 필요합니다.