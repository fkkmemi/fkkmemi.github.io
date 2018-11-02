---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 48 로그인 해야 들어갈 수 있는 페이지 만들기(네비게이션 가드)
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

로그인 해야만 진입할 수 있는 페이지를 만들어보겠습니다.

{% include toc %}

# 네비게이션 가드란?

뷰라우터에서 지원하는 기능입니다.

참고: [https://router.vuejs.org/kr/guide/advanced/navigation-guards.html](https://router.vuejs.org/kr/guide/advanced/navigation-guards.html)

네비게이션 가드를 이용해서 페이지로 진입하지 못하게 막을 수 있습니다.

# 화면 정리겸 메뉴 준비하기

토큰 존재 여부로 가드로 막아보기 위해서는 로그인/아웃이 편해야하니 쓰잘데 없는 기능 정리하고 만들어 보겠습니다.

**fe/src/app.vue**  
```vue
<template>
  <v-app>
    <v-navigation-drawer
      persistent
      v-model="drawer"
      enable-resize-watcher
      fixed
      app
    >
      <v-list>
        <v-list-tile
          value="true"
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
        >
          <v-list-tile-action>
            <v-icon v-html="item.icon"></v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="item.title"></v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar
      app
    >
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title v-text="title"></v-toolbar-title>
      <v-spacer></v-spacer>
      <v-toolbar-items>
        <v-menu bottom left>
          <v-btn icon slot="activator">
            <v-icon>more_vert</v-icon>
          </v-btn>
          <v-list>
            <v-list-tile @click="$router.push('sign')">
              <v-list-tile-title>로그인</v-list-tile-title>
            </v-list-tile>
            <v-list-tile @click="signOut">
              <v-list-tile-title>로그아웃</v-list-tile-title>
            </v-list-tile>
          </v-list>
        </v-menu>
      </v-toolbar-items>
    </v-toolbar>
    <v-content>
      <router-view/>
    </v-content>
    <v-footer fixed app>
      <span>&copy; 2017</span>
    </v-footer>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      drawer: null,
      items: [
        {
          icon: 'home',
          title: '홈aaa',
          to: {
            path: '/'
          }
        },
        {
          icon: 'face',
          title: '사용자관리',
          to: {
            path: '/user'
          }
        },
        {
          icon: 'face',
          title: 'header',
          to: {
            path: '/header'
          }
        }
      ],
      title: this.$apiRootPath
    }
  },
  mounted () {
  },
  methods: {
    signOut () {
      localStorage.removeItem('token')
      this.$router.push('/')
    }
  }
}
</script>
```

- 브라우저에서 치고 들어가기 귀찮으니 메뉴에 header를 추가해 놨습니다.

- v-menu를 이용해서 홈으로, 로그인, 로그아웃을 만들었습니다.

- 로그아웃(signOut)함수에서 토큰을 지워버리고 로그인 페이지로 보냅니다.

참고: [https://vuetifyjs.com/ko/components/menus](https://vuetifyjs.com/ko/components/menus)

![alt menu added](/images/nemv/스크린샷 2018-11-01 오전 9.53.27.png)

이제 깔끔하게 정리되고 버튼이 있어서 편리하게 로그인/아웃 작업이 가능해졌습니다.

# 가드 알아보기

페이지에 들어가기 전에 beforeEnter 함수에 먼저 들어갑니다.

> 문서상에는 router.beforeEach 처음으로 등장하는데 그것은 전체 페이지 적용입니다.  
여기서는 특정 페이지만 막기 때문에 쓰지 않습니다.  

인자로는 to(갈 곳), from(온 곳), next(진행)가 있습니다.

**fe/src/router.js**  
```javascript
{
      path: '/header',
      name: '헤더',
      component: () => import('./views/header'),
      // beforeEnter: authCheck
      beforeEnter: (to, from, next) => {
        console.log(to)
        console.log(from)
        next(false)
      }
```

/header에만 beforeEnter를 주고 to, from을 찍어보면 path을 찍어 볼 수 있습니다.

next()가 없으면 진입이 안되니 주의하셔야 합니다.

- next(false) : 페이지로 진입이 안됩니다. (_next() 없는 것과 동일 효과인듯 합니다._) 
- next('/e404') : /header로 안가고 다른페이지로 넘길 수 있습니다.

직접 next(false)를 넣어보고 화면에서 눌러보면 멈춰 있는 것을 보실 수 있습니다.  

# 토큰 존재 유무로 가드해보기

이제 /header는 next(false) 라 진입 할 수 없습니다.

토큰이 없을 때만 막아보면 좋겠죠?

**fe/src/router.js**  
```javascript
beforeEnter: (to, from, next) => {
    if (!localStorage.getItem('token')) return next(false)
    next()
}
```

이제 로그인 했을 때만 /header에 들어갈 수 있게 되었습니다.

# 가드 안내 페이지 만들기

근데 안내 문구도 없고 좀 답답하죠?

그래서 간단하게 안내 페이지를 만들어 봅니다.

**fe/src/views/block.vue**  
```vue
<template>
  <v-container>
    <v-alert type="error" :value="true">
      로그인이 필요한 페이지 입니다.
    </v-alert>
  </v-container>
</template>
```

당연히 등록을 잊으면 안되죠.

**fe/src/router.js**  
```javascript
{
  path: '/header',
  name: '헤더',
  component: () => import('./views/header'),
  // beforeEnter: authCheck
  beforeEnter: (to, from, next) => {
    // console.log(to)
    // console.log(from)
    if (!localStorage.getItem('token')) return next('block')
    next()
  }
},
{
  path: '/block',
  name: '차단',
  component: () => import('./views/block')
},
```

이제 토큰이 없으면 차단페이지가 열리게 됩니다.

# 문제점

아직 아쉬운 부분들이 있습니다. 다음 강좌에서 해결해야할 문제들입니다.

## 토큰 유무만 가지고 보안이 될까?

절대 안됩니다. 크롬 개발자모드에서 Application중 LocalStorage 부분의 값을 추가하면 끝입니다.

페이지 이동시 서버와 교신후에 beforeEnter로 들어가도록 해야합니다.

## 로그인 / 로그아웃이 메뉴에 함께?

뭔가 어색하죠.. 로그인이 되어있으면 로그아웃이 보여야하고 로그아웃 상태면 로그인이 보이는게 정상입니다.

역시 토큰 유무만으로는 자연스럽지 못하므로 유저 정보를 담아 놓을 저장소 시나리오가 필요합니다.

# 영상

{% include video id="e9ihBg1CPW4" provider="youtube" %}   




