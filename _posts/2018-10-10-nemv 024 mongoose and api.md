---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 24 api와 몽구스 연결
category: nemv
tag: [nemv,mongo,mongodb,node,express,vuetify,vue]
comments: true
sidebar:
  nav: "nemv2"
---

이제 디비를 조작할 수 있으니 api에서 실제 디비데이터로 몽구스(mongoose)를 사용해서 CRUD 중 CR 즉 쓰고 읽기를 해보겠습니다.

{% include toc %}

# 모델 외부로 빼기

be에 models란 디렉토리를 만듭니다.

이제 이곳에 모델들을 저장할 것입니다.

**be/models/users.js**  
```javascript
const mongoose = require('mongoose')

const userSchema = new mongoose.Schema({
    name: { type: String, default: '', unique: true, index: true },
    age: { type: Number, default: 1 }
})

const User = mongoose.model('User', userSchema)

module.exports = User
```

이렇게 파일로 만들어 놓으면 다른 곳에서 쓸 수 있는 것입니다.

# api에 연결해보기

**be/routes/api/user/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const User = require('../../../models/users')

router.get('/', function(req, res, next) {
  User.find()
    .then(r => {
      res.send({ success: true, users: r })
    })
    .catch(e => {
      res.send({ success: false })
    })
});

router.post('/', (req, res, next) => {
  const { name, age } = req.body
  const u = new User({ name, age })
  u.save()
    .then(r => {
      res.send({ success: true, msg: r })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

이렇게 API를 준비 해둡니다.

post는 create가 아닌 save로 한번 해봤습니다. save는 먼저 객체를 만들고 하는 것이죠.. 결과는 같습니다.

# 프론트에서 써보기

**fe/src/views/user.vue**  
```javascript
    postReq () {
      axios.post('http://localhost:3000/api/user', {
        name: '가정', age: 444 // req.body
      })
        .then((r) => {
          this.postMd = JSON.stringify(r.data)
        })
        .catch((e) => {
          console.error(e.message)
        })
    },
```

지난번 만든 post함수에서 데이터를 넘겨봅니다. 

{ name: '가정', age: 444 } 라는 데이터가 백엔드에서는 req.body가 됩니다.

눌러 보면 잘 동작하는 것을 확인 할 수 있습니다.

# 프론트에서 확인 해보기

![alt 프론트 화면](/images/nemv/스크린샷 2018-10-17 오후 7.51.51.png)

# 영상

에디터 저장 안하고 서버 껏다키는 바보짓을 많이 하니 잘 감안해서 영상 보세요~

{% include video id="f2Okg08Pf5I" provider="youtube" %}  


