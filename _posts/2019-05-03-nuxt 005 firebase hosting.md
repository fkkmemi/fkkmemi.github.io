---
layout: single
title: NUXT로 혼자 웹사이트 만들기 5 firebase로 hosting해보기
category: nuxt
tag: [nuxt,node,vue,vuetify,plugin,firebase,hosting]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

넉스트(nuxt) 소스를 파이어베이스 호스팅(google firebase hosting)을 이용해서 배포(deploy)해봅니다.

# 개요

구글 파이어베이스 호스팅이란 node.js v8을 이용해서 정적인 파일만 돌아가게 해주는 것입니다.(_내부적으로는 익스프레스 같은 것이 돌아가고 있겠죠.._)

> 정적파일이란 말그대로 변화가 없는 파일 뭉치입니다. index.html 같은..

과거에 정적파일로 생성된 웹이란.. 그저 회사 홍보페이지 같은 동적으로 아무 짓도 안하는 그런 웹이었습니다.

지금은 얘기가 다릅니다. 정적파일이지만 파일 안에 내부적으로 다양한 외부 api를 통해서 데이터를 송수신해서 html요소를 변경하니.. 마치 동적으로 동작하는 웹처럼 동작합니다. 

결국 프론트에서 백엔드 없이 데이터를 사용할 수 있기 때문에 가능한 일입니다.

백엔드가 없어서 그런지 서버리스(serverless)라고 불리우기도 합니다. 

정적인 파일로만 서비스가 가능하기 때문에 AWS S3 같은 곳에도 올릴 수 있습니다.

참고: [https://nuxtjs.org/faq/deployment-aws-s3-cloudfront](https://nuxtjs.org/faq/deployment-aws-s3-cloudfront)

그 밖에도 다양한 곳에 배포가 가능한데 파이어베이스 배포가 없길래 한번 시험해보고 결과를 작성해봅니다.

# 파이어베이스 프로젝트 만들기

## 프로젝트 추가

![alt project add](/images/nuxt/2019-05-03_13.38.30.png)

이동: [https://console.firebase.google.com/](https://console.firebase.google.com/)

## 프로젝트 생성

![alt project make](/images/nuxt/2019-05-03_13.41.50.png)

좀 어려운 이름으로 하면 원하는 이름으로 가능합니다.

nuxt 같이 쉬운걸로 선택하면 nuxt01ef3f 같이 되니 주의

## 호스팅 시작하기

![alt hosting start](/images/nuxt/2019-05-03_13.43.36.png)

## 파이어베이스 도구 안내

![alt hosting tools](/images/nuxt/2019-05-03_13.43.55.png)

파이어베이스 도구를 설치하라는 안내문구가 나옵니다.

## 파이어베이스 배포 안내

![alt hosting deploy](/images/nuxt/2019-05-03_13.44.13.png)

생성하고 배포하는 것이 이렇게 쉽다~ 라고 안내해줍니다.

# 파이어베이스 설치하기

## 노드 버전 맞추기

```bash
$ sudo n 8
```

현재 파이어베이스 펑션스(functions)가 노드6는 지원안하고(deprecated) 노드8이 정식입니다. 

노드10은 베타라고 워닝 뜨기 때문에 노드8로 해보기로 했습니다.

> n 모듈 사용법은 지난강좌 참고.. 

## 디렉토리 생성

```bash
$ mkdir firebase-nuxt && cd firebase-nuxt
```

파이어베이스 프로젝트 생성시 디렉토리를 만들어주지 않으므로 만들고 안으로 들어가야합니다.

## 파이어베이스 도구 설치하기

```bash
$ sudo npm i -g firebase-tools
/usr/local/bin/firebase -> /usr/local/lib/node_modules/firebase-tools/lib/bin/firebase.js
+ firebase-tools@6.8.0
removed 1 package and updated 1 package in 12.028s
```

해당 명령어는 설치도 되지만 업그레이드도 되기 때문에 무조건 해주면 됩니다.

## 파이어베이스 로그인하기

```bash
$ firebase login
Already logged in as anyuser@gmail.com # 로그인이 이미 되어있음
```

로그인이 안되어 있을 경우 브라우저로 연결되고 로그인하면 완료됩니다.

## 파이어베이스 생성하기

```bash
$ firebase init
? Which Firebase CLI features do you want to set up for this folde
r? Press Space to select features, then Enter to confirm your choi
ces. 
 ◉ Database: Deploy Firebase Realtime Database Rules
 ◯ Firestore: Deploy rules and create indexes for Firestore
 ◉ Functions: Configure and deploy Cloud Functions
❯◉ Hosting: Configure and deploy Firebase Hosting sites
 ◯ Storage: Deploy Cloud Storage security rules
```

- Database, Functions, Hosting 3개만 설치해봅니다.(_나중에 추가해도 됩니다._)

```bash
? Select a default Firebase project for this directory: 
  [don't setup a default project]
❯ fb-nuxt-memi (fb-nuxt-memi) 
```

- 위에 생성했던 프로젝트명이 보입니다~ 매우 편리합니다~

```bash
? What file should be used for Database Rules? (database.rules.jso
n) 
```

- 읽고 쓰는 룰을 만드는 것인데 나중에 할것이기 때문에 엔터..

```bash
? What language would you like to use to write Cloud Functions? 
JavaScript
```

- 타입스크립트 모르니 자바스크립트 선택

```bash
? Do you want to use ESLint to catch probable bugs and enforce sty
le? No
```

- ESLint는 넉스트 안에서 이미 실컷 고통받고 있기 때문에 패스

```bash
? Do you want to install dependencies with npm now? (Y/n)
```

- 종속요소 설치 YES!

```bash
? What do you want to use as your public directory? (public)
? Configure as a single-page app (rewrite all urls to /index.html)
? (y/N) 
✔  Wrote public/404.html
✔  Wrote public/index.html

i  Writing configuration info to firebase.json...
i  Writing project information to .firebaserc...
i  Writing gitignore file to .gitignore...

✔  Firebase initialization complete!
```

이 이렉토리(public/)가 정적 페이지들이 위치할 곳이 됩니다.

이제 엔트리포인트 파일은 /index.html이 되게 만들어야합니다.

# 파이어베이스 시험해보기

```bash
$ sudo firebase serve
```

> sudo를 안쓰면 에러가 나는데 왜 필요한지 모르겠습니다.  
제가 설치를 잘못했을지도..

![alt firebase web](/images/nuxt/2019-05-03_14.22.31.png)

이 페이지가 나왔으면 일단 성공입니다~

# 파이어베이스 기본 페이지 배포하기

```bash
$ firebase deploy
Please note that it can take up to 30 seconds for your updated functions to propagate.
Project Console: https://console.firebase.google.com/project/fb-nuxt-memi/overview
Hosting URL: https://fb-nuxt-memi.firebaseapp.com
```

30초 정도 걸리나봅니다. 안내한 url로 이동해서 잘 뜨면 성공입니다.

잘 생각해보면 알겠지만, 결국 public/ 에 넉스트든 리액트 앵귤러든 정적인 파일로 빌드한 놈을 넣어주면 끝입니다.

# 넉스트 설치하기

지난 강좌에서 설치할 때와 거의 같습니다.

파이어베이스 디렉토리 안에 추가할 뿐~

```bash
$ yarn create nuxt-app src
? Project name firebase-nuxt
? Project description firebase hosting with nuxt
? Use a custom server framework none
? Choose features to install Linter / Formatter, Prettier, Axios
? Use a custom UI framework vuetify
? Use a custom test framework none
? Choose rendering mode Universal
? Author name fkkmemi
? Choose a package manager yarn
```

소스 디렉토리가 될 것이기 때문에 src로 지정했습니다.

한가지 다른 것은 우리는 백엔드가 필요 없기 때문에 서버 선택을 express가 아닌 none으로 지정해야하는 것입니다.

> 서버는 필요 없는 정도가 아니라.. 설치시 아예 동작(generate)을 안할 것 같습니다.. 말이 안되는 조합이니..(해보지는 않았지만...)

# 넉스트 빌드하기

정적 파일(static web)으로 빌드한 결과를 public/에 넣으면 끝입니다.

참고: [https://nuxtjs.org/api/configuration-generate](https://nuxtjs.org/api/configuration-generate)

**src/nuxt.config.js**  
```javascript
  build: {
    //  
  },
  generate: {
    dir: '../public'
  }
```

제일 하단에 생성할 위치를 지정해줍니다.

```bash
$ cd src
$ yarn generate
```

![alt nuxt structure](/images/nuxt/2019-05-03_14.59.20.png)

빌드하고 나면 **index.html**이 있는 것이 확인됩니다.

# 파이어베이스 배포하기

이제 다시 배포하고 확인하면 끝입니다~

```bash
$ cd ..
$ firebase deploy
```

잘 동작하는 것을 확인 할 수 있습니다.

![alt nuxt web](/images/nuxt/2019-05-03_15.00.38.png)

# 마치며

사실 너무 뻔한 내용이라 강좌에 추가해야할 지 고민을 많이 했습니다.

하지만 누군가에겐 도움이 될 수 있고, 최근 문의가 많아져서 다뤄봤습니다.

넉스트도 파이어베이스도 참 핫한 녀석들인데 의외로 구글링해보니 아티클이 많이 없더군요..

이제 둥지는 만들어 졌으니 nuxt디렉토리에서 파이어베이스 디비에 CRUD만 하면 끝입니다.

# 소스

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

```
# 해당 코드 확인하기
$ git checkout tags/v0.0.5
```

# 영상

{% include video id="szRuA5_tFTU" provider="youtube" %}
