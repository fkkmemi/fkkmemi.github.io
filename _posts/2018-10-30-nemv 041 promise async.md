---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 41 프라미스(promise) 어씽크(async) 이정도만 하자!
category: nemv
tag: [nemv,javascript,promise,async,mongodb,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

비동기식 언어인 자바스크립트의 불편함을 해소해주는 프라미스(promise), 어씽크(async)에 대해 설명합니다.

{% include toc %}

# 개요

이제부터 진행 해야 할 백엔드 쪽의 api 요청 대부분 프라미스와 어씽크로 처리할 것이기 때문에 이번에 완벽히 정리하고 넘어갑니다.

제대로 하려면 어씽크만 해도 강좌 10개 정도 만들어서 할 수 있습니다만.. 개념만 알면 된다고 생각합니다.

개념만 잘 이해하려면 이번 강좌면 충분합니다.

# 콜백과 프라미스 최근 동향

몽구스는 콜백 방식과 프라미스 방식 두가지 중 아무거나 써도 됩니다.

몽구스5 부터 프라미스가 전체 지원이고 현재 대부분 다른 모듈들도 콜백 방식에서 프라미스로 넘어가는 분위기 입니다.

칠옹성같던 노드 fs 모듈도 결국 노드 10버전부터 프라미스버전이 나왔습니다.

참고: [https://nodejs.org/dist/latest-v11.x/docs/api/fs.html#fs_fs_promises_api](https://nodejs.org/dist/latest-v11.x/docs/api/fs.html#fs_fs_promises_api)

> 그런데 의외로 백만명이 쓰는 jwt 모듈이 여전히 리뉴얼이 안되서 의아합니다..  

결국 프라미스 리턴이 대세이기 때문에 꼭 익혀야합니다.

# 괴로운 시나리오 만들기

간단하게 여러 절차가 필요한 가상 시나리오를 꾸며봤습니다.

- 시작시 관리자 아이디가 있는지 확인
- 없으면 관리자 아이디를 생성
- 관리자 아이디의 나이+1 증가
- 관리자로 토큰 발행하기

순서대로 진행해야하는 일이라고 가정해봅니다.

# 콜백과 프라미스 방식의 예

## 콜백 방식

```javascript
User.findOne({}, (err, r) => {
  if (err) return console.error(err)
  console.log(r)
})
```

## 프라미스 방식

```javascript
User.findOne({})
  .then(r => console.log(r))
  .catch(err => console.error(err))
```

아직까지는 콜백이 더 나아보이기도 합니다.

# 콜백(callback) 방식 구현하기(콜백 지옥)

콜백 지옥은 콜백형 프로그래밍에서는 언젠가는 찾아옵니다.

자스(javascript)를 익히며 한번쯤은 들어봤을 콜백지옥에 대해 설명해보겠습니다.

위의 시나리오처럼 대충 꾸며 보면..

```javascript
User.findOne({ name: 'aaa'}, (err, u) => {
  if (err) return console.error(err.message)
  if (!u) {
    console.log(u) // null
    User.create({ name: 'aaa', age: 10 }, (err, cu) => {
      if (err) return console.error(err.message)
      console.log(cu) // { name: 'aaa', age: 10, _id: 5bd81974ad66cf3832db0838, __v: 0 }
      jwt.sign({ name: cu.name, age: cu.age}, key, (err, token) => {
        if (err) return console.error(err.message)
        console.log(token) // eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
        jwt.verify(token, key, (err, v) => {
          if (err) return console.error(err.message)
          console.log(v) // { name: 'aaa', age: 10, iat: 1540888948 }
        })
      })
    })
  }
  else {
    console.log(u) // { name: 'aaa', age: 10, _id: 5bd81974ad66cf3832db0838, __v: 0 }
    const user = u
    User.updateOne({ _id: u._id }, { $inc: { age: 1 }}, (err, ur) => {
      if (err) return console.error(err.message)
      console.log(ur) // { n: 1, nModified: 1, ... }
      jwt.sign({ name: user.name, age: user.age + 1 }, key, (err, token) => {
        if (err) return console.error(err.message)
        console.log(token) // eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
        jwt.verify(token, key, (err, v) => {
          if (err) return console.error(err.message)
          console.log(v) // { name: 'aaa', age: 11, iat: 1540889007 }
        })
      })
    })
  }
})
// User.deleteMany({})
//   .then(r => console.log(r))
//   .catch(err => console.error(err))
```

- name이 'aaa' 인 것을 찾습니다.
- 없다면(null)
    - name이 'aaa'고 age가 10으로 생성합니다.
    - 생성된 cu(_{ name: 'aaa', age: 10 }_)로 토큰을 발행합니다.
    - 발행된 토큰을 풀어봅니다.
- 있다면
    - user라는 지역변수에 대입해놓습니다.(_update 결과가 user값이 아니기 때문..._)
    - 해당 유저를 찾아서 age를 1 증가시킵니다.(_업데이트 결과값이 유저가 아님.._)
    - 대입했던 user(_{ name: 'aaa', age: 10 + 1 }_)로 토큰을 발행합니다.
    - 발행된 토큰을 풀어봅니다.
    
단순한 로직인데도 이렇게 지옥을 경험할 수 있습니다.

> 실제로 저는 노드4때 실무에서 저렇게 작업했습니다.  
코드에서 탭이 12칸까지 늘어날 수도 있답니다..

코드만 봐도 문제점을 생각할 수 있습니다.

- **에러처리를 매번 해야함.**
- **똑같은 행위인데 2번 적어야함(_토큰 발행 부분_)**

테스트를 하기 위해서는 아래 처럼 하시면 됩니다.
 
1. 윗 부분을 주석 처리해두고 아래 코드(deleteMany)만 살려서 실행
2. 윗 부분을 주석 해제하고 실행: 없으니까 만드는 로직
3. 다시한번 실행: 있으니까 업데이트하는 로직

# 프라미스(Promise) 방식 구현하기

## 콜백함수 프라미스로 변환하기

JWT는 아직 프라미스방식이 공식 지원하지 않아서 아쉽긴 하지만 쉽게 변경할 수 있습니다.

```javascript
const signToken = (u, k) => {
  return new Promise((resolve, reject) => {
    jwt.sign({ name: u.name, age: u.age }, k, (err, token) => {
      if (err) reject(err)
      resolve(token)
    })
  })
}
const verifyToken = (t, k) => {
  return new Promise((resolve, reject) => {
    jwt.verify(t, k, (err, v) => {
      if (err) reject(err)
      resolve(v)
    })
  })
}
```

이런식으로 함수를 만들어버리면 됩니다.(_몽구스도 내부적으로는 저렇게 해놨을 것입니다...._)

해결되면 resolve에 결과값을 담아서 나오는 것이고 에러가 나면 reject에 에러값을 담아서 나오게 되는 것입니다.

## 프라미스로 콜백지옥 탈출하기

위에서 콜백 방식으로 만든 코드를 프라미스 방식으로 변경해보겠습니다.

```javascript
let user
User.findOne({ name: 'aaa' })
  .then((u) => {
    if (!u) return User.create({ name: 'aaa', age: 10 })
    return Promise.resolve(u)
  })
  .then((u) => {
    user = u
    return User.updateOne({ _id: u._id }, { $inc: { age: 1 }})
  })
  .then((r) => {
    if (!r.nModified) throw new Error('수정된 것이 없네요..')
    user.age++
    return signToken(user, key)
  })
  .then((token) => {
    return verifyToken(token, key)
  })
  .then(v => console.log(v))
  .catch((err) => {
    console.error(err.message)
  })
```

**프라미스 체인으로 순서대로 진행**  
- name이 'aaa'인 것을 찾습니다.
- 없다면 생성(User.create)하고 있다면 그대로(Promise.resolve) 보냅니다.
- user 정보를 받아 놓고(_토큰 만들 때 사용해야함_) age를 1 증가 시킵니다.
- 업데이트가 성공적이지 않다면(!r.nModified) 강제로 에러를 냅니다(throw new Error).
- 업데이트가 성공적이라면 age를 1증가 시키고 토큰을 만듭니다.
- 만든 토큰을 풀어냅니다.
- 풀어낸 결과값을 콘솔에 찍어봅니다.

콜백방식 보다 훨씬 직관적이죠?

콜백방식에서 매번 에러처리해주던 것을 catch에 몰아 넣었습니다.(_그래서 어디서 에러가 났는지 확인이 어려운 단점도 있긴 합니다._)

하지만 약간 아쉬운 부분이 있습니다.

프라미스 체인으로 내려오면서 때로는 유저 정보 때로는 업데이트 정보가 오기 때문에 중간 변수 let user를 선언해야 했습니다.

age가 10일때는 증가를 안시키고 싶다면 또 약간 귀찮아 지게 됩니다.

# 어씽크(async) 방식 구현하기

## 어씽크 함수 간단히 만들어보기

프라미스 체인처럼 물흐르듯이 꼭 내려가지 않고 조건에 따라 조금 다른 동작을 해야할 때 어씽크가 유용합니다.  

먼저 간단하게 어씽크를 알아보겠습니다.

**주의: node7 이하 버전에선 async가 없어서 안됩니다..**

```javascript
const asyncTest = async (i) => {
  if (i > 10) throw new Error('10보다 큰건 싫어요')
  return i + 2
}

asyncTest(1)
  .then(r => console.log(`2 더해져서 ${r} 입니다.`))
  .catch(e => console.error(`에러났네요: ${e.message}`))
  
asyncTest(11)
  .then(r => console.log(`2 더해져서 ${r} 입니다.`))
  .catch(e => console.error(`에러났네요: ${e.message}`))
```

어씽크를 사용하면 결과치를 항상 프라미스로 뱉어냅니다.(then, catch)

매우 편리하죠?

## 어웨이트(await)란

어씽크 혼자서는 사실 할만한 것이 별로 없습니다.

단짝인 어웨이트와 함께 있어야 의미가 있습니다.

뜻 그대로 async 함수 내부에서 사용하면 기다려(await) 줍니다.

**주의할 사항은 꼭 async 함수 아래에 있어야합니다!!**

## 어씽크 어웨이트로 합리적인 코드로 만들어보기

이제 콜백, 프라미스체인을 조미료 약간 첨가해서... 어씽크 어웨이트로 바꿔보겠습니다.

```javascript
const getToken = async (name) => {
  let u = await User.findOne({ name }) // name: name을 축약하고 await로 기다려 줍니다.
  if (!u) u = await User.create({ name , age: 10 }) // 만들어주고 u를 갱신 합니다.
  if (u.age > 12) throw new Error(`${u.age}는 나이가 너무 많습니다.`)
  const ur = await User.updateOne({ _id: u._id }, { $inc: { age: 1 }}) // age를 증가시키고 ur(user result)에 결과값을 담아놓습니다.
  if (!ur.nModified) throw new Error('수정된 것이 없네요..') // 수정된 값이 없다면 에러와 함께 내보냅니다.
  u = await User.findOne({ _id: u._id }) // age가 증가 된 것으로 갱신해줍니다.
  const token = await signToken(u, key) // 토큰을 만듭니다.
  const v = await verifyToken(token, key)
  return v
}

getToken('aaa')
  .then(v => console.log(v))
  .catch(err => console.error(err.message))
```

**어씽크 어웨이트로 순서대로 진행**  
- getToken이라는 어씽크 함수를 만듭니다.
- u라는 변수를 선언하고 어웨이트를 이용해 User.findOne이 끝날때 까지 기다려줍니다.(_블러킹됨_)
- 결과값이 없다면 age 10으로 만들어주고 u를 갱신해줍니다.(_블러킹됨_)
- 증가하다가 age가 13이 되면 에러로 내보냅니다.
- ur(user result) 변수를 만들어서 어웨이트로 업데이트 결과를 받으며 기다립니다.(_블러킹됨_)
- 혹시나 업데이트가 안되면 에러로 내보냅니다.
- u변수를 age가 증가 된 것으로 갱신해줍니다.
- token 변수를 만들어서 어웨이트로 토큰이 발행될 때 까지 기다립니다.(_블러킹됨_)
- v 변수를 만들어서 어웨이트로 토큰을 풀 때 까지 기다립니다.(_블러킹됨_)
- 결과치를 프라미스로 내보냅니다.

테스트 방법: User.deleteMany로 처음에 싹 지우고 계속 껏다 켜면(yarn dev) 나이가 13일때 에러 날 것입니다.

프라미스에서 곤란했던 let user 같은 선언 없이 내부에서 재사용되서 편리합니다.

단점은 어씽크 함수를 만들어야 되기 때문에 조금 귀찮습니다.

사실 강좌 진행중 어씽크를 쓸일은 별로 없습니다.

저렇게 복잡하게 여러가지 일을 한번에 조건절 풀어야할 일이 그다지 많지 않기 때문이죠..

거의 프라미스 체인으로 마무리 되지만, 하다 복잡해질 때 어씽크를 사용하세요~

# 마치며

이 강좌만으로는 이해가 안될 수 있습니다.

프라미스 자체가 왜 필요한지, 그리고 아 이해가 안가실 분들도 있기 때문인데요..

꼭 직접 해보시기 바랍니다.

콜백방식은 최근 라이브러리들은 거의 쓰지 않기 때문에 꼭 프라미스 체인은 연습해보시기 바랍니다.

직접 해보면 어렵지 않습니다~

> 사실 좀더 좋은 몽구스의 메쏘드들이 있어서 한방에 해결되는 함수가 많습니다. (_eg: findOneAndUpdate 같은..._)  
예를 들기위해 작성한 코드이니 오해 하지 마시기 바랍니다~

# 영상

준비중..

{% include video id="" provider="youtube" %}   




