---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 75 HTTP 예외처리 제대로 하기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

공식 HTTP 에러코드에 맞는 결과를 전송해보겠습니다. 

{% include toc %}

{% raw %}

# 개요

지금까지 응답은 대부분 res.send({ success: false, msg: '어쩌고' }) 등으로 임의로 만든 것입니다.

물론 이렇게 써도 문제 되지는 않지만, HTTP 공식 에러코드로 처리해보겠습니다.

공식 에러코드 참고: [https://ko.wikipedia.org/wiki/HTTP_상태_코드](https://ko.wikipedia.org/wiki/HTTP_상태_코드)

# http-errors 모듈

http-errors 모듈은 express-generator로 설치할때 기본적으로 깔려있습니다.

**be/app.js**  
```javascript
var createError = require('http-errors');

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.send({ msg: err.message })
  console.error(err.message)
});
```

이미 없는 경로에 대해 잘 사용하고 있습니다.

참고: [https://www.npmjs.com/package/http-errors](https://www.npmjs.com/package/http-errors)

의도된 상황이 있을 경우 next(createError(401)) 등으로 보내주면 되는 것이죠

# 작전

잘못된 상황은 크게 두가지로 나눌 수 있습니다.

바로 공식 에러코드에 내용과 아닌 것입니다.

- 에러코드가 있을 상황: 로그인 할 때 폼 없이 전송(400 bad request)  
- 일반상황: 아이디가 없거나 비밀번호가 틀린 경우

에러코드는 400~511 까지 여러가지 있지만 그 중 알고 있는 것만 처리해봅니다.

| 에러코드 | 내용 |
| --- | --- |
| 400 | 잘못된 요청 |
| 401 | 인증 오류 |
| 403 | 권한 오류 |
| 404 | 페이지 없음 오류 |

# 백엔드 상태별로 처리하기

## 400(BadRequest)의 경우

**be/routes/api/sign/index.js**  
```javascript
// if (!id) return res.send({ success: false, msg: '아이디가 없습니다.'})
// if (!pwd) return res.send({ success: false, msg: '비밀번호가 없습니다.'})
// if (remember === undefined) return res.send({ success: false, msg: '기억하기가 없습니다.'})
  if (!id) throw createError(400, '아이디가 없습니다') 
  if (!pwd) throw createError(400, '비밀번호가 없습니다')
  if (remember === undefined) throw createError(400, '기억하기가 없습니다.')
  User.findOne({ id })
    .then((r) => {
      // throw createError(400, '몰라몰라1') // 아래 catch로 내려감
      if (!r) throw new Error('존재하지 않는 아이디입니다.')
      const p = crypto.scryptSync(pwd, r._id.toString(), 64, { N: 1024 }).toString('hex')
      if (r.pwd !== p) throw new Error('비밀번호가 틀립니다.')
      return signToken(r._id, r.id, r.lv, r.name, remember)
    })
    .then((r) => {
      res.send({ success: true, token: r })
    })
    .catch((e) => {
      // throw createError(400, '몰라몰라2')
      res.send({ success: false, msg: e.message })
      // next(createError(401, e.massage))
    })
```

잘못된 요청을 createError로 App.js에 있는 에러 핸들러로 전달 해줍니다. (_throw 혹은 return next(createError)로 전달할 수 있습니다._)

주의 할 것은 프라미스 체인 안에서 createError로 에러를 발생시키면 프라미스 체인의 캐치(catch) 로 가게 됩니다.

프라미스 체인 안의 catch는 의도된 에러 이기 때문에 실질적으로는 res.status(200).send... 로 정상으로 나가게 되는 것이죠.

## 401(Unauthorized)의 경우

인증 오류이기 때문에 토큰에서 문제가 있을 경우 처리하면 됩니다.

**be/routes/api/index.js**  
```javascript
router.all('*', function(req, res, next) {
  // 토큰 검사
  getToken(req.headers.authorization)
    .then((v) => {
      console.log(v)
      req.user = v.user
      req.token = v.token
      next()
    })
    // .catch(e => res.send({ success: false, msg: e.message }))
    .catch(e => next(createError(401, e.message)))
})
```

## 403(Forbidden)의 경우

권한 오류이기 때문에 레벨 제한되는 곳에서 발생시키면 됩니다.

**be/routes/api/manage/index.js**  
```javascript
router.all('*', function(req, res, next) {
   // if (req.user.lv) return res.send({ success: false, msg: '권한이 없습니다.' })
   if (req.user.lv) throw createError(403, '권한이 없습니다.')
   next()
 })
```

## 의도치 않은 오류들(500등)의 경우

예상하지 못한 오류가 났을 경우 알아서 app.js의 에러 핸들러로 갑니다.

**be/routes/api/sign/index.js**  
```javascript
router.post('/in', (req, res, next) => {
  fs.readFile('a')
  // ..
```

선언한 적도 없는 fs 모듈을 넣어봤습니다.

app.js의 콘솔로그와 함께 500 status를 보내줍니다.

fs is not defined  
POST /api/sign/in 500 15.221 ms - 27

이제 프론트에서도 서버에 무슨문제가 있는 지 알 수 있습니다.

# 프론트 처리하기

대부분 에러처리를 console.error(error) 로 브라우저 콘솔에 띄어봤자 에러 메세지만 찍어주고 상세 내용을 알기 힘듭니다.

액시오스 에러의 경우 쌩뚱 맞은 곳에 내용이 있습니다.

바로 response에 들어 있는데 모든 응답 에러를 처리하기 위해 수신 가로채기(인터셉터)를 활용합니다.

## 가로채기 처리

**fe/src/router.js**  
```javascript
axios.interceptors.response.use(function (response) {
  const token = response.data.token
  if (token) localStorage.setItem('token', token)
  return response
}, function (error) {
  console.log(error)
  console.log(error.response)
  return Promise.reject(error)
})
```

![alt console.error](/images/nemv/스크린샷 2018-11-19 오전 11.55.37.png)

error 만 찍어봤더니 메세지 뿐이라 자세한 확인이 불가능하지만 error.response에는 데이터들이 있음을 확인할 수 있습니다.

error.response.data.msg 에 app.js에서 보낸 사용자 메세지가 있는 것이죠.

이것을 이용해 상황별 에러 메세지를 표시했습니다.

**fe/src/router.js**  
```javascript
  // ..
  console.log(error.response)
  switch (error.response.status) {
    case 400:
      store.commit('pop', { msg: `잘못된 요청입니다(${error.response.status}:${error.message})`, color: 'error' })
      break
    case 401:
      store.commit('delToken')
      store.commit('pop', { msg: `인증 오류입니다(${error.response.status}:${error.message})`, color: 'error' })
      break
    case 403:
      store.commit('pop', { msg: `이용 권한이 없습니다(${error.response.status}:${error.message})`, color: 'warning' })
      break
    default:
      store.commit('pop', { msg: `알수 없는 오류입니다(${error.response.status}:${error.message})`, color: 'error' })
      break
  }
  return Promise.reject(error)
})
```

![error 500](/images/nemv/스크린샷 2018-11-19 오후 1.03.23.png)

위에 fs로 알수 없는 오류를 억지로 줘서 나온 결과입니다.

인증 오류일 때는 토큰을 삭제해버렸습니다.(_기한 만료되거나 잘못된 토큰이니 401로 왔겠죠?_)

## 페이지 내부 처리

공용 가로채기로 처리했지만. 페이지에서도 에러가 있을 수 있습니다.

**fe/src/views/board/index.js**  
```javascript
getBoard () {
  this.$axios.get(`board/read/${this.$route.params.name}`)
    .then(({ data }) => {
      if (!data.success) throw new Error(data.msg)
      this.board = data.d
      this.list()
    })
    .catch((e) => {
      this.$store.commit('pop', { msg: e.message, color: 'warning' })
    })
},
```

이렇게 하면 throw new Error(data.msg) 메세지가 가로채기의 내용을 덮어버립니다.

왜냐하면 프라미스 내부에서 일어나는 throw new Error()가 캐치로 가서 한번더 실행하면서 가로채기에서 표현했던 메세지를 덮어버리기 때문입니다.

방법은 수신인지 내부 오류인지 구분을 해주면 됩니다.

수신은 error.response 가 있고 내부 오류(throw new Error)를 내면 받은 데이터가 아니기 때문에 error.response 가 undefined입니다.

```javascript
    .catch((e) => {
      if (!e.response) this.$store.commit('pop', { msg: e.message, color: 'warning' })
    })
```

이렇게 처리해주면 응답이 있는 에러처리는 가로채기에서 해결하고 페이지 내부 오류는 페이지에서 해결하게 됩니다.

## 권한 처리 부분

```javascript
const pageCheck = (to, from, next) => {
  axios.post('page', { name: to.path })
    // ..
    .catch((e) => {
      // next(`/block/${e.message.replace(/\//gi, ' ')}`)
      if (!e.response) store.commit('pop', { msg: e.message, color: 'warning' })
      next(false)
    })
}
```

기존에 블럭킹 페이지로 가는 것에서 메세지를 표시해주고 next(false)로 못가게 막았습니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/c8a17867e48b13aeeeea39c767fd311d60046b70)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}

