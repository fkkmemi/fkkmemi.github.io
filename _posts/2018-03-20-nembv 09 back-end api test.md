---
layout: single
title: NEMBV 9 Back-end api test
category: nembv
tag: [nembv,nodejs,express,api]
comments: true
sidebar:
  nav: "nembv"
---

> 이 강좌는 종료되었습니다.  
새로운 강좌로 시작하세요~  
[모던웹(NEMV) 제작 강좌](/nemv/){: .btn .btn--success}  

api 설계를 하기전 어떻게 테스트를 하며 기능을 추가하는 지가 중요하다.

백엔드 개발자라면 장황하게 api를 작성하고 [포스트맨](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 등으로 테스트를 할 수도 있지만..

이미 프론트엔드가 돌아가고 있기 때문에 실제 프론트엔드로 직접 버튼을 추가해서 api 동작 상황부터 체크 해보겠다. 

> 포스트맨은 구글 크롬에서 동작하는 확장 프로그램이다 다양한 패킷을 정의해서 http요청을 할 수 있기 때문에 웹 개발자라면 필수 아이템인것 같다.

{% include toc %}

# api test
RESTful api를 사용하기 위한 최소한의 골격으로 진행해보겠다.

## routes/api
![api 구조](/images/nembv/2018-03-20 10-20-02 nembv.png)

### api/data/index.js
```javascript
const router = require('express').Router();
const company = require('./company');
const group = require('./group');

router.use('/company', company);
router.use('/group', group);

router.all('*', (req, res) => {
    res.status(404).send({ success: false, msg: `unknown uri ${req.path}` });
});

module.exports = router;
```

- comapny와 group을 라우트하고 나머진 404로 보낸다

### api/data/company/index.js, group/index.js 공통
```javascript
const router = require('express').Router();
const ctrl = require('./ctrls');

router.get('/', ctrl.list);
router.post('/', ctrl.add);
router.put('/', ctrl.mod);
router.delete('/', ctrl.del);

router.all('*', (req, res) => {
    res.status(404).send({ success: false, msg: `unknown uri ${req.path}` });
});

module.exports = router;
```

- CRUD(post, get, put, delete)를 할 수 있도록 각 라우터 정의를 한다.

### api/data/company/ctrls.js, group/ctrls.js 공통
```javascript
exports.list = (req, res) => {
  res.send({ success: false, msg: 'list 준비중입니다' });
};

exports.add = (req, res) => {
  res.send({ success: false, msg: 'add 준비중입니다' });
};

exports.mod = (req, res) => {
  res.send({ success: false, msg: 'mod 준비중입니다' });
};

exports.del = (req, res) => {
  res.send({ success: false, msg: 'del 준비중입니다' });
};
```

- 동작 확인만 할 수 있는 최소한의 응답을 만들었다.

### localhost:3000/api/data/company, group test

브라우저에서 할 수 있는 것은 get요청 정도다.. 물론 개발자 모드로 가능한 부분은 있지만.

서버를 시작하고 [http://localhost:3000/api/data/company](http://localhost:3000/api/data/company) 잘 동작하는지 확인한다.
 