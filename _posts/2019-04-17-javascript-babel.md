---
title: "Javascript babel"
date: 2019-04-17 12:00:00 +0900
excerpt: "Javascript babel"
header:
  overlay_image: /assets/images/wallpaper.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing javascript
tags: javascript babel
---
Javascript babel
=============

## Overview
* * *

이번 포스팅은 npm webpack과 같이 최신 자바스크립트 개발환경에서 필수적으로 사용하는 바벨에 대해서 소개합니다.

자바스크립트에는 es6부터 여러가지 문법이 추가되었습니다.

최근에도 새로운 버전이 등장하면서 여러가지 유용한 문법이 추가되고 있습니다.

하지만 모든 브라우저가 최신버전의 자바스크립트 엔진을 사용하고 있지 않습니다.

웹 환경은 유저 유입에 제한이 없는 만큼 반드시 하위 호환성을 유지해야합니다.

이런 하위 호환성 이슈를 해결하기 위해 바벨을 사용합니다.


## Babel' Role

바벨은 아래와 같이 상위버전 문법을 하위 버전 문법으로 치환해 줍니다.

1.
```
[1, 2, 3].map(n => n ** 2);
```

```
[1, 2, 3].map(function (n) {
  return Math.pow(n, 2);
})
```

2.
```
var [a,,b] = [1,2,3];
```

```
var _ref = [1, 2, 3,],
    a = _ref[0];
    b = _ref[2];
```

3.
```
const x = [1, 2, 3];
foo([...x]);
```

```
var x = [1, 2, 3];
foo([].concat(x))
```

## Babel

바벨의 설치는 매우 간단합니다. npm을 통해 설치합니다.
```
npm install babel --save
```

babel 설치 이후 package.json에 여러가지 모듈이 설치된 것을 확인 할 수 있습니다.

각각의 역할을 간단하게 소개합니다.

### Babel Core
* * * 

바벨 컴파일러(transpiling)의 코어 라이브러리입니다.

### Babel Loader
* * *

웹펙에서 바벨을 사용할 수 있도록 연결해주는 loader입니다.

위의 webpack.config.js에서 명시한 것 처럼 module 항목에 넣어 사용 합니다.

### Babel Preset
* * *
현재 자바스크립트 버전에서 사용하는 문법의 transpiling 프리셋입니다.

```
@babel/preset-env
@babel/preset-flow
@babel/preset-react
@babel/preset-typescript
```

### Babel Polyfill
* * *
babel polyfill은 import시 자바스크립트 런타임 환경을 es6 환경으로 변환 시켜줍니다.

Array.prototype.includes 같은 함수를 별도의 설정없이 사용하게 해줍니다.

프리셋이 저장되어 있는 core-js를 글로벌로 선언하고 동작하기 때문에 npm 을 통해 내려받은 라이브러리가 상위 버전 함수를 사용하더라도 신경 쓰지 않고 개발 할 수 있습니다.

npm 추가 후 아래와 같이 webpack.config.json의 entry의 맨 앞에 명시하여 사용합니다.

```
module.exports = {
  entry: ["@babel/polyfill", "./app/js"],
};
```

주의해야할 것은 글로벌 스코프이기 때문에 전역 오염이 발생한다는 점, 모든 함수에대한 폴리필을 적용하기 때문에 번들 사이즈가 커진다는 점,

두 개의 폴리필이 임포트 되면 아래와 같이 에러가 발생한다는 점입니다.

```
Uncaught Error : only one instance of babel-polyfill is allowed
```

한 페이지에 여러개의 폴리필이 선언 되어 있다는 의미입니다.

본인이 만든 모듈에서 위 문구가 노출 될 경우 폴리필을 제거하거나 아래와 같이 babel transform runtime 을 사용합시다.

### Babel Transform Runtime
* * *

Babel Transform Runtime은 Babel Polyfill 과 다르게 전역으로 es6이상의 환경을 만들어 주는 것이 아닌 각각의 파일에 헬퍼 함수를 추가해 줍니다.

같이 사용하는 Babel Plugin Transform Runtime 은 Babel Transform Runtime을 효과적으로 사용하기 위한 플러그인으로 빌드타임에 함수들이 alias로 

여러번 선언하지 않고 한 개의 Babel Transform Runtime 헬퍼를 참조하도록 해줍니다.

사용하는 함수에 따라 개별적으로 적용되기 때문에 빌드 타임에 사용하지 않는 preset에 대해서는 strip 하고, 이에따라 번들의 용량이 줄어드는 장점이 있습니다.

아래와 같이 설치합니다.

```
//빌드 타임에만 사용되기 때문에 --save-dev입니다.
npm install babel-plugin-transform-runtime --save-dev 

//런타임에 alias를 통해 핼퍼가 호출되기 때문에 --save입니다.
npm install babel-runtime --save
```

webpack.config.js loader 항목에 아래와 같이 선언하여 사용합니다.
```
loaders: [
    {
        test: /\.(js|jsx)$/,
        include: [
            /src\/js/,
        ],
        loader: 'babel',
        query: {
            plugins: [
                ['transform-runtime'],
            ],            
            presets: [
                ['env', {
                    targets: {
                        browsers: ['ie >= 8']
                    },
                    loose: true,
                }],
            ],
        }
    },
    ...
],

```