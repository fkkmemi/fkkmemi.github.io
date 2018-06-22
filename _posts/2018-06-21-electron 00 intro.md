---
layout: single
title: Electron 0 일렉트론뷰으로 우아한 데스크탑 앱 만들기 기초
category: electron
tag: [electron,talk,idea,nodejs,vue,vuetify]
comments: true
---

javascript는 정말 편리한 언어입니다.

하지만 브라우저 안의 개구리였습니다.

브라우저 안에서는 보안 측면등의 이유로 많은 시스템자원 엑세스가 힘듭니다.

특히 파일, 위치정보를 브라우저가 가져가면 곤란하기 때문입니다.

node.js의 V8엔진은 javascript가 브라우저 밖으로 나가게 도와주었습니다.

script 주제에 파일은 기본이고 웹서버를 구동할 수 있게했습니다. 

서버용도 외에도 사용이 가능하지만 데스크탑에서는 패키징이 불편해서 주로 콘솔 테스트용으로 사용했습니다.(_$ node go.js 이런식으로.._)

그리고 처음 배울 때 서버부터 만들기가 여건이 어렵죠

서버 로컬테스트 잘 해봐야 어딘가에 배포해야할 실제서버 작업 및 튜닝이 또 다른 복잡도가 따라오기 때문입니다.

서버 쪽을 해보고 싶으신 분은 [nemvv 강좌](https://fkkmemi.github.io/categories/#nemvv)를 참고하세요

서버에 비해 데스크탑앱은 단순하고 직관적입니다.

node로 데스크탑앱을 만들기위해 nw.js라는 것을 써본적도 있습니다.

자세히는 설명이 어렵지만 제 경험으로는 일렉트론이 4배이상 좋습니다.

이제 V8의 자유도로 브라우저와 서버를 넘어 일렉트론뷰으로 우아한 데스크탑 앱을 만들어 보겠습니다.

{% include toc %}

# 시작하게된 이유

저는 잡개발자입니다. 

펌웨어, 데스크탑앱, 웹서버, 디비, 모바일등 대부분 혼자 만듭니다. 

닥치는 대로 필요한 것을 취하기 때문에.. 

어짜피 새로 만드는것 대부분 신기술을 취합니다.

어찌보면 풀스택 개발자라고 불리울 수도 있지만.. 반대로는 뭐하나 제대로 내세울 것이 없습니다.

잡스러운 기술 중 그나마 진득하게 잘하고 오래했던 것은 역시나 윈도우 프로그램인데요..

일반적인 VS로 만들지 않고, c++builder/delphi라는 기묘한 물건으로 윈도우 프로그램은 뚝딱 만들었죠..

회사 리포지토리에 등록된 앱만 300개가 넘을 정도로 많이 만들었습니다.(_지그앱, 파일분석툴, 관리툴등.._)

그 중 디비 연동하면서 인스톨 패키지 까지 입혀서 배포 하는 서비스형 앱들도 서너가지 있습니다.(_5년이 흐른 지금도 잘들 쓰고 있다는.._)

갑자기 뜬금 없는 윈도우 프로그램 개발 자랑 같은 얘기를 하는 이유는..

윈도우 프로그램을 잘 만드는데도 불구하고 굳이 일렉트론으로 데스크탑을 만들어야 되는 이유를 설명하기 위해서입니다.

# 일렉트론으로 전향한 이유

1. 크로스플랫폼: 윈도우, 맥, 리눅스(크롬 돌아가는 os 전부) 지원    
윈도우 프로그램(.exe)은 맥에서 돌아가지 않습니다.  
이부분이 제일 주효했습니다.  
뱅킹 외에 윈도우는 사용도 안하는데(_뱅킹마저 거의 모바일_) 윈도우 프로그램 개발자라니...  
이젠 윈도우 프로그램 개발자 -> 데스크탑 앱 개발자가 되겠네요

2. 언어: javascript(vue.js)  
C/C++, java등의 전통 언어는 여전히 우수한 언어이지만 대부분의 용도에서는 javascript에 비해 생산성이 떨어집니다.(_물론 코어나 정밀 모듈쪽은 당연히 c/c++같은 네이티브 언어가 좋다고 봅니다. mongoDB도 c기반입니다_)  
javascript는 처음 접하는 사람도 익히기 쉽고 웹개발하시던 분은 보너스죠  
특히나 비동기 방식이라 C/C++때 늘 고민했던 블러킹 되서 프리징 걸리지 않게 하기 위한 쓰레드 처리가 거의 필요가 없습니다.  
사실 javascript자체가 익히기 쉽다기보다는 진화된 javascript프레임웤인 vue.js로 작업할 것이기 때문이기 때문입니다.  
vue.js를 완벽하게 다루는 것은 당연히 너무 어렵지만(react, angular도 마찬가지) 제 철학은 딱 필요한 만큼 쉽게 접근할 부분만 쓰기 때문에 괜찮습니다. 

3. 라이브러리: npm의 수십만개의 라이브러리가 공짜!(_물론 상용 배포시에는 전 공짜가 아닙니다 주의.._)  
예를 들면 윈도우 프로그램할 때 기초적인 232통신을 하려해도 상용 컴포넌트를 돈주고 사야했습니다.(_물론 공짜도 있지만.._)  
단순히 npm i serialport 하면 그냥 바로 사용됩니다. 
그 밖에도 chartjs나 axios등은 정말 훌륭합니다.

4. 디자인: 제목에 적혀 있는 것 처럼 우아한 것이 중요합니다.  
제 개발 스타일은 무조건 표현이 우선입니다.(_펌웨어를 하더라도 콘솔보다는 액정에 찍어서 디버그.._)
클라이언트나 협력업체를 대할 때도 산출물을 보고 얘기하는 것이 몇장의 도큐보다 나았습니다.  
내실이 알차게 작성된 코드도 중요하지만 끌리는 디자인이 여러모로(_영업적으로도.._) 더 도움이 된다고 생각했습니다.  
그래서 자칭 **비쥬얼디벨로퍼** 입니다  
예전에 윈도우 프로그램할 때는 상용콤포넌트(tms)등을 이용해서 버튼등을 우아하게 했습니다..  
관점의 차이는 있겠지만 더 우아하게 만들 수 있습니다.  
이유는 지난 강좌 nemvv에서 사용했던 vuetify라는 프레임웤을 그대로 사용할 수 있기 때문입니다.  
당연히 데스크탑앱이지만 웹이고 웹이지만 데스크탑앱이고 이도저도 아니라 그런지 아주 오묘하고 아름답습니다.
 
# 목표

저의 경우 생산 공정용(지그, 디비 저장), 각종 기기 파일 분석, 개인용 장난감등으로 사용중입니다.  

특정 파일을 읽고 로컬디비(nedb:mongodb의 작은 버전)를 이용해 소트가 가능한 게시판을 만들어 보겠습니다.

디비는 뭔가 복잡해보여서 기피하시는 분들이 많이 보이는데요 데이터를 처리하는 앱이라면 디비는 꼭 하시는 것이 여러모로 편리합니다.(_두뇌건강에도 좋아보입니다.._)

모바일 앱같은 것도 대부분 작은 로컬디비(sqlite같은)를 사용합니다.

저 같은 경우와 비슷한 분들이 도움이 되었으면 좋겠습니다.
 
# 준비물

node.js 만 홈페이지 가서 다운로드 받아서 설치하면 됩니다. 1분도 안걸립니다.

> 부가 준비물로는 yarn 정도를 깔아두면 좋습니다.  
npm을 좀 더 편리하게 써주게 합니다.  
npm보다 많이 쓰는 추새입니다.

# 설치

그냥 일렉트론이 아닌 vuetify화된 일렉트론으로 설치합니다.

[https://vuetifyjs.com/ko/getting-started/quick-start](https://vuetifyjs.com/ko/getting-started/quick-start)

설치 방법은 위 링크에 자세하게 나와있지만 처음 만든다고 생각하고 한땀 한땀 구현하며 작성하겠습니다.

위 링크를 타고 가면 어떤 형태로 구현할 것인지 템플릿들이 나온 표가 있습니다.

webpack도 있고 nuxt도 있고 다양한데 그중 electron이 있네요

깃헙 고양이 버튼을 클릭하면 자세한 사용법이 나옵니다.

[https://github.com/vuetifyjs/electron](https://github.com/vuetifyjs/electron)

프로젝트 명만 변경하고 공식페이지의 내용 대로 해봅니다.

## 공식페이지의 설치 방법

```bash
# Install vue-cli and scaffold boilerplate
npm install -g vue-cli
vue init vuetifyjs/electron elecapp

# Install dependencies and run your app
cd elecapp
yarn # or npm install
yarn run dev # or npm run dev
```

## 실제 해본 결과

```npm install -g vue-cli``` 는 vue-cli가 이미 설치되어 있어서 패스했습니다.

```bash
$ vue init vuetifyjs/electron elecapp # 엔터만 계속 쳤습니다 저는
? Application Name elecapp
? Project description An electron-vue project
? Select which Vue plugins to install axios, vue-electron, vue-router, vuex
? Use linting with ESLint? Yes
? Which eslint config would you like to use? Standard
? Setup unit testing with Karma + Mocha? Yes
? Setup end-to-end testing with Spectron + Mocha? Yes
? What build tool would you like to use? builder
? author fkkmemi <fkkmemi@gmail.com>

   vue-cli · Generated "elecapp".

---

All set. Welcome to your new electron-vue project!

Make sure to check out the documentation for this boilerplate at
https://simulatedgreg.gitbooks.io/electron-vue/content/.

Next Steps: # 다음 스텝까지 친절하게 나오네요

  $ cd elecapp
  $ yarn (or `npm install`)
  $ yarn run dev (or `npm run dev`)
  
$ yarn
# 어쩌고 저쩌고 깔림..
Done in 75.04s. # 75초가 걸렸네요

$ yarn run dev
```

![alt start](/images/electron/2018-06-22 13-18-52 elecapp.png)

자 보이시나요 정말 앱이 둥 하고 떠있을 것입니다.

elecapp 이라는 프로그램을 만든 것입니다.

기본은 긴 서론이 허무하게? 끝나버렸습니다.

> yarn이 설치하기 귀찮으면 위의 과정을 아래와 같이 바꾸시면 됩니다.(_물론 yarn이 훨씬 빠릅니다 병렬로 내려받아서_)    
yarn -> npm i  
yarn run dev -> npm run dev  

그런데 옆에 무슨 개발자 모드 같은 콘솔창이 있네요..

개발을 위한 용도라는 거죠 dev(development) 크롬의 개발자 창과 같습니다.

앱 안에 크롬부라우저가 떠있다고 생각하면 됩니다.

비즈니스 로직을 넣을 때 뭔가 궁금할때 console.log()를 이용하면 옆에 표시 됩니다.

그 밖에도 스타일이나 이벤트등도 전부 보이며.. 직접 코드를 수정하면 화면에도 반영됩니다.

개발할 때 너무 고마운 창이죠..

하지만 실사용자는 안보이게 해야겠죠.. 

이제 실사용자를 위한 앱을 만들어 보겠습니다.

# 배포

배포(deploy) 관련 처리를 먼저 알려드립니다.

배포는 일반적으로는 매뉴얼 이나 레퍼런스 사이트등에서 맨 마지막에 언급되는 부분이지만.. 

이 일렉트론 프로젝트가 실제 사용자가 쓸 수 있을까? 진짜 되는거야? 라는 고민이 먼저될 것 같아서입니다.

윈도우 프로그램을 제작할 때 제일 신경 썼던 부분이 배포입니다.

실제 사용자는 yarn run dev 같은 걸 하는 게 아닌 exe파일이나 dmg등으로 빠른 접근을 원합니다..

실행파일 하나로 할 수도 있고 이런저런 dll덩어리를 묶은 패키지 매니져로 만든 적도 있습니다.

일렉트론은 맥, 윈도우32~64비트, 리눅스 로 배포가 가능합니다.

vuetifyjs&electron 세트메뉴라 배포용 툴(electron-builder)까지 다 완비되어 있으니 걱정할 필요가 없습니다.

```bash
$ yarn build
```

![alt build](/images/electron/2018-06-22 13-31-14 elecapp.png)

기나긴 과정(약 1~2분?)이 빌드 과정이 끝나면 build 디렉토리에 셋업본이 나옵니다.

셋업본이라는 말이 좀 어색하긴 한데 install shield 이런거 보신 적 있을 겁니다. 인스톨러인데요..

저 같은 경우 윈도우 프로그램을 제작할때 dll이나 리소스등을 exe파일에 죄다 집어넣은 단일 파일 빌드를 즐겨 사용했는데요

그러면 실행파일 하나만 받아도 바로 실행이 가능했습니다.

하지만 사실 몰래 레지스트리에 뭐도 잔뜩 써놓고 local/appdata 같이 엑세스 가능한 곳에 파일도 왕창 저작한 앱도 만들어 봤습니다.(_그야말로 똥덩어리들..._)

그래서 인스톨보다는 언인스톨이 더 중요하기 때문에 스크립트등을 작성해서 인스톨러를 배포했습니다.

나름 인스톨 패키징하는 것도 정성과 시간이 필요한 것인데...

그 정성과 시간이 없이 인스톨러 형태로 빌드가 되었다는 것입니다.

저는 맥에서 빌드 했기 때문에 .dmg 라는 파일이 나왔습니다.

윈도우에서 빌드하면 .exe가 나오는데.. 그렇다고 귀찮게 윈도우 켜서 빌드할 수는 없겠죠?

그래서 빌드 방법을 추가해보도록 하겠습니다.

우선 빌드를 위한 것을 간단하게 알아보겠습니다.

**package.json**  
```javascript
{
  "name": "elecapp",
  "version": "0.0.0",
  "author": "fkkmemi <fkkmemi@gmail.com>",
  "description": "An electron-vue project",
  "license": null,
  "main": "./dist/electron/main.js",
  "scripts": {
    "build": "node .electron-vue/build.js && electron-builder",
    "build:dir": "node .electron-vue/build.js && electron-builder --dir",
    "build:clean": "cross-env BUILD_TARGET=clean node .electron-vue/build.js",
    "build:web": "cross-env BUILD_TARGET=web node .electron-vue/build.js",
    "dev": "node .electron-vue/dev-runner.js",
    "watch": "cross-env BUILD_TARGET=web node .electron-vue/dev-runner.js",
    "e2e": "npm run pack && mocha test/e2e",
    "lint": "eslint --ext .js,.vue -f ./node_modules/eslint-friendly-formatter src test",
    "lint:fix": "eslint --ext .js,.vue -f ./node_modules/eslint-friendly-formatter --fix src test",
    "pack": "npm run pack:main && npm run pack:renderer",
    "pack:main": "cross-env NODE_ENV=production webpack --progress --colors --config .electron-vue/webpack.main.config.js",
    "pack:renderer": "cross-env NODE_ENV=production webpack --progress --colors --config .electron-vue/webpack.renderer.config.js",
    "test": "npm run unit && npm run e2e",
    "unit": "karma start test/unit/karma.conf.js",
    "postinstall": "npm run lint:fix"
  },
  ...
```

빌드, 런등 모든 행위는 package.json이라는 파일의 정의에 의해서 일어납니다.

yarn run dev 는 곧 위의 정의 대로 ```node .electron-vue/dev-runner.js``` 라는 명령을 줄여준 것이고

yarn run build 는 ```node .electron-vue/build.js && electron-builder``` 라는 명령을 줄인 것이죠

그래서 왠지 윈도우로 빌드 하라는 것은 뒤에 옵션을 넣으면 될 것 같은 느낌이 들죠

아래 링크에 자세히 나와있지만 뭐가 뭔지 잘 모르겠습니다. 찾은 것은 --w를 넣으면 될 것 같은 판단 뿐...

[https://www.electron.build/configuration/target#targetconfiguration](https://www.electron.build/configuration/target#targetconfiguration)

```javascript
  "scripts": {
    "build": "node .electron-vue/build.js && electron-builder",
    "build:win": "node .electron-vue/build.js && electron-builder --w", // 추가
    "build:dir": "node .electron-vue/build.js && electron-builder --dir",
    ...
```

build:win이라는 것을 하나 만들어봤습니다. 달랑 --w 추가한 것이죠

```bash
$ yarn build:win
```

![alt build:win](/images/electron/2018-06-22 14-00-52 elecapp.png)

> yarn run build:win 이라고 쳐도 됩니다. build할 때는 run을 생략해도 되도록 정의를 해놨습니다.

# 실행

정말 잘 빌드 된 것인지 확인해봅니다.

## 맥용

### 더블클릭: elecapp-0.0.0.dmg

![alt build:mac](/images/electron/2018-06-22 14-07-37 build.png)

### 드래그 앤 드랍

![alt build:drag](/images/electron/2018-06-22 14-08-13 elecapp 0.0.0.png)

### 설치된 파일 더블 클릭  

런치 패드에서는 처음에는 안됩니다 사인이 안된 앱이라..  파인더에서 직접 클릭하고 나서 됩니다.  
120메가나 되네요

![alt build:app](/images/electron/2018-06-22 14-08-49 app.png)

### 실행 결과  

잘 되는 것을 확인 했습니다.

![alt build:drag](/images/electron/2018-06-22 14-12-34 elecapp.png)

### 제거

맥은 그냥 휴지통에 드래그엔 드랍하면 지워집니다.

## 윈도우용

저는 맥에서 윈도우를 간혹 쓸 때 페러럴즈라는 가상화 앱을 이용합니다.

페러럴즈로 윈도우에서 시험해보겠습니다.

### 더블클릭: elecapp Setup 0.0.0.exe

![alt win click](/images/electron/2018-06-22 14-27-26 Windows.png)

### 설치 진행

프로그레스바가 올라가며 설치가 진행됩니다.

![alt win install](/images/electron/2018-06-22 14-28-00 Windows.png)

### 설치 끝나자 마자 바로 실행

설치가 끝나고 바로 실행되버리네요.. 잘 되는 것을 확인 했습니다.

![alt win run](/images/electron/2018-06-22 14-28-57 Windows.png)

### 제거

제어판의 프로그램 추가/삭제에서 잘 지워지는 것 확인되었습니다.

![alt win remove](/images/electron/2018-06-22 14-33-01 Windows.png)

자 이제 정말 되는 것을 확인했습니다.

이제 해야할 일은 기본 헬로월드 같은 화면이 아닌 내가 원하는 화면을 넣는 것입니다.

안의 내용을 변형/추가 하면 되는 것이죠

# 구조 간단히 알아보기

우선 메뉴는 어디있고 메뉴를 누를 때의 화면은 어디있는지 알아야 변형이 가능하니 딱 필요한 정도로만 알아보겠습니다.

![alt struct](/images/electron/2018-06-22 14-56-52 elecapp.png)

위 그림중 src/renderer 요 폴더만 주의 깊게 보시면 됩니다.

- App.vue: 이곳에 메뉴등 전체 틀(레이아웃)이 있습니다.
- components/* : 이곳에 각 메뉴를 눌렀을때의 화면입니다.
- router/index.js: 이곳에 등록되어 있는 파일만 작동합니다.

이정도만 알고 변형을 하러 가봅시다.

# 간단한 프로그램 체험

## 변형

이제 안의 내용을 바꿔 보도록 하겠습니다.

글자 하나 수정하는데 2,3분이나 걸리면 정말 개발할 맛 안나겠죠? 그래서 dev 모드가 있는 것입니다.

dev 모드에서는 수정후 저장(cmd+s or ctrl+s) 만 해주면 바로 반영된 결과가 나옵니다.

```bash
$ yarn run dev
```

바로 앱이 뜹니다.

Welcome 과 Inspire라는 메뉴가 있네요..

글자를 수정해 보겠습니다.

**App.vue**  
```javascript
// 파일 하단의 내용 수정
<script>
  export default {
    name: 'elecapp',
    data: () => ({
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
        { icon: 'cloud', title: '메뉴바꿈!', to: '/' }, // 이부분 수정
        { icon: 'bubble_chart', title: 'Inspire', to: '/inspire' }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    })
  }
</script>
```

수정후 저장을 하면 바로 적용이 됩니다.(_참 쉽죠?_)

![alt change](/images/electron/2018-06-22 15-07-27 elecapp.png)

> icon은 [https://material.io/tools/icons/?icon=attachment&style=baseline](https://material.io/tools/icons/?icon=attachment&style=baseline) 기서 확인 가능합니다.  
사용법은 [https://vuetifyjs.com/ko/components/icons](https://vuetifyjs.com/ko/components/icons) 서 확인하면 됩니다.  

이제 뭔가를 수정하면 잘 수정이 되는 것을 확인했습니다.

남이 만들어 놓은 뼈다구 말고 실제를 만들어 볼 차례입니다.

## 추가

위에 구조도에 언급한 것처럼 3단계가 필요합니다.

1. 표시될 파일 만들기
2. 라우터에 등록
3. 메뉴에 추가

### 표시될 파일 만들기

연습장으로 쓸 빈 깡통을 만들어 보겠습니다.

{% raw %}

**src/renderer/components/test.vue**  
```html
<template>
  <v-layout row wrap>
    <v-flex xs12 sm4>
      <v-chip color="red" text-color="white">
        {{msg1}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange">
        {{msg2}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange">
        {{msg3}}
      </v-chip>
    </v-flex>
  </v-layout>
</template>

<script>
  export default {
    name: 'test',
    data: () => ({
      msg1: 'vuetify 해보자',
      msg2: 'vue 해보자',
      msg3: 'electron 해보자'
    }),
    methods: {
    }
  }
</script>

<style scoped>
</style>
```

이제부터는 vuetify와 vue를 접해야 할 상황입니다.

구체적인 것은 아래 링크를 확인해보시기 바랍니다.

vuetify 그리드 시스템: [https://vuetifyjs.com/ko/layout/grid](https://vuetifyjs.com/ko/layout/grid)

vuetify chip: [https://vuetifyjs.com/ko/components/chips](https://vuetifyjs.com/ko/components/chips)

vue 간단 렌더링 관련: [https://kr.vuejs.org/v2/guide/index.html](https://kr.vuejs.org/v2/guide/index.html)

> vuetify 링크를 보고 혹시 눈치 채셨나요? 콤포넌트와 색상 배치 모두 위 링크 참고해서 카피엔 페이스트 해가면서 하면 쉽습니다.  
문제는 vue.js 자체의 스터디가 조금 더 중요합니다.

**src/renderer/router/index.js**  
```javascript
import Vue from 'vue'
import Router from 'vue-router'

Vue.use(Router)

export default new Router({
  routes: [
    {
      path: '/',
      name: 'welcome-view',
      component: require('@/components/WelcomeView').default
    },
    {
      path: '/inspire',
      name: 'inspire',
      component: require('@/components/InspireView').default
    },
    { // 추가
      path: '/test',
      name: 'test',
      component: require('@/components/test').default
    },
    {
      path: '*',
      redirect: '/'
    }
  ]
})
```

**src/renderer/App.js**  
```javascript
// 하단 스크립트 부분에 추가
export default {
    name: 'elecapp',
    data: () => ({
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
        { icon: 'cloud', title: '메뉴바꿈!', to: '/' },
        { icon: 'bubble_chart', title: 'Inspire', to: '/inspire' }, 
        { icon: 'title', title: 'test', to: '/test' } // 추가
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    })
  }
```  

### 추가 실행 결과

![alt change](/images/electron/2018-06-22 15-41-18 elecapp.png)

이런 식으로 페이지를 추가하고 원하는 기능을 넣으면 되겠습니다.

하지만 이런 건 웹에서도 다 되던 것들이죠.. 아직까진 웹 이상의 의미가 있어보이지 않습니다.

이제 시스템 자원에 엑세스를 해보도록 하겠습니다.

## 데스크탑 앱으로서 의미 있는 기능 시험

파일을 로드해서 읽어보도록 하겠습니다.

파일을 읽으려면 고정된 패스 보다는 아무래도 다이얼로그로 선택하게 하는 것이 편하겠죠?

구현 순서는 

1. 다이얼로그 띄울 버튼 추가
2. 버튼에 반응할 이벤트 추가
3. 선택후 가져온 패스대로 읽는 기능 추가
4. 읽고나서 표현되는 ui 추가

### button 추가

코드를 작성한 후 저장을 눌러보도록 합시다.

**src/renderer/components/test.vue**  
```html
<template>
  <v-layout row wrap>
    <v-flex xs12 sm4>
      <v-chip color="red" text-color="white">
        {{msg1}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange" text-color="white">
        {{msg2}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange" text-color="white">
        {{msg3}}
      </v-chip>
    </v-flex>
    <v-flex xs12>
      <v-card>
        <v-card-title>
          {{fileContent}}
        </v-card-title>
        <v-card-actions>
          <v-btn color="primary" @click="dialogOpen">
            <v-icon left>save</v-icon>
            파일 선택
          </v-btn>
        </v-card-actions>
      </v-card>

    </v-flex>
  </v-layout>
</template>

<script>
  export default {
    name: 'test',
    data: () => ({
      msg1: 'vuetify 해보자',
      msg2: 'vue 해보자',
      msg3: 'electron 해보자',
      fileContent: '파일을 보고 싶네요'
    }),
    methods: {
      dialogOpen () {
        this.fileContent = '봤니?'
      }
    }
  }
</script>

<style scoped>
</style>
```

![alt change](/images/electron/2018-06-22 15-54-30 elecapp.png)

우아한 데스크탑의 의미중 큰 역활을 차지하는 버튼입니다.

자세히 버튼을 누르고 보면 얼마나 vuetify button이 디테일하게 스타일링 되었는지 알 수 있습니다.

버튼의 좌측 눌렀을 때와 우측 눌렀을 때의 트랜지션 효과가 다릅니다.

v-card를 사용했는데 일반적으로 panel 같은 개념이라고 보시면 됩니다. 지저분하게 나열되지 않게 잘 정돈해주는 용도라고 보면 됩니다.

@click="dialogOpen" 으로 methods에 정의된 핸들러로 갑니다.(_이벤트 처리 쉽죠~_)

### 실제 파일 읽기  

electron에서 제공하는 다이얼로그와 node.js에 기본 포함되어 있는 fs 모듈로 파일을 읽어보겠습니다.

**파일 내용**  
ab,cd,ef,g

**src/renderer/components/test.vue**  
```html
<template>
  <v-layout row wrap>
    <v-flex xs12 sm4>
      <v-chip color="red" text-color="white">
        {{msg1}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange" text-color="white">
        {{msg2}}
      </v-chip>
    </v-flex>
    <v-flex xs12 sm4>
      <v-chip color="orange" text-color="white">
        {{msg3}}
      </v-chip>
    </v-flex>
    <v-flex xs12>
      <v-card>
        <v-card-title>
          {{fileContent}}
        </v-card-title>
        <v-card-text>
          <v-chip color="info" v-for="c in chips">
            {{c}}
            <v-icon right>school</v-icon>
          </v-chip>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="dialogOpen">
            <v-icon left>save</v-icon>
            파일 선택
          </v-btn>
        </v-card-actions>
      </v-card>

    </v-flex>
    <v-snackbar
        :timeout="snackbar.timeOut"
        top
        v-model="snackbar.act"
        :color="snackbar.color"
    >
      {{ snackbar.text }}
      <v-spacer></v-spacer>
      <v-btn flat color="white" @click.native="snackbar.act = false">닫기</v-btn>
    </v-snackbar>
  </v-layout>
</template>

<script>
  import * as fs from 'fs'

  export default {
    name: 'test',
    data: () => ({
      msg1: 'vuetify 해보자',
      msg2: 'vue 해보자',
      msg3: 'electron 해보자',
      fileContent: '파일을 보고 싶네요',
      snackbar: {
        act: false,
        text: '',
        color: 'success',
        timeOut: 5000
      },
      chips: []
    }),
    methods: {
      pop (msg, cl, t) {
        this.snackbar.act = true
        this.snackbar.text = msg
        this.snackbar.color = cl
        this.snackbar.timeOut = t
      },
      dialogOpen () {
        this.$electron.remote.dialog.showOpenDialog((r) => {
          if (!r) return this.pop('파일을 선택하세요', 'error', 5000)
          if (!r.length) return this.onError('파일을 선택하세요', 'error', 5000)

          fs.readFile(r[0], (err, fd) => {
            if (err) return this.pop(err.message, 'error', 5000)
            this.fileContent = fd.toString()
            this.chips = this.fileContent.split(',')
            this.pop('파일 로드가 성공적이네요!!', 'success', 3000)
          })
        })
      }
    }
  }
</script>

<style scoped>
</style>
```

#### 실행 결과

![alt change](/images/electron/2018-06-22 16-16-03 elecapp.png)

#### html 설명

- v-for 로 배열을 엘리먼트로 한꺼번에 렌더링이 됩니다. 위에서 한번 써먹은 v-chip에 표시 해봤습니다.

- v-snackbar 는 모바일의 토스트 같은 것입니다.  
:timeout, :color등은 스크립트쪽에 선언된 변수와 바인드 되어있다는 것입니다. 값이 바뀌면 화면도 바뀌는 신통방통한 것입니다.

#### script 설명

- pop 이라는 메쏘드를 추가해서 스낵바를 다용도로 쓰게 만들었습니다.

- this.$electron.remote.dialog.showOpenDialog는 콜백으로 경로(들)을 불러옵니다.  
그 얘기는 파일 여러개도 된다는 것이죠.  
선택을 안했을 경우 예외처리가 되어 있고 선택 했을 경우 fs 모듈로 파일을 읽습니다.

- fs로 파일을 읽으면 문자열형태가 아닌 node의 buffer 시스템(binary)으로 읽습니다.  
그러므로 toString()으로 문자열로 변경해서 표현했습니다.

- javascript의 편리한 split함수로 chips 어레이에 ['ab', 'cd', 'ef', 'g']로 정리해서 넣습니다.

# 결론

이것으로 일렉트론뷰으로 적은 시간을 들여 우아한 데스크탑앱을 만들 수 있다는 가능성을 보여주었다고 생각합니다.

다음에는 외부 라이브러리(chartjs, nedb 등)를 이용해서 더 많은 것을 할 수 있다는 가능성을 다시 보여드리겠습니다.

{% endraw %}
