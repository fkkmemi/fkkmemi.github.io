---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 46 실제 데이터로 로그인하기
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

이제 실제 데이터로 로그인을 해보겠습니다.

{% include toc %}

# 토큰 발행시 사용될 보안키 설정값에 추가하기

**config/index.js**  
```javascript
admin: {
    id: 'memi',
    // ...
},
secretKey: 'abcdefg'
```

# 토큰 발행 함수 작성

**be/api/sign/index.js**  
```javascript
const jwt = require('jsonwebtoken')
const cfg = require('../../../../config')
const User = require('../../../models/users')

const signToken = (id, age) => {
  return new Promise((resolve, reject) => {
    jwt.sign({ id, age }, cfg.secretKey, (err, token) => {
      if (err) reject(err)
      resolve(token)
    })
  })
}
```

id, age와 cfg.secretKey로 토큰을 만드는 함수를 만듭니다.

# 로그인 라우터 변경하기

**be/api/sign/index.js**  
```javascript
router.post('/in', (req, res) => {
  const { id, pwd } = req.body
  if (!id) return res.send({ success: false, msg: '아이디가 없습니다.'})
  if (!pwd) return res.send({ success: false, msg: '비밀번호가 없습니다.'})

  User.findOne({ id })
    .then((r) => {
      if (!r) throw new Error('존재하지 않는 아이디입니다.')
      if (r.pwd !== pwd) throw new Error('비밀번호가 틀립니다.')
      return signToken(r.id, r.age)
    })
    .then((r) => {
      res.send({ success: true, token: r })
    })
    .catch((e) => {
      res.send({ success: false, msg: e.message })
    })
})
```

- 사용자 id로 찾습니다.
- 없거나 패스워드가 틀리면 에러메세지와 함께 success: false로 데이터를 전송합니다.
- 정상적인 경우에 id, age로 토큰을 만들어서 success: true와 토큰을 전달합니다.

# 결과 

프론트에서 로그인이 정상 수행된 결과 입니다.

```javascript
{success: true, token: "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Im1lb…M5N30.EE1PK1QHCGF_LizjaC2BK2qC8xHgM94X5YcHFNoOmT4"}
```

# 영상

준비중..

{% include video id="" provider="youtube" %}   




