---
layout: single
title: NUXT로 혼자 웹사이트 만들기 13 파이어베이스(firebase) 인증 기능 화면 표시하기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,authorization,vuex]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스 인증 기능을 넉스트 저장소(vuex)를 이용해 표현해봅니다.

{% raw %}

# 개요

참고: [https://nuxtjs.org/examples/auth-external-jwt](https://nuxtjs.org/examples/auth-external-jwt)

# 뷰저장소 사용하기

**store/index.js**  
```javascript
export const state = () => ({
  user: null
})
export const mutations = {
  setUser(state, user) {
    if (!user) return (state.user = null)
    state.user = {}
    Object.entries(user).forEach(([k, v]) => {
      if (typeof v === 'string') state.user[k] = v
    })
  }
}
```

# 파이어베이스 플러그인 분리

**plugin/firebase.js**  
```javascript
import Vue from 'vue'
import firebase from 'firebase/app'
// import 'firebase/firestore'
import 'firebase/auth'

const config = {
  apiKey: process.env.FIREBASE_APIKEY,
  authDomain: process.env.FIREBASE_AUTHDOMAIN,
  projectId: process.env.FIREBASE_PROJECTID
}

export default function({ store, redirect }) {
  if (!firebase.apps.length) firebase.initializeApp(config)
  Vue.prototype.$auth = firebase.auth

  firebase.auth().onAuthStateChanged(user => {
    store.commit('setUser', user)
  })
}
```

auth.vue -> move

etc.js -> firebase.js

# 플러그인 등록

**nuxt.config.js**  
```javascript
  plugins: [
    '@/plugins/vuetify',
    '@/plugins/etc',
    '@/plugins/firebase'
    // { src: '~plugins/firebase.js', mode: 'client' }
  ],
```

# 사용자 화면에 표시해보기

## lodash 설치

```bash
$ yarn add lodash
```

**plugins/etc.js**  
```javascript
import _ from 'lodash'
Vue.prototype._ = _
```

## 네비게이션 드로워 수정

**layout/default.vue**  
```vue
  <!-- -->
    <v-navigation-drawer v-model="drawer" fixed app>
      <v-toolbar flat class="transparent">
        <v-list class="pa-0">
          <v-list-tile avatar>
            <v-list-tile-avatar>
              <img src="https://randomuser.me/api/portraits/men/85.jpg" />
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>
                {{ _.get($store.state.user, 'displayName', '로그인 필요') }}
              </v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-toolbar>
      <v-list>
        <v-divider></v-divider>
  <!-- -->
```

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

{% endraw %}

# 영상

{% include video id="" provider="youtube" %}
