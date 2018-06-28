---
layout: single
title: NEMBV 8 Back-end mongoose model
category: nembv
tag: [nembv,nodejs,mongodb,mongoose,model,playground]
comments: true
sidebar:
  nav: "nembv"
---

mongoose는 ODM(Object Document Mapping)이다. 

문서(Document)를 자바스크립트 객체(Object)로 바꿔준다고 생각하면 될 것 같다.

mongoDB는 컬럼이 rdbms처럼 고정적이지 않고 플렉서블한데(alter 같은 거 필요 없음) 이 자유도 때문에 장단점이 있다.

- 가변 필드를 고정하는 용도

만약 users라는 컬렉션을 name, age, remark 3개의 필드로 디자인 했다고 가정할 때 

native mongo-driver로는

name:'xx', age:33, remark:'hi' 기입하고 insert 하면 원했던 3컬럼의 문서가 쓰여지고

name:'yy', remark:'??' 를 기입하고 insert 하면 2컬럼의 문서가 쓰여진다.

물론 이 자유도가 필요할 때도 있으나..

쓸데 없는 코드가 스파게티될 수 있다.

```javascript
if (doc.age === undefined) age = 0;
```

age에 기본값을 넣을 경우

```javascript
const userSchema = new mongoose.Schema({
    name: { type: String, index: true },
    age: { type: Number, default: 11 },
    remark: { type: String, default: '신규' },
});
```
mongoose에서는

이제 User에 이름만 넣어도 age와 remark 가 기본값이 자동으로 들어간다.

- 형식을 고정하는 경우

name은 string이어야 하며 age는 숫자여야한다.

이제 name에 1234를 넣으면 '1234' age에 '01011'를 넣으면 1011이 들어간다. 

{% include toc %}

# models 추가

일반적으로 상용 웹을 구현할 때 꼭 필요하면서 상호 연관이 되야하는

companies, groups 두개를 추가해서 사용해본다.

아래의 모델들을 models 디렉토리 안에 추가한다.

**companies.js**

```javascript
const mongoose = require('mongoose');

const companySchema = new mongoose.Schema({
    name: { type: String, index: true }, // 이름
    ut: { type: Date, default: Date.now }, // 변경 날짜 : timestamp
    pos: { // 위치 lat,lng로 해야 구글에서 바로 쓰기 좋다
        lat: { type: Number, default: 37.1 },
        lng: { type: Number, default: 127.1 },
    },
    type: { type: Number, default: 0 }, // 추후 용도
    rmk: { type: String, default: '신규' }, // 리마크 : 설명
    gr_ids: [{ type: mongoose.Schema.Types.ObjectId, ref: 'Group' }], // 포함될 그룹들
});

module.exports = mongoose.model('Company', companySchema);
```

**groups.js**

```javascript
const mongoose = require('mongoose');

const groupSchema = mongoose.Schema({
    name: { type: String, index: true }, // 이름
    ut: { type: Date, default: Date.now }, // 변경 날짜 : timestamp
    cp_id: { type: mongoose.Schema.Types.ObjectId, ref: 'Company', required: true }, // 지정된 회사가 꼭 필요
    rmk: { type: String, default: '' }, // 리마크 : 설명
});

module.exports = mongoose.model('Group', groupSchema);
```

# playground 추가

일단 모델이 만들어 졌으면 당장 쓰고 읽고 싶은데 api router까지 적용하기엔 너무 멀고 복잡하기 때문에 나 같은 경우 놀이터를 하나 만들어 놓고 모듈테스트를 진행한다.

최상단에 playGround.js를 추가한다.

**playGround.js**

```javascript
exports.test = {
  model: () => {
    console.log('모델 테스트');
  },
};
```

그리고 app.js에 모듈을 추가하고 mongoose 연결 콜백 받은후 실행한다.

**app.js**

```javascript
const pg = require('./playGround');

mongoose.connect(cfg.db.url, (err) => {
  if (err) return console.error(err);
  console.log('mongoose connected');
  pg.test.model();
});
```

# company test

## 쓰기

정상적으로 '모델 테스트'라고 떳다면 컴패니에 데이터를 넣어보자

**playGround.js**

```javascript
const Company = require('./models/companies');

exports.test = {
  model: () => {
    console.log('모델 테스트');

    const cp = new Company({
      name: '테스트',
    });
    cp.save()
      .then((r) => {
        console.log(r);
      })
      .catch(err => console.error(err));
  },
};

/* 결과물
{ pos: { lat: 37.1, lng: 127.1 },
  type: 0,
  rmk: '신규',
  gr_ids: [],
  _id: 5aafa22e504cc627450cac77,
  name: '테스트',
  ut: 2018-03-19T11:42:38.386Z,
  __v: 0 }
*/
```

- save method는 추가한 객체를 반환한다. *insert method는 성공유무를 반환*

- name만 적었지만 기본값이 들어가 있는 것을 확인할 수 있다.

- mongoose 5부터는 모든 반환을 promise로 받을 수 있다. *추후 promise 편 보강*

- __v는 mongoose에서 자동으로 부여하는 필드다. *무슨 용도인지는 잘 모르겠지만 냅두자*

## 읽기

하나를 썼으니 이번엔 읽기만 해본다..

**playGround.js**

```javascript
const Company = require('./models/companies');

exports.test = {
  model: () => {
    console.log('모델 테스트');

    Company.findOne({ name: '테스트' })
      .then((r) => {
        console.log(r);
      })
      .catch(err => console.error(err));
  },
};

/* 결과물
{ pos: { lat: 37.1, lng: 127.1 },
  type: 0,
  rmk: '신규',
  gr_ids: [],
  _id: 5aafa22e504cc627450cac77,
  name: '테스트',
  ut: 2018-03-19T11:42:38.386Z,
  __v: 0 }
*/
```

- 제대로 쓰여진 것을 확인 할 수 있다.

> 여기서 중요한 것 하나는 굳이 모델 이름을 왜 **companies**라고 했는지다  
mongoose는 자동으로 컬렉션을 만들때 **복수** 로 만드는데 단순히 뒤에 s가 붙는 경우가 아닌  
사전적인 의미가 있는 단어는 mongoose가 굳이! 컬렉션이름을 사전적 복수명으로 변경한다..  
그러므로 groups, tables, rows 등은 s만 붙지만...  
person -> people, company -> companies, 가 되니 mongoose로만 읽고 쓸때는 상관 없느나  
native-mongo driver로 처리할 때 기겁할 수 있으니 주의...


# group test

그룹은 조금 난이도가 있는데 company와 엮여 있기 때문이다.

company가 없는 group은 존재할 수 없다고 칠 경우 그렇다.

## 쓰기

**playGround.js**

```javascript
const Company = require('./models/companies');
const Group = require('./models/groups');

exports.test = {
  model: () => {
    console.log('모델 테스트');
    
    Company.findOne({ name: '테스트' })
      .then((cp) => {
        console.log(cp);
        const gr = new Group({
          name: '소속1',
          cp_id: cp._id,
        });
        return gr.save();
      })
      .then((gr) => {
        console.log(gr);
        return Company.update({ _id: gr.cp_id }, { $addToSet: { gr_ids : gr._id } });
      })
      .then((r) => {
        console.log(r);
      })
      .catch(err => console.error(err));
  },
};
/* 출력
{ pos: { lat: 37.1, lng: 127.1 },
  type: 0,
  rmk: '신규',
  gr_ids: [],
  ut: 2018-03-19T11:48:56.838Z,
  _id: 5aafa3a8f8b7a8274f97560e,
  name: '테스트',
  __v: 0 }
{ rmk: '',
  _id: 5aafac643ab6dc27e71e07af,
  name: '소속1',
  cp_id: 5aafa3a8f8b7a8274f97560e,
  ut: 2018-03-19T12:26:12.946Z,
  __v: 0 }
{ n: 1,
  nModified: 1,
  opTime: 
   { ts: Timestamp { _bsontype: 'Timestamp', low_: 2, high_: 1521462373 },
     t: 3 },
  electionId: 7fffffff0000000000000003,
  ok: 1 }
 */
```

- 그룹 추가가 어려운 이유는 company _id를 꼭 알아야 하기 때문이다.

- 컴패니의 _id를 얻기위해 '테스트' 인 컴패니를 검색한다.

- 컴패니의 _id: 5aafa3a8f8b7a8274f97560e 검색후

- 그룹 이름과 컴패니 _id로 그룹을 생성한다.

- 리턴된 그룹 _id를 컴패니의 gr_ids(array)에 추가한다.

- update 결과는 nModified: 1 로 확인이 되었다.

## 개선된 쓰기

nModified: 1 로 잘되었다는 소식은 들었지만 실제 데이터를 확인해 보고 싶을 것이다.. 그리고 앞으로 api에서 보내줘야할 정보 또한 추가된 데이터다.

그래서 개선을 하면 이렇다.

**playGround.js**

```javascript
const Company = require('./models/companies');
const Group = require('./models/groups');

exports.test = {
  model: () => {
    console.log('모델 테스트');
    
    Company.findOne({ name: '테스트' })
      .then((cp) => {
        if (!cp) throw new Error('회사가 존재하지 않음');
        const gr = new Group({
          name: '소속2',
          cp_id: cp._id,
        });
        return gr.save();
      })
      .then((gr) => {
        return Company.findOneAndUpdate(
          { _id: gr.cp_id }, 
          { $addToSet: { gr_ids : gr._id } }, 
          { new: true })
          .populate('gr_ids');
      })
      .then((r) => {
        console.log(r);
      })
      .catch(err => console.error(err));
  },
};
/* 출력
{ pos: { lat: 37.1, lng: 127.1 },
  type: 0,
  rmk: '신규',
  gr_ids: 
   [ { rmk: '',
       ut: 2018-03-19T12:26:12.946Z,
       _id: 5aafac643ab6dc27e71e07af,
       name: '소속1',
       cp_id: 5aafa3a8f8b7a8274f97560e,
       __v: 0 },
     { rmk: '',
       ut: 2018-03-19T12:36:47.415Z,
       _id: 5aafaedf408fdb27f5d6181c,
       name: '소속2',
       cp_id: 5aafa3a8f8b7a8274f97560e,
       __v: 0 } ],
  ut: 2018-03-19T11:48:56.838Z,
  _id: 5aafa3a8f8b7a8274f97560e,
  name: '테스트',
  __v: 0 }
 */
```

- company가 없을 수도 있기 때문에 예외처리함 *promise chain에서 나갈 때는 꼭 throw로 보내야한다. return할 경우 다음 then으로 간다*

- 전에 update에서는 수행 유무만 나왔지만 findOneAndUpdate와 맨 마지막 { new: true } 옵션으로 수행 유무 대신 갱신된 도큐를 출력한다.

- populate('gr_ids')로 해당 그룹 2개에 대한 내용이 각각 출력되었다. *populate는 추후 다시 언급*

