---
title: "UNIT TEST CASE"
date: 2019-08-17 15:51:00 +0900
excerpt: "UNIT TEST CASE"
header:
  overlay_image: /assets/images/wallpaper_2.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing testcase java
tags: java unit test case
---
UNIT TEST CASE
=============

## Overview
* * *

이번 포스팅은 어플리케이션이 문제 없이 빌드되고 기능에 맞춰 동작하는지 확인하기 위한 방법인 Unit Test Case(단위 테스트 케이스)에 대하여 포스팅합니다.

Unit Test Case는 작성은 소프트웨어 생명주기인 [요구사항 분석 - 설계 - 구현 - 테스트 - 유지보수] 에서 거의 마지막 단계에 해당하는 테스트(Stress Test, Benchmark Test, Integration Test 등)에 포함됩니다.

하지만 현대에는 TDD(Test Driven Development)라하여 개발 초기에 기능에 대한 Unit Test Case를 미리 작성하고 설계 단계에서 진행하는 경우도 많습니다.

개념적으로 Unit Test의 'Unit(단위)' 은 언어별로 Function 혹은 Method에 해당하며 각각의 단위 테스트는 '독립적'으로 작성되어야 합니다.

'실용주의 프로그래머를 위한 단위 테스트' 에서는 이 '독립적' 이라는 의미를 아래와 같이 기술하였습니다.

```
Independent(독립적)
테스트는 깔끔함과 단정함을 유지해야 한다. 즉, 확실히 한 대강에 집중한 상태여야 하며, 환경과 다른 개발자들(명심하라. 다른 개발자들이 동시에 같은 테스트를 실행해 볼 수도 있다)에게서 독립적인 상태를 유지해야 한다.
또한 독립적이라는 것은 어떤 테스트도 다른 테스트에 의존하지 않는다는 것을 의미한다. 어느 순서로든, 어떤 개별 테스트라도 실행해 볼 수 있어야 한다. 처음 것을 실행할 때 그 밖의 다른 테스트에 의존해야 하는 상황을 원하지는 않을 것이다.
모든 테스트는 섬이어야 한다.
출처 : 실용주의 프로그래머를 위한 단위 테스트 with JUnit
```

따라서 각각의 테스트는 서로에 대해 독립적이어서 변수를 공유하지도 않고 순서에도 영향이 없어야합니다. 

하지만 실제로 로직이 내부의 각각의 객체들이 모두 독립적일 수는 없습니다. 

단위 테스트에서는 이런 비즈니스 로직을 담당하고 있는 객체들을 독립화하는 것을 격리(Isolation)라고 합니다.

격리를 위해 로직에서 사용하는 객체들의 '대역'을 생성하는데 이를 '테스트 더블(Test Double)'이라 하고 대역의 종류에는 Stub/Mock 가 있습니다.

![table](/assets/images/testdouble.gif)

## Stub 이란?

Test Case에서 사용하는 객체에 대해 미리 정해진 답을 정해 놓는 것을 Stub 이라고 합니다.

예를들어 A객체를 Stub이라 하면 A.afunc('test') 는 항상 'afunc-test'라는 값을 리턴하고 A.afunc('stub')은 항상 'afunc-stub'이라는 결과값을 내놓는 것입니다.

데이터베이스와 연결하는 테스트를 진행할때 위와같이 임의의 값을 지정해놓으면 직접 데이터베이스에 접근하지 않고 결과 값을 받아오는것 '처럼' 코드를 작성할 수 있습니다.

아래는 이미지 서비스에서 이미지 레포지터리로 접근하여 이미지 리소스를 받아오는 함수의 TestCase입니다.
```
given:
def imageResources = Stub(List)

imageService.@imageRepo = Stub(ImageReop) {
    getImageResources() >> imageResources
}

when:
def ret = imageService.getImageResources(_)

then:
ret == imageResources
```

### 장점

- 의존하는 것에 독립적으로 개발/테스트가 가능 하다.
- Stub 다양한 응답 결과(canned answer) 케이스(결제 성공/실패/예외)를 만들어 테스트할 수 있다.
- 이로인해 아직 작성되지 않은 객체에 대해서도 결과 값만 먼저 작성하여 로직 테스트를 진행할 수 잇다.

## Mock 이란?

Stub이 결과에 대해서 집중하였다면 Mock은 객체가 호출 된 횟수에 집중합니다. 예를 들어 

A.getImage('a')라는 함수를 실행 했을때,  imageService.getRepo 함수가 몇번 호출되었는지 확인 할 수 있게됩니다.

만약 코드가 잘못되어 3번 혹은 4번 5번 호출이 되었다면, Unit Test Case를 통해서 에러를 확인 할 수 있게됩니다.

```
given:
def imageResources = Stub(List)

imageService.@imageRepo = Mock(ImageRepo)

when:
def ret = imageService.getImageResources(_)

then:
1 * imageService.@imageRepo.getRepo(_)
```


## Example

아래는 자바에서 사용되는 Unit Test Case 프레임워크인 [Spock](http://spockframework.org/spock/docs/1.3/index.html) 입니다. Mock과 Stub 객체를 쉽게 작성할 수 있고 given/when/then 을 명시적으로 선언하여 
Test Case의 가독성을 높혀줍니다.


## ETC

unit test case의 경우 고정 된 최상의 결과값만 사용하므로 Mock 객체를 사용하지 않는 Integration Test도 같이 진행해주는 것이 검증된 어플리케이션 작성에 도움이 됩니다. 
