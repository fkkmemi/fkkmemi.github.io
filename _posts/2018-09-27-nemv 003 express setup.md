---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 3 익스프레스 설정
category: nemv
tag: [nemv,node,express]
comments: true
sidebar:
  nav: "nemv1"
---

익스프레스 설치 및 약간의 설명입니다.

# 익스프레스란

혹시 APM(Apache PHP MySQL) 아시는 분이라면

Apache + PHP 역할을 해준다고 생각하면 됩니다.

웹서버 기능과 파일 혹은 데이터 가공이 가능합니다.

RESTful한 api 설계를 쉽게 할 수 있습니다.

> RESTful api 예제  
xxx.com/api/user : post로 회원 등록  
xxx.com/api/user/list : get으로 회원들 조회  
xxx.com/api/user/id : put으로 회원 수정
xxx.com/api/user/id : delete로 회원 삭제

# 익스프레스 제네레이터 설치

순수 익스프레스가 아닌 미리 짜여진 골격으로 설치하기 위함입니다.

```bash
$ npm i express-generator -g
```

제네레이터를 전역으로 설치합니다.

자세한 경로는 아래에서 확인 가능합니다.

[http://expressjs.com/en/starter/generator.html](http://expressjs.com/en/starter/generator.html)

# 익스프레스 설치

```bash
$ express --view=pug be
$ cd be
$ npm i
$ DEBUG=be:* npm start 
```

be라는 프로젝트로 익스프레스 고수들이 만들어 줬다고 생각하면 됩니다.

# 익스프레스 확인

브라우저로 http://localhost:3000에 접속해서 웰컴 메세지가 나오면 성공입니다. 

![alt browser](/images/nembv/Express 2018-03-16 13-53-54.png)

# 영상

{% include video id="P7B6RzNrO7M" provider="youtube" %}   


 