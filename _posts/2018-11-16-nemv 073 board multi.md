---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 73 게시판 재활용하기(뷰라우터)
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

만들어진 게시판 하나로 여러개를 운영해보겠습니다.

{% include toc %}

{% raw %}

# 개요

뷰라우터의 동적 라우트 매칭을 이용해서 한 콤포넌트로 게시판 여러개를 사용해보겠습니다.

공식홈이 정리가 잘되어 있으므로 읽어보시면 많은 도움이 될 것입니다.

참고: [https://router.vuejs.org/kr/guide/essentials/dynamic-matching.html](https://router.vuejs.org/kr/guide/essentials/dynamic-matching.html)

# 연습해보기

## 동적 라우트 매칭이란

라우터에서 파라메터를 이용하는 것입니다.

백엔드의 파람스(params) 사용과 같습니다.

- 기존 접근 방법: /board/anyone
- 변경 접근 방법: /board/:name

라우팅된 프론트페이지에서 this.$route.params.name 에 '누구나' 등이 올 수 있는 것입니다.

## 게시판 파일명 변경

fe/src/views/board/anyone.vue → fe/src/views/board/index.vue

이제 동적으로 사용할 것이기 때문에 파일명을 변경해줍니다.

## 게시판 라우터 변경

**fe/src/router.js**  
```javascript
    // ..
    {
      path: '/',
      name: 'dashboard',
      component: () => import('./views/dashboard'),
      beforeEnter: pageCheck
    },
    {
      path: '/board/:name',
      name: 'board',
      component: () => import('./views/board'),
      beforeEnter: pageCheck
    },
    // ..
```

- /를 아무페이지나 하나 만들어서 할당해줍니다.
- board path를 동적으로 할당했습니다.
- 파일명이 index.vue이기 때문에 import('./views/board') 로 줄일 수 있습니다.

## 메뉴 링크 추가

**fe/src/App.vue**  
```javascript
subItems: [
  // ..
  {
    icon: 'home',
    title: '아무나',
    to: {
      path: '/board/아무나'
    }
  },
  {
    icon: 'home',
    title: '지호',
    to: {
      path: '/board/지호'
    }
  }
  // ..
]
```
 
## 게시판 파라메터로 로드해보기

**fe/src/views/board/index.vue**  
```javascript
console.log(this.$route.params) // { name: '아무나' or '지호 }
this.$axios.get(`board/${this.$route.params.name}`)
    // .then()
```

이제 아무나와 지호 게시판을 들어 갈 수 있습니다.

## 렌더링 문제

아무나 게시판에서 다른 페이지에 들어갔다가 지호 게시판을 들어가면 문제가 없지만..

아무나 게시판에서 지호 게시판으로 들어가면 변경이 안됩니다.

왜그럴까요?

같은 콤포넌트를 렌더링하기 때문에 변경이 없다고 판단하고 아무 짓도 안하는 것이라고 공식홈에 나와있습니다.

그래서 공식홈에서는 $route를 감시하라고 친절하게 코드까지 적어주셨습니다.

**fe/src/views/board/index.vue**  
```javascript
  watch: {
    // ..
    '$route' (to, from) {
      console.log(to.path, from.path)
      this.getBoard()
    }
  },
```

이렇게 처리를 해두면 잘 동작합니다.

# 메뉴 자동화 및 최종 정리

![alt boards](/images/nemv/스크린샷%202018-11-15%20오전%2010.58.06.png)

App.vue에 수동으로 등록해둔 부분을 위의 리스트 정보를 얻어서 자동으로 구현해주면 편리하겠죠?

## 백엔드 API 변경

게시물(Article)에서 list와 read로 나눈 것처럼 게시판(Board) 도 두가지로 나눠줍니다.

**be/routes/api/board/index.js**  
```javascript
router.get('/read/:name', (req, res, next) => {
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
})

router.get('/list', (req, res, next) => {
  Board.find().sort({ lv: -1 })
    .then(rs => {
      res.send({ success: true, ds: rs, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
```

기존 api의 /를 없애고 /read와 /list를 만들었습니다.

**be/routes/api/index.js**  
```javascript
router.use('/site', require('./site'))
router.use('/board', require('./board'))
```

아무나 접근할 수 있도록 토큰 검사 위로 올려줍니다.

이제 App.vue에서 게시판 목록을 얻어올 수 있습니다.

## 프론트 메뉴 구성하기

**fe/src/App.vue**  
```javascript
  items: [
    {
      icon: 'donut_large',
      title: '현황',
      act: true,
      subItems: [
        {
          title: '오늘',
          to: {
            path: '/'
          }
        }
      ]
    },
    {
      icon: 'chat',
      title: '끄적끄적',
      subItems: [
        // {
        //   icon: 'home',
        //   title: '아무나',
        //   to: {
        //     path: '/board/아무나'
        //   }
        // },
        // {
        //   icon: 'clear',
        //   title: '지호',
        //   to: {
        //     path: '/board/지호'
        //   }
        // }
      ]
    },
```

- /를 다른 내용으로 채우고 
- subItmes를 비우고 시작합니다.

**fe/src/App.vue**  
```javascript
  // ..
  mounted () {
    this.getSite()
    this.getBoards()
  },
  methods: {
    // ..
    getBoards () {
      this.$axios.get('/board/list')
        .then(({ data }) => {
          data.ds.forEach(v => {
            this.items[1].subItems.push({
              title: v.name,
              to: {
                path: `/board/${v.name}`
              }
            })
          })
        })
        .catch(e => console.error(e.message))
    }
```

- 게시판 목록을 가져와서 this.items[1].subItems에 추가합니다.(_this.items[0]은 '/'임_)

## 프론트 api 경로 변경

```javascript
// ..
  getBoard () {
    this.$axios.get(`board/read/${this.$route.params.name}`)
// ..
```

경로 변경: board/ → board/read

## 결과

![alt result](/images/nemv/스크린샷 2018-11-16 오후 5.56.13.png)

게시판 관리에서 게시판을 추가하고 새로고치면 자동으로 메뉴에 추가되고 잘 동작하는 것을 확인할 수 있습니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/07737506d00925391d3c1be2a61a607ea52325a3)

# 버그 픽스

## 게시물 삭제 부분

**be/routes/api/article/index.js**  
```javascript
// ..
      if (!r) throw new Error('게시물이 존재하지 않습니다')      
      if (!r._user) {
        if (req.user.lv > 0) throw new Error('손님 게시물은 삭제가 안됩니다')
      }
      else {
        if (r._user._id.toString() !== req.user._id) {
          if (r._user.lv < req.user.lv) throw new Error('본인이 작성한 게시물이 아닙니다')
        }        
      }      
      return Article.deleteOne({ _id })
// ..
})
```

손님이 작성한 경우 r._user가 null이기 때문에 확인이 필요합니다.

r._user._id 부분에서 바로 에러가 나서 수정해봤습니다. 

[버그 픽스 소스](https://github.com/fkkmemi/nemv3/commit/a2a3df293cfc05c5634a47398aaf5bdfa3099446)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}
