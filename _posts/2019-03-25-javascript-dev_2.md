---
title: "Javascript Dev Overview"
date: 2019-03-25 22:34:28 +0900
excerpt: "Javascript Dev Overview"
header:
  overlay_image: /assets/images/wallpaper.jpg
  overlay_filter: 0.5 # same as adding an opacity of 0.5 to a black background
categories: programing javascript
tags: javascript Node NPM
---
Javascript Dev Overview (2)
=============

## Overview
* * *

지난 포스팅에 이어서 이번엔 개인적으로 자바스크립트 개발환경에 가장 큰 변화를 줬다고 생각하는
NPM 대해서 소개하려고 합니다.

각각에대해 간단히 말씀드리면 NPM은 Package Manager입니다. 
Java에 익숙하신 분들이라면 Maven의 bean repository 기능을 생각하시면 좋습니다.
MS의 C#에 익숙하신 분들은 NuGet이라 생각하시면 좋을 것 같습니다.
리눅스의 yum이나 mac os의 brew도 있겠네요.
다들 '이젠 없으면 어떻게 작업하나' 싶은 툴들입니다. 
때문에 npm도 역시 자바스크립트 개발에 빠질 수 없는 툴이 되었습니다.

이제 npm이 어떤 필요에 의해 생성되었고, 어떻게 사용하는지 소개해 드리겠습니다.

## NPM (Node Package Manager)
* * *

2012년 쯤 웹 외주를 하고 있을 때, jquery나 bootstrap을 적용하기 위해 각각 공식사이트에 접속해서 자바스크립트 파일을 직접 다운로드 받았던 기억이 있습니다. 혹은 cdn 주소를 복사해서 html에 붙여넣기 하거나요.
모듈 하나 둘 어딘가에 찾아서 copy & paste를 통해 사용했지만, 규모가 커지고 여러 프로젝트가 늘어날 수록 각 모듈들의 관리가 힘들었습니다.
npm은 이런 문제점을 해결해 주는 툴입니다. 
공개된 레퍼지터리에서 모듈을 쉽게 다운로드 받을 수 있고, 또한 업로드도 할 수 있습니다.
각 모듈의 공식홈페이지에 접속해서 다운로드 받을 필요가 없어진거죠.

npm은 이름에서 확인 할 수 있는 것 처럼 node.js의 패키지 매니저입니다. 따라서 npm 사용을 위해 node.js를 설치해야합니다.

node 설치는 공식 홈페이지에서 [다운로드](https://nodejs.org/ko/) 받거나 brew나 yum과 같은 패키지매니저를 통해 간단히 설치할 수 있습니다.

```
//mac os
brew install node
npm install -g npm // -g 옵션을 넣으면 전체 시스템에서 사용 함을 의미합니다.

//cent os
yum install npm
```

설치를 했으므로 새로운 모듈을 생성해봅시다.
이제 원하는 디렉토리로 이동하여 cmd를 입력합니다.

```
npm init
```

그럼 시스템은 새로생성하는 모듈의 패키지 이름, 버전, 설명등을 입력받습니다.
적당히 입력하면 해당 디렉토리에 아래와 같은 package.json 파일이 생성됩니다.
```
{
  "name": "nodetest",
  "version": "1.0.0",
  "description": "test",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "author": "Hyungjung Lee <lhj5772@me.com> (https://hyungjung-lee.github.io)",
  "license": "ISC"
}
```
생성 된 package.json은 이제 여러분이 작업 할 모듈의 메타속성이 됩니다.
main은 모듈의 entry 파일입니다.
scripts는 모듈을 이용해서 실행할 명령어 리스트입니다. 모듈을 실행하거나, 테스트, 빌드하는 작업이 들어갈 수 있습니다.
scripts에 들어간 명령어는 아래와 같이 실행 할 수 있습니다.
```
npm run test
```

이제 앞서 말씀드린대로 npm을 통해 다른 모듈을 내려받는 방법에 대해 알아봅시다.
package.json이 위치하는 디렉토리에서 아래와 같이 cmd를 실행합니다.

```
npm install react --save //--save 명령어는 package.json에 내려받은 정보를 기입한다는 의미 입니다. (필수)
```

다시 확인해보면 아래와 같이 package.json에 dependencies가 추가되고 react가 추가된 것을 확인할 수 있습니다.
```
{
  "name": "nodetest",
  "version": "1.0.0",
  "description": "test",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "author": "Jaewon Jo <jaewon.jo@linecorp.com> (http://)",
  "license": "ISC",
  "dependencies": {
    "react": "^16.8.5"
  }
}
```

또한 디렉토리를 확인해 보면 아래와 같이 node_modules와 package-lock.json이 추가되어 있습니다.
```
node_modules  package-lock.json	 package.json
```

node_modules는 install을 통해 내려받은 모듈이 저장되어있습니다.
package-lock.json에는 react 다운로드 정보와 react의 dependency관계에 있는 모듈이 같이 기입되어 있습니다.
npm에서 자동생성 되는 파일이므로 직접 수정하는 일이 없도록 합니다.

마지막으로 개발환경에서 사용되는 모듈을 내려받는 방법인 --save-dev를 알아봅시다.
개발을 진행하다보면 복호화, 빌드 전처리 등을 통해서 추가모듈을 내려받는 경우가 있습니다.
npm에서는 이를 별도의 기능으로 지원하여 --save-dev로 내려받은 모듈은 production 빌드에 해당 모듈이 들어가지 않습니다.
빌드 전, 빌드 후에만 해당 모듈을 사용하는 것이죠.

아래와 같이 개발환경에만 사용되는 모듈을 다운로드 해봅시다.
```
npm install webpack --save-dev
```

방금 전 다운로드받은 react와 다르게 devDepenencies에만 추가되어있는 것을 확인할 수 있습니다.

```
{
  "name": "nodetest",
  "version": "1.0.0",
  "description": "test",
  "main": "index.js",
  "scripts": {
    "test": "jest"
  },
  "author": "Jaewon Jo <jaewon.jo@linecorp.com> (http://)",
  "license": "ISC",
  "dependencies": {
    "react": "^16.8.5"
  },
  "devDependencies": {
    "webpack": "^4.29.6"
  }
}

```

## Next

다음 포스팅에서는 내려받은 자바스크립트 모듈과 작업중인 모듈을 하나의 자바스크립트로 번들링할 수 있는 툴인 
webpack에 대해서 소개드리겠습니다.