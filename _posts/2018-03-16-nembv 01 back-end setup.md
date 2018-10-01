---
layout: single
title: NEMBV 1 Back-end Setup
category: nembv
tag: [nembv,nodejs,express,yarn,git]
comments: true
sidebar:
  nav: "nembv"
---

nembv를 사용한 새로운 프로젝트를 시작해보겠다.

각 벤더 라이브러리에서 제공하는 기본골격으로 따라하기식으로 진행할 것이니 한땀한땀 직접 따라 만들어 보면 되겠다. 

Back-end 기본 설치 부터 시작한다.

{% include toc %}

# git 사용

git 사용 없이 진행하려면 밑으로 이동

git은 혼자 쓸때는 딱 3가지면 끝나니 써두면 절대 후회 없을 것이다. 3가지만 학습해보고 오길 권장한다.

- commit
- push
- pull

이력관리도 좋고, 나중에 협업 필요시 매우 좋은 솔루션이다.

## git 저장소 만들기

[https://github.com](https://github.com) 에서 계정이 없다면 가입하고 있으면


![alt new](/images/nembv/GitHub 2018-03-16 11-51-47.png) 를 눌러 신규 저장소를 만든다..

아래와 같이 폼을 입력한다 

![alt Create a New Repository 2018-03-16 11-56-22](/images/nembv/Create a New Repository 2018-03-16 11-56-22.png) 

> public은 공개 private는 비공개이며 유료다

> gitignore에 node를 추가하면 node 세팅 관련 데이터는 무시된다.. eg) node_modules 같은 디렉터리


![alt git repo](/images/nembv/fkkmemi:nembv: Node Express Mongo Bootstrap Vue 2018-03-16 12-06-54.png) 

## git download

적당한 위치를 찾아서 소스를 다운 받는다

ssh 방식과 https 방식이 있는데 ssh 방식은 디바이스 세팅이 필요하기 때문에 https 방식으로 받는다

> 물론 편리함과 보안때문에 ssh로 세팅하기를 추천한다.

```bash
$ git clone https://github.com/fkkmemi/nembv // 현재 제작중인 소스를 받을때
$ git clone https://github.com/만든 계정/nembv // 만든 계정
```

> private 저장소의 경우 id/password를 물어본다.

# node.js 설치

[https://nodejs.org/en/](https://nodejs.org/en/) 에서 최신버전을 받아서 설치한다

## express generator 설치

express 제네레이터를 이용해서 미리 짜여진 구성으로 nembv 디렉토리에 덮어 씌우도록 하겠다.

```bash
$ npm install express-generator -g
```

> -g는 전역 설치이며 앞으로 express 명령을 통해 어디서나 코드를 생성할 수 있다.

## express 설치

nembv 디렉토리 바로 위로 이동하여 아래와 같이 입력한다 

비어있지 않은데 계속할꺼냐고 물어보는데 y를 누르면 된다

```bash
$ express --view=pug nembv
$ destination is not empty, continue? [y/N] 
```

> --view=pug 를 넣은 이유는 기본으로 설치하면 pug의 옛버전인 jade가 설치되는데 어짜피 디프리케이트되었다고 워닝이 뜬다

## directory 구성

아래와 같이 디렉토리가 구성되었다.

![alt dir](/images/nembv/nembv 2018-03-16 12-54-46.png)

## 의존 요소 설치

해당 위치로 내려가서 express에 필요한 의존요소들을 설치한다.


```bash
$ npm install
```

or

```bash
$ yarn 
```

> 최근에는 npm 보다는 [yarn](https://yarnpkg.com/en/)을 많이 사용하는 추세다 명령이 간단하고 병렬로 데이터를 다운로드 받아서 많은 양의 모듈을 받을 때 좋다

# 웹서버 구동

```bash
$ npm start
$ > nembv@0.0.0 start /Users/fkkmemi/WebstormProjects/nembv
$ > node ./bin/www
```

웹서버를 실행하면 위와같은 설명이 출력되는데 node ./bin/www를 사용한 것이며

```bash
$ node ./bin/www
```

당연히 이렇게 해도 똑같이 실행된다


package.json에 start를 이렇게 등록했기 때문에 실행되는 것이다  

```bash
{
  "name": "nembv",
  "version": "0.0.0",
  "private": true,
  "scripts": {
    "start": "node ./bin/www"
  },
  "dependencies": {
    "body-parser": "~1.17.1",
    "cookie-parser": "~1.4.3",
    "debug": "~2.6.3",
    "express": "~4.15.2",
    "morgan": "~1.8.1",
    "pug": "~2.0.0-beta11",
    "serve-favicon": "~2.4.2"
  }
}
```

브라우저에서 http://localhost:3000 을 입력하여 아래 이미지가 뜨면 익스프레스 기본 설치가 완료된 것이다..

![alt browser](/images/nembv/Express 2018-03-16 13-53-54.png)
