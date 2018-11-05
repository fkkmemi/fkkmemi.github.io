---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 57 회원가입 완성하기
category: nemv
tag: [nemv,vee-validate,vue,vuetify,axios]
comments: true
sidebar:
  nav: "nemv4"
---

백엔드를 만들어서 만들어둔 프론트와 연동시켜보겠습니다.

{% include toc %}

{% raw %}

# 백엔드 API 만들기

**be/routes/api/index.js**  
```javascript
router.use('/sign', require('./sign'))
router.use('/register', require('./register'))
```

회원가입 API 레지스터를 등록해줍니다.

**be/routes/api/register/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const User = require('../../../models/users')

router.post('/', (req, res) => {
  const u = req.body
  if (!u.id) return res.send({ success: false, msg: '아이디가 없습니다.'})
  if (!u.pwd) return res.send({ success: false, msg: '비밀번호가 없습니다.'})
  if (!u.name) return res.send({ success: false, msg: '이름이 없습니다.'})

  User.findOne({ id: u.id })
    .then((r) => {
      if (r) throw new Error('이미 등록되어 있는 아이디입니다.')
      return User.create(u)
    })
    .then((r) => {
      res.send({ success: true })
    })
    .catch((e) => {
      res.send({ success: false, msg: e.message })
    })
})

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

회원가입 절차는 있으면 에러고 없으면 만드는 것이 전부입니다.

# 테스트 해보기

## 회원가입

![alt register](/images/nemv/스크린샷 2018-11-05 오후 5.25.10.png)

## 가입후 로그인

![alt sign](/images/nemv/스크린샷 2018-11-05 오후 5.26.41.png)

## 관리자 계정으로 확인

![alt sign admin](/images/nemv/스크린샷 2018-11-05 오후 5.27.53.png)

{% endraw %}

# 영상

{% include video id="pYQGWheK4nA" provider="youtube" %}   



