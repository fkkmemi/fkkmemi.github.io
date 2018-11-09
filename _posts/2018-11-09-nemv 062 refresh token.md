---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 62 로그인 시간 제한하기(토큰 갱신)
category: nemv
tag: [nemv,token,api,vue]
comments: true
sidebar:
  nav: "nemv5"
---

토큰 생성시 만료시간 없이 만들 었기 때문에 브라우저 로컬스토리지에 토큰이 남아있다면 100년이 지나도 로그인이 가능합니다.

믿을 수 있는 본인 피씨면 상관 없지만 다른 피씨에서 접속하고 로그아웃이라도 까먹으면 큰일입니다.

이제 로그인 저장에 대한 판단을 사용자가 할 수 있 만들어보도록 하겠습니다. 

{% include toc %}

{% raw %}

# 시나리오

리프레시 토큰 관련 문서는 찾아보면 많지만..

물리적인 개념을 토대로 제 생각대로 시나리오를 만들어 봤습니다.

토큰 만료시간을 두면 재발급이 관건인데 단순하게 한번 생각하고 진행해봅니다.

- 로그인창에 기억하기 체크박스를 만든다.
- 기억하기 체크박스를 켜고 로그인 하면 서버는 7일 아닐 경우 5분 기한의 토큰을 보낸다.
- 서버는 토큰을 받을 때 유효시간을 판단해서 2분 이하로 남았으면 7일 혹은 5분 기한의 토큰을 보낸다.
- 클라이언트는 최종 2분안에 클릭만 한다면 갱신토큰을 받아서 항상 로그인 상태가 유지된다.
- 5분동안 아무 입력이 없다면 토큰 기간이 만료되어 로그인 페이지로 안내한다.

# 로그인창에 기억하기 만들기

**fe/src/views/sign.vue**  
```vue
<v-form>
  <v-text-field prepend-icon="person" v-model="form.id" label="아이디" type="text"></v-text-field>
  <v-text-field prepend-icon="lock" v-model="form.pwd" label="비밀번호" type="password"></v-text-field>
  <v-checkbox
    v-model="form.remember"
    label="암호 기억하기(최대 7일간 보관 됩니다.)"
  ></v-checkbox>
</v-form>
<script>
export default {
  data () {
    return {
      form: {
        id: '',
        pwd: '',
        remember: false
      }
    }
  },
</script>
```

이제 form.remember가 같이 날라갑니다.

# 설정 파일 변경하기

**cfg/index.js**  
```javascript
module.exports = {
  dbUrl: 'mongodb://localhost:27017/nemv',
  admin: {
    id: 'test',
    pwd: '1234',
    name: '관리자'
  },
  jwt: {
    secretKey: '아주 어려운 토큰 발급용 키',
    issuer: 'xxx.com',
    algorithm: 'HS256',    
    expiresIn: 60 * 3, // 기본 3분
    expiresInRemember: 60 * 60 * 24 * 6 // 기억하기 눌렀을 때 6일
    expiresInDiv: 3, // 토큰시간 나누는 기준
  }
}
```

토큰 만들 때 필요한 정보를 추가합니다.

- jwt.secretKey: secretKey -> jwt.secretKey
- jwt.issuer: 만든 사람, 서버
- jwt.subject: 토큰 이름
- jwt.algorithm: 암호화 10개중 고르면 됨

| alg Parameter Value | Digital Signature or MAC Algorithm |
| --- | --- |
|HS256 | HMAC using SHA-256 hash algorithm |
|HS384 |	HMAC using SHA-384 hash algorithm|
|HS512 |	HMAC using SHA-512 hash algorithm|
|RS256 |	RSASSA using SHA-256 hash algorithm|
|RS384 |	RSASSA using SHA-384 hash algorithm|
|RS512 |	RSASSA using SHA-512 hash algorithm|
|ES256 |	ECDSA using P-256 curve and SHA-256 hash algorithm|
|ES384 |	ECDSA using P-384 curve and SHA-384 hash algorithm|
|ES512 |	ECDSA using P-521 curve and SHA-512 hash algorithm|
|none |	No digital signature or MAC value included|

참고: [https://www.npmjs.com/package/jsonwebtoken](https://www.npmjs.com/package/jsonwebtoken)

토큰을 만들때 다른 정보를 추가했으니 해독이 더 어려워 지겠죠?

- jwt.expiresIn: 기억하기 안 누른 토큰 시간(3분)
- jwt.expiresInRemember: 기억하기 누른 토큰 시간(6일)
- jwt.expiresInDiv: 토큰 시간 나눈 시간(1분 혹은 2일), 토큰 재발급 할때 쓰일 시간입니다.

복잡하게 만든 이유는 이소스를 가지고 다양한 토큰이 나오게 하기 위함이죠..

보안이 걸려있는 문제니 잘 만들도록 해야겠죠?

# 토큰 기한 두고 만들기

**be/api/routes/sign/index.js**  
```javascript
const signToken = (id, lv, name, rmb) => {
  return new Promise((resolve, reject) => {
    const o = {
      issuer: cfg.jwt.issuer,
      subject: cfg.jwt.subject,
      expiresIn: cfg.jwt.expiresIn, // 3분
      algorithm: cfg.jwt.algorithm
    }
    if (rmb) o.expiresIn = cfg.jwt.expiresInRemember // 6일
    jwt.sign({ id, lv, name, rmb }, cfg.jwt.secretKey, o, (err, token) => {
      if (err) reject(err)
      resolve(token)
    })
  })
}

router.post('/in', (req, res) => {
  const { id, pwd, remember } = req.body
  if (!id) return res.send({ success: false, msg: '아이디가 없습니다.'})
  if (!pwd) return res.send({ success: false, msg: '비밀번호가 없습니다.'})
  if (remember === undefined) return res.send({ success: false, msg: '기억하기가 없습니다.'})
  User.findOne({ id })
      .then((r) => {
        if (!r) throw new Error('존재하지 않는 아이디입니다.')
        const p = crypto.scryptSync(pwd, r._id.toString(), 64, { N: 1024 }).toString('hex')
        if (r.pwd !== p) throw new Error('비밀번호가 틀립니다.')
        return signToken(r.id, r.lv, r.name, remember)
      })
      .then((r) => {
        res.send({ success: true, token: r })
      })
      .catch((e) => {
        res.send({ success: false, msg: e.message })
      })
  })
```

설정파일에 적혀있는데로 토큰을 만들었습니다.

기억하기를 눌렀으면 7일짜리 안눌렀으면 3분짜리 유효기간을 가지고 있습니다.

괜히 cfg.어쩌고로 복잡해 보이지만 옵션만 넣어준 것입니다.

# 토큰이 확인하기

이제 로그인을 해서 토큰이 잘 만들어져있는지 확인해봐야겠죠?

미들웨어 부분에서 로그인 해서 확인 해봅니다.

**be/routes/api/index.js**  
```javascript
const verifyToken = (t) => {
  return new Promise((resolve, reject) => {
    if (!t) resolve({ id: 'guest', name: '손님', lv: 3 })
    if ((typeof t) !== 'string') reject(new Error('문자가 아닌 토큰 입니다.'))
    if (t.length < 10) resolve({ id: 'guest', name: '손님', lv: 3 })
    jwt.verify(t, cfg.jwt.secretKey, (err, v) => {
      if (err) reject(err)
      resolve(v)
    })
  })
}
router.all('*', function(req, res, next) {
  // 토큰 검사
  const token = req.headers.authorization
  verifyToken(token)
    .then(v => {
      console.log(v)
      console.log(new Date(v.exp * 1000))
      req.user = v
      next()
    })
    .catch(e => res.send({ success: false, msg: e.message }))
})
```

cfg.secretKey 에서 cfg.jwt.secretKey 으로 맞게 변경하고 결과를 찍어봅니다.

```javascript
{ id: 'memi',
  lv: 0,
  name: '관리자',
  rmb: false,
  iat: 1541758540,
  exp: 1541758720,
  iss: 'xxx.com',
  sub: 'user-token' }
2018-11-09T10:18:40.000Z
```

토큰이 잘 만들어 져있습니다.

발행시간(iat = 1541757507)과 만료시간(exp: 1541757687)의 차이는 딱 180 납니다.

180초에는 못 쓰는 토큰인지 180초 후 아무데나 클릭해보면 됩니다.

180초 후에는 jwt expired 가 뜨게 됩니다.

exp: 1541758720 는 시간인데 답답해 보이죠?

바로 시간을 초로 누적한 정수 입니다. new Date()는 미리세컨드 기준이기 때문에 1000을 곱해서 표현해 본 것입니다.

# 재발급 로직 생각해보기

우리는 180초 후에 토큰이 만료되는 것을 원하는 것이 아니죠.

쓸 때는 계속 180초가 유지되다가 사용 안하고 3분 있을 때 다시 로그인 하라고 해야되는 것이죠.

재발급을 가장 쉽게 하는 법은 매번 토큰을 재발급해주면 끝입니다.

그러나 나름 토큰도 긴데이터인데.. 용량 낭비죠?

그래서 3~1분 까지 클릭하면서 사용하다가 마지막 1분 남았을 때만 토큰을 재발급해줘서 다시 3분으로 만드는 것이죠.

# 재발급 로직 만들기

## 시간 차이 구하기

**be/routes/api/index.js**  
```javascript
  verifyToken(token)
    .then(v => {
      console.log(v)
      console.log(new Date(v.exp * 1000))
      const diff = moment(v.exp * 1000).diff(moment(), 'seconds')
      console.log(diff)
      req.user = v
      next()
    })
```

지난번 강좌에서 배웠던 모먼트를 사용하여 시간 차를 구했습니다.

로그인하고 여기저기 다녀보면 179 170 160 이렇게 줄어드는 시간을 확인 할 수 있습니다.

이러다 60 이하 였을때 다시 180이 되게 만들어야겠죠?

## 토큰 검사 로직 수정

**be/routes/api/index.js**  
```javascript
const signToken = (id, lv, name, rmb) => {
  // ..
}

const getToken = async(t) => {
  let vt = await verifyToken(t)
  if (vt.lv > 2) return { user: vt, token: null }
  const diff = moment(vt.exp * 1000).diff(moment(), 'seconds')
  // return vt
  console.log(diff)
  if (diff > (vt.exp - vt.iat) / cfg.jwt.expiresInDiv) return { user: vt, token: null }

  const nt = await signToken(vt.id, vt.lv, vt.name, vt.rmb)
  vt = await verifyToken(nt)
  return { user: vt, token: nt }
}
router.all('*', function(req, res, next) {
  // 토큰 검사
  getToken(req.headers.authorization)
    .then((v) => {
      console.log(v)
      req.user = v.user
      req.token = v.token
      next()
    })
    .catch(e => res.send({ success: false, msg: e.message }))
})
```

- getToken이라는 어씽크 함수를 만들었습니다.
- 토큰 풀기를 해서 정보를 가져오고 기다립니다.
- 토큰이 손님 권한이면(vt.exp > 2) 그냥 나갑니다.
- 시간차를 구합니다.
- 60초보다 클 경우 사용자 정보와 토큰이 없다는 결과를 주며 나옵니다.
- 그렇지 않을 경우 기존 정보로 다시 토큰을 만듭니다.
- 갱신된 사용자 정보와 토큰을 리턴합니다.

## 모든 API 요청에 갱신토큰 전송 (노가다 작업)

req.token을 모든 api에 success와 함께 친구로 보내줘야합니다.

프론트에서는 널인지 있는지 파악해서 있을때 로컬스토리지를 갱신해야합니다.

응답시에는 refreshToken 이라는 명칭으로 많이들 하지만. 전 그냥 심플하게 token 이라고 하겠습니다.

**api 토큰 추가 예**  
```javascript
res.send({ success: true, sites: r, token: req.token })
```

인증이 필요한 api 모두 위와 같은 형식으로 재발급 토큰을 넣어줘야합니다..

> 함수를 하나 만들어서 할수도 있지만.. 더 복잡해보여서 ctrl+f로 success: true 찾아서 일괄 추가 했습니다.

## 프론트에 토큰 항상 저장하기

이제 정상적인 요청에는 항상 토큰 응답이 오게 됩니다.

토큰이 있을때 저장만하고 원래 응답 시나리오대로 하면 되는 것이죠.

**fe/src/router.js**  
```javascript
axios.interceptors.response.use(function (response) {
  // Do something with response data
  const token = response.data.token
  console.log(token)
  if (token) localStorage.setItem('token', token)
  return response
}, function (error) {
  // Do something with response error
  return Promise.reject(error)
})
```

토큰이 있을 때 응답부에서 가로 채기로 저장하고 원래 진행하던 곳으로 갑니다.

토큰이 바뀌었다면 요청부에서 가로채기로 변경된 토큰을 전달하겠죠?

# 테스트 해보기

로그인 후 계속 여기저기 들락날락 거리면 됩니다.

```javascript
55 // 55초 남음
{ user:
   { id: 'memi',
     lv: 0,
     name: '관리자',
     iat: 1541763174,
     exp: 1541763354,
     iss: 'xxx.com',
     sub: 'user-token' },

  token:
   'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpZCI6Im1lbWkiLCJsdiI6MCwibmFtZSI6
Iuq0gOumrOyekCIsImlhdCI6MTU0MTc2MzE3NCwiZXhwIjoxNTQxNzYzMzU0LCJpc3MiOiJ4eHguY
29tIiwic3ViIjoidXNlci10b2tlbiJ9.uKt531jEkKBYk7tZJBswjRJaEbosPFLyGi5PsRI4SY4'
}
POST /api/page 200 524.858 ms - 365
OPTIONS /api/manage/user 204 0.083 ms - 0
179 // 179초 남음
{ user:
   { id: 'memi',
     lv: 0,
     name: '관리자',
     iat: 1541763174,
     exp: 1541763354,
     iss: 'xxx.com',
     sub: 'user-token' },
  token: null }
```

계속 클릭하다가 55초에 클릭하니 토큰을 재발급 받은 상태로 179초 초기화 되어 있는 것을 확인 할 수 있습니다.

```javascript
604791
{ user:

   { id: 'memi',
     lv: 0,
     name: '관리자',
     iat: 1541763462,
     exp: 1542368262,
     iss: 'xxx.com',
     sub: 'user-token' },
  token: null }
POST /api/page 200 1050.317 ms - 142
OPTIONS /api/manage/page 204 0.084 ms - 0
604789
{ user:
   { id: 'memi',
     lv: 0,
     name: '관리자',
     iat: 1541763462,
     exp: 1542368262,
     iss: 'xxx.com',
     sub: 'user-token' },
  token: null }
GET /api/manage/page 200 243.887 ms - 634
```

604789초가 남아서 암호 기억하기로는 테스트가 어렵네요~

604789/3 초 남았을 때 재발급되는 건 테스트 안해봐도 잘 되겠죠?

# 마치며

사실 리프레시토큰의 좀더 잘된 개념은 따로 있습니다.

패스포트라는 로그인 전용 모듈도 있죠..

하지만 결국 물리적으로는 이 이상은 크게 없습니다.

여기까지 구현하시면 추후 OAuth등 다른 기법을 도입할 때 도움이 될 것입니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/a8d8135282769ac7bd21e949129d0be638b28646)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}
