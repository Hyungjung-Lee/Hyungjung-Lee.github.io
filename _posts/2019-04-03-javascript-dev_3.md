---
title: "Javascript Dev Overview - Webpack"
date: 2019-04-03 22:34:28 +0900
excerpt: "Javascript Dev Overview"
header:
  overlay_image: /assets/images/webpack.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing javascript
tags: javascript Node Webpack
---
Javascript Dev Overview (3)
=============

## Overview
* * *

지난 포스팅에 이어서 자바스크립트 개발 환경에 빠질 수 없는 webpack에 대해 소개해 드리도록 하겠습니다.

앞서 소개한 npm은 여러개의 모듈을 다운로드 받을 수 있는 툴입니다.
이번에 소개할 webpack은 npm을 통해 다운로드받은 모듈들과 개발자의 로직을 하나의 모듈로 bundling하고 optimization 해주는 툴입니다. 
간단하게 compile 툴 이라고 하면 되겠습니다.

webpack이 등장하게 된 배경은 역시 개발 환경의 복잡성때문이었습니다.

옛 웹 개발환경에서는 아래와 같이 모듈들을 html에 직접 넣어주는 경우가 많았습니다.
모듈간의 종속관계에 따라 스크립트 순서도 조정해야하는 불편함이 있었습니다.
```
<script src="external_module1.js"></script>
<script src="external_module2.js"></script>
<script src="external_module3.js"></script>
<script src="external_module4.js"></script>
...
...
<script src="userModule.js"></script>
```

하지만 webpack을 이용하여 각각의 모듈들을 bundling하면 entry가 되는 하나의 파일만 임포트하면 됩니다.
```
<script src="moudle.js"></script>
```

## Webpack
* * *

공식 홈페이지에 있는 webpack 설명 이미지 입니다. 

webpack을 설명하기에 가장 적절하지 않나 생각합니다.

모듈간의 종속성을 확인하여 하나의 js로 만듭니다.

![webpack](/assets/images/webpack.png)

이미지와 같이 다른 파일들도 산출되는 모습이 보이는데, 이는 다음 포스팅에서 소개해 드리도록 하겠습니다.

### Install & Compile

webpack 설치는 Node, npm을 이용하여 설치합니다.

```
npm install webpack --save-dev
```

아래는 bundle1.js와 bundle2.js를 묶는 예시입니다.
각각의 js파일에는 foo와 bar 함수가 있고, index.js에서 이를 사용하는 예제입니다. 
```
// src/bundle1.js
export default function foo() {
  //console.log('foo')
}
```

```
// src/bundle2.js
export default function bar() {
  //console.log('bar')
}
```

```
// src/index.js
import bar from './foo';
import bar from './bar';

foo();
bar();
```

package.json과 같은 경로에 webpack.config.js 파일을 만듭니다.
webpack은 이 파일을 보고 compile을 진행합니다.

```
const path = require('path');

module.exports = {
  // 모듈의 entry가 되는 파일을 지정합니다.
  entry: './src/index.js',
  // 빌드가 끝났을 때 나오는 경로를 지정합니다.
  output: {
    path: path.resolve(__dirname, 'dist'),
    filename: 'bundle.js'
  }
};
```

package.json에 script 하위에 아래와같이 cmd를 넣어줍니다.
```
//package.json
{
  "name": "test",
  "version": "1.0.0",
  "description": "test",
  "scripts": {
    "build:dist:debug": "webpack --debug",
    "build:dist": "webpack",
  }
}
```
package.json이 있는 경로에서 아래 cmd를 실행합니다.
```
// cmd
$ npm run build:dist
```

index.html에서 아래와 같이 사용가능합니다.
```
//index.html
<!doctype html>
<html>
  <head>
    ...
  </head>
  <body>
    ...
    <script src="dist/bundle.js"></script>
  </body>
</html>
```

### webpack.config.js

아래는 optimization 기능이 포함된 webpack.config.js 입니다.

```
const path = require('path')
const webpack = require('webpack')

//cmd에서 --debug option을 확인합니다.
const DEBUG = process.argv.includes('--debug')

module.exports = {
  entry: {
    main: path.join(__dirname, 'index.js'),
  },
  output: {
    path: path.join(__dirname, './dist'),
    filename: DEBUG ? 'test.js' : 'test.min.js',
    library: 'TEST_MODULE',
  },
  // debug값을 통해서 development인지 production인지 확인합니다.
  // production의 경우 optimization 이 진행됩니다.
  mode: (DEBUG && 'development') || 'production',
  //module 을 통해 빌드시에 동작하는 추가행동을 정의할 수 있습니다.
  module: {
    rules: [
      {
        test: /\.js$/,
        loader: 'babel-loader',
      }
    ]
  },
  // optimization옵션을 설정할 수 있습니다.
  optimization: {
    minimize: !DEBUG
  },
  // 빌드할 때 포함할 확장자를 지정할 수 있습니다.
  // .js 파일에 대해 빌드를 진행한다는 의미입니다.
  resolve: {
    extensions: ['.js']
  }
}
```
## Next

다음 포스팅에서는 ES6이상에서 작업한 자바스크립트 모듈을 ES5에서 동작하게 해주는 babel에 대해서 소개드리겠습니다.