---
layout: single
title: NUXT로 혼자 웹사이트 만들기 12 파이어베이스(firebase) 인증 기능 토큰 알아보기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,authorization,token]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스 인증 기능중 토큰에 대해 알아봅니다.

# 개요

먼저 토큰이란 것이 뭔지 간단히 알고 시작해야합니다.

기존 강좌 참고: [https://fkkmemi.github.io/nemv/nemv-040-hello-token/](https://fkkmemi.github.io/nemv/nemv-040-hello-token/)

인증 기능의 의미는 로그인 단한번이 끝이 아닙니다.

사실 로그인 후가 더 중요한 것이죠.

최근 페이스북이나 구글에 로그인을 몇번이나 하시나요?

예전 처럼 매번 사이트에 접속할 때마다 로그인을 하지는 않을 것입니다.

최근에는 *로그인 저장하기* 같은 체크박스가 보기 힘든데도 불구하고 로그인 없이도 쉽게 웹서핑이 가능합니다.

그 이유는 로컬스토리지나 쿠키등에 사용자 정보, 즉 토큰이 저장되어있기 때문입니다.

저장된 토큰은 무적이 아닙니다.

만료시간도 있고, 문제가 발생할 경우 못 쓰는 토큰(_아래 문서 참고_)으로 만들 수도 있습니다.

참고: [https://firebase.google.com/docs/auth/users?authuser=0](https://firebase.google.com/docs/auth/users?authuser=0)

결국 서명된 토큰을 백엔드에서 신원을 파악하는 것입니다.

# 토큰 확인

로그인이 되고 나면 파이어베이스는 어딘가에(로컬스토리나, 쿠키) 토큰을 저장해둡니다.

**src/pages/auth.vue**  
```vue
async getToken() {
  const tk = await this.$auth().currentUser.getIdToken()
  console.log(tk)
  // eyJhbGciOiJSUzI1NiIsImtpZCI6IjU0
},
```

토큰이 어디있는지 찾을 필요 없이 파이어베이스 인증 메쏘드로 쉽게 가져올 수 있습니다.

토큰을 읽어보면 알수 없는 문자열이 적혀있습니다.

여기서 오해하면 안되는 것은 알수 없다고 해서 암호화 되어 있는 것은 아닙니다.

파이어베이스가 만든 토큰은 jwt(json webtoken)으로 https://jwt.io 에서 간단히 해독(디코드)해볼 수 있습니다.

![alt jwt.io](/images/nuxt/2019-06-10_14.38.38.png)

이제 이 정보를 매번 백엔드 서버 요청시 보내게 됩니다.

백엔드 서버 역시 jwt.io 사이트와 같이 해독이 됩니다.

그러면 조작할 수 있지 않을까?

위의 사이트에서 토큰의 한 글자라도 바꿔보면 유효하지 않아서 해독이 안되는 것을 바로 알 수 있습니다.

# 토큰 전송하기

토큰은 주로 http header의 authorization 에 넣어 보냅니다.(_일반적인 방법일 뿐 절대적이진 않습니다. 바디나 쿼리에 넣어서 보내는 것은 사용자의 마음입니다._)

매번 직접 넣어 보내는 방법도 있지만, 넉스트 액시오스에서는 메쏘드로 만들어 놨습니다.

바로 setToken인데, 결국 원리는 axios common header 조작입니다.

참고: [https://axios.nuxtjs.org/helpers](https://axios.nuxtjs.org/helpers)

**src/pages/auth.vue**  
```vue
async reqNormal() {
  const tk = await this.$auth().currentUser.getIdToken()
  this.$axios.setToken(tk)
  const data = await this.$axios.get(
    'http://localhost:5000/xxx/us-central1/widgets'
  )
  console.log(data)
}
```

이제 요청시 headers.authorization = 토큰 과 전송 됩니다.

# 토큰 받아보기

이제 백엔드 서버인 파이어베이스 펑션스에서 받아보겠습니다.

**functions/index.js**  
```javascript
const verifyToken = async (req, res, next) => {
  console.log(JSON.stringify(req.headers))
  const tk = req.headers.authorization
  const u = await admin.auth().verifyIdToken(tk)
  console.log(u)
  next()
}

app.use(cors({ origin: true }))
app.use(verifyToken)
app.get('/', (req, res) => res.send('abcdefg'))
```

이제 모든 요청은 verifyToken 함수를 거치게 됩니다.

요청 헤더를 출력해보고 파이어베이스의 토큰 확인 기능을 해보면 jwt.io에서 해독한 결과와 일치함을 알 수 있습니다.

참고: [https://firebase.google.com/docs/auth/admin/verify-id-tokens?authuser=0](https://firebase.google.com/docs/auth/admin/verify-id-tokens?authuser=0)

# OAuth 적용

OAuth는 토큰 인증 규격입니다. 다른 곳에서도 토큰을 쓰기 위함입니다.

참고: [https://tools.ietf.org/html/rfc6750](https://tools.ietf.org/html/rfc6750)

방법은 간단합니다. 토큰 앞에 'Bearer ' 만 붙혀주면 됩니다.

**src/pages/auth.vue**  
```vue
async reqNormal() {
  const tk = await this.$auth().currentUser.getIdToken()
  this.$axios.setToken('Bearer ' + tk)
  const data = await this.$axios.get(
    'http://localhost:5000/xxx/us-central1/widgets'
  )
  console.log(data)
}
```

보낼 때 붙히고

**functions/index.js**  
```javascript
const verifyToken = async (req, res, next) => {
  console.log(JSON.stringify(req.headers))
  const tk = req.headers.authorization.split.split(' ')[1]
  const u = await admin.auth().verifyIdToken(tk)
  console.log(u)
  next()
}
```

받을 때 판단 후 빼주면 끝

# 정리

토큰은 암호화가 아니기 때문에 클라이언트에서도 해독이 가능합니다.

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

# 영상

{% include video id="pwrey4nBukc" provider="youtube" %}
