---
title: "Javascript async control"
date: 2019-04-10 23:00:00 +0900
excerpt: "Javascript async control"
header:
  overlay_image: /assets/images/wallpaper.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing javascript
tags: javascript async
---
Javascript async control
=============

## Overview
* * *

프론트 작업을 하다보면 여러개의 데이터를 얻어와야하는 경우가 많습니다.

특히 form 페이지에서는 모든 데이터를 수신 받고 작성을 시작해야 예외상황이 발생하지 않기 때문에,

데이터 수신을 기다리도록 별도의 작업을 해야합니다.

하지만 다량의 fetch를 호출하게되면 비동기->동기 처리를 하는 과정에서 코드가 난잡해지기 쉽습니다.


이 포스팅에서는 비동기 처리 제어를 위한 유틸을 소개하고 사용 패턴을 간단히 소개합니다.


## Example
* * *

자바스크립트에서는 비동기 작업을 위한 promise와 async/await를 제공하고 

react-redux에서는 비동기 dispatch를 위해 thunk middleware를 제공합니다.


이를 조합하여 action 함수의 encapsulation을 유지하면서 비동기 제어를 처리할 수 있습니다.


아래는 fetch 선언입니다.

```
const fetch1 = (id) => {
  return async dispatch => { //!important
    //dispatch(startFetch())
    let response = {}
    try {
      response = await axios.get(`/api/${id}`)
      dispatch(loadAction({ data: response.data }))
    } catch (e) {
      dispatch(errorAction({ data: e }))
    } finally {
      dispatch(endAction())
    }
    return response.data // then result value
  }
}
 
 
// fetch2..
// fetch3..
 
 
export {
  fetch1,
  fetch2,
  fetch3
}
```
return을 통해 thunk middleware에 비동기 메세지를 전송합니다.

이때 return 값에 async 키워드를 선언하여 promise를 생성합니다.

비동기 코드블럭 내부에서는 await를 이용하여 fetch를 기다립니다.

이렇게 되면 action을 호출 했을 때 action 내부에서 로딩바처리 (ex - startFetch), 데이터 저장 (ex - loadAction), 에러처리 ( ex - endAction())을 진행 할 수 있게됩니다.

각각의 액션은 encapsulation 처리가 되어있기 때문에, 사용하는 외부에서는 fetch 함수에 대해서만 알고 있으면 되기에 코드가 깔끔해집니다.



httpClient는 axios를 사용했는데, fetch를 이용하여도 같은 패턴으로 사용 할 수 있습니다.



아래는 호출 방법입니다.

```
import { bindActionCreators } from 'redux'
import * as actions from './actions'
 
 
// bind action
function mapDispatchToProps(dispatch) {
  return {
    actions: bindActionCreators( ...actions , dispatch)
  }
}
 
 
class Example extend React.Component {
    /// ..other funcs...
    componentDidMount() {
        //await fetch1 only
        this.props.actions.fetch1('id')
                          .then(response=>{ setInit(true) })
                          .catch(e => {})
        //await all fetchs..
        Promise.all([
            this.props.actions.fetch1('id'),
            this.props.actions.fetch2('id'),
            this.props.actions.fetch3('id')
        ]).then(response =>{ setInit(true) }
          .catch(e => {})
    }
}
 
 
connect(mapStateToProps)(Example)
```
mapDispatchToProps 함수에서 bindActionCreator를 이용해 dispatch와 action을 결합합니다.
이렇게 하면 사용시 props.dispatch(props.action)과 같이 호출하지 않아도 되기때문에 코드가 조금 깔끔해집니다.

componentDidMount 에서 fetch를 호출하면 위에서 정의했을때 async 값으로 리턴값을 정의 했기때문에 promoise가 리턴됩니다
따라서 then을 통해 비동기 처리를 진행하면됩니다.

이글의 목적인 다량의 fetch를 사용했을 경우는 Promise.all을 통해서 각각의 리퀘스트에 대해 await 처리 할 수 있습니다.

### Redux Thunk Middleware

thunk middleware는 위에서 소개한 것과 같이 비동기 dispatch를 위한 미들웨어입니다.

따라서 사용을 위해 createStore시 추가해 줘야 합니다.

```
import thunkMiddleware from 'redux-thunk'
 
 
const store = createStore(
 reducers,
 compose(
 applyMiddleware(
   thunkMiddleware
 ),
 )
)
```

## Async/await

자바스크립트 es2017 버전부터 사용가능한 비동기 키워드입니다. 해당 키워드를 함수앞에 정의하면 return 값은 promise 객체로 반환됩니다.

이때 async로 선언한 함수 앞에 await를 사용하면 리턴값은 promise 대신 그 객체내에서 resolve 된 결과값이 반환됩니다.

따라서 then을 통해 비동기제어를 할 필요가 없지고, 코드가 간결해집니다.

이때 promise 객체 내부에서 reject을 하였을 경우는 try/catch 의 catch로 핸들링이 가능합니다. 

## Promoise All

자바스크립트 es6 버전부터 사용가능한 promise의 유틸 함수입니다. All 의 args로 promise 객체를 넘겨주게되면 배열의 모든 객체가 완료될 때 then을 호출해 줍니다.

Promise.all([promise1, promise2, promise3])

           .then(function(values) {

              console.log(values);
           });

## Redux-bindActionCreators

redux에서 제공해주는 action 유틸입니다. dispatch 없이 action을 호출할 수 있게됩니다.

https://redux.js.org/api/bindactioncreators