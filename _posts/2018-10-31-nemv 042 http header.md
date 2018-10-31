---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 42 HTTP 헤더 알아보기
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

토큰을 보낼 때는 HTTP 헤더(header)를 통해 전송하는 것이 일반적입니다.

헤더는 어떻게 전송해야하는 지 알아보겠습니다.

{% include toc %}

# 헤더 확인해보기

헤더에 전송하는 가장 큰 이유는 브라우저에서 접근하지 못하게 하는 것입니다.

**be/api/test/index.js**  
```javascript
router.get('/', function(req, res, next) {
  console.log(req.headers)
  res.send({ msg: 'hello', a: '괜찮아' })
});
```

```javascript
// req.headers
{ host: 'localhost:3000',
  connection: 'keep-alive',
  'upgrade-insecure-requests': '1',
  'user-agent':
   'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36',
  accept:
   'text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8',
  'accept-encoding': 'gzip, deflate, br',
  'accept-language': 'ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7',
  cookie: '_ga=GA1.1.1057823852.1539581341' }
```

서버를 구동 후, 브라우저에서 http://localhost:3000/api/test 를 요청 하면 브라우저에 값이 뜨게 되서 곤란합니다.

/api 는 헤더가 포함된 axios 요청만 응답하게 하는 것이 좋겠죠..

헤더(req.headers)를 찍어보면 어떻게 생겼는 지 확인 가능합니다.

# vue에 페이지 만들기

헤더를 추가해서 보내기 위해 페이지를 추가해봅니다.

**fe/src/views/header.vue**  
```vue
<template>
  <v-container>
    <v-btn @click="headerSend">헤더 전송</v-btn>
  </v-container>
</template>
<script>
import axios from 'axios'

export default {
  methods: {
    headerSend () {
      axios.get(`${this.$apiRootPath}test`)
        .then(r => console.log(r))
        .catch(e => console.log(e))
    }
  }
}
</script>
```

라우터에 등록도 해줍니다.

**fe/src/router.js**  
```javascript
{
  path: '/header',
  name: '헤더',
  component: () => import('./views/header.vue')
},
```

간단하게 버튼 하나 달랑 넣고 아까와 똑같은 겟(GET) 요청을 해봅니다.

브라우저를 열고 http://localhost:8080/header 에서 버튼을 누르면됩니다.

```javascript
{ host: 'localhost:3000',
  connection: 'keep-alive',
  accept: 'application/json, text/plain, */*',
  origin: 'http://localhost:8080',
  'user-agent':
   'Mozilla/5.0 (Macintosh; Intel Mac OS X 10_14_1) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/70.0.3538.77 Safari/537.36',
  referer: 'http://localhost:8080/header',
  'accept-encoding': 'gzip, deflate, br',
  'accept-language': 'ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7',
  'if-none-match': 'W/"1f-/jikrrKGPUXaaMlJAkK9G76sZQw"' }
``` 

브라우저와는 뭔가 다름을 알 수 있습니다.

# 헤더에 임의값 추가해서 보내보기

겟 요청 뒤에 옵션을 넣어주면 됩니다.

```javascript
  axios.get(`${this.$apiRootPath}test`, { headers: { xxx: 1234 } })
    .then(r => console.log(r))
    .catch(e => console.log(e))
```

xxx:1234 라는 키/값을 전송해봤습니다.

```javascript
{ host: 'localhost:3000',
  ...
  xxx: '1234',
  ...
  'if-none-match': 'W/"1f-/jikrrKGPUXaaMlJAkK9G76sZQw"' }
```

xxx가 잘 전송된 것이 확인 됩니다.

# 헤더의 종류

헤더의 종류 참고: [https://en.wikipedia.org/wiki/List_of_HTTP_header_fields](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

당연히 xxx는 비정규(?) 헤더입니다.

제가 맘로 만들었기 때문이죠..

기본 헤더가 아니라 제약이 있을 수도 있습니다.

# 토큰 보내기

토큰을 넣으려면 기본 목록에 있는 Authorization에 넣어서 보내는 것이 좋겠죠?

```javascript
  axios.get(`${this.$apiRootPath}test`, { headers: { Authorization: 'fake token!' } })
    .then(r => console.log(r))
    .catch(e => console.log(e))
```

# 영상

준비중..

{% include video id="KW3sCB60UqI" provider="youtube" %}   




