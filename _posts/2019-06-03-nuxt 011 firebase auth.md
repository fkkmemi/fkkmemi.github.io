---
layout: single
title: NUXT로 혼자 웹사이트 만들기 11 파이어베이스(firebase) 인증 기능(email&password) 사용하기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,authorization]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스 인증기능중 이메일+비밀번호를 사용해서 로그인해봅니다.

# 개요

웹사이트를 제작할 때 항상 고민스러운 부분이 있습니다.

바로 인증입니다.

저의 경우도 매번 향상된 보안의식과 더불어 프로젝트마다 제각각의 인증 코딩을 했습니다.

그러한 고민을 통일해서 파이어베이스 인증 상품으로 묶었습니다.

파이어베이스 인증 API만 사용하면 확인 이메일이나 계정 복구등 다양한 기본 인증 기능을 수행하는 것입니다.

# 콘솔 설정

![alt fb auth console](/images/nuxt/2019-06-03_13.36.07.png)

먼저 콘솔에서 이메일 패스워드에 체크를 해야합니다.

# 파이어베이스 설정

**src/plugins/etc.js**  
```
import firebase from 'firebase/app'
import 'firebase/auth'

Vue.prototype.$auth = firebase.auth
```

전역 사용이 가능하게 프로토타입으로 만들었습니다.

# 사용자 관리

참고: [https://firebase.google.com/docs/auth/web/manage-users?authuser=0](https://firebase.google.com/docs/auth/web/manage-users?authuser=0)

공식홈에 있는 자료대로 쓸만한 기능 위주로 실험해봤습니다.

{% raw %}

## 회원가입

**src/pages/auth.vue**  
```vue
<template>
  <v-card>
    <v-card-title>
      Auth test
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
    <v-card-text>
      {{ msg }}
    </v-card-text>
    <v-card-actions>
      <v-btn @click="signUp">
        signUp
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
      },
      msg: ''
    }
  },
  mounted() {},
  methods: {
    async signUp() {
      try {
        const r = await this.$auth.createUserWithEmailAndPassword(
          this.form.email,
          this.form.password
        )
        console.log(r)
      } catch (e) {
        console.error(e.message)
      }
    }
  }
}
</script>
```

회원가입이 잘 되는 것을 확인할 수 있습니다.

## 사용자 확인하기

```vue
<v-btn @click="getUser">
    getUser
</v-btn>
// ...

getUser() {
    const u = this.$auth().currentUser
    this.msg = JSON.stringify(u)
}
```

다양한 정보를 확인할 수 있습니다.

## 사용자 수정하기

```vue
<v-btn @click="setUser">
    setUser
</v-btn>
// ...
async setUser() {
    const u = this.$auth().currentUser
    try {
        await u.updateProfile({ displayName: this.form.email })
        console.log('update ok')
    } catch (e) {
        console.error(e.message)
    }
}
```

# 사용자 기능

회원가입과 동시에 로그인이 되어 있는 상태이기 때문에 먼저 로그아웃이 필요합니다.

참고: [https://firebase.google.com/docs/auth/web/password-auth?authuser=0](https://firebase.google.com/docs/auth/web/password-auth?authuser=0)

## 로그아웃

```vue
<v-btn @click="signOut">
    signOut
</v-btn>
// ..
async signOut() {
    try {
        await this.$auth().signOut()
    } catch (e) {
        console.error(e.message)
    }
}
```

## 로그인

```vue
<v-btn @click="signIn">
    signIn
</v-btn>
// ..
async signIn() {
    try {
        const r = await this.$auth().signInWithEmailAndPassword(
            this.form.email,
            this.form.password
        )
        console.log(r)
    } catch (e) {
        console.error(e.message)
    }
},
```

회원가입과 펑션이름만 다릅니다.

## 상태확인하기

로그인 상태가 파악이 된다면 vuex스토어등에서 관리하기 편리해집니다.

다행히도 인증상태 훅이 있습니다.

```vue
mounted() {
    this.$auth().onAuthStateChanged(function(user) {
        if (user) {
            console.log(user)
        } else {
            console.log('not actions...')
        }
    })
},
```

로그 인/아웃을 해보면 해당 이벤트로 들어오는 것을 알 수 있습니다.

# 마치며

{% endraw %}

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

# 영상

{% include video id="1JnMsyLOa6k" provider="youtube" %}
