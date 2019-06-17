---
layout: single
title: NUXT로 혼자 웹사이트 만들기 14 파이어베이스(firebase) 인증 기능 라우팅해보기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,authorization,vuex]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스 인증 상태에 따라 페이지 전환을 해보겠습니다.

{% raw %}

# 개요

인증 상태에 따라서 페이지 진입이 가능/불가능하게 만들어 보겠습니다.

# 로그인 페이지 만들기

**pages/auth/signIn.vue**  
```vue
<template>
  <v-card>
    <v-card-title primary-title>
      로그인
    </v-card-title>
    <v-card-text>
      <v-form>
        <v-text-field v-model="form.email" label="email"></v-text-field>
        <v-text-field
          v-model="form.password"
          label="password"
          type="password"
        ></v-text-field>
      </v-form>
    </v-card-text>
    <v-card-actions>
      <v-btn @click="signIn">
        signIn
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
export default {
  data() {
    return {
      form: {
        email: '',
        password: ''
      }
    }
  },
  mounted() {},
  methods: {
    async signIn() {
      try {
        const r = await this.$auth().signInWithEmailAndPassword(
          this.form.email,
          this.form.password
        )
        this.$router.push('/')
        console.log(r)
      } catch (e) {
        console.error(e.message)
      }
    }
  }
}
</script>
```

간단한 로그인 페이지를 만듭니다.

로그인이 정상이면 메인페이지로 이동시킵니다.

# 미들웨어 만들기

**middleware/auth.js**  
```javascript
export default function({ store, redirect, route }) {
  if (!store.state.user && route.path !== '/auth/signIn')
    return redirect('/auth/signIn')
}
```

뷰저장소에 사용자가 없고 현재 페이지가 로그인 페이지가 아닐 때 로그인 페이지로 이동시킵니다.

> route.path !== '/auth/signIn' 조건이 없다면 무한 반복 에러가 나니 조심해야합니다.

# 로그아웃 만들기

**layout/default.vue**  
```vue
<v-list-tile-action>
  <v-btn v-if="$store.state.user" icon @click="signOut">
    <v-icon>lock_open</v-icon>
  </v-btn>
</v-list-tile-action>
// 
async signOut() {
  try {
    await this.$auth().signOut()
    this.$router.push('/auth/signIn')
  } catch (e) {
    console.error(e.message)
  }
}
```

로그아웃이 되면 로그인 페이지로 이동시킵니다.

# 설정파일

**nuxt.config.js**  
```javascript
router: {
  middleware: 'auth'
},
```

상단에 미들웨어 파일을 지정해줍니다.

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

```
# 해당 코드 확인하기
$ git checkout tags/v0.0.14
```

{% endraw %}

# 영상

{% include video id="jJIiZCh2WY0" provider="youtube" %}
