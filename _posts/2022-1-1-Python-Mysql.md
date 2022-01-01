---
title: "python mysql flask"
date: 2021-12-5 21:53:00 +0900
excerpt: "python mysql"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study flask mysql
---
Python Flask Mysql 연동
=============

# Mysql 연동

## 설치 

Python에서 Mysql을 연동할 때는 PyMySql 패키지를 사용합니다.

pip3를 통해 설치할 수 있습니다.

```
pip3 install PyMySQL
```

## 연결

```
import pymysql

class_db = pymysql.connect(user='root', passwd='1234', host='127.0.0.1', db='classicmodels', charset='utf8')
```

- `user`는 database 유저를 뜻합니다. root를 사용합니다.
- `passwd`는 database의 비밀번호를 뜻합니다. 
- `host`는 데이터베이스의 주소를 뜻합니다. 현재 로컬에서 테스트하므로 127.0.0.1을 입력합니다.
- `db`는 접속할 db의 이름입니다. 미리 설정되어있는 db의 이름을 입력합니다.
- `charset`은 데이터베이스의 인코딩 타입 입니다. utf8을 사용합니다.

## 커서

```
cursor = class_db.cursor(pymysql.cursors.DictCursor)
```

데이터베이스에서 데이터를 읽기위해 커서 객체를 생성합니다. PyMySql 은 여러가지 커서가 있지만 사용하기 쉬운 DictCursor 를 사용합니다. 이 커서는 읽은 데이터를 딕셔너리 형태로 리턴합니다.

## 조회

pymysql은 일반 sql 쿼리를 통해 데이터베이스의 정보를 갖고 올 수 있습니다. select 문을 사용해서 데이터를 갖고옵니다.

```
sql = "SELECT * FROM customers"
```

sql을 스트링형태로 작성해서 cursor에 입력합니다.

```
cursor.execute(sql)
result = cursor.fetchall()
```

커서에 담겨있는 데이터를 fetchall()을 통해 전부 갖고옵니다. 사용할 수 있는 함수는 아래와 같습니다.

|메서드|설명|
|------|------|
|fetchall()|모든 데이터를 한 번에 가져올 때 사용|
|fetchone()|한 번 호출에 하나의 행만 가져올 때 사용|
|fetchmany(n)|n개만큼의 데이터를 가져올 때 사용|


전달 받은 데이터는 `pandas` 패키지를 통해 표시할 수 있습니다.

pandas 는 pip3를 통해 아래와 같이 설치 할 수 있습니다.

```
pip3 install pandas
```

아래와 같이 import합니다 .
```
import pandas as pd
```

이후 fetchall()을 통해 전달 받은 데이터를 pandas를 통해 dataFrame으로 변경합니다.

```
result = pandas.DataFrame(result)
```

### 결과

![git](/assets/images/python-pandas.png)


## 삽입

Insert 도 sql query를 스트링으로 생성해서 호출합니다.

```
sql = '''INSERT INTO `customers` (customerNumber, customerName, contactLastName, contactFirstName, phone, addressLine1, addressLine2, city, state, postalCode, country) 
    VALUES (1, 'testName', 'lee', 'hyungjung', '01035425772', 'test', 'dong-back', 'young-in', 'gyung-ki-do', '1234', 'korea');'''

```

sql query를 작성하고 cursor를 통해 execute 합니다.

```
cursor.execute(sql)
```

excute 한 내용을 commit() 하면 데이터베이스에 적용됩니다.

```
class_db.commit()
```

### 결과

저장 된 내용을 mysql workbench를 통해서 확인 할 수 있습니다.

![git](/assets/images/python-insert.png)


## 변경

update 역시 sql로 가능합니다.

```
sql = '''update customers set customerName = 'test' where customerNumber = 1'''
```

```
cursor.execute(sql)
class_db.commit()
```

### 결과 

![git](/assets/images/python-update.png)


## 삭제

```
sql = '''delete from customers where customerNumber = 1'''
```

```
cursor.execute(sql)
class_db.commit()
```

### 결과 

![git](/assets/images/python-delete.png)


# Mysql Flask 연동 

이제 flask를 통해 웹 브라우저에서 데이터를 확인합니다.


```
from flask import Flask, request, jsonify
import pymysql
import json

class_db = pymysql.connect(user='root', passwd='1234', host='127.0.0.1', db='classicmodels', charset='utf8')

app = Flask(__name__)
@app.route('/')
@app.route('/get', methods=['GET'])
def get():
    cursor = class_db.cursor(pymysql.cursors.DictCursor)
    sql = "SELECT * FROM customers"
    cursor.execute(sql)
    result = cursor.fetchall()
    return json.dumps(result)

@app.route('/new', methods=['POST'])
def post():
    data = request.json
    cursor = class_db.cursor(pymysql.cursors.DictCursor)
    sql = '''INSERT INTO customers (customerNumber, customerName, contactLastName, contactFirstName, phone, addressLine1, addressLine2, city, state, postalCode, country)  VALUES (%s, %s, %s, %s, %s, %s, %s, %s, %s, %s, %s);'''
    cursor.execute(sql, (data['customerNumber'], data['customerName'], data['contactLastName'], data['contactFirstName'], data['phone'], data['addressLine1'], data['addressLine2'], data['city'], data['state'], data['postalCode'], data['country']))

    return jsonify(data)


if __name__ == '__main__':
    app.run(host='0.0.0.0', port=8080, debug=True)
```