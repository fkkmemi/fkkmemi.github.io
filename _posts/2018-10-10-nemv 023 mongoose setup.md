---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 23 몽구스 설치 및 확인
category: nemv
tag: [nemv,mongo,mongodb,node,crud]
comments: true
sidebar:
  nav: "nemv2"
---

노드에서 몽고디비를 사용하는 방법은 여러가지 입니다.

그중 몽구스(mongoose)라는 것이 디비를 핸들링하기 쉽게 해줍니다.

몽구스를 이용해서 CRUD(Create Read Update Delete) 쓰고 읽고 수정하고 지우고를 해보도록 하겠습니다.

# 몽고디비 켜놓기

```bash
$ mongod
```

# 몽구스 설치

백엔드에 몽구스를 설치 합니다.

```bash
$ cd be
$ yarn add mongoose
```

# 몽구스 연결하기

**be/app.js**  
```javascript
const mongoose = require('mongoose')

mongoose.connect('mongodb://localhost:27017/nemv', { useNewUrlParser: true }, (err) => {
   if (err) return console.error(err)
   console.log('mongoose connected!')
})
```

백엔드의 시작점인 app.js 하단에 코드를 넣어서 연결되었다는 문자를 확인해봅니다.

# 데이터 써보기

**be/app.js**  
```javascript
const mongoose = require('mongoose')

const userSchema = new mongoose.Schema({
   name: { type: String, default: '', unique: true, index: true },
   age: { type: Number, default: 1 }
 })

const User = mongoose.model('User', userSchema)

mongoose.connect('mongodb://localhost:27017/nemv', { useNewUrlParser: true }, (err) => {
   if (err) return console.error(err)
   console.log('mongoose connected!')
   User.create({ name: '하하' })
    .then(r => console.log(r))
    .catch(e => console.error(e))
})
```

먼저 User라는 모델을 만들어 줍니다.

- type이 지정되면 그 타입으로만 저장합니다.  
예를 들어 문자타입인 name을 숫자 123으로 넣어도 '123' 이라는 문자로 자동으로 바꿔줍니다.
- default를 정해놓으면 값이 없을 경우, default 값으로 추가됩니다.
- unique는 중복된 데이터가 추가되지 못하게 합니다.
- index는 빠르게 찾을 때 사용합니다.

연결 후 name이 '하하' 를 추가했습니다. age는 넣지 않았기 때문에 기본값인 1이됩니다.

# 데이터 읽어보기

**be/app.js**  
```javascript
    User.find()
      .then(r => console.log(r))
      .catch(e => console.error(e))
```

위에 써둔 name이 '하하'인 데이터를 확인 할 수 있습니다.

콘솔로 확인해 보면 _id 라는 값이 추가 되어 있습니다.

_id: 5bbe038d7588bf14393d08bf

_id는 몽고디비가 어떤 데이터든 만들어 줍니다.

지울 수도 없습니다.

항상 유니크한 값을 가지기 때문에 이 _id를 찾아서 CRUD 중 RUD를 하면 편리합니다.

# 데이터 수정 후 읽어보기

**be/app.js**  
```javascript
 User.updateOne({ _id: '5bbe038d7588bf14393d08bf' }, { $set: { age: 34 } })
   .then(r => {
     console.log(r)
     console.log('updated')
     return User.find()
   })
   .then(r => console.log(r))
   .catch(e => console.error(e))
``` 

위에서 찾은 _id를 이용해 나이를 34로 변경 해보았습니다.

그리고 반영된 결과를 보기위해 다시 읽어 본것을 확인 해봤습니다.

이렇게 연달아서 처리할 때 *프라미스 체인* 이라는 방법을 사용합니다.

프라미스가 뭔지 아직 잘 모르지만 then 과 catch를 뱉는 다는 것만 알면 됩니다.

위에 코드를 보면 updateOne()을 한 이후에 .then 으로 이어지고 find()를 한 이후에 다음으로 이동하기 위해 return을 붙힌 것입니다.

아직 이해하기가 힘들죠? 하다보면 이해갑니다. 지금 알 필요는 없습니다.

그냥 패턴만 이런식이다 라고 보면 됩니다.

**프라미스 패턴 예**  
```javascript
Company.findOne()
    .then(r => {
         return Group.findOne()
    })
    .then(r => {
         return Student.findOne()
    })
    .then(r => {
      if (!r) throw new Error('데이터가 없으면 수정할 수 없잖아') // 하단 캐치로 내려가게 할 수 있음
         return Parent.updateOne()
    })
    .then(r => {
         return Car.deleteOne()
    })
    .then(r => {
      console.log('휴 여기까지 잘 왔네..')
    })
    .catch((e) => {
      console.error(e.message) // 어디에서 에러가 나든 이리로 옴
    })
```

> 몽구스는 5버전 부터 어떤 결과든 프라미스를 뱉어냅니다.

# 데이터 삭제하고 읽어보기

**be/app.js**  
```javascript
 User.deleteOne({ _id: '5bbe038d7588bf14393d08bf' })
   .then(r => {
     console.log(r)
     console.log('removed')
     return User.find()
   })
   .then(r => console.log(r))
   .catch(e => console.error(e))
``` 

지우고나서 읽어보면 데이터가 없는 것을 확인 할 수 있습니다.

> 언제부터 인지는 잘 모르지만.. 모델.remove 가 없어졌습니다..  
이제 deleteOne or deleteMany로 꼭 명시를 해줘야합니다.    
remove()로 전체 데이터 여러번 날려먹은 저에겐 축하할 일입니다.

# 영상

{% include video id="gC0ZnTE7QiI" provider="youtube" %}  


