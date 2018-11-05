---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 55 회원가입 유효성 판단
category: nemv
tag: [nemv,vee-validate,vue,vuetify,axios]
comments: true
sidebar:
  nav: "nemv4"
---

뷰티파이 폼을 이용해 회원가입을 간단하게 만들어보겠습니다.

회원가입을 아무나 시켜줄 수는 없죠.. 너무 쉬운 1234 비밀번호나 입력이 꼭 필요한 경우(required) 예외처리를 걸어줘야합니다.

그런 예외를 처리해 줄 유효성 판단에 대해 알아보겠습니다.

{% include toc %}

{% raw %}

# 회원가입 페이지 만들기

참고: [https://vuetifyjs.com/ko/components/forms](https://vuetifyjs.com/ko/components/forms)

예제중에서 vee-validate를 이용한 유효성 판단으로 만들어 보겠습니다.

> 이유는 기본 검사는 너무 코드가 지저분해지고.. vuelidate 보다 npm 사용자 수가 많아서 선택했습니다.

# 비벨(vee-validate)란?

프론트에서 입력등을 검사해서 유효성 검사를 하는 것입니다. 

회원가입 양식에 아이디나 패스워드를 공백으로 두거나 하면 안되니 그런 것들을 검사해서 막는 것입니다.

# 유효성 검사 수비수

항상 폼 데이터 전송시에는 예외처리나 제한을 잘 두어야됩니다.

수비는 아래 순서대로 진행해야합니다.

1. 프론트 폼 전송 버튼: 최전방 수비수입니다. 하지만 완벽하지 않습니다. 프론트는 언제나 가공할 수 있다고 생각하셔야합니다.
2. 백엔드 API: 미드필더 입니다. 프론트를 악의적으로 가공했거나 프로그래머 실수를 막아줍니다.
3. 데이터베이스 스키마: 근본적으로 데이터 구조에 제한을 둡니다. (_eg: 문자가 들어가야할 곳에 숫자가 들어가거나 하는 것들_) 

항상 3단계를 전부 염두하고 민감 정보를 다루는 것이 좋습니다.

# 비벨 설치

참고: [https://www.npmjs.com/package/vee-validate](https://www.npmjs.com/package/vee-validate)

```bash
$ cd fe
$ yarn add vee-validate
```

# 비벨 전역 등록

**fe/src/main.js**  
```javascript
import '@babel/polyfill'
import Vue from 'vue'
import VeeValidate from 'vee-validate' // add
import './plugins/vuetify'
// ..
Vue.config.productionTip = false

Vue.use(VeeValidate) // add
// ..
new Vue({
```

이제 다른 페이지에서도 비벨을 쓸 수 있습니다.

# 페이지 만들기

sign.vue의 껍데기에 뷰티파이 예제를 그대로 가져와서 만들었습니다.

**fe/src/views/register.vue**  
```vue
<template>
  <v-container fluid fill-height>
    <v-layout align-center justify-center>
      <v-flex xs12 sm8 md4>
        <v-card class="elevation-12">
          <v-toolbar dark color="primary">
            <v-toolbar-title>회원 가입</v-toolbar-title>
            <v-spacer></v-spacer>
          </v-toolbar>
          <v-card-text>
            <form>
              <v-text-field
                v-validate="'required|max:10'"
                v-model="name"
                :counter="10"
                :error-messages="errors.collect('name')"
                label="Name"
                data-vv-name="name"
                required
              ></v-text-field>
              <v-text-field
                v-validate="'required|email'"
                v-model="email"
                :error-messages="errors.collect('email')"
                label="E-mail"
                data-vv-name="email"
                required
              ></v-text-field>
              <v-select
                v-validate="'required'"
                :items="items"
                v-model="select"
                :error-messages="errors.collect('select')"
                label="Select"
                data-vv-name="select"
                required
              ></v-select>
              <v-checkbox
                v-validate="'required'"
                v-model="checkbox"
                :error-messages="errors.collect('checkbox')"
                value="1"
                label="Option"
                data-vv-name="checkbox"
                type="checkbox"
                required
              ></v-checkbox>

              <v-btn @click="submit">submit</v-btn>
              <v-btn @click="clear">clear</v-btn>
            </form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>

export default {
  $_veeValidate: {
    validator: 'new'
  },

  data: () => ({
    name: '',
    email: '',
    select: null,
    items: [
      'Item 1',
      'Item 2',
      'Item 3',
      'Item 4'
    ],
    checkbox: null,
    dictionary: {
      attributes: {
        email: 'E-mail Address'
        // custom attributes
      },
      custom: {
        name: {
          required: () => 'Name can not be empty',
          max: 'The name field may not be greater than 10 characters'
          // custom messages
        },
        select: {
          required: 'Select field is required'
        }
      }
    }
  }),

  mounted () {
    this.$validator.localize('en', this.dictionary)
  },

  methods: {
    submit () {
      this.$validator.validateAll()
    },
    clear () {
      this.name = ''
      this.email = ''
      this.select = null
      this.checkbox = null
      this.$validator.reset()
    }
  }
}
</script>
```

코드를 보면 대략 짐작이 가능합니다.

공식 홈에서 적어놓은 소스코드가 잘못되어 있을 리는 없으니 긴 설명 보다는 직접 구동시키고 코드를 변경해 보는 것이 이해가 빠를 것 같습니다.

> $_veeValidate: { 같은 것은 저도 뭐하는 짓인지 모르지만 페이지 초기화에 필요하다는 느낌을 받게됩니다.  

# 라우터 등록

**fe/src/router.js**  
```javascript
    // ..
      component: () => import('./views/sign')
    },
    {
      path: '/register',
      name: '회원가입',
      component: () => import('./views/register')
    },
    {
      // ..
```

sign 아래에 아무나 접근할 수 있는 페이지로 등록합니다.

# 접근 메뉴 만들기

**fe/src/App.vue**  
```vue
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
```

토큰이 없을 때 로그인과 회원가입이 나오게 했습니다.

자세히 보면 template이라는 것을 사용하는데요.. 저렇게 의미 없는 묶음을 표시할때 div 같은 역할을 해줍니다.

# 결과

![alt vv](/images/nemv/스크린샷 2018-11-05 오후 2.43.46.png)

값들을 입력할 때도 검사하고 전송(submit)을 누를 때도 검사를 하는 것을 알 수 있습니다.

# 유효성 판단 결과 보기

현재 유효성 검사만 하고 이후 아무것도 안하고 있는데.. then과 catch를 넣으면 왠지 결과를 줄 것 같습니다.

한번 넣어봅니다.

**fe/src/views/register.vue**  
```javascript
this.$validator.validateAll()
    .then(r => console.log(r))
    .catch(e => console.error(e.message))
```

예상했던 데로 true or false를 리턴으로 보내 줍니다.

# 해야할 일

- 전송할 내용을 변경
- 유효성 검사를 마치고 서버에 전송.

이제 두가지를 적용해서 바꿔주면 되는 것입니다.

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}   



