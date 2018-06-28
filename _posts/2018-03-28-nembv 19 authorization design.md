---
layout: single
title: NEMBV 19 인증 시스템 준비
category: nembv
tag: [nembv,api,mongoose,vue,bootstrap,bootstrap-vue]
comments: true
sidebar:
  nav: "nembv"
---

jwt(Json Web Token)을 이용해 사용자를 구분할 수 있는 인증 시스템을 만들어 보겠다.

그러기 위해서 최소한의 골격을 만들어 보겠다.

{% include toc %}

# 개요

인증을 위한 방법은 다양하다.

정석이 어떤 것이지는 모르겠지만 내 방식대로 한번 구현해보겠다. 

# 계획

- 모든 /api/ 이하 모든 요청에는 헤더에 authorization를 추가하여 받을 것이다.
- 미들웨어에서 헤더를 판단해서 토큰 상태 검증으로 인증 유무를 판단.
- 유효 토큰 상태로 프론트 페이지가 로그인 페이지 혹은 인증 후 페이지로 리다이렉트 될것이다.

# 구성

## 헤더 테스트 해보기

기본적으로 http header에 인증 정보가 와야 되는데 테스트를 위해 미들웨어에 헤더를 한번 찍어보자.

**route/api/index.js**  
```javascript
router.all('*', (req, res, next) => {
  console.log(req.headers);
  next();
})
```

**fe/src/main.js**  
```javascript
axios.defaults.headers.common.Authorization = 'abcd';
Vue.prototype.$axios = axios;
```

- api 최상단 위치에 모든 요청을 받아서 헤더만 로그로 찍고 next()로 넘긴다.
- fe 최상단에 전역으로 모든 요청에 Authorization에 값을 넣어서 보낸다.

**결과**  
```bash
GET /api/data/board/talk/5abae47d90893c0fc5f25655 200 427.888 ms - 826
{ host: 'localhost:3000',
  connection: 'keep-alive',
  'content-length': '45',
  accept: 'application/json, text/plain, */*',
  origin: 'http://localhost:8080',
  authorization: 'abcd',
  'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36',
  'content-type': 'application/json;charset=UTF-8',
  referer: 'http://localhost:8080/?',
  'accept-encoding': 'gzip, deflate, br',
  'accept-language': 'ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7' }
POST /api/data/board/talk 200 268.478 ms - 16
{ host: 'localhost:3000',
  connection: 'keep-alive',
  accept: 'application/json, text/plain, */*',
  origin: 'http://localhost:8080',
  authorization: 'abcd',
  'user-agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_13_3) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36',
  referer: 'http://localhost:8080/?',
  'accept-encoding': 'gzip, deflate, br',
  'accept-language': 'ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7',
  'if-none-match': 'W/"24d-4IHvpbj74SqvVIWwBxich3W67pA"' }
GET /api/data/board/talk?draw=2&search=&skip=0&limit=5&order=ut&sort=1
```

- 저번에 만들었던 게시판에서 게시물을 읽을 때도 쓸 때도 authorization: 'abcd' 임을 알 수 있다.
- axios global이 생각대로 동작한 것이고 api/ 이하 요청이 다 콘솔에 찍힌 것을 검증할 수 있었다.

## user model 제작

```javascript
const mongoose = require('mongoose');

const userSchema = new mongoose.Schema({
    id: { type: String, index: true, unique: true },
    pwd: { type: String, required: true },
    email: { type: String, required: true, unique: true, lowercase: true },
    lv: { type: Number, default: 5, max: 10 },
    rmk: { type: String, default: '' },
    lang: { type: String, default: 'ko' },
    gender: { type: String, default: 'Male' },
    hidden: { type: String },
    // expires: { type: Date },
});

module.exports = mongoose.model('User', userSchema);
```

- id는 unique해야한다.
- lv, rmk, lang, gender, hidden등은 예시로 넣어두었으니 신경쓰지말자.

> 대충 생각나는 대로 만든 것이니 id, pwd, email 제외 나머지는 알아서 만들자.

## 인증 폴더 구조

- api
    - auth
        - register
            - ctrls.js
            - index.js
        - sign
            - ctrls.js
            - index.js
- fe
    - src
        - components
            - page
                - register.vue
                - sign.vue
                
## index 추가

**routes/api/index.js**  
```javascript
const auth = require('./auth')

router.use('/auth', auth);
```

auth 라우터 추가

## auth router

**routes/api/auth/index.js**  
```javascript
const sign = require('./sign');
const register = require('./register');

router.use('/sign', sign);
router.use('/register', register);
```

auth하위 sign, register 라우터 추가
                
## 회원가입 만들기

우선 회원이 있어야 뭘 테스트해볼 수 있으니 가입부터 만들어본다.
        
### register api

**routes/api/auth/register/index.js**  
```javascript
const ctrl = require('./ctrls');

router.post('/', ctrl.add);
```

register에는 post 루트만 넣었다.

**routes/api/auth/register/ctrls.js**  
```javascript
const User = require('../../../../models/users');

exports.add = (req, res) => {
  const { id, email, pwd } = req.body;

  if (id === undefined) return res.send({ success: false, msg: 'param err id' });
  if (email === undefined) return res.send({ success: false, msg: 'param err email' });
  if (pwd === undefined) return res.send({ success: false, msg: 'param err pwd' });

  const u = new User({
    id: id,
    email: email,
    pwd: pwd,
  });

  u.save()
    .then(() => {
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
};
```

### 회원가입 폼 작성

```html
<template>
  <b-container class="p-3">
    <b-card header="회원가입">
      <b-form @submit="register">
        <b-form-group label="아이디"
                      label-for="f-id"
                      description="사이트 이용을 위해 필요합니다.">
          <b-form-input id="f-id"
                        type="text"
                        v-model="form.id"
                        required
                        placeholder="입력...">
          </b-form-input>
        </b-form-group>
        <b-form-group label="이메일"
                      label-for="f-email"
                      description="비밀번호 변경시 필요합니다.">
          <b-form-input id="f-email"
                        type="email"
                        v-model="form.email"
                        required
                        placeholder="입력...">
          </b-form-input>
        </b-form-group>
        <b-form-group label="비밀번호"
                      label-for="f-pwd">
          <b-form-input id="f-pwd"
                        type="password"
                        v-model="form.pwd"
                        required
                        placeholder="비밀번호를 입력하세요">
          </b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">회원가입</b-button>
      </b-form>
    </b-card>
  </b-container>
</template>

<script>
export default {
  name: 'register',
  data() {
    return {
      path: 'auth/register',
      form: {
        id: '',
        email: '',
        pwd: '',
      },
    };
  },
  methods: {
    register(evt) {
      evt.preventDefault();
      this.$axios.post(`${this.$cfg.path.api}${this.path}`, this.form)
        .then((res) => {
          if (!res.data.success) throw new Error(res.data.msg);
          this.swalSuccess('회원가입 성공');
        })
        .catch((err) => {
          this.swalError(err.message);
        });
    },
  },
};
</script>
```

- bootstrap-vue b-form submit을 이용해서 최소한의 패턴(email형식, require 등) 예외처리는 기본적으로 된다.

### 회원가입 테스트

http://localhost:8080/#/register

간단하게 3필드가 디비에 저장된다.

**db 확인**  
![db](/images/nembv/2018-03-28 15-14-14 db.png)

> 유저리스트 페이지를 만들어서 확인해야하지만..(추후) 우선 디비 확인을 해본다.


## 로그인 만들기

### sign api

**routes/api/auth/sign/index.js**  
```javascript
const ctrl = require('./ctrls');

router.post('/in', ctrl.in);
router.post('/out', ctrl.out);
```

sign/in sign/out 두개의 라우터를 만들었다.

**routes/api/auth/sign/ctrls.js**  
```javascript
const User = require('../../../../models/users');

exports.in = (req, res) => {
  const { id, pwd } = req.body;

  if (id === undefined) return res.send({ success: false, msg: 'param err id' });
  if (pwd === undefined) return res.send({ success: false, msg: 'param err pwd' });

  User.findOne()
    .where('id').equals(id)
    .then((r) => {
      if (!r) throw new Error('id not exists');
      if (pwd !== r.pwd) throw new Error('password diff');
      res.send({ success: true, token: 'xxxx' });
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
};

exports.out = (req, res) => {
  res.send({ success: true });
};
```

- 단순 로그인 자체 테스트를 위한 최소한의 설계만 했다.
- 해당 id로 디비에서 검색해서
- 검색 결과가 없는 것과 패스워드가 다른 것을 각각 따로 예외처리
- 예외처리에 걸리지 않으면 임의의 토큰을 발행했다.

### 로그인 폼 만들기

```html
<template>
  <b-container class="p-3">
    <b-card header="로그인">
      <b-form @submit="signin">
        <b-form-group label="아이디"
                      label-for="f-id"
                      description="사이트 이용을 위해 필요합니다.">
          <b-form-input id="f-id"
                        type="text"
                        v-model="form.id"
                        required
                        placeholder="입력...">
          </b-form-input>
        </b-form-group>
        <b-form-group label="비밀번호"
                      label-for="f-pwd">
          <b-form-input id="f-pwd"
                        type="password"
                        v-model="form.pwd"
                        required
                        placeholder="비밀번호를 입력하세요">
          </b-form-input>
        </b-form-group>
        <b-button type="submit" variant="primary">로그인</b-button>
      </b-form>
    </b-card>
  </b-container>
</template>

<script>
export default {
  name: 'sign',
  data() {
    return {
      path: 'auth/sign/in',
      form: {
        id: '',
        pwd: '',
      },
    };
  },
  methods: {
    signin(evt) {
      evt.preventDefault();
      this.$axios.post(`${this.$cfg.path.api}${this.path}`, this.form)
        .then((res) => {
          if (!res.data.success) throw new Error(res.data.msg);
          this.swalSuccess(`token: ${res.data.token}`);
        })
        .catch((err) => {
          this.swalError(err.message);
        });
    },
  },
};
</script>
```

- 간단하게 id, pwd를 전송해서 임시 토큰을 출력해보았다.
