---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 39 API 미들웨어란?
category: nemv
tag: [nemv,node,express,api]
comments: true
sidebar:
  nav: "nemv4"
---

API 미들웨어에 대해 간단히 알아봅니다.

{% include toc %}

# 개요

API 미들웨어라고 거창하게 이름 지었지만, 엄청 단순한 것입니다.

요청이 들어올 때마다 거치는 곳을 만들면 끝입니다.

나중에 요청이 들어올 경우 로그인 및 권한 판단을 거치고 아래로 내려가게 하는 것이죠..

# 미들웨어 만들기

**routes/api/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
router.all('*', function(req, res, next) {
  // 미들웨어 작성 부분
  next()
});

router.use('/test', require('./test'))
```

다 만들었습니다~ 허무하지만 이게 다입니다.

미들웨어라는 것은 단순히 처리후 next 하나로 끝나는 것입니다.

router.all은 get, post, delete, option 등등 어떤 요청이든 /api/ 로 들어오는 것은 이제 여길 거치게 됩니다.

# 미들웨어 제한 두기

**routes/api/index.js**  
```javascript
router.all('*', function(req, res, next) {  
  if (req.path === '/xxx') return res.send({ msg: '보안상 여긴 못와요!' })
  next()
});

router.use('/test', require('./test'))
```

이렇게 해두고 브라우저에서 http://localhost:3000/api/xxx에 접근하면 msg를 받게 됩니다.

아래로 못 내려가는 것이죠.

나중에 저부분에서 토큰을 검사하는 로직을 넣으면 될 것 같죠?

# 미들웨어 여러개 사용하기

```javascript
router.all('*', (req, res, next) => {
  console.log(req.headers)
  console.log(req.path)
  req.x = '보안 인증 통과'
  next()
})
router.all('*', (req, res, next) => {
  req.x = req.x + ' 2번째 인증 또한 통과'
  next()
})
router.all('*', (req, res, next) => {
  res.send({ status: req.x })
})
```

브라우저에서 http://localhost:3000/api 을 요청해보면

```javascript
{
  status: "보안 인증 통과 2번째 인증 또한 통과"
}
```

req.x라는 변수를 갑자기 만들어서 2번 처리한 후에 보낸 결과 입니다.

요청에 대한 변수(세션 변수)는 위와 같이 req. 뒤에 추가해서 사용하면 됩니다.

하지만 이미 있는 변수는 사용하면 안되겠죠~ eg) req.header, req.path 등..

나중에 토큰으로 req.user를 지지고 볶고 해야겠죠..

# 영상

{% include video id="pnbzfJe3EuU" provider="youtube" %}   




