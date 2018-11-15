---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 66 게시판 정보 읽기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

기본적인 게시판 기능 중 쓰고 읽기만 해봅니다. 

{% include toc %}

{% raw %}

# 개요

게시판이 웹학습의 꽃인 이유는 CRUD를 다 해야하고 페이징 개념이 들어가기 때문입니다.

> 실제 커뮤니티형 게시판은 고려할 사항이 많고 아마 대략적인 지침이 있을 것입니다만..  
저는 게시판을 실무에서 쓰지 않습니다.. 생각나는 대로 만들어보는 것이니 참고용으로 사용하세요.

게시판을 다용도로 사용하기 위해 boards 라는 컬렉션을 만들었습니다.

게시판 관리에서 "아무나"를 생성합니다.

![alt boards](/images/nemv/스크린샷 2018-11-15 오전 10.58.06.png)

boards 컬렉션 중 "아무나" 라는 게시판을 사용해서 articles 컬렉션의 게시물과 연동해보겠습니다.

# 계획

- 프론트 페이지 "아무나" 를 만듬
- 페이지 진입시 axios로 "아무나" 게시판의 정보를 얻음
- 그후 게시물중 "아무나"로 지정되어 있는 게시물만 읽어옴
- 게시물 작성시 "아무나"로 지정해서 게시물을 쓰기 및 읽기

**boards collection**

| 게시판 이름 | 권한 | 설명 |
| --- | --- | --- |
| 지호 | 2 | 어쩌고 |
| 고은성 | 2 | 저쩌고 |
| 아무나 | 3 | ㅇㅇ |

**articles collection**

| 게시판 | 제목 | 내용등 |
| --- | --- | --- |
| 아무나 | 하이 | 어쩌고 |
| 아무나 | 배고파 | 저쩌고 |
| 지호 | 그런데 | ㅇㅇ |
| 아무나 | 배고파 | 저쩌고 |
| 지호 | 그런데 | ㅇㅇ |

게시판과 게시물은 디비에 이런식으로 저장되는 것이고 이중 "아무나" 가져오는 형태이죠..

> 게시물을 따로 나누는 것도 좋지만 나중에 통합 게시판으로도 활용할 수 있을 것 같아서 한곳에 넣었습니다.

# 게시판 얻기

**be/routes/api/board/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Board = require('../../../models/boards')

router.get('/:name', (req, res, next) => {
  const name = req.params.name
  Board.findOne({ name })
    .then(r => {
      // 권한으로 못보게 하려면..
      // if (r.lv < req.lv) return res.send({ success: false, msg: `${name} 게시판을 볼 수 있는 자격이 없습니다.`})
      res.send({ success: true, d: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
});

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

**be/routes/api/index.js**  
```javascript
router.use('/page', require('./page'))
router.use('/board', require('./board')) // add
```

이제 "아무나" 게시판 정보를 가져올 수 있습니다.

# 게시판 화면 확인

## 메뉴 구성

**fe/src/App.vue**  
```javascript
  items: [
     {
       icon: 'chat',
       title: '끄적끄적',
       act: true,
       subItems: [
         {
           icon: 'home',
           title: '아무나',
           to: {
             path: '/'
           }
         }
       ]
     },
     {
       icon: 'pan_tool',
       title: '레벨테스트',
       // act: true,
       subItems: [
// ..
```

메뉴를 최상단에 만들고 루트(/)에 연결했습니다.

![alt menu](/images/nemv/스크린샷 2018-11-15 오전 11.37.17.png)

## 라우터 추가

**fe/src/router.js**  
```javascript
 export default new Router({
   mode: 'history',
   base: process.env.BASE_URL,
   routes: [
     {
       path: '/',
       name: 'boardAnyone',
       component: () => import('./views/board/anyone'),
       beforeEnter: pageCheck
     },
     {
       path: '/test/lv3',
       name: 'testLv3',
```

anyone 이라는 페이지를 등록했습니다.

## 게시판 페이지 만들기

**fe/src/views/board/anyone.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12>
        <v-card>
          <v-img
            class="white--text"
            height="70px"
            src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg"
          >
            <v-container fill-height fluid>
              <v-layout fill-height>
                <v-flex xs6 align-end flexbox>
                  <span class="headline">{{board.name}}</span>
                </v-flex>
                <v-flex xs6 align-end flexbox>
                  <span>{{board.rmk}}</span>
                </v-flex>
              </v-layout>
            </v-container>
          </v-img>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>

export default {
  data () {
    return {
      board: {
        name: '로딩중...',
        rmk: '무엇?'
      }
    }
  },
  mounted () {
    this.get()
  },
  methods: {
    get () {
      this.$axios.get('board/아무나')
        .then(({ data }) => {
          if (!data.success) throw new Error(data.msg)
          this.board = data.d
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    pop (m, c) {
      this.sb.act = true
      this.sb.msg = m
      this.sb.color = c
    }
  }
}
</script>
```

시작시 board/아무나 로 정보만 가져와서 표시한 예입니다.

게시판 이름 표현이 너무 심심해서 뷰티파이 예제중 카드쪽에 있는 샘플을 긁어다 표시했습니다.

![alt anyone](/images/nemv/스크린샷 2018-11-15 오전 11.52.09.png)

# 그밖에 소소한 정리

## 라우터 페이지 이름 변경

**fe/src/router.js**  
```javascript
const pageCheck = (to, from, next) => {
   // axios.post('page', { name: to.path.replace('/', '') }, { headers: { Authorization: localStorage.getItem('token') } })
   axios.post('page', { name: to.path })
     .then((r) => {
       if (!r.data.success) throw new Error(r.data.msg)
       next()
     })
     .catch((e) => {
       // next(`/block/${e.message}`)
       next(`/block/${e.message.replace(/\//gi, ' ')}`)
     })
 }
```

기존 페이지 경로 저장시 /를 빼고 하는 것이 맘에 안들어서 그냥 전체로 저장합니다.  
(_대신 에러 처리할때 / 를 공백으로 처리합니다. /가 들어가면 get요청의 파라메터로 인식하기 때문입니다._)

## 토큰 재발급 버그 픽스

로그인 할때는 remember를 통해서 토큰의 시간을 저장해뒀지만.

다시 만들 때는 remember 인 토큰인지 알수 없습니다.

결국 토큰 기한은 만료시간 - 발급시간으로 하면 됩니다.

**be/routes/api/index.js**  
```javascript
const signToken = (id, lv, name, exp) => {
  return new Promise((resolve, reject) => {
    const o = {
      issuer: cfg.jwt.issuer,
      subject: cfg.jwt.subject,
      expiresIn: cfg.jwt.expiresIn, // 3분
      algorithm: cfg.jwt.algorithm,
      expiresIn: exp
    }
    jwt.sign({ id, lv, name }, cfg.jwt.secretKey, o, (err, token) => {
      if (err) reject(err)
      resolve(token)
    })
  })
}

const getToken = async(t) => {
  let vt = await verifyToken(t)
  if (vt.lv > 2) return { user: vt, token: null }
  const diff = moment(vt.exp * 1000).diff(moment(), 'seconds')
  // return vt
  console.log(diff)
  const expSec = (vt.exp - vt.iat)
  if (diff > expSec / cfg.jwt.expiresInDiv) return { user: vt, token: null }

  const nt = await signToken(vt.id, vt.lv, vt.name, expSec)
  vt = await verifyToken(nt)
  return { user: vt, token: nt }
}
```

# 소스

commit no: f80d5f7c111a344aafe5df8ae2210ac295e02404

[소스 확인](https://github.com/fkkmemi/nemv3/commit/f80d5f7c111a344aafe5df8ae2210ac295e02404)

- 해당 소스로 가려면

```bash
$ git checkout f80d5f7c111a344aafe5df8ae2210ac295e02404
// or
$ git checkout f80d5f
```

- 마지막 소스로 가려면

```bash
$ git checkout master
```

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}
