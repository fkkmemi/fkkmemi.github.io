---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 77 구글 리캡챠로 로봇 막아보기(recaptcha)
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify,recaptcha]
comments: true
sidebar:
  nav: "nemv5"
---

구글에서 제공하는 리캡챠(recaptcha)를 이용하여 로봇을 막아보겠습니다.

{% include toc %}

{% raw %}

# 개요

회원가입이나 게시물 작성은 권한 없이 이용가능하기 때문에 매크로를 이용해서 무한 회원가입을 시킬 수 있습니다.

물론 회원가입은 이메일이나 sms인증을 나중에 추가하면 해결될 문제이지만 게시물은 막을 수가 없죠..

그래서 리캡챠라는 것이 퀴즈를 내서 사람인지 매크로(로봇)인지 구분하는 방법입니다.

최근 recaptcha v3가 나왔는데 도메인이 있어야해서 v2로 진행하고 서버에 옮기면 v3로 변경하겠습니다. 

참고: [https://developers.google.com/recaptcha/docs/invisible](https://developers.google.com/recaptcha/docs/invisible)

# 구글에서 사용키 받기

![alt google register](/images/nemv/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-11-21%20%EC%98%A4%ED%9B%84%203.01.40.png)

구글로 로그인 한 후 사이트 등록을 합니다.

[https://www.google.com/recaptcha/admin#list](https://www.google.com/recaptcha/admin#list)

- v3 퀴즈 내지 않고 투표형식으로 한답니다.

- v2 checkbox의 경우 항상 화면에 붙어 있는 것입니다.

- v2 invisible은 행위를 판단하고(마우스 움직임등) 오래되었을 경우(캡챠 토큰 발급한지) 팝업으로 나타납니다. 

- 도메인은 적용시킬 사이트 명입니다. 우선 테스트 해볼 것이기 때문에 localhost 하나만 남깁니다.

**사이트 생성 후 화면**  
![alt google site](/images/nemv/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-11-21%20%EC%98%A4%ED%9B%84%203.08.29.png)

위 화면처럼 클라이언트와 서버 두개다 인증을 받아 사용하게 됩니다.

# 사용키 없이 테스트하기

참고: [https://developers.google.com/recaptcha/docs/faq](https://developers.google.com/recaptcha/docs/faq)

For reCAPTCHA v3, create a separate key for testing environments. Scores may not be accurate as reCAPTCHA v3 relies on seeing real traffic.

For reCAPTCHA v2, use the following test keys. You will always get No CAPTCHA and all verification requests will pass.

Site key: 6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI
Secret key: 6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe

![alt testkey](https://developers.google.com/recaptcha/images/recaptcha_test.png)

v2 는 위의 2키를 사용해서 테스트가 가능합니다.

# 작전

회원가입시 리캡챠(퀴즈 맞추기)로 토큰 받음

회원가입 내용과 리캡챠토큰을 서버에 전송

서버에서 구글에 리캡챠토큰과 시크릿키로 확인

프론트 리캡챠 토큰 발급 -> 토큰 전송 -> 백엔드수신 -> 구글에 전송 -> 구글 응답 -> 프론트 전달

# 프론트

## vue-recaptcha 설치

뷰에서 리캡챠를 편리하게 사용할 수 있는 모듈을 설치합니다.

> 사실 네이티브로 코딩해도 크게 어려울 것은 없습니다..

참고: [https://www.npmjs.com/package/vue-recaptcha](https://www.npmjs.com/package/vue-recaptcha)
```bash
$ cd fe
$ yarn add vue-recaptcha
```

## 구글 api 로드

### 일반적인 방법

일반적인 구글 api 로드 방법은 프론트의 뼈다귀에 스크립트 코드를 추가하면 됩니다.

**fe/public/index.html**  
```html
<script src="https://www.google.com/recaptcha/api.js?onload=vueRecaptchaApiLoaded&render=explicit" async defer></script>
```

vue-recaptcha 공식 문서에는 위와 같이 추가하라고 되어 있습니다.

구글 api를 사용하면서 로드가 되었을때 vue-recaptcha가 만든 vueRecaptchaApiLoaded 함수에서 뭔가를 하는 모양입니다.

저런식으로 하면 동작에는 문제가 없지만 vueRecaptchaApiLoaded 함수가 없다고 워닝이 뜹니다.

### 스크립트 동적 로드(vue-plugin-load-script)

그래서 동적으로 로드 할 수 있게 만들 모듈을 설치 해봅니다.

참고: [https://www.npmjs.com/package/vue-plugin-load-script](https://www.npmjs.com/package/vue-plugin-load-script)

```bash
$ yarn add vue-plugin-load-script
```

## 설정파일 제작(리캡챠 사이트키)

이 소스를 가지고 다른 도메인에서 사용하려면 구글 api키를 소스 관리대상에서 빼줘야합니다.

**fe/config/index.js**  
```javascript
module.exports = {
  recaptchaSiteKey: '6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI'
}
```

## 공용 등록

다른 곳에서 사용할 수 있도록 설치한 모듈들을 main.js에 등록해봅니다.

**fe/src/main.js**  
```javascript
// ..
import VeeValidate from 'vee-validate'
import LoadScript from 'vue-plugin-load-script'
import VueRecaptcha from 'vue-recaptcha'
import './plugins/vuetify'
// ..
import cfg from '../config'

Vue.config.productionTip = false

Vue.prototype.$cfg = cfg

Vue.use(VeeValidate)
Vue.use(LoadScript)

Vue.loadScript("https://www.google.com/recaptcha/api.js?onload=vueRecaptchaApiLoaded&render=explicit")
  .then(() => {
    Vue.component('vue-recaptcha', VueRecaptcha)
  })
  .catch((e) => {
    console.error(`google api load failed: ${e.message}`)
  })
// ..
```

- cfg의 사이트키를 전역으로 사용하기 위해 프로토타입으로 설정합니다.
- 동적으로 구글 api를 로드한 후에 전역으로 사용할 vue-recaptcha 콤포넌트를 등록해 줍니다.

## 회원가입 페이지에 리캡챠 테스트

**fe/src/views/register.vue**  
```vue
<!-- -->
  ></v-checkbox>
  <vue-recaptcha
    ref="recaptcha"
    :sitekey="$cfg.recaptchaSiteKey"
    size="invisible"
    @verify="onVerify"
    @expired="onExpired"
  >
  </vue-recaptcha>
  <v-spacer></v-spacer>
  <v-btn @click="exec()">봇체크</v-btn>  
  <v-btn @click="reset()">리셋</v-btn>  
  <v-btn @click="submit()">가입</v-btn>  
<!-- -->
<script>
  methods: {
    onVerify (r) {
      console.log(r)
    },
    onExpired () {
      this.$refs.recaptcha.reset()
    },
    exec () {
      this.$refs.recaptcha.execute()
    },
    reset () {
      this.$refs.recaptcha.reset()
    },
  }
</script>
```

이렇게 해두고 봇체크를 클릭하면 동작 느낌과 토큰을 찍어 볼 수 있습니다.

```text
03ADlfD1-i_5yO6-XUnLsZdiZePK4qhernmXSV-_H0RrLhZIJ8KzXympbo5uylP0CabYdxiApp2ve17It2LcjeIwvaWug9
EQawNSwL1jI0nn1qFPZO0ByAAO1fd3FNVPo-GLAFM4nib-9Y_GdPCKPl4jn3BnAnf2MOtZcOWIdiaik60fdR_Jc43dd8Edm8gL
_pyIgYK8WJ1m0dBTBHqIyd2FFqaeJAPeEvIEHipgMEJtogG8aG_on1h9Xsa3-ZJ3TBLNKCeH9KB6oDcEdD8YS92sTmUE2xnB_Y
6g
```

## 백엔드에 전송까지

사실 토큰 유무로도 프론트에서 판단해서 마무리 할 수 있습니다.

하지만 프론트는 항상 믿을 것이 못됩니다.

서버 확인까지 해서 확실하게 처리합니다.

**fe/src/views/register.vue**  
```vue
<!-- -->
  ></v-checkbox>
  <vue-recaptcha
    ref="recaptcha"
    :sitekey="$cfg.recaptchaSiteKey"
    size="invisible"
    @verify="onVerify"
    @expired="onExpired"
  >
  </vue-recaptcha>
  <v-spacer></v-spacer>
  <v-btn @click="checkRobot()">가입</v-btn>
  <v-btn @click="clear">초기화</v-btn>
<!-- -->
<script>
export default {
  // ..
  data: () => ({
    form: {
      id: '',
      name: '',
      pwd: '',
      response: '' // add
    },
    // ..
  methods: {
    onVerify (r) {
      this.form.response = r
      this.$refs.recaptcha.reset()
      this.submit()
    },
    onExpired () {
      this.form.response = ''
      this.$refs.recaptcha.reset()
    },
    checkRobot () {
      if (this.form.response) this.submit()
      else this.$refs.recaptcha.execute()
    },
    submit () {
    // ..
</script>
```

- form.response를 만들어서 전송시 같이 보내게 합니다.
- 가입 버튼을 누르면 checkRobot() 이 호출됩니다.
- 토큰(this.form.response)이 있다면 리캡챠 없이 가입하고 아니면 리캡챠를 호출합니다.
- 리캡챠 퀴즈를 맞추면 onVerify에 들어옵니다.  
토큰을 저장하고 리캡챠가 다음에 또 사용할 수 있게 리셋합니다.
- 기간이 오래되면 onExpired에 들어옵니다.  
토큰을 지우고 리캡챠를 리셋해줍니다.

> 페이지 이동시에도 토큰을 지키고 싶으면(리캡챠 등장이 너무 잦으면..)  
로컬스토리지에 토큰을 저장해두시면 됩니다.(localStorage.setItem('recaptcha', r))

# 백엔드

## 설정파일 제작(리캡챠 시크릿키)

이 소스를 가지고 다른 도메인에서 사용하려면 구글 api키를 소스 관리대상에서 빼줘야합니다.

**config/index.js**  
```javascript
  recaptchaSecret: '6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe' // google testkey
```

## request 모듈 설치

프론트에서 전달 받은 토큰과 시크릿키로 구글에 전달해야 합니다.

백엔드가 요청하는 것입니다.

그래서 http 요청을 간단하게 할 수 있는 모듈인 request를 사용해보겠습니다.

> 백엔드에도 당연히 axios가 설치 되서 해봤더니 데이터 에러가 나서 익숙한 고전 request로 구현했습니다.  
   
참고: [https://www.npmjs.com/package/request](https://www.npmjs.com/package/request)   
   
```bash
$ cd be
$ yarn add request
```

## api 리캡챠 유효성 판단 넣기

**be/routes/api/sign/index.js**  
```javascript
const request = require('request')
const cfg = require('../../../../config')

router.post('/up', (req, res, next) => {
  const u = req.body
  if (!u.id) throw createError(400, '아이디가 없습니다')
  if (!u.pwd) throw createError(400, '비밀번호가 없습니다')
  if (!u.name) throw createError(400, '이름이 없습니다')
  if (!u.response) throw createError(400, '로봇 검증이 없습니다')

  const ro = {
    uri: 'https://www.google.com/recaptcha/api/siteverify',
    json: true,
    form: {
      secret: cfg.recaptchaSecretKey,
      response: u.response,
      remoteip: req.ip
    }
  }
  request.post(ro, (err, response, body) => {
    if (err) throw createError(401, '로봇 검증 실패입니다')

    User.findOne({ id: u.id })
      .then((r) => {
        if (r) throw new Error('이미 등록되어 있는 아이디입니다')
        return User.create(u)
      })
      .then((r) => {
        const pwd = crypto.scryptSync(r.pwd, r._id.toString(), 64, { N: 1024 }).toString('hex')
        return User.updateOne({ _id: r._id }, { $set: { pwd } })
      })
      .then((r) => {
        res.send({ success: true })
      })
      .catch((e) => {
        res.send({ success: false, msg: e.message })
      })
  })
})
```

- 기존 /api/register 에서 /api/sign/up 으로 변경했습니다.(_단순 변심_) 
- 수신시 u.response를 추가했습니다.
- request를 이용하여 시크릿키, 프론트에서 받은 키, 접속 아이피(option)을 POST로 전송합니다.
- 콜백을 받아서 에러가 없다면 원래 하던 일을 진행시킵니다.

# 결과

![alt result](/images/nemv/%EC%8A%A4%ED%81%AC%EB%A6%B0%EC%83%B7%202018-11-21%20%EC%98%A4%ED%9B%84%205.02.48.png)

# 소스

실제 소스에는 게시판도 추가되어 있습니다.

```bash
git checkout bafa9a2a71415fab3377b234ac9ed69ab9067f46
```

[소스 확인](https://github.com/fkkmemi/nemv3/commit/bafa9a2a71415fab3377b234ac9ed69ab9067f46)

{% endraw %}

# 영상

{% include video id="oU5NIQkhTd0" provider="youtube" %}

