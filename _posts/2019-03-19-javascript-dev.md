---
title: "Javascript Dev Overview"
date: 2019-03-04 22:34:28 +0900
excerpt: "Javascript Dev Overview"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing javascript
tags: javascript webpack
---
Javascript Dev Overview
=============

## Overview
* * *

시간이 지나면서 자비스크립트 개발환경은 다른 언어와 비교하여 손색이 없을 정도로 점점 견고해지고 있습니다.
제가 처음 자바스크립트를 접했던 시점에 비해서 정말 따라갈 수 없는 정도로 많이 변했는데요.

작년 한해동안 새로운 자바스크립트를 접하면서 처음보는 개념, 프레임워크가 정말 많았습니다.

이번 포스팅은 공부한 내용을 정리하고 비교적 최근(?)의 자바스크립트 개발환경을 소개하려고 합니다.

## JavaScript (ECMAScript)
* * *

먼저 소개드릴 것은 언어 자체인 자바스크립트입니다. 그 역사부터 간단하게 설명하면,
자바스크립트는 1996년에 개발된 해석형 언어입니다. 정적인 HTML과 CSS를 보완하기 위해 사용되었습니다.
브라우저에서 컴파일 없이 즉시 실행할 수 있고, 코드의 strict가 낮아서 쉽게 입문 할 수 있는 것이 특징입니다.
하지만 다른 언어들과 비교하여 빈약한 객체지향성을 가지고 있고 마땅한 IDE도 없기 때문에 작업이 어려웠습니다.
특히 패키지간의 종속성이 스파게티가 되기 쉬워 copy & paste 를 통해 전승되는 경우가 많았습니다.
넷스케이프와 MS의 브라우저간 문법이 다른 것도 큰 허들이었습니다.

이러한 문법 상의 통합을 위해 ECMAScript를 명명하고 표준화, 개선 움직임이 시작됩니다.
그리고 2015년 HTML5의 등장과 ECMAscript6의 발표로 큰 변화를 맞이하게 됩니다.
아래는 ECMAScript6 부터 추가된 문법 중 중요하다 생각한 문법을 정리해봤습니다.

#let, const

let과 const는 ECMA6부터 추가 된 키워드입니다.
간단하게 var, let는 변수를 선언하는 키워드이고, const는 상수를 선언하는 키워드입니다.
var, let은 변수선언 키워드 이므로 리터럴 값의 재할당이 가능하지만, const는 리터럴 값의 재할당이 불가능합니다.

var는 global scope가 될 수도 있고, function scope 가 될 수 있는 변수타입 입니다.
중복 선언도 허용하기 때문에 코드량이 많아질 수록 문제가 생길 수 있습니다.
반면에 let과 const는 block scope이고 중복 또한 허용되지 않습니다.
때문에 let과 const로 변수를 사용 하는 것이 추천됩니다.


#Destructuring Assignment

기존에는 오브젝트의 프로퍼티를 분리할때, 여러줄에 걸쳐 변수를 선언해야 했습니다.
ECMA6 부터는 한 줄에 선언이 가능합니다.

```

var obj = {
  body : {
    name : 'name',
    country : 'country',
    email: 'email'
  }
}

//ES5
var name = obj.body.name
var country = obj.body.country
var email = obj.body.email

//ES6
const {name, country, email} = obj.body

const names = ['Luke', 'Eva', 'Phil']; 

const [first] = names;
```

#Object Initialize - Property Shorthand

간결한 객체 선언방법이 추가 되었습니다.

```
// 단축 속성명 (ES6)
let a = "foo", b = 42, c = {};
let o = { a, b, c };

// 단축 메서드명 (ES6)
let o = {
  property([parameters]) {},
  get property() {},
  set property(value) {},
  * generator() {}
};

// 속성 계산명 (ES6)
let prop = "foo";
let o = {
  [prop]: "hi",
  ["b" + "ar"]: "there",
};
```

#Default Parmeter

함수의 디폴트 파라미터 기능이 추가되었습니다.
이로인해 불필요한 초기화 코드가 삭제 됩니다.

```
function multiply(a, b = 1) {
  return a*b;
}
```

# promise 와 await/async

ajax를 통해 비동기 네트워크 작성할때, 리퀘스트가 조금만 중첩되어도 콜백 지옥에 빠지기 쉬웠습니다.
es6 에서 promise라는 개념의 등장과 es8의 await, async의 등장으로 c#의 코루틴과 같은 형태의 비동기 코드를 작성할 수 있게 되었습니다.

```
//es5의 콜백지옥

setTimeout(function (name) {
  var catList = name + ',';

  setTimeout(function (name) {
    catList += name + ',';

    setTimeout(function (name) {
      catList += name + ',';

      setTimeout(function (name) {
        catList += name + ',';

        setTimeout(function (name) {
          catList += name;

          console.log(catList);
        }, 1, 'Lion');
      }, 1, 'Snow Leopard');
    }, 1, 'Lynx');
  }, 1, 'Jaguar');
}, 1, 'Panther');
```


```
//프로미스 생성
const promise1 = function(param){
  return new Promise(function(resolve,reject){
    if(param){
      resolve("성공");
    }
    else{
      reject("실패");
    }
  });
}

//프로미스 실행
promise1(true).then(function(result){
  console.log(result) ;//성공
},function(err){
  console.log(err); //실패
});


//실제 작업의 예
getData(actionId)
  .then(renderAction);
  .catch(handleError);
  .finally(clearAction);
```

es8에 등장한 async는 promise를 리턴하는 함수입니다.
스레드 프로그래밍의 join처럼 await를 이용하여 비동기 리퀘스트를 컨트롤 할 수 있습니다.
```
//es7~8
async function loadData() {
    // `rp` is a request-promise function.
    var promise1 = rp('https://api.example.com/endpoint1');
    var promise2 = rp('https://api.example.com/endpoint2');

    var response1 = await promise1;
    var response2 = await promise2;
    return response1 + ' ' + response2;
}

loadData().then((result) => console.log(result));
```

## NPM
* * *

NPM은 Node Pakcage Manager의 약자입니다. 이름 그대로 javascript package를 관리합니다.
c#의 nuget, java의 maven 과 gradle과 같은 역할을 한다고 생각하시면 됩니다.
package run 등의 기능 역시 담당하기 때문에 특히 gradle과 유사하다고 볼 수 있습니다.

## Webpack
* * *

번들러

## Babel
* * *

하위 호환 라이브러리