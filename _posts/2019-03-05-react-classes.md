---
title: "React Component Types"
date: 2019-03-04 22:34:28 +0900
excerpt: "Introduce Hook"
header:
  overlay_image: /assets/images/wallpaper.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing react
tags: react class hook
---
React Component Types
=============

## Overview
* * *

2018년 10월 React Conf에서 새로운 React 컴퍼넌트 작성방식인 Hook을 공개하였습니다. [영상](https://www.youtube.com/watch?v=dpw9EHDh2bM)

이후 한동안 Alpha 버전이었던 Hook은 2019년 2월 릴리즈 된 React v16.8.0 버전부터 정식으로 사용할 수 있게 되었습니다. 

영상에서 소개되는 Hook은 얼핏보아도 기존 React 에서 Component를 작성할때 사용되던 모습과 조금 다릅니다.

기존에도 여러 종류의 Component 작성방식이 있었는데, Hook이 추가되면서 각각 어떤 부분들이 다른지 이번 포스팅에서 차이점을 간략하게 소개하려고 합니다.

## Class Base Component
* * * 

Component는 React의 UI Element가 어떤식으로 노출이 되는지 정의 되어있는 '함수'입니다. 

하지만 상태, lifecycle등을 함수 하나로 표현하기는 개념적으로 쉽지 않았습니다.

React는 이러한 요소들을 javascript의 특수한 문법을 사용하여 앞서말한 요소들을 하나의 묶음으로 제공합니다.

es6에서부터 javascript에서는 c/c++, c#, java와 같이 기성언어서 사용할 수 있는 class를 제공합니다.

React는 이를 이용하여 함수를 class 형태로 만들어 UI를 작성합니다. base class를 제공하여 사용자로 하여금 '상속' 하고 lifecycle 메서드를 override 하는 형태로 Component를 작성하도록 하였습니다.

또한 클래스 생성자로부터 받는 변수집합인 'props' 다른 언어의 클래스들과 같이 member variable과 같은 역할을 하는 'state' 라는 개념이 존재합니다.

### React.Component
* * *

React.Component는 그러한 class 중 가장 기본이 되는 base class 입니다. 기본적인 모습은 아래와 같습니다.

~~~
class HelloWorld extends React.Component {
  //객체가 생성될 때 호출됩니다.
  //초기 state를 설정할 수 있습니다.
  constructor(props) {
    super(props);
    this.setState({test:'helloWorld'})
  }
  //객체가 DOM 위에 랜더링 될 때 호출됩니다.
  componentDidMount() {
  }

  //객체가 삭제될 때 호출됩니다.
  componentWillUnmount() {
  }

  //사용자 커스텀 함수입니다.
  handlePrint() {
    //this 객체를 이용하여 객체 값에 접근합니다.
    return <div>this.state.test</div>
  }

  //객체를 실제 랜더링 할때 호출합니다.
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
        {handlePrint()}
      </div>
    );
  }
}

//React DOM 위에 랜더링 하는 코드입니다.
ReactDOM.render(
  <HelloWorld />,
  document.getElementById('root')
);
~~~

기본 제공되는 lifecycle 함수를 위와 같이 오버라이드 하고 원하는 로직을 넣을 수 있습니다.

물론 handlePrint와 같이 사용자가 원하는 함수도 작성 할 수 있습니다. 

react에는 위와 같은 일반적인 객체 생성 lifecycle 외에도 추가적인 lifecyle이 존재합니다. 

그 배경부터 알아보면, 최근 웹페이지는 새로고침을 하지않고도 데이터를 추가로 노출하거나 애니메이션 되는 경우가 많습니다. 

react는 이러한 상황에 객체를 새로 만들지 않고 기존 객체의 render 함수를 다시 호출(re-rendering)하여 새롭게 화면에 표시합니다.

React에는 객체가 re-rendering되는 경우가 두가지있습니다.

- 첫째는, React는 객체를 소유한 상위 객체로부터 변수를 주입받습니다. 이러한 변수를 Props라고 합니다. 이 Props가 변경되면 re-rendering 하게 됩니다.
- 둘째는, 객체가 가지고 있는 상태변수 'state'가 변경 될 때 입니다.

아래 코드는 re-rendering이 되는 lifecycle 함수의 종류입니다.

~~~
class HelloWorld extends React.Component {
  //상위 객체로부터 props가 변경 될 때 호출됩니다.
  componentWillReceiveProps(nextProps) {

  }
  //해당 객체가 re-rendering 되어야하는지 가/부를 리턴값으로 판단합니다.
  shouldComponentUpdate(nextProps) {

    return true;
  }

  //해당 객체를 rendering 하기 직전 호출됩니다.
  componentWillUpdate() {

  }

  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
      </div>
    );
  }

  // rendering이 완료되고 호출됩니다.
  componentDidUpdate() {
    
  }
}

ReactDOM.render(
  <HelloWorld />,
  document.getElementById('root')
);
~~~

### React.PureComponent
* * *

PureComponent는 개발자 편의를 위해 만들어진 base class로 볼 수 있습니다. 위에 기술 된 Component와 다른 점이 shouldComponentUpdate 함수 외에는 없는데, 
props의 값을 얕은 비교를 통해 알아서 rendering 가/부를 판단합니다,


## Functional Stateless Component
* * *

위에서 보신 것과 같이, class based component는 단순한 component를 작성하는데도 여러줄의 코드가 사용됩니다. 
React 14 버전부터 제공되는 functional stateless component는 이러한 boilerplate 코드 없이 간결하게 작성 될 수 있습니다.

~~~
const HelloWorld = ({name}) => (
    <div>{`Hi ${name}`}</div>
);
~~~

앞서 기술한 component의 정의인 '함수'의 모습과 일치합니다. 다만 간결해진 만큼 커버할 수 있는 기능도 많이 줄 었습니다.

- 첫째로, state가 존재하지 않습니다. props 값을 통해서만 동작합니다.

- 둘째로, props 비교없이 항상 랜더링 되기 때문에 복잡한 연산을 처리하는 comonent일 수록 속도는 결과적으로 느립니다.

다만 아래와 같은 장점을 가지고 있습니다.

- 첫째로, 불필요한 lifecycle이 없어 코드 간결합니다.

- 둘째로, state가 존재하지 않기 때문에, 상태 고려 없이 재사용이 가능합니다.

따라서 항상 다시 랜더링이 되어야하는 간단한 ui 컴포넌트에 적합하다고 볼 수 있습니다.

아래는 javascript에서 사용되는 class에 대한 의견글 입니다.
개발자들이 class에 대해 회의감을 느끼는 것을 볼 수 있습니다.

[클래스에대한 의견](https://medium.com/@housecor/in-defense-of-javascript-classes-e50bf2270a95)

## Recompose
* * *

react에서 제공 되는 기본 component 작성방식으로는 재사용성, 코드 가독성이 떨어진다고 판단한 개발자들은 이를 개선하기 위해 'Recompose'라는 추가 라이브러리를 작성하게됩니다.

react의 component 작성 패턴인 HOC (Higher-order Component)를 최대한 활용한 라이브러리 입니다.

state가 있는 functional component를 만들 수 있게 됩니다. 

state에 대한 정의, lifecycle에 대한 정의를 함수단위로 작성하기 때문에, 재사용성이 올라갑니다.

아래는 recompose의 예제 코드입니다.

~~~
const enhance = compose(
  withState('counter', 'setCounter', 0),
  lifecycle({
    componentDidMount() {

    }
  })
)
const Counter = enhance(({ counter, setCounter }) =>
  <div>
    Count: {counter}
    <button onClick={() => setCounter(n => n + 1)}>Increment</button>
    <button onClick={() => setCounter(n => n - 1)}>Decrement</button>
  </div>
)
~~~

## Hook
* * *

3rd party 라이브러리인 recompose의 장점을 정식 react기능으로 편입한 형태입니다.

functional component에 useState를 이용하여 state 처리가 가능하게 되었고,
useEffect를 작성하여 특정 props가 변경되었을때 랜더 처리를 할 수 있게 되었습니다.

```
function Example() {
  // Declare a new state variable, which we'll call "count"
  const [count, setCount] = useState(0);

  useEffect(_=>{
    //do action when comonentDidMount timing.
  },[])

  useEffect(_=>{
    //do action when change count
  },[count])


  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>
        Click me
      </button>
    </div>
  );
}
```