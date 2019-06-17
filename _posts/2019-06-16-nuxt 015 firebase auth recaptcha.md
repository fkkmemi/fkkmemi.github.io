---
layout: single
title: NUXT로 혼자 웹사이트 만들기 15 파이어베이스(firebase) 리캡챠(recaptcha v3) 사용하기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,authorization,recaptcha,v3]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

넉스트(nuxt) 와 파이어베이스 펑션스 (firebase functions)로 리캡챠(recaptcha v3)를 사용해봅니다.

{% raw %}

# 개요

리캡챠는 로봇의 공격을 막는 방법중 하나입니다.

악의적인 사용자가 봇을 이용해 엄청난 게시물을 작성하거나 무한 회원가입을 하는 것등을 방지하는 것입니다.

```javascript
const 악의성댓글s
for (let 댓글 in 악의성댓글s) axios.post('공격경로', { 댓글.title, 댓글.content, 댓글.etc })
```

사람이 아닌 위와 같은 스니펫등은 당연히 막아줘야하는 것이죠.

구글 리캡챠는 무료입니다.

하지만 사용자가 정보 제공자이기 때문에 빅데이터 쌓는 것을 도와주는 격입니다.

사용자들이 푼 퀴즈(그림 중 버스를 찾으시오 등)를 머신러닝 시켜서 정확도를 높히는 방식이랍니다.

리캡챠도 많은 발전을 이뤄서 현재 v3가 나온 상태입니다.

자세한 v3 동작 방식은 아래 링크를 참고해보시면 됩니다.

참고: [https://www.google.com/recaptcha/intro/v3.html](https://www.google.com/recaptcha/intro/v3.html)

# 동작방식

내부적인 동작방식보다 우리에게 중요한 것은 구현방식입니다.(_내부 동작은 어짜피 구글이 알아서 잘 할일.._)

- 요청: 프론트엔드(nuxt) -> 백엔드(firebase functions) -> 구글(api server)
- 응답: 구글(api server) -> 백엔드(firebase functions) -> 프론트엔드(nuxt)

이렇게 항상 3단계가 필요합니다.

**여기서 중요한 것은 파이어베이스 펑션스에서 구글 서버로 접속할 때 일반(스파크) 요금제로는 불가능 하다는 것입니다.**

해당 프로젝트를 블레이즈 요금제로 바꿔야 나가는 패킷이 허용됩니다..

> 에러메세지 보고 한참 찾아서 들어가보니 이러했습니다.. 설명이라도 잘 써놓지.. 좀..
> 블레이즈라고 해봐야 스파크 요금제 포함이기 때문에 어짜피 테스트 용도는 무료 입니다.
> 그래도 조심해야하는 상황이 되어 버린것이죠.
> 스파크 요금제는 할당량 다채우면 동작을 안하지만. 블레이즈는 갑자기 돈이 나가게 되니 말이죠..

# 테스트

테스트가 어렵습니다..

로컬호스트에서 테스트가 아예 안됩니다.

뭔가 방법이 있을 지는 모르겠는데.. 저는 못찾았습니다.

그래서 실제 서버에 배포(deploy)하고 확인해가면서 힘겹게 테스트를 마쳤습니다.

# 구글 리캡챠 키 얻기

생성: [https://www.google.com/recaptcha/admin](https://www.google.com/recaptcha/admin)

해당 페이지에서 허용 주소 백/프론트엔드 경로를 입력해주면 키를 얻습니다.(_둘 다 해야하는지는 테스트 안해봤습니다._)

![alt recaptcha get](/images/nuxt/2019-06-17_16.37.07.png)

# 넉스트 모듈 설치하기

넉스트를 사용할 때 뭔가를 설치하게될 경우 @nuxt 로 시작되는 모듈을 먼저 찾아보고 없으면 플러그인 형태로 하면 됩니다.

다행히 리캡챠 모듈이 있었습니다.

참고: [https://github.com/nuxt-community/recaptcha-module](https://github.com/nuxt-community/recaptcha-module)

```bash
$ yarn add @nuxtjs/recaptcha
```

**nuxt.config.js**  
```javascript
head: {
  title: pkg.name,
  meta: [
    // 어쩌구 저쩌구
  ],
  script: [
    {
      src: 'https://www.google.com/recaptcha/api.js?render=클라이언트_키' // 왜 추가해야할까..
    }
  ],
  link: [
    { rel: 'icon', type: 'image/x-icon', href: '/favicon.ico' }
    // 어쩌구 저쩌구
  ]
},
modules: [
  //
  '@nuxtjs/recaptcha'
],
recaptcha: {
  hideBadge: true,
  siteKey: '클라이언트_키!',
  version: 3
},
```

기본적으로 모듈스에 추가하고 탑레벨 옵션?으로 위와 같이 주면 됩니다.

설정 중 제일 중요했던 것은 위의 head.script.src 부분 입니다.

위와 같이 직접 넣어주지 않으면 동작하지 않습니다.

문제는 모듈 가이드의 어느 부분에도 설명이 없습니다.

그냥 하도 안되서 느낌적인 느낌으로 넣어서 테스트 되길래 한 행위입니다.

여기까지만 하고 리캡챠가 페이지에서 돌아가는지 확인합니다.

> 아마도 버그 같은데.. 역시 진행할 수록 넉스트 사용자 수가 적은 생태계의 문제가 슬슬 나오는 것 같습니다.

# 페이지 작성

```vue
<script>
  async mounted() {
    await this.$recaptcha.init()
  },
  methods: {
    async signIn() {
      try {
        const token = await this.$recaptcha.execute('login')
        console.log('ReCaptcha token:', token)
        if (!token) return false
        // const r = await this.$auth().signInWithEmailAndPassword(
        //   this.form.email,
        //   this.form.password
        // )
        const r = await this.$axios.post(
          'https://us-central1-xxx.cloudfunctions.net/auth/signin',
          {
            email: this.form.email,
            password: this.form.password,
            token: token
          }
        )
        this.$router.push('/')
        this.$store.commit('setUser', { displayName: 'test' })
        console.log(r)
      } catch (e) {
        console.error(e.message)
      }
    }
  }
</script>
```

시작시 리캡챠를 초기화 해줍니다.

그리고 구글에 직접 로그인 하지 않고 파이어베이스 펑션스에 토큰과 함께 전송합니다.

![alt localhost recaptcha](/images/2019-06-17_16.54.45.png)

> 백엔드 작성 전에 우선 구동해서 위와 같이 리캡챠가 둥둥 떠있는지 확인할 필요가 있습니다.

# 백엔드

프론트에서 받은 내용을 구글 서버로 전달하기 위해 axios를 설치해봅니다.

**axios 설치**  
```bash
$ cd functions && yarn add axios
```

> 구글 공식홈 예제에는 request 모듈을 사용했습니다.
> 아무거나 써도 무관합니다~

**functions/auth/index.js**  
```javascript
const express = require('express')
const axios = require('axios')
const cors = require('cors')

const app = express()

app.use(cors({ origin: true }))
app.post('/signin', (req, res) => {
  const body = req.body
  if (!body.email || !body.password || !body.token) return res.status(400).end()
  const url = 'https://www.google.com/recaptcha/api/siteverify'
  const opt = {
    secret: '서버키',
    response: body.token,
    remoteip: req.ip
  }
  axios
    .post(url, JSON.stringify(opt))
    .then(r => {
      res.send({ success: true })
    })
    .catch(e => {
      // req.status(401).end()
      res.send(e.message)
    })
})

module.exports = app
```

이제 백엔드에서 구글서버에 접속해서 프론트에서 받은 토큰을 검증 받습니다.

**이때 중요한 것은 액시오스의 경우, 데이터를 문자화(JSON.stringify) 시켜서 보내야합니다.**

아니면 액시오스 설정을 뭔가 바꾸면 될 것 같습니다...

> 이래서 request 모듈 쓰는지도..

# 테스트

로컬에서는 테스트가 안되기 때문에 배포하고 확인해야합니다.

```bash
$ firebase deploy
```

잘 동작하고 있음을 확인할 수 있습니다.

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

```
# 해당 코드 확인하기
$ git checkout tags/v0.0.15
```

{% endraw %}

# 영상

{% include video id="9hZTGEHXHis" provider="youtube" %}
