---
layout: single
title: NEMBV 13 Back-end api paging
category: nembv
tag: [nembv,nodejs,express,mongoose,api,paging]
comments: true
sidebar:
  nav: "nembv"
---

데이터를 여러개 부를 때 db, be, fe 등 어딘가에 무리가 가는 것은 당연하다.

데이터 집합이 몇개 안되는 것을 알고 있어도 개발자는 늘 페이징처리를 할 준비가 되어 있어야한다.

{% include toc %}

# 페이징을 위한 최소한의 규칙
be<>fe 사이에 서버사이드 페이징을 위해 주고 받아야할 최소한의 것들은 아래와 같다.

- 요청시
    - search: 검색어
    - sortName: 정렬할 필드
    - sortDir: 정렬 방향
    - skip: 어디서부터
    - limit: 최대 몇개까지
- 수신시
    - totalCount: 총 데이터 개수(*검색어로 필터된*)
    - dataCount: 페이징된만큼의 데이터 개수
    - datas: 실제 데이터들
    
# back-end api

저번에 단순하게 전체 목록을 불렀던 api를 페이징 되게 수정하고 나머지 기본 기능도 정리하여 추가했다.

## api paging for company

company 최종 수정

**routes/api/data/company/ctrls.js**  
```javascript
const Company = require('../../../../models/companies');
const Group = require('../../../../models/groups');

exports.list = (req, res) => {
  // res.send({ success: false, msg: 'list 준비중입니다' });
  // Company.find()
  //   .then(rs => res.send({ success: true, ds: rs }))
  //   .catch(err => res.send({ success: false, msg: err.message }));
  let { draw, search, skip, limit, order, sort } = req.query;

  if(draw === undefined) return res.send({ success: false, msg: 'param err draw' });
  if(search === undefined) return res.send({ success: false, msg: 'param err search' });
  if(skip === undefined) return res.send({ success: false, msg: 'param err skip' });
  if(limit === undefined) return res.send({ success: false, msg: 'param err limit' });
  if(order === undefined) return res.send({ success: false, msg: 'param err order' });
  if(sort === undefined) return res.send({ success: false, msg: 'param err sort' });

  skip = parseInt(skip);
  limit = parseInt(limit);
  sort = parseInt(sort);

  let d = {
    draw: draw,
    cnt: 0,
    ds: [],
  };

  Company.count()
    .where('name').regex(search)
    .then((c) => {
      d.cnt = c;
      const s = {}
      s[order] = sort;
      return Company.find()
        .where('name').regex(search)
        .populate('gr_ids')
        .sort(s)
        .skip(skip)
        .limit(limit);
    })
    .then((ds) => {
      d.ds = ds;
      res.send({success: true, d: d});
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
};

exports.add = (req, res) => {
  // res.send({ success: false, msg: 'add 준비중입니다' });
  // const { name, rmk } = req.body;
  //
  // if (!name) return res.send({ success: false, msg: '이름 없음' });
  // if (!rmk) return res.send({ success: false, msg: '비고 없음' });
  // const cp = new Company({ name: name, rmk: rmk });
  // cp.save()
  //   .then(r => res.send({ success: true, d: r }))
  //   .catch(err => res.send({ success: false, msg: err.message }));

  const { name } = req.body;
  if (!name) res.send({success: false, msg : 'name not exists'});
  const cp = new Company({ name: name });
  cp.save()
    .then(() => {
      res.send({success: true});
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
};

exports.mod = (req, res) => {
  // res.send({ success: false, msg: 'mod 준비중입니다' });

  const set = req.body;
  if (!Object.keys(set).length) return res.send({ success: false, msg: 'body not set' });
  if (!set._id) return res.send({ success: false, msg: 'id not exitst' });
  set.ut = new Date();

  const f = { _id: set._id };
  const s = { $set: set };
  Company.findOneAndUpdate(f, s)
    .then(() => {
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg: err.message });
    });
};

exports.del = (req, res) => {
  // res.send({ success: false, msg: 'del 준비중입니다' });

  const { id } = req.query;
  if (!id) return res.send({ success: false, msg: 'id not exists' });
  let cp;
  Company.findOne({ _id: id })
    .then((r) => {
      cp = r;
      return Group.remove({ _id: { $in: r.gr_ids }});
    })
    .then(() => {
      return Company.remove({ _id: id });
    })
    .then(() => { // { n: 1, ok: 1 }
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg: err.message });
    });
};
```     
### list
- draw: 추후 보안+IE에서 같은 요청 오작동 문제 때문에 추가하였다 *1씩 증가여 요청*
- Company.count(): 먼저 검색어로 필터된 결과만큼의 개수를 받는다.
- s[order] = sort: 어떤것으로 어떻게 정렬할 것인지의 객체를 정의한다.
- .where: 등의 문법은 mongoose의 querybuilder 기능이다 *대신 find안에 검색 내용을 넣어도 되지만 편리하고 이쁘다.*
- populate: 소속된 그룹데이터까지 보낸다. 
- 정리된 데이터를 d에 담아서 보낸다.

> populate는 내부적으로는 실제 rdbms의 join처럼 디비엔진이 계산하는 것이 아니고 mongoose가 받아놓은 데이터를 forEach로 매치한다고 보면된다.  
그러므로 백엔드 물리성능을 쓰기 때문에 무분별하게 쓰면 안된다.

### add
- 이름만 넣으면 추가하게 변경
- 어짜피 추가후 리스트 갱신할 것이기 때문에 데이터 낭비를 줄이고 성공유무만 리턴

### mod
- 제일 중요한 것은 _id인데 없으면 리젝이다.
- 가변적인 데이터에 대응할 수 있게 객체 자체를 저장한다. *비어있으면 예외처리*
- findOneAndUpdate 대신 update만 해도 된다 *추후 대비 용: 수정후 결과를 봐야할때*

### del
- 역시 제일 중요한 것은 _id인데 없으면 리젝이다.
- company를 찾아야하는 이유는 소속 그룹을 제거해야하기 때문
 
## api paging for group

그룹 최종 수정

**routes/api/data/group/ctrls.js**  
```javascript
const Company = require('../../../../models/companies');
const Group = require('../../../../models/groups');

exports.list = (req, res) => {
  let { draw, search, skip, limit, order, sort, cp_id } = req.query;

  if(draw === undefined) return res.send({ success: false, msg: 'param err draw' });
  if(search === undefined) return res.send({ success: false, msg: 'param err search' });
  if(skip === undefined) return res.send({ success: false, msg: 'param err skip' });
  if(limit === undefined) return res.send({ success: false, msg: 'param err limit' });
  if(order === undefined) return res.send({ success: false, msg: 'param err order' });
  if(sort === undefined) return res.send({ success: false, msg: 'param err sort' });

  skip = parseInt(skip);
  limit = parseInt(limit);
  sort = parseInt(sort);
  let d = {
    draw: draw,
    cnt: 0,
    ds: [],
  };

  let f = {};
  if (cp_id) f.cp_id = cp_id;

  Group.count(f)
    .where('name').regex(search)
    .then((c) => {
      d.cnt = c;
      const s = {}
      s[order] = sort;
      return Group.find(f)
        .where('name').regex(search)
        .populate('cp_id')
        .sort(s)
        .skip(skip)
        .limit(limit);
    })
    .then((ds) => {
      d.ds = ds;
      res.send({success: true, d: d});
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
}

exports.add = (req, res) => {
  const { name, cp_id } = req.body;
  if (!cp_id) return res.send({success: false, msg : 'cp_id not exists'});
  if (!name) return res.send({success: false, msg : 'name not exists'});
  const gr = new Group({ name: name, cp_id: cp_id });
  gr.save()
    .then((r) => {
      const f = { _id: r.cp_id };
      const s = { $addToSet: { gr_ids: r._id }};
      return Company.updateOne(f, s);
    })
    .then((r) => {
      if(!r.nModified) return res.send({ success: false, msg : 'already group' });
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
}

exports.mod = (req, res) => {
  const set = req.body;
  if (!Object.keys(set).length) return res.send({ success: false, msg: 'body not set' });
  if (!set._id) return res.send({ success: false, msg: 'id not exitst' });
  const f = { _id: set._id };
  const s = { $set: set };
  Group.findOneAndUpdate(f, s)
    .then((r) => {
      res.send({ success: true });
    })
    .catch((err) => {
      if (err) console.error(err);
      res.send({ success: false, msg: err.message });
    });
}

exports.del = (req, res) => {
  const _id = req.query._id;
  if (!_id) return res.send({ success: false, msg : 'param id not exists' });
  Group.findOne({_id:_id})
    .then((r) => {
      if (!r) throw new Error('group not exists');
      const f = { _id: r.cp_id };
      const s = { $pull: { gr_ids: r._id }};
      return Company.updateOne(f, s);
    })
    .then(() => { // { n: 1, nModified: 1, ok: 1 }
      return Group.remove({ _id: _id });
    })
    .then(() => { // { n: 1, ok: 1 }
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({success: false, msg : err.message});
    });
}
```     
### list
- 그룹입장에서 populate를 cp_id로 해서 컴패니 정보를 같이 보낸다.

### add
- 그룹 추가는 회사에 의존적이기 때문에 회사 gr_ids에 $addToSet으로 추가된 아이디를 넣는다.

> $addToSet은 중복된 것은 넣지 않기 때문에 $push 보다 유용하다.

### mod

### del
- 그룹 제거는 회사에 의존적이기 때문에 회사 gr_ids에서 $pull로 아이디를 제거한다.