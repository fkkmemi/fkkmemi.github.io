---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 40 토큰(JWT)이란?
category: nemv
tag: [nemv,express,token,jwt,node]
comments: true
sidebar:
  nav: "nemv4"
---

토큰(JWT)에 대해 간단히 알아봅니다.

{% include toc %}

# 개요

토큰 인증을 너무 무겁고 어렵게 생각하는 경향이 있는 것 같습니다.(_특히 세션처리만 하시던 분들_)

실제로는 2가지 큰 원리만 생각하면 됩니다.

**암호화 된 내용을 볼 수 있는 자와 없는 자**

## 토큰을 이용한 사용자 로그인 처리

클라이언트가 id를 입력하고 서버에 전송합니다.

서버가 id명으로 토큰을 만들면 그냥 알 수 없는 문자 덩어리입니다.

그 문자 덩어리인 토큰을 클라이언트에게 주면(_로그인시_) 클라이언트는 토큰 내용을 열심히 풀어보려 노력해도 소용이 없습니다.

하지만 클라이언트가 토큰을 서버에 전달하면 서버는 만든사람이라 토큰 내용을 풀어 헤칩니다.(_디코드_)

로그인 처리는 이게 전부입니다.

## 세션처리란 무엇일까요?

클라이언트가 id를 입력하고 서버에 전송합니다.

서버는 id로 세션이라는 디비 콜렉션에 id를 기억합니다.

그 이후 부터는 req.session.user 로 맘데로 요리할 수 있습니다.

## 토큰 vs 세션

당연히 세션이 개발 편의상 압도적입니다.

디비 부하가 좀 크다는 단점이 있습니다.

토큰의 장점은 한번 토큰을 브라우저에 받아놓으면 로그인이 필요 없다는 것이죠.

클라이언트는 토큰이 더 행복한 것이죠.. 

그리고 대세는 토큰이기 때문에 시덥잖은 고민할 시간에 그냥 토큰 가면 됩니다.

추후 채팅, 사용자 통계등을 고려한다면 세션처리도 병행하는 것이 좋습니다.

# 토큰 간단히 사용해보기

JWT는 JSON Web Token 입니다. 그냥 대세죠..

원리를 알면 좋지만 알 필요는 없습니다.

그냥 jwt로 토큰을 만들고 푼다만 기억하세요.

##  설치

참고: [https://www.npmjs.com/package/jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken
)
```bash
$ cd be
$ yarn add jsonwebtoken
```

## 토큰 만들기

**be/app.js**  
```javascript
const jwt = require('jsonwebtoken');
const key = '베리베리어려운키'
const token = jwt.sign({ id: 'memi', email: 'memi@xxx.com' }, key);
console.log(token)
// eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Im1lbWkiLCJlbWFpbCI6Im1lbWlAeHh4LmNvbSIsImlhdCI6MTU0MDg3NjI0N30.llOQTZb4p5cFTz_4jrhb1YMTroFJq0TJtk-wRlub5FQ
```

id와 email로 알 수 없는 문자열을 만들었습니다. 

나중에 클라이언트에게 전달 될 토큰이죠..

> 키 뒤에 암호화 방식등도 옵션으로 지정할 수 있으나 패스합니다.  
공식 문서에는 옵션이 없을 때는 Sign with default (HMAC SHA256) 로 되어 있네요..  
자세한 내용은 위의 공식 링크 참고

## 토큰 풀기

**be/app.js**  
```javascript
const decoded = jwt.verify(token, key) 
console.log(decoded) 
// { id: 'memi', email: 'memi@xxx.com', iat: 1540876247 }
console.log(new Date(decoded.iat * 1000).toLocaleString())
```

위에 선언한 "베리베리어려운키" 로 풀어냈습니다.

나중에 서버가 미들웨어에서 판단해서 풀어지지 않는다면 쫒아낼 수 있겠죠?..

여기서 iat라는 쌩뚱 맞은 키값이 나왔는데 이것은 발행시간입니다.

32비트 정수로 되어 있어서 그냥 봐서는 알 수 없고, 1000 곱한 후 날짜형식으로 바꾸면 알아 낼 수 있습니다.

> 토큰을 만들 때 exp 라는것을 넣어주면 토큰 유효기간이 생기고 해당 시간이되면 못 쓰는 토큰이 됩니다.

# 동기식 함수의 문제

위에 만들고 풀기는 공식홈의 예제이지만 문제가 조금 있습니다.

에러상황이 났을 때 노드가 꺼져버린다는 것인데요..

동기식 사용법이라 문제인 것입니다.

## 동기와 비동기식의 차이

### 동기식
```javascript
console.log(jwt.sign({ id: 'memi' }, 'key'))
console.log('헬로?')
```

예상대로 토큰이 표시된 후 "헬로?" 가 뜹니다. 

이런 경우를 jwt.sign함수가 블러킹 하고 있다고 합니다.

### 비동기식(콜백)
```javascript
jwt.sign({ id: 'memi'}, 'key', (err, token) => {
  console.log(token)
})
console.log('헬로?')
```

예상과는 다르게(?) "헬로?"가 먼저 뜨고 토큰이 뜹니다. 

이번에는 jwt.sign함수가 블러킹해주지 않은 것이죠.

오래걸리는 jwt.sign은 나중에 처리된 것입니다.

## 문제 있는 코드

콜백형식으로 해야하는 이유는 이런 경우 입니다.

```javascript
const jwt = require('jsonwebtoken');
const key = '베리베리어려운키'
const token = jwt.sign({ id: 'memi', email: 'memi@xxx.com' }, key);
console.log(token)

const decoded = jwt.verify(token, '안맞는키') 
console.log(decoded) 
```

"베리베리어려운키"가 아닌 다른 키로 풀어보면 에러가 나는 정도가 아니라 백엔드 서버가 아예 죽어버립니다.

그래서 try, catch 같은 것을 써서 해결할 수도 있지만 콜백이 지원되니 콜백지원 함수를 사용해야겠습니다.  

## 문제 없는 코드로 만들기

```javascript
jwt.verify(token, 'ㄴㄹㅈㄷㄹ', (err, r) => {
  if (err) return console.error(err.message) 
  // invalid signature
  console.log(r) // bar
  console.log(new Date(r.iat * 1000).toLocaleString())
})
```

콜백형식으로 변경해서 에러가 있을 때 죽지 않고 표시만 합니다.

# 영상

{% include video id="jWFjkH5Ycp8" provider="youtube" %}   




