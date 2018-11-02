---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 51 백엔드로 페이지 권한 얻기
category: nemv
tag: [nemv,express,vue,vuetify,vuex,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

토큰 유무만 가지고 화면 보안 접근을 하는 것은 역시 위험합니다.

프론트는 늘 조작 가능하다는 것을 명심해야합니다.

페이지에 진입하기 전에 서버 허락을 받고 페이지를 열어보도록 하겠습니다.

> 지금부터 만드는 사용자 관련 시나리오는 지금까지의 강좌 내용 기반으로 즉흥적으로 만든 룰입니다.  

{% include toc %}

{% raw %}

# 백엔드에서 할일: API 만들기

## 토큰 검사 

**be/routes/index.js**  
```javascript
router.use('/manage', require('./manage'))

const verifyToken = (t) => {
  return new Promise((resolve, reject) => {
    if (!t) resolve({ id: 'guest', name: '손님', lv: 3 })
    if (t.length < 10) resolve({ id: 'guest', name: '손님', lv: 3 })
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
      req.user = v
      next()
    })
    .catch(e => res.send({ success: false, msg: e.message }))
})

router.use('/page', require('./page'))
router.all('*', function(req, res, next) {
  // 또 검사해도 됨
  if (req.user.lv > 2) return res.send({ success: false, msg: '권한이 없습니다.' })
  next()
})
```

토큰검사 함수에 예외처리를 몇개 넣었습니다.
  
- token이 undefined 이면 손님으로 만듭니다.
- 문자열 체크를 넣은 이유는 토큰이 없을때 'null'로 문자 4글자가 오기 때문입니다. null도 손님으로 변경했습니다.(_추후 개선이 필요합니다._)
- manage는 아직 보안 테스트 중이므로 맨위에 있습니다.(_토큰 검사 아래 있으면 권한을 설정해 볼 수 없기 때문입니다._)
- 토큰 검사가 정상이면 req.user에 값을 저장하고 내려갑니다.
- 페이지 검사 후 레벨로 다른 api들에 대한 접근을 막을 수 있습니다.

## 페이지 검사

페이지 검사는 이제 프론트에서 페이지에 진입하기 전에 들어오게 됩니다. (_/api/page/_)

**be/routes/page/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Page = require('../../../models/pages')

router.post('/', function(req, res, next) {  
  const { name } = req.body
  Page.findOne({ name })
    .then((r) => {
      if (!r) {
        if (req.user.lv) throw new Error(`페이지 ${name} 생성이 안되었습니다.`) // req.user.lv > 0
        return Page.create({ name })
      }
      if (r.lv < req.user.lv) throw new Error(`페이지 ${name} 이용 자격이 없습니다.`)
      return Page.updateOne({ _id: r._id }, { $inc: { inCnt: 1 } })
    })
    .then(() => {
    //   return Page.find()
    // })
    // .then((rs) => {
    //   console.log(rs)
      res.send({ success: true, d: req.user })
    })
    .catch((e) => {
      res.send({ success: false, msg: e.message })
    })
});

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

위에서 내려온 req.user 정보로 상황에 맞는 일을 할 수 있습니다.

- 프론트로 부터 포스트 요청을 받습니다.
- 바디에는 페이지 이름이 들어 있습니다.(_eg /home 이면 home_)
- 해당 페이지가 있는지 찾아봅니다.(_Page.findOne_)
- 없을 경우 
    - 접속한 사용자가 레벨0(관리자)가 아니면 에러를 보냅니다.(_req.user.lv > 0_)
    - 관리자일 경우 해당 페이지를 만듭니다.(_모델 기본값 으로_)
- 있을 경우
    - 페이지 레벨이 사용자 레벨보다 적을 경우 에러를 보냅니다.
    - 페이지 진입 카운트를 증가시키고 성공메세지와 사용자정보를 보냅니다.(_나중에 사용자정보로 페이지 내부 꾸미기 위함_)

# 프론트에서 할일

## 라우터 설정하기

**fe/src/router.js**  
```javascript
// ..
Vue.prototype.$axios = axios
const apiRootPath = process.env.NODE_ENV !== 'production' ? 'http://localhost:3000/api/' : '/api/'
Vue.prototype.$apiRootPath = apiRootPath

const pageCheck = (to, from, next) => {
  // return next()
  axios.post(`${apiRootPath}page`, { name: to.path.replace('/', '') }, { headers: { Authorization: localStorage.getItem('token') } })
    .then((r) => {
      if (!r.data.success) throw new Error(r.data.msg)
      next()
    })
    .catch((e) => {
      // console.error(e.message)
      next(`/block/${e.message}`)
    })
}

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'lv0',
      component: () => import('./views/lv0'),
      beforeEnter: pageCheck
    },
    // ..
    {
      path: '/lv3',
      name: 'lv3',
      component: () => import('./views/lv3'),
      beforeEnter: pageCheck
    },
    {
      path: '/user',
      name: '사용자',
      component: () => import('./views/user')
    },
    {
      path: '/page',
      name: '페이지',
      component: () => import('./views/page')
    },
    {
      path: '/block/:msg',
      name: '차단',
      component: () => import('./views/block')
    },
    // ..
  ]
})
```

이제 lv0~3 페이지는 서버에서 접근 허락을 받아야 열립니다.

- 토큰과 함께 axios로 /api/page에 접속합니다.
- 이때 전송할 name은 /lv3 일 경우 /를 제외한 lv3만 넘깁니다.(_replace_)
- 성공이면 진입을 허락하고 아니면 에러메세지와 함께 block 페이지를 보냅니다.  
에러메세지를 파람으로 보낸 것입니다.(_'/block/:msg' 익스프레스 라우트와 비슷한 원리입니다._)

동적 매치 참고: [https://router.vuejs.org/kr/guide/essentials/dynamic-matching.html](https://router.vuejs.org/kr/guide/essentials/dynamic-matching.html)

{% endraw %}

# 테스트 방법

- 먼저 사용자가 있다면 지우고 백엔드 서버를 다시 시작해서 생성합니다.

- 생성시 레벨0(관리자) 상태로 각 페이지(lv0~3.vue)에 들어가 봅니다.

- 페이지 관리에 4개의 페이지가 생성되있음을 확인 할 수 있습니다.

- 각 페이지에 권한을 각각 다르게 부여합니다.

![alt pages](/images/nemv/스크린샷 2018-11-02 오후 9.05.36.png)

- 사용자 관리에 들어가서 본인 레벨을 조절하고 수정합니다.

- 로그아웃을 한 후 새로운 토큰을 받아서 각 페이지에 들어가 봅니다.

![alt result](/images/nemv/스크린샷 2018-11-02 오후 9.01.32.png)

# 영상

{% include video id="ZNF6QrwHT_4" provider="youtube" %}   




