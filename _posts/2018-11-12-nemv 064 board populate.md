---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 64 게시판 모델 만들기(populate)
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

게시물들은 누가 썼는지, 어느 게시판인지가 제일 중요합니다.

몽구스의 파퓰레이션(population) 기능에 대해 알아봅니다.

{% include toc %}

{% raw %}

# 모델 만들기

**be/models/articles.js**  
```javascript
const mongoose = require('mongoose')

mongoose.set('useCreateIndex', true)
const articleSchema = new mongoose.Schema({
  title: { type: String, default: '', index: true },
  content: { type: String, default: '' },
  cnt: {
    view: { type: Number, default: 0 },
    like: { type: Number, default: 0 }
  },
  ip: { type: String, default: '' },
  comments: [],
  _user: { type: mongoose.Schema.Types.ObjectId, ref: 'User', index: true },
  _board: { type: mongoose.Schema.Types.ObjectId, ref: 'Board', index: true }
})

module.exports = mongoose.model('Article', articleSchema)
```

게시판의 일반적인 요소를 생각나는 대로 넣어봤습니다.

그 중 묘한 것이 있습니다.

바로 _user, _board 인데요..

mongoose.Schema.Types.ObjectId 라는 형식을 가지고 있습니다.

레퍼런스(ref) 로 모델을 가르키고 있는 형태입니다.

바로 User._id, Board._id 를 보관하겠다는 것입니다.

게시물의 작성자와 어떤 형식인지를 _id만 보관 해서 사용하는 것입니다.

# 사용해보기

설명보다는 역시 직접 해보는 것이 더 빠릅니다.

## _id 구하기

**be/app.js**  
```javascript
const User = require('./models/users')
const Board = require('./models/boards')
const Article = require('./models/articles')

User.findOne()
  .then(r => console.log(r.id, r._id)) // 5be1c7eb0ff40640c81ecc0d

Board.findOne()
  .then(r => console.log(r.name, r._id)) // 5be97f5f8fb2da704ad95273
```

사용자와 게시판을 검색해서 _id를 구합니다.

## 구한 _id 들로 게시물 등록하기

```javascript
Article.create({ title: 'aaa', content: 'kkfjf', _user: '5be1c7eb0ff40640c81ecc0d', _board: '5be97f5f8fb2da704ad95273' })
  .then(r => console.log(r))
/*
{ cnt: { view: 0, like: 0 },
  title: 'aaa',
  content: 'kkfjf',
  ip: '',
  comments: [],
  _id: 5be98c8968c61077b643423c,
  _user: 5be1c7eb0ff40640c81ecc0d,
  _board: 5be97f5f8fb2da704ad95273,
  __v: 0 }
*/
```

_user, _board는 쓸모가 없죠 아직은..

## 의미 있는 데이터 채우기

```javascript
Article.find()
    .then(r => {
      return User.findOne({_id: r._user})
    })
    .then(r => {
      return Board.findOne({_id: r._board})
    })
    // ..
```

의미 있게 채우려면 프라미스 체이닝을 해줘야하는데 불편하죠

## 파퓰레이트 사용하기

참고: [https://mongoosejs.com/docs/populate.html](https://mongoosejs.com/docs/populate.html)

```javascript
Article.find({ _board: '5be97f5f8fb2da704ad95273'})
  .populate('_user', 'name')
  .populate('_board')
  .then(r => console.log(r))
/*
{ cnt: { view: 0, like: 0 },
    title: 'aaa',
    content: 'kkfjf',
    ip: '',
    comments: [],
    _id: 5be98c8968c61077b643423c,
    _user: { name: '관리자', _id: 5be1c7eb0ff40640c81ecc0d },
    _board:
     { name: '지호',
       lv: 0,
       rmk: 'test',
       _id: 5be97f5f8fb2da704ad95273,
       __v: 0 },
    __v: 0 } ]  
 */  
```

이런식으로 풀어 줄 수 있습니다.

훨씬 간편하죠?

# 마치며

주의할 것은 파퓰레이트는 몽고가 하는 것이 아닌 몽구스가 해주는 것입니다. 

즉 백엔드 컴퓨팅파워가 필요하다는 것입니다.(_내부적으로는 반복문으로 찾고 매치하고 해서 결과를 주는 것이죠_)

너무 많은 것을 담으면 결국 메모리, CPU가 괴로워하니 적당하게 딱 필요한 곳에서 사용해야합니다.

> 너무 간단한 것이지만 파퓰레이트만 검색하실 분들을 위해 챕터로 뺍니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/cdeefa29133d7e8dbf94e8e4595caeafadd6ee93)

{% endraw %}

# 영상

{% include video id="OSTYCpMMvYE" provider="youtube" %}
