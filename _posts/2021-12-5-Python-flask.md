---
title: "python flask"
date: 2021-12-5 21:53:00 +0900
excerpt: "python flask"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: python
tags: python study flask
---
Python Flask에 대해서
=============

![git](/assets/images/1200px-Flask_logo.png)

# Python Flask 란?

Python 에서 웹 서비스를 개발 할 수 있는 Flask 에 대해서 소개합니다.

## 설치

Flask는 pip3를 이용해서 쉽게 설치 할 수 있습니다.

```
pip3 install flask
```

### pip3 란?

파이썬으로 작성 된 외부 라이브러리를 설치 할 수 있도록 도와주는 서비스입니다.

python3.4 부터는 pip가 기본적으로 함께 설치 됩니다.

로컬에 python3 만 설치되어 있는 경우에는 `pip install flask`를 이용해서 다운로드 받을 수 있습니다.

하지만 python2이 로컬에 설치 되어있는 경우에는 명시적으로 pip3를 입력해서 다운로드 받는 것이 좋습니다.

## Flask를 이용한 Hello World 

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def home():
    return 'Hello, World!'

if __name__ == '__main__':
    app.run(debug=True)
```

flask 사용을 위해서는 import 를 통해 Flask를 선언해야합니다. pip3를 통해 Flask를 받았기 때문에 별도의 설정 없이 사용 할 수 있습니다.

```
app = Flask(__name__)
```
코드상의 위 구문은 python 파일과 동일한 이름의 flask 객체를 만든다는 의미입니다. 이제 `__name__` 값에 해당하는 이름이 부여된 flask 객체를 app 변수로 사용할 수 있게 됩니다.

```
@app.route('/')
def home():
    return 'Hello, World!'
```

app 객체로 웹페이지의 특정 주소로 들어오는 입구를 설정할 수 있습니다. 이것을 `route`라고 합니다.
위 구문을 통해 `/` 경로로 들어오는 경우에 `home`함수를 호출하고 `Hello, World`를 리턴하는 기능을 실행하게 됩니다.

```
if __name__ == '__main__':
    app.run(debug=True)
```

위 구문을 통해 위에서 생성한 app 객체를 `run()`함수를 통해 호출합니다. 이때 `debug=True` 값을 parameter로 사용하여 코드가 변경 되면 app의 동작을 변경하는 기능을 사용하게 됩니다.

이제 웹 브라우저에 127.0.0.1:5000으로 접근하면 아래와 같이 웹페이지에 `Hello, World` 가 노출 되게 됩니다.

![git](/assets/images/flask_hello_world.png)
