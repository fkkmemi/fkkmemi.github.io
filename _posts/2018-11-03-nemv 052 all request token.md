---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 52 모든 요청 토큰 설정으로 접근 막기
category: nemv
tag: [nemv,express,vue,vuetify,axios0.,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

매번 액시오스로 api 요청을 할 때마다 헤더에 토큰을 넣어야 작동하는 부분을 사전 정의해서 생략해봅니다. 

{% include toc %}

{% raw %}

# 백엔드

## 사용자 추가

사용자 테스트하기 귀찮으니 추가로 만듭니다.

**be/models/users**  
```javascript
  User.findOne({ id: 'lv2' })
    .then((r) => {
      if (!r) return User.create({ id: 'lv2', pwd: '1234', name: 'lv2', lv: 2 })
      return Promise.resolve(null)
    })
    .then((r) => {
      if (r) console.log(`admin:${r.id} created!`)
    })
    .catch((e) => {
      console.error(e.message)
    })
```

레벨2 일반사용자 lv2 계정을 없으면 만듭니다.

## api 접근 막기

api/manage는 테스트를 위해 열어놓았지만 제일 위험한 곳입니다. 

**be/routes/api/index.js**  
```javascript
router.use('/page', require('./page'))
router.use('/manage', require('./manage'))
```

토큰 검사 아래로 내립니다.

## api 권한 따로 부여

라우팅 포인트(/manage, /test, 등) 마다 개별적으로 다른 권한을 부여합니다.

**be/routes/api/manage/index.js**  
```javascript
var router = express.Router();
router.all('*', function(req, res, next) {
  if (req.user.lv) return res.send({ success: false, msg: '권한이 없습니다.' })
  next()
})
router.use('/user', require('./user'))
```

이제 /manage 아래로 가려면 사용자 레벨 0(관리자) 만 가능합니다.

# 프론트

## 페이지 요청 접근 막기

**fe/src/router.js**  
```javascript
{
      path: '/user',
      name: '사용자',
      component: () => import('./views/user'),
      beforeEnter: pageCheck // add
    },
    {
      path: '/page',
      name: '페이지',
      component: () => import('./views/page'),
      beforeEnter: pageCheck // add
    },
```

이제 사용자, 페이지도 접근할때 서버에서 검사를 받아야 합니다.

## 액시오스 공용 설정

매번 요청마다 헤더를 전송하는 것은 귀찮으므로 지정해둡니다.

참고: [https://www.npmjs.com/package/axios](https://www.npmjs.com/package/axios)

**fe/src/router.js**  
```javascript
const apiRootPath = process.env.NODE_ENV !== 'production' ? 'http://localhost:3000/api/' : '/api/'
Vue.prototype.$apiRootPath = apiRootPath
axios.defaults.baseURL = apiRootPath // add
axios.defaults.headers.common['Authorization'] = localStorage.getItem('token') // add
```

- 기본 경로(axios.defaults.baseURL)를 설정해 놓으면 이제 $axios 요청은 하단부만 넣으면 됩니다.(_eg: axios.get('user') -> http://localhost:3000/api/user_)
- 기본 헤더(axios.defaults.headers) 에 공통(common) 되는 부분에 토큰 정보를 추가합니다.

# 문제점

아쉽게도 다른 아이디 로그인 할 경우 잘 동작하지 않습니다.

기본 헤더로 토큰을 읽는 부분(router.js)이 페이지 갱신 후 단 한번만 이루어지기 때문입니다.

다른 아이디로 로그인해도 기본 헤더(axios.defaults.headers)는 변경되지 않는 것이죠..

물론 로그인 후 location.reload() 같은 것으로 새로고침하면 해결되긴 하지만, 그 문제 해결은 너무 원초적입니다.

다음 강좌에서 변경되는 토큰이 반영되게 변경해보겠습니다.

{% endraw %}

# 영상

{% include video id="O1IB_gqMlxE" provider="youtube" %}   




