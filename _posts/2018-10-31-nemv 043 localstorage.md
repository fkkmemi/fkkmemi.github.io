---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 43 사이트 저장소 다루기
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

서버로 부터 토큰을 받아서 저장해놔야 다음에 로그인 없이 api요청이 가능합니다.

{% include toc %}

# 쿠키(Cookie) VS 로컬스토리지(LocalStorage) VS 인덱스드디비(indexedDB)

## 쿠키
 
쿠키는 거의 웹 시작과 동시에 태어난 것이라서 전통 저장소입니다.

그래서 어떤 브라우저라도 쿠키는 정상작동합니다.

하지만 모든 요청시 쿠키값이 서버에 전달됩니다.

쿠키에 토큰을 저장해두고 보낼 경우 헤더와 쿠키가 같이 날라가는 바보 같은 상황이 됩니다.(_header.authorization과 cookie.._)

## 인덱스드디비

sql 처럼 사용할 수 있는 저장소 입니다.

부드럽고 고급스럽게 웹을 처리하려면 인덱스드디비를 로컬 캐쉬로 사용하여 화면 렌더링하고 변경값 관련만 서버와 패치해서 쓰는 것이 용도로 적합해 보입니다.

## 로컬스토리지

로컬스토리지도 인터넷 익스플로러 8부터 되니 거의 호환 된다고 보면됩니다.

로컬스토리지 참고: [https://developer.mozilla.org/ko/docs/Web/API/Window/localStorage](https://developer.mozilla.org/ko/docs/Web/API/Window/localStorage)

용도는 고작 토큰 몇바이트 저장이라 용량은 상관 없지만.. 쿠키(4kb)보다 훨씬 크고 브라우저마다 조금 다르지만 5Mb 이상은 됩니다.

단순한 구조이며 쓰기가 매우 간편합니다.

그래서 로컬스토리지에 토큰을 담아보겠습니다.

# 브라우저 전역변수?

로컬스토리지를 다루기전에 알아둬야 할 것은 window 라는 전역변수입니다.

어떤 브라우저든 이게 있을 거라 봅니다.

확인해보면..

**fe/src/views/header.vue**  
```javascript
export default {
  mounted () {
    console.log(window)
  }
}
```  

엄청나게 많은 요소들이 있습니다.(_브라우저 너비/높이 이름등_)

잘 찾아보면 localStorage가 있다는 것을 확인 할 수 있습니다.

그리고 indexedDB도 크롬에서는 보입니다.

있다는 것은 쓸 수 있다는 것입니다.

브라우저로 로컬스토리지 유무를 판단하려면

```javascript
if (!window.localStorage) alert('이 브라우저는 지원되지 않습니다')
``` 

이런식으로 쓰면 됩니다.

그리고 앞의 window.은 빼도 잘 동작합니다.
 
# 로컬스토리지 다루기

단순히 쓰고 읽기만 하면됩니다.

키와 값 구조입니다.

아쉽게도 오브젝트 형 값은 들어가지 않습니다.

## 쓰기

```javascript
localStorage.setItem('token', 1234)
```

## 읽기

```javascript
console.log(localStorage.getItem('token'))
```

## 지우기

```javascript
localStorage.removeItem('token')
```

## 전체 삭제

```javascript
localStorage.clear()
```

# 영상

{% include video id="xzrEZzBGGik" provider="youtube" %}   




