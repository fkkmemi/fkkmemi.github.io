---
layout: single
title: NEMBV 2 Front-end Setup
category: nembv
tag: [nembv,bootstrap,vue]
comments: true
---

front-end는 javascript는 vue로 css는 bootstrap으로 설치할 것이다

각각 따로 설치할 수도 있지만

마침 bootstrap이 4로 업데이트 되면서 vue와 결합한 프로젝트가 있다

바로 **bootstrap-vue**

설치는 [https://bootstrap-vue.js.org/docs](https://bootstrap-vue.js.org/docs) 중 풀패키지로 아예 vue-cli 까지 통합된 버전으로 설치 해보겠다

### BootstrapVue 설치

기존 nembv 디렉토리 하부에 fe라는 디렉토리에 설치할 것이다.

공식사이트의 vue-cli 부분을 풀패키지 설치버전으로 약간 변형을 했다.

```bash
# Ensure vue-cli is installed and up to date
$ npm i -g vue-cli
# Initialize a bootstrap project in the directory 'fe'
$ vue init bootstrap-vue/webpack fe
$ ? Project name fe
$ ? Project description nembv stack
$ ? Author fkkmemi <fkkmemi@gmail.com>
$ ? Vue build standalone
$ ? Install vue-router? Yes
$ ? Use ESLint to lint your code? Yes
$ ? Pick an ESLint preset Airbnb
$ ? Setup unit tests with Karma + Mocha? Yes
$ ? Setup e2e tests with Nightwatch? Yes
# Change into the directory
$ cd fe
# Install dependencies
$ npm i
# Fire up the dev server with HMR
$ npm run dev

```

- vue-cli를 전역으로 설치한다.  
    > *Vue.js는 단일 페이지 응용 프로그램을 빠르게 스캐폴딩하기 위한 공식 CLI를 제공합니다. 현대적인 프론트엔드 워크플로우를 위해 잘 구성된 빌드 설정을 제공합니다. 핫 리로드, lint-on-save 및 프로덕션 준비가 된 빌드로 시작하고 실행하는데 몇 분 밖에 걸리지 않습니다.*
- vue 기본 골격과 bootstrap-vue, webpack을 같이 fe 디렉토리에 설치하면 위와 같은 물음이 나오는데 
설명과 eslint만 airbnb 스타일로 변경하고 나머진 전부 엔터로 기본 구성을 마쳤다. 설치하는데 시간이 상당히(5분?) 걸리니 당황하지말고 기다리면 된다.  
    > *eslint는 기본 스타일 보다 airbnb가 내게는 더 깔끔해 보였는데 이건 취향이니 아무거나*
- fe 디렉토리로 이동해서 의존요소들을 설치한다
- 개발서버를 실행(npm run dev)함과 동시에 브라우저가 팝업된다  
    > http://localhost:8080으로 열리면 성공이지만 에러가 나있다..   2  https://google.com/#q=import%2Ffirst
                                                    1  http://eslint.org/docs/rules/quotes
- eslint error 때문에 로드가 안된 것인데 아마도 bootstrap-vue가 eslint로 안만들어 놓은 듯하다
- 문제되는 부분을 잘 수정해도 되지만 어렵다면 .eslintignore를 수정해서 우선 돌아가게 만들어 본다.
- 문제되는 부분은 src/main.js의 css import 관련인데.. .eslintignore 파일에 src/main.js를 등록해서 무시한다. 
- ctrl+c 로 서버를 정지하고 다시 npm run dev를 하면 정상 작동을 한다.  
    ![vue 화면](/images/nembv/2018-03-16 14-40-56 fe.png) 가 나오면 성공!  
    
### 설치 확인

이제 백엔드+프론트엔드의 기본 골격이 모두 갖춰졌다.

![설치 화면](/images/nembv/2018-03-16 14-48-09 nembv be_fe.png)
