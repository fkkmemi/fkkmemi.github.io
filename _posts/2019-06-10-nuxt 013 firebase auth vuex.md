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

파이어베이스 인증 클라이언트에서는 변화가 감지(로그인, 아웃, 비밀번호 변경등)될때마다 추적이 가능합니다.

변화된 인증 요소를 뷰저장소에 연결해두면 상태확인도 되고 적당한 페이지로 연결해줄 수 있습니다.

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

기존 etc.js에 있던 파이어베이스 내용을 가져와서 새로운 파일로 만듭니다.

기존 auth.vue에 있던 변화 감지 함수(onAuthStateChanged)를 가져옵니다.

export default 를 사용해서 store를 참고할 수 있습니다.

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

store 디렉토리에 엔트리 파일을 만듭니다.

> state.user = user로 하면 에러가 납니다. state.user에 올바르지 못한 변이가 일어났다고 경고합니다.  
> 많은 파이어베이스 user요소중 문제가 있는 것 같습니다.(콘솔로 user를 찍어보면 무지 많음)
> 그래서 그 중 필요한 문자열 정보만 가져왔습니다.

이제 어디서든 this.$store.state.user 로 상태를 확인할 수 있습니다.

# 사용자 화면에 표시해보기

## lodash 설치

```bash
$ yarn add lodash
```

예외처리를 위해서 로대시를 설치합니다.

**plugins/etc.js**  
```javascript
import _ from 'lodash'
Vue.prototype._ = _
```

this._. 로 사용하기 위해 프로토타입 등록을 해둡니다.

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

로대시의 get메쏘드로 예외 처리를 해주면 완성입니다.

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

```
# 해당 코드 확인하기
$ git checkout tags/v0.0.13
```

{% endraw %}

# 영상

{% include video id="d-r6DZ4Ik5Y" provider="youtube" %}
