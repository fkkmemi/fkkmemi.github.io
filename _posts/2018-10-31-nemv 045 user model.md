---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 45 사용자 모델 정리하기
category: nemv
tag: [nemv,express,vue,vuetify,node,mongo,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

기존에는 name, age 로만 테스트 했으니 로그인이 가능할 모델로 변경합니다.

{% include toc %}

# 몽구스 워닝메세지 제거

그동안 _DeprecationWarning: collection.ensureIndex is deprecated. Use createIndexes instead._ 메세지가 계속 떴을 텐데요.

스키마에 있는 index: true 가 ensureIndex 같은 이상한 짓을 한다는 것입니다.

버전업이되면 없어질 문제이긴 하지만 보기 싫으니 아래와 같이 넣어주면 워닝이 뜨지 않습니다.

**be/models/users.js**  
```javascript
mongoose.set('useCreateIndex', true)
```

# 계정 컬럼 추가

NoSQL의 장점인 맘대로 컬럼늘리기를 해봅니다.

**be/models/users.js**  
```javascript
const userSchema = new mongoose.Schema({
  name: { type: String, default: '', index: true },
  age: { type: Number, default: 1 },
  id: { type: String, default: '', unique: true, index: true },  
  pwd: { type: String, default: '' },
  retry: { type: Number, default: 0 }
})
```

id, pwd(비밀번호), retry(재시도 횟수)를 추가했습니다.

# 쓰레기 인덱스 정리

기존에 name이 unique: true 였기 때문에 인덱스를 초기화하지 않으면 중복 에러가 납니다.

**be/models/users.js**  
```javascript
User.collection.dropIndexes({ name: 1 })
```

이렇게 인덱스를 지워버리고 주석 처리하면 됩니다.

# 관리자 계정 만들기

로그인 테스트를 하려면 계정이 있어야됩니다.

CRUD식의 순서로 볼 때 사실 회원가입부터 만들어야합니다.

하지만 우선 토큰 인증에 관심이 있으니 관리자 계정을 수동으로 만들고 시작하겠습니다.

# 어드민 계정 설정 준비

**config/index.js**  
```javascript
  // dbUrl: 'mongodb://localhost:27017/nemv',
  admin: {
    id: 'memi',
    pwd: '1234',
    name: '관리자'
  }
```

소스오픈 하면 안되는 관리자 계정 정보를 작성합니다.

**완성한 users model**

**be/models/users.js**  
```javascript
const mongoose = require('mongoose')
const cfg = require('../../config')

mongoose.set('useCreateIndex', true)
const userSchema = new mongoose.Schema({
  name: { type: String, default: '' },
  age: { type: Number, default: 1 },
  id: { type: String, default: '', unique: true, index: true },
  pwd: { type: String, default: '' },
  retry: { type: Number, default: 0 }
})

const User = mongoose.model('User', userSchema)
// User.collection.dropIndexes({ name: 1})

User.findOne({ id: cfg.admin.id })
  .then((r) => {
    if (!r) return User.create({ id: cfg.admin.id, pwd: cfg.admin.pwd, name: cfg.admin.name })
    return Promise.resolve(null)
  })
  .then((r) => {
    if (r) console.log(`admin:${r.id} created!`)
  })
  .catch((e) => {
    console.error(e.message)
  })

module.exports = User

```

서버 구동시 프라미스 체인을 이용해서 id를 찾고 없으면 만드는 것입니다.

# 영상

준비중..

{% include video id="JHLmkGnktyg" provider="youtube" %}   




