---
layout: single
title: NEMBV 19 jwt를 이용한 인증
category: nembv
tag: [nembv,api,mongoose,vue,bootstrap,bootstrap-vue,jwt]
comments: true
---

이제 임시 토큰이 아닌 실제 토큰을 사용한 인증 구현을 해보겠다.

{% include toc %}

# 준비 과정

## jwt 설치

back-end(이하 be)에 jwt를 설치한다.

```bash
$ npm i jsonwebtoken --save
```

## cfg.js 에 secret-key 등록

```javascript
module.exports = {
  web: {
    host: 'xxx.com',
    secret_key: 'veryhardultrakey',
  },
};    
```

- 발급자로 사용될 host를 넣어준다.
- 패스워드를 평문으로 저장하지 않고 시크릿키로 만든 해쉬값으로 저장하기 위함
- jwt 발급할 때도 필요 
- 당연히 어렵게 만들어야 한다.

## app.js 에 key set

```javascript
app.set('jwt-secret', cfg.web.secret_key);  // add
app.use('/api', require('./routes/api'));
```

api route 위에 키셋을 해준다.

# 토큰 발급

로그인 성공시 토큰을 발급해준다.

**fe/src/components/auth/sign/ctrls.js**  
```javascript
User.findOne()
    .where('id').equals(id)
    .then((r) => {
      if (!r) throw new Error('id not exists');
      if (pwd !== r.pwd) throw new Error('password diff');

      const secret = req.app.get('jwt-secret');
      const p = new Promise((resolve, reject) => {
        jwt.sign(
          {
            _id: r._id,
            id: r.id,
            email: r.email
          },
          secret,
          {
            expiresIn: '2m',
            issuer: cfg.web.host,
            subject: 'user-token'
          }, (err, token) => {
            if (err) reject(err);
            resolve(token);
          })
      })
      return p;
    })
    .then((tk) => {
      res.send({ success: true, token: tk });
    })
```

- set 해두었던 jwt-secret을 이용해 토큰을 만든다.
- 시험용으로 만기시간(expiresIn)을 2분으로 낮게 잡았다.
- 유효기간 2분짜리 토큰을 전송한다.

# 발급된 토큰 저장 및 페이지 이동

토큰을 받고 어딘가에 저장해두고 매번 요청할 때 마다 헤더에 넣어 보낸다.

vue-cookie 를 이용 해서 저장한다.

## vue-cookie 설치

```bash
$ cd fe
$ npm i vue-cookie --save
```

> 저장할 수 있는 아무 위치나 상관 없다. 호환성 이슈가 그나마 덜한 쿠키로 선택했을 뿐..

## 전역 axios 응답 가로채서 저장 및 페이지 이동

로그인 페이지에서 쿠키를 저장하고 페이지 이동할 수도 있지만.. 다른 페이지도 감안해서 전역으로 처리하였다.

**fe/src/main.js**  
```javascript
import VueCookie from 'vue-cookie';

const token = VueCookie.get('token');
if (token) axios.defaults.headers.common.Authorization = VueCookie.get('token');
axios.interceptors.response.use((res) => {
  if (res.data.token) {
    VueCookie.set('token', res.data.token, { expires: '2m' });
    axios.defaults.headers.common.Authorization = VueCookie.get('token');
  }
  return Promise.resolve(res);
}, (err) => {
  if (err.response.status === 401) {
    location.href = '/#/sign';
    return;
  }
  return Promise.reject(err);
});
```

- axios의 모든 응답은 이곳을 거친다.
- cookie를 가져와서 있다면 공용헤더 인증에 넣어준다.
- sign.vue에서 요청을 할 경우 응답에 토큰이 있으니, cookie에 저장하고 공용헤더 인증에 넣어준다.
- 쿠키 만료시간도 역시 2분으로 주었다. 1년으로 준다 하더라도 api에서 리젝당하니 별 의미는 없다.
- 다른 페이지 요청시 토큰이 없을 경우 그대로 res를 넘겨준다.
- 다른 페이지 요청시 응답에러에 401이 있을 경우 로그인 페이지로 이동 시킨다.

## 로그인 페이지 응답 완료 후 

```javascript
signin(evt) {
  evt.preventDefault();
  this.$axios.post(`${this.$cfg.path.api}${this.path}`, this.form)
    .then((res) => {
      if (!res.data.success) throw new Error(res.data.msg);
      return this.swalSuccess(`token: ${res.data.token}`);
    })
    .then(() => {
      location.href = '/#/';
    })
    .catch((err) => {
      this.swalError(err.message);
    });
},
```

토큰데이터 창을 한번 보여주고 메인페이지로 이동 시킨다.

# 토큰 확인

## 미들웨어 작성

모든 /api/ 요청에 대해 토큰을 검사하는 루틴을 추가한다.

**routes/api/index.js**  
```javascript
const check = require('./check');
router.all('*', check.verify);
```

- 기존 작성해두었던 middleware 부분을 따로 check.js에 따로 정리했다.
- verify는 헤더의 토큰 검사용이며, 다른 미들웨어를 추가 할 수도 있다.

**routes/api/check.js**  
```javascript
const jwt = require('jsonwebtoken');

exports.verify = (req, res, next) => {
  // console.log(req.headers.authorization);
  // console.log(req.path);
  if (req.path === '/auth/sign/in') return next();
  if (req.path === '/auth/register') return next();
  if (!req.headers.authorization) return res.status(401).send({ success: false, msg: 'authorization empty' });

  const token = req.headers.authorization;

  jwt.verify(token, req.app.get('jwt-secret'), (err, d) => {
    if (err) return res.status(401).send({ success: false, msg: 'your token expired' });
    console.log(new Date(d.exp*1000).toLocaleString());
    req.token = d;
    console.log(req.token);
    next();
  });
};
```

- 로그인 페이지나 회원가입 페이지에서는 토큰이 없으므로 다음 라우터로 간다.
- 토큰이 비었으면 401에러를 보내준다.
- 시크릿키로 토큰을 디코드한다.
- 만료되었거나 잘못된 문자열이면 에러에 걸린다.
- req.token에 변환된 값을 저장해둔다. *다음 라우트에서 req.token의 값으로 커스텀한 응답을 줄수 있다. eg) req.token.lang === 'ko' 면 한글로*
- 변환된 값: 위에 console.log 된 부분  
```bash
{ _id: '5abb2c234bed101776c74eb6',
  id: 'aaaa',
  email: 'a@a.aaa',
  iat: 1522238762, // 발급한 시간
  exp: 1522238882, // 만료 시간
  iss: 'localhost:3000', // 발급한 서버
  sub: 'user-token' }
exp 변환값 : 2018-3-28 21:08:02
```  
- iat, exp는 unixtimestamp로 4바이트 정수이므로 시간을 찍어 볼 수 있었다.  
   
# 테스트

## 토큰이 없을 경우 로그인 페이지 강제 이동
![sign](/images/nembv/2018-03-28 21-40-49 sign.vue.png)

## 로그인 성공후 개발용 토큰 확인 창
![sign dev](/images/nembv/2018-03-28 21-41-22 sign token.png)

## 확인창을 누른후 루트로 이동
![intro](/images/nembv/2018-03-28 21-41-48 intro.png)

> 인트로 페이지는 api를 사용하지 않기 때문에 원래 로그인 안해도 들어와진다..

## api를 사용하는 아무페이지나 갔을 때 토큰이 확인되어 제대로 동작
![api use](/images/nembv/2018-03-28 21-42-16 token ok.png)

## 토큰 만료시간인 2분이 지나고 api사용하는 아무페이지 누를 시 다시 로그인 페이지로 이동
![api not use](/images/nembv/2018-03-28 21-40-49 sign.vue.png)

## 전체 소스

[https://github.com/fkkmemi/nembv](https://github.com/fkkmemi/nembv)

## 실제 테스트

만들어둔 id: aaaa, password: aaaa 로 아래 링크에서 시험해볼 수 있다.

[http://fkkmemi.com:3000](http://fkkmemi.com:3000)

# 결론

구현은 되었는데 생각난 데로 코딩 하면서 작성하다보니 구멍이 많아 보인다.

## 해야할 일들

- sign/out
- 잘못된 에러메세지 처리
- 패스워드 암호화  
*현재 생텍스트*
- authorization 에 bearer 추가  
*토큰 앞에 보통 붙히는데 스터디해봐야 안다. 현재 모름*
- vue route 전단에서 요청 보내서 맞는 페이지 라우팅하기  
*현재 api를 안쓰는 페이지(eg: /#/test/hello등)가 그대로 노출되는데 어떤 방식이 맞는지는 사용해봐야 알 것 같다*
- 토큰 재발행  
*요청시 적당한 시간을 봐서 다시 토큰을 줘야할텐데.. 정상적인 데이터와 함께 발행하는 방법을 생각중이다.*
- 현재 접속자 확인 디비  
*요청시 세션처럼 접속자 수와 상호 연계가 필요할 것 같다*
 