---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 47 토큰 인증하기
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

프론트에 토큰을 저장해서 매번 토큰과 함께 요청을 해보겠습니다.

{% include toc %}

# 로그인 성공시

**fe/src/views/sign.vue**  
```vue
    signIn () {
      axios.post(`${this.$apiRootPath}sign/in`, this.form)
        .then(r => {
          if (!r.data.success) return console.error(r.data.msg)
          localStorage.setItem('token', r.data.token)
          this.$router.push('/header')         
        })
        .catch(e => console.error(e.message))
```

- 토큰 저장
- 페이지 이동: 만들어 두었던 header.vue 로 갑니다.

> this.$router.push 대신 location.href = '/header' 로 해도 상관 없습니다.

# 토큰 검사하기

**be/routes/api/index.js**  
```javascript
const jwt = require('jsonwebtoken');
const cfg = require('../../../config')

router.use('/sign', require('./sign'))

const verifyToken = (t) => {
  return new Promise((resolve, reject) => {
    jwt.verify(t, cfg.secretKey, (err, v) => {
      if (err) reject(err)
      resolve(v)
    })
  })
}
router.all('*', function(req, res, next) {
  // 토큰 검사
  const token = req.headers.authorization
  verifyToken(token)
    .then(v => {
      console.log(v)
      next()
    })
    .catch(e => res.send({ success: false, msg: e.message }))  
});
router.use('/test', require('./test'))
```

- 토큰 확인 함수를 만듭니다.
- /sign을 제외한 모든 api 요청은 .all에 걸립니다.
- 토큰을 확인하고 문제가 없다면 next로 아래로 내려갑니다.
- 문제가 있다면 success: false와 에러메세지를 보내서 아래로 못내려가게 합니다.

> 사실 인증 에러가 났을 때 success: false 같은 정상적인 데이터보다는 http 공식 에러메세지인 401 포비든으로 날려주는 것이 좋습니다.  
이해를 돕기위한 것이므로 나중에 변경될 것입니다.

# 토큰과 함께 전송해보기

**fe/src/views/header.vue**  
```vue
<template>
  <v-container>
    <v-btn @click="apiWithToken">토큰과 함께 전송</v-btn>
    <v-btn @click="apiWithTrash">이상한 문자와 함께 전송</v-btn>
  </v-container>
</template>
<script>
import axios from 'axios'

export default {
  methods: {    
    apiWithToken () {
      const token = localStorage.getItem('token')
      axios.get(`${this.$apiRootPath}test`, { headers: { Authorization: token } })
        .then(r => console.log(r.data))
        .catch(e => console.log(e.message))
    },
    apiWithTrash () {
      axios.get(`${this.$apiRootPath}test`, { headers: { Authorization: 'abcdefghijk' } })
        .then(r => console.log(r.data))
        .catch(e => console.log(e.message))
    }
  }
}
</script>
```

- 저장되어있던 토큰을 받아서 요청(/api/test)시 전송합니다.

# 결과

![alt api with token](/images/nemv/스크린샷 2018-10-31 오후 4.32.42.png)

- 정상적인 토큰과 함께 전송하면 : {msg: "hello", a: "괜찮아"}
- 비정상적인 토큰과 함께 전송하면 : {success: false, msg: "jwt malformed"}

# 영상

준비중..

{% include video id="" provider="youtube" %}   




