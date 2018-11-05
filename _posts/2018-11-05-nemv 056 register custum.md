---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 56 회원가입 프론트 적용하기
category: nemv
tag: [nemv,vee-validate,vue,vuetify,axios]
comments: true
sidebar:
  nav: "nemv4"
---

뷰티파이 예제로 만들고 있는 모델에 맞게 수정해보겠습니다. 

{% include toc %}

{% raw %}

# 비벨(vee-validate) 한글화

참고: [https://baianat.github.io/vee-validate/guide/localization.html](https://baianat.github.io/vee-validate/guide/localization.html)

**fe/src/views/register.vue**  
```javascript
import ko from 'vee-validate/dist/locale/ko' // add

export default {
  $_veeValidate: {
    validator: 'new'
  },

  data: () => ({
    // ..
    dictionary: {
      messages: ko.messages, // add
      // ..
    }
  }),

  mounted () {
    this.$validator.localize('ko', this.dictionary) // en -> ko
  },
  // .. 
}
```

- locale/ko 등록
- 사전(dictionary) 메세지 등록
- 시작할때 ko로 로컬라이즈

공식홈의 예제로 간단히 변경해두었습니다.

간단한 표현만 한글화가 지원되고 더 디테일하게 변경하고 싶으면 dictionary custom 부분에서 변경해주면됩니다.

# 알림 메세지(스낵바) 추가

**fe/src/views/register.vue**  
```vue
    <v-snackbar
      v-model="sb.act"
    >
      {{ sb.msg }}
      <v-btn
        :color="sb.color"
        flat
        @click="sb.act = false"
      >
        닫기
      </v-btn>
    </v-snackbar>
  </v-container>
</template>
<script>
  data: () => ({
    sb: {
      act: false,
      msg: '',
      color: 'warning'
    },
  // ..
  methods: {
    pop (m, cl) {
      this.sb.act = true
      this.sb.msg = m
      this.sb.color = cl
    },
    // ..
</script>
```

console.log 대신 pop이라는 함수를 만들어서 스낵바로 표시해봤습니다.

# 모델에 맞게 페이지 변경

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
                v-validate="'required|min:4|max:20'"
                v-model="form.id"
                :counter="20"
                :error-messages="errors.collect('id')"
                label="아이디"
                data-vv-name="id"
                required
              ></v-text-field>
              <v-text-field
                v-validate="'required|min:6|max:40'"
                v-model="form.pwd"
                :counter="40"
                :error-messages="errors.collect('pwd')"
                label="비밀번호"
                data-vv-name="pwd"
                required
                type="password"
              ></v-text-field>
              <v-text-field
                v-validate="'required|min:1|max:40'"
                v-model="form.name"
                :counter="40"
                :error-messages="errors.collect('name')"
                label="이름"
                data-vv-name="name"
                required
              ></v-text-field>

              <v-checkbox
                v-validate="'required'"
                v-model="agree"
                :error-messages="errors.collect('agree')"
                value="1"
                label="약관동의: 암호화도 안되어 있는 사이트인데 정말 가입하겠습니까?"
                data-vv-name="agree"
                type="checkbox"
                required
              ></v-checkbox>

              <v-btn @click="submit">가입</v-btn>
              <v-btn @click="clear">초기화</v-btn>
            </form>
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
    <v-snackbar
      v-model="sb.act"
    >
      {{ sb.msg }}
      <v-btn
        :color="sb.color"
        flat
        @click="sb.act = false"
      >
        닫기
      </v-btn>
    </v-snackbar>
  </v-container>
</template>

<script>
import ko from 'vee-validate/dist/locale/ko'

export default {
  $_veeValidate: {
    validator: 'new'
  },

  data: () => ({
    form: {
      id: '',
      name: '',
      pwd: ''
    },
    sb: {
      act: false,
      msg: '',
      color: 'warning'
    },
    agree: null,
    dictionary: {
      messages: ko.messages,
      attributes: {
        id: '아이디',
        pwd: '비밀번호',
        name: '이름',
        agree: '약관동의'
        // custom attributes
      },
      custom: {
        // name: {
        //   required: () => 'Name can not be empty',
        //   max: 'The name field may not be greater than 10 characters'
        //   // custom messages
        // },
        // select: {
        //   required: 'Select field is required'
        // }
      }
    }
  }),

  mounted () {
    this.$validator.localize('ko', this.dictionary)
  },

  methods: {
    submit () {
      this.$validator.validateAll()
        .then(r => {
          if (!r) throw new Error('모두 기입해주세요')
          return this.$axios.post('register', this.form)
        })
        .then(r => {
          if (!r.data.success) throw new Error('서버가 거부했습니다.')
          this.pop('가입 완료 되었습니다.', 'success')

          this.$route.push('/sign')
        })
        .catch(e => this.pop(e.message, 'warning'))
    },
    pop (m, cl) {
      this.sb.act = true
      this.sb.msg = m
      this.sb.color = cl
    },
    clear () {
      this.form.id = ''
      this.form.pwd = ''
      this.form.name = ''
      this.agree = null
      this.$validator.reset()
    }
  }
}
</script>
```

- 전송될 데이터를 form 변수에 담았습니다.
- 텍스트 필드 룰: ```required|min:4|max:20``` 이면 꼭 넣어야하고 4글자 이상 20글자 이하 인것이죠
- v-model로 값들과 양방향 바인딩 상태입니다.
- error.collect()로 아마도 data-vv-name와 연결되어 메세지가 표시되는 것 같습니다.(_자세히는 모름.._)
- :counter의 경우 그저 표시용입니다. 제한사항은 아님!
- dictionary.attr.. 는 메세지에서 'id'를 '아이디'로 표시해줍니다.
- this.$validator.validateAll() 로 false 가 나올경우 에러로 보냅니다.
- 액시오스 요청으로 form 내용을 전송합니다.

# 적용된 화면

![alt apply](/images/nemv/스크린샷 2018-11-05 오후 4.43.45.png)

# 마치며

역시 직접 설명하는 것 보다는 값들을 변경해보며 확인하는 것이 훨씬 이해가 빠릅니다~ 

{% endraw %}

# 영상

{% include video id="5xLWANQZo70" provider="youtube" %}   



