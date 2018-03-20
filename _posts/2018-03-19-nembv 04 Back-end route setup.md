---
layout: single
title: NEMBV 4 Back-end route setup
category: nembv
tag: [nembv,express,nodejs]
comments: true
---

지난번 라우트 원리를 이해했다면 이제 계획을 새울 차례다.

```bash
/..
/bin
/fe
/public
/route
app.js  
package.json
``` 

### 작동 정의

1. 백엔드서버는 /, /api 두 곳을 라우팅한다.
    - /(fe/dist): vue로 빌드된 정적인 html 요소가 있는 곳을 라우트
    - /(public): 정적인 이미지등이 있는 곳을 라우트 
    - /api/* : 데이터베이스와 연동하여 각 요청에 필요한 json 데이터를 응답

2. 프론트엔드는 fe/dist 디렉토리에 정적인 파일들을 보관  
    - / 이하 라우트는 해쉬태그 이하로 다시 라우트 된다 eg) /#/group/
    
### 프론트엔드 빌드

지난번 npm run dev는 개발자용으로 디버그를 위한 것이었다.

이번에는 배포용 파일을 빌드해보자

```bash
$ cd fe
$ npm run build
```    

> npm run build 이에외도 fe/package.json을 확인해보면 다양한 스크립트를 등록되어 있다...
 
기본값인 /fe/dist 에 파일들이 가지런히 있음을 알 수 있다

바로 [webpack](https://webpack.js.org)으로 변환된 것이다.

![변환된 파일들](/images/nembv/2018-03-19 11-41-18 nembv fe_dist.png)

### 백엔드에서 스태틱으로 vue 배포 디렉토리를 지정

백엔드에 있는 app.js에서 /fe/dist를 스태틱 라우트 포인트로 추가해본다.

```javascript
app.use(express.static(path.join(__dirname, 'fe', 'dist'))); // added here
app.use(express.static(path.join(__dirname, 'public')));
```

이제 최우선 순위로 /fe/dist/index.html을 먼저 라우트 할 것이다.

### 백+프론트엔드 확인

```bash
$ npm start
```

이제 localhost:3000 을 다시 확인해보자.

vue-router가 경로를 http://localhost:3000/#/ 로 바꾸고 vue 시작페이지가 나온 것을 확인 할 수 있다.

