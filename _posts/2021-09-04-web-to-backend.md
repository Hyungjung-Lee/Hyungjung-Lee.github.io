---
title: "Web To Backend"
date: 2021-09-04 09:00:00 +0900
excerpt: "Web To Backend"
header:
  overlay_image: /assets/images/wallpaper_2.png
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: study 
tags: study javascript web
---
Web To Backend
=============

## Web Browser

![webbrowser_example](/assets/images/webbrowser_example.png)

### Architecture

![web](/assets/images/webbrowser_arch.png)

### Rendering Engine

![rendering_engine](/assets/images/rendering_engine.png)

#### Webkit

Safari, Chrome 에서 사용

![webkit](/assets/images/webkit.png)

#### Gecko

파이어폭스에서 사용

![webkit](/assets/images/gecko.png)


### Javascript Engine

- V8 : 오픈소스로 구글에서 개발. C++로 작성, 구글 크롬, Node.js에서 사용.

- SpiderMonkey : 최초의 자바스크립트 엔진. 넷스케이프 네비게이터 웹 브라우저에서 사용. 지금은 모질라 파이어폭스에 사용.

- Rhino : 모질라 재단에서 운영. 오픈소스이며, 전체가 자바로 개발.

- JavaScriptCore : 오픈소스, 니트로라는 이름으로도 알려져 있으며 애플이 사파리를 위해 개발.

- Chakra(Jscript9) : 인터넷 익스플로러.

- Chakra(JavaScript) : 마이크로소프트 엣지.

- Nashron : 오픈JDK의 일환으로 오픈소스이며 Oracle Java Languages and Tool Group이 개발. 

- JerryScript : 사물인터넷을 위한 경량 엔진.
  

### Rendering

#### RenderTree

![web_parsing](/assets/images/web_parsing.png)

#### Dom

Html파일의 텍스트를 읽어서 객체화한 것. Document Object Mode

```
var header = document.createElement('h2'); 
```

#### Virtual Dom

Dom을 매번 갱신하는것은 비용이 크기때문에, 가상환경의 dom을 생성해서 필요한 부분만 업데이트 하는 것을 virtual dom이라고 함.


![virtual_dom](/assets/images/virtual_dom.svg)

![virtual_dom2](/assets/images/virtual_dom_render.png)


**React는 실제 Dom에 접근할 때 useRef를 통해서 접근**

#### CSS

Cascading Style Sheet

- Internal
  - html 파일내에 선언
- Inline
  - tag 내에 style 선언
- External
  - 외부 CSS 파일


### URI

서버에 접근하기 위한 주소. Uniform Resource Identifier

#### HTTP

일반적인 Web 통신에서 사용한다. Hypertext Transfer Protocol

##### REST API

- GET
  - QueryParam, Header
- POST
  - Header, Body
- PUT
  - Header, Body
- DELETE
  - Header, Body

#### HTTPS

SSL을 사용하는 HTTP의 보안형태. Hypertext Transfer Protocol Over Secure Socket Layer

### Storage

Stateful 한 작업을 위해 사용.

- Cookie
  - 만료기간, 헤더에 내용을 담아서 서버에 전달
- Session
  - 세션아이디만 클라이언트에 저장, 실제내용은 서버에 있음
- Cache
  - 리소스 파일의 임시저장소, hash check를 하여 동일한 파일은 내려받지 않는다.

### Resource

MINE Type (Multipurpose Internet Mail Extensions)

- HTML
- CSS
- Font
- JavaScript
- Json
- PDF
- Image
- Video
- ETC
  
## Backend

![backend](/assets/images/backend.png)

### Load Balance

부하분산 또는 로드 밸런싱(load balancing)은 컴퓨터 네트워크 기술의 일종으로 둘 혹은 셋이상의 중앙처리장치 혹은 저장장치와 같은 컴퓨터 자원들에게 작업을 나누는 것을 의미

[출처](https://gmldbd94.tistory.com/84)

![backend](/assets/images/dns_loadbalance.png)

#### l4

물리적으로 구분 

![loadbalance](/assets/images/loadbalance_l4.png)

#### l7

어플리케이션의 로직에 따라 구분

![loadbalance](/assets/images/loadbalance_l7.png)

## Reference

[Naver D2](https://d2.naver.com/helloworld/59361)

[webkit](https://github.com/WebKit/webkit/tree/master/Source/WebCore/html/parser)

[gecko](https://github.com/mozilla/gecko-dev/tree/master/parser/htmlparser)

[rendering 방식](https://falsy.me/브라우저의-이해-1-reflow-repaint에-대하여-알아봅니다/)

[css](https://aboooks.tistory.com/49)

[dom](https://www.howdy-mj.me/dom/what-is-dom/)