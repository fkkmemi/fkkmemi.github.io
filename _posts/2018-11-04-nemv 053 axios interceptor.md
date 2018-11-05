---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 53 새로고침 없이 로그인 하기(by 인터셉터)
category: nemv
tag: [nemv,vue,axios]
comments: true
sidebar:
  nav: "nemv4"
---

매번 요청시 코드 신경쓰지 않으면서 토큰을 매번 날리도록 만들어보겠습니다. 

매번 액시오스로 api 요청을 할 때마다 헤더에 토큰을 넣어야 작동하는 부분을 사전 정의해서 생략해봅니다. 

{% include toc %}

{% raw %}

# 인터셉터란?

뜻 그대로 가로채는 놈입니다.

보내기/받기 전에 뭔가 해줄 수 있는 것입니다.

액시오스 인터셉터를 전역설정을 해두고 사용하면 됩니다.

참고: [https://www.npmjs.com/package/axios](https://www.npmjs.com/package/axios)

```javascript
// Add a request interceptor
axios.interceptors.request.use(function (config) {
    // Do something before request is sent
    return config;
  }, function (error) {
    // Do something with request error
    return Promise.reject(error);
  });
 
// Add a response interceptor
axios.interceptors.response.use(function (response) {
    // Do something with response data
    return response;
  }, function (error) {
    // Do something with response error
    return Promise.reject(error);
  });
```

현재 코드는 아무 일도 일어나지 않습니다. 주석 부분의 do somthing에 뭔가를 하라는 거죠...

# 코드 추가

**fe/src/router.js**  
```javascript
Vue.prototype.$apiRootPath = apiRootPath
axios.defaults.baseURL = apiRootPath
axios.defaults.headers.common['Authorization'] = localStorage.getItem('token')
// axios.defaults.headers.common['Authorization'] = localStorage.getItem('token')
 // Add a request interceptor
axios.interceptors.request.use(function (config) {
  // Do something before request is sent
  config.headers.Authorization = localStorage.getItem('token')
  return config
}, function (error) {
  // Do something with request error
  return Promise.reject(error)
})
 // Add a response interceptor
axios.interceptors.response.use(function (response) {
  // Do something with response data
  return response
}, function (error) {
  // Do something with response error
  return Promise.reject(error)
})
```

코드를 그대로 넣고 config.headers.Authorization 부분만 넣어주면 매번 보내기전에 거쳐서 토큰을 읽어서 헤더에 넣어놓는 것이죠..

# 추가적인 방법들

- 받는 부분에서도 토큰이 있을 경우 저장해 놓는 로직을 넣으면 좋습니다.  
그 부분은 나중에 토큰 재발급 로직 때 다시 다루도록 하겠습니다.

- 액시오스 설정중 인스턴스를 만들어 놓고 하는 방법도 있습니다.

```javascript
var instance = axios.create({
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  headers: {'X-Custom-Header': 'foobar'}
  interceptors //
});
```

인스턴스에 이것저것 등록해두고 사용하면 코드가 더 간결해지겠죠?

하지만 지금은 조금 복잡해 보이니 나중에 다루겠습니다.

{% endraw %}

# 영상

{% include video id="DnHnZ7xVUPU" provider="youtube" %}   



