---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 65 게시판을 위한 메뉴 구성
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

편의를 위해 메뉴를 구성해서 게시판 진입하도록 하겠습니다.

{% include toc %}

{% raw %}

# 개요

뷰티파이 리스트그룹을 이용해서 메뉴를 계층적으로 만들어보겠습니다.

참고(최하단부): [https://vuetifyjs.com/ko/components/lists](https://vuetifyjs.com/ko/components/lists)

# 프론트 라우터 변경

**fe/src/router.js**  
```javascript

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/test/lv3',
      name: 'testLv3',
      component: () => import('./views/test/lv3'),
      beforeEnter: pageCheck
    },
    {
      path: '/test/lv2',
      name: 'testLv2',
      component: () => import('./views/test/lv2'),
      beforeEnter: pageCheck
    },
    {
      path: '/test/lv1',
      name: 'testLv1',
      component: () => import('./views/test/lv1'),
      beforeEnter: pageCheck
    },
    {
      path: '/test/lv0',
      name: 'testLv0',
      component: () => import('./views/test/lv0'),
      beforeEnter: pageCheck
    },
    {
      path: '/manage/users',
      name: 'manageUsers',
      component: () => import('./views/manage/user'),
      beforeEnter: pageCheck
    },
    {
      path: '/manage/pages',
      name: 'managePages',
      component: () => import('./views/manage/pages'),
      beforeEnter: pageCheck
    },
    {
      path: '/manage/sites',
      name: 'manageSites',
      component: () => import('./views/manage/sites'),
      beforeEnter: pageCheck
    },
    {
      path: '/manage/boards',
      name: 'manageBoards',
      component: () => import('./views/manage/boards'),
      beforeEnter: pageCheck
    },
    {
      path: '/block/:msg',
      name: '차단',
      component: () => import('./views/block')
    },
    // {
    //   path: '/test',
    //   name: 'test',
    //   component: () => import('./views/test')
    // },
    {
      path: '/sign',
      name: '로그인',
      component: () => import('./views/sign')
    },
    {
      path: '/register',
      name: '회원가입',
      component: () => import('./views/register')
    },
    {
      path: '*',
      name: 'e404',
      component: () => import('./views/e404')
    }
  ]
})
```

이제부터 점점 늘어날 파일들이 걱정되서 디렉토리로 옮겨 봤습니다.

# 프론트 메뉴 변경

**fe/src/App.vue**  
```vue
<template>
  <v-app :dark="siteDark">
    <v-navigation-drawer
      persistent
      v-model="drawer"
      :mini-variant.sync="mini"
      enable-resize-watcher
      fixed
      app
    >
      <v-toolbar flat class="transparent">
        <v-list class="pa-0">
          <v-list-tile avatar>
            <v-list-tile-avatar>
              <v-badge
                overlap
                color="orange"
              >
                <v-icon
                  slot="badge"
                  dark
                  small
                >notifications</v-icon>
                <v-icon
                  large
                  color="grey darken-1"
                >
                  account_box
                </v-icon>
              </v-badge>
            </v-list-tile-avatar>
            <v-list-tile-content>
              <v-list-tile-title>관리자</v-list-tile-title>

            </v-list-tile-content>
            <v-list-tile-action>
              <v-btn icon @click.native.stop="mini = !mini">
                <v-icon>chevron_left</v-icon>
              </v-btn>
            </v-list-tile-action>
          </v-list-tile>
        </v-list>
      </v-toolbar>
      <v-list>
        <v-list-group
          v-for="(item, i) in items"
          v-model="item.act"
          :prepend-icon="item.icon"
          :key="i"
          no-action
        >
          <v-list-tile slot="activator">
            <v-list-tile-content>
              <v-list-tile-title>{{ item.title }}</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
          <v-list-tile
            v-for="subItem in item.subItems"
            :key="subItem.title"
            :to="subItem.to"
          >
            <v-list-tile-content>
              <v-list-tile-title>{{ subItem.title }}</v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list-group>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar
      app
    >
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-toolbar-title v-text="siteTitle"></v-toolbar-title>
      <v-spacer></v-spacer>
      <v-toolbar-items>
        <v-menu bottom left>
          <v-btn icon slot="activator">
            <v-icon>more_vert</v-icon>
          </v-btn>
          <v-list>
            <template v-if="!$store.state.token">
              <v-list-tile  @click="$router.push('/sign')">
                <v-list-tile-title>로그인</v-list-tile-title>
              </v-list-tile>
              <v-list-tile  @click="$router.push('/register')">
                <v-list-tile-title>회원가입</v-list-tile-title>
              </v-list-tile>
            </template>
            <v-list-tile v-else @click="signOut">
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
      <span>{{siteCopyright}}</span>
    </v-footer>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      drawer: null,
      mini: false,
      siteTitle: '기다리는중',
      siteCopyright: '기다리는중',
      siteDark: false,
      items: [
        {
          icon: 'pan_tool',
          title: '레벨테스트',
          act: true,
          subItems: [
            {
              icon: 'home',
              title: '손님용 페이지',
              to: {
                path: '/test/lv3'
              }
            },
            {
              icon: 'pets',
              title: '일반유저용 페이지',
              to: {
                path: '/test/lv2'
              }
            },
            {
              icon: 'pan_tool',
              title: '슈퍼유저용 페이지',
              to: {
                path: '/test/lv1'
              }
            },
            {
              icon: 'motorcycle',
              title: '관리자용 페이지',
              to: {
                path: '/test/lv0'
              }
            }
          ]
        },
        {
          icon: 'settings',
          title: '관리메뉴',
          subItems: [
            {
              icon: 'face',
              title: '사용자관리',
              to: {
                path: '/manage/users'
              }
            },
            {
              icon: 'pageview',
              title: '페이지관리',
              to: {
                path: '/manage/pages'
              }
            },
            {
              icon: 'settings',
              title: '사이트관리',
              to: {
                path: '/manage/sites'
              }
            },
            {
              icon: 'settings',
              title: '게시판관리',
              to: {
                path: '/manage/boards'
              }
            }
          ]
        }
      ],
      title: this.$apiRootPath
    }
  },
  mounted () {
    this.getSite()
  },
  methods: {
    signOut () {
      this.$store.commit('delToken')
      this.$router.push('/')
    },
    getSite () {
      this.$axios.get('/site')
        .then(r => {
          this.siteTitle = r.data.d.title
          this.siteCopyright = r.data.d.copyright
          this.siteDark = r.data.d.dark
        })
        .catch(e => console.error(e.message))
    }
  }
}
</script>
```

카드나 리스트는 항상 순서에 맞게 쓰면 이해가 빠릅니다.

계층이 v-list / v-list-tile, v-list-group / v-list-tile-avatar, v-list-tile-content 등으로 구성되는 것을 확인할 수 있습니다.

작명 센스를 봐도 한눈에 의도를 파악할 수 있습니다.

# 결과

![alt app.vue](/images/nemv/스크린샷 2018-11-15 오전 10.08.59.png)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/140bd7a0a037d22f1ba4943ed2f4c450d40e9db3)

{% endraw %}

# 영상

{% include video id="wIfZVsqOMCU" provider="youtube" %}
