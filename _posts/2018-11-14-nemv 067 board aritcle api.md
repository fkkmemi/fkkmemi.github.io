---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 67 게시판 게시물 API 만들기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

게시물 CRUD를 할 수 있게 API를 만들어 보겠습니다.

{% include toc %}

{% raw %}

# 개요

프론트에서 게시판 정보를 얻고 왔기 때문에 쓰고 읽을 때 게시판 _id가 항상 요구 됩니다.

/api/article/_boardId 식으로만 접근이 가능한 것입니다.

반면 수정 혹은 삭제 시에는 게시물 _id만 있으면 됩니다.

# 작전

저는 이렇게 작전을 짜봤습니다.

| 요청 유형 | 파라메터(params) | 내용(body or query) | 설명 |
| --- | --- | --- | --- |
| Create, POST | _board | 게시물 내용 | 어떤 게시판에 내용을 씀 |
| Read, GET | _board | 없음 | 어떤 게시판의 게시물들을 가져옴 |
| Update, PUT | _id | 수정된 게시물 내용 | 어떤 게시물의 내용을 변경 |
| Delete, DELETE | _id | 없음 | 어떤 게시물의 내용을 삭제 |     

> 작전은 다음강좌에서 또 바뀌기 때문에 참고만하세요.

# 토큰 추가 작업

현재까지의 토큰에는 사용자의 _id가 없습니다.

각종 api에서 사용하려면 req.user._id 가 있어야 합니다.

> 깜박했네요..

**be/routes/api/sign/index.js**  
```javascript
const signToken = (_id, id, lv, name, rmb) => { // _id add
    jwt.sign({ _id, id, lv, name }, cfg.jwt.secretKey, o, (err, token) => { // _id add
    // ..
}

router.post('/in', (req, res) => {
// ..
      return signToken(r._id, r.id, r.lv, r.name, remember) // _id add
    })
})
```

**be/routes/api/index.js**  
```javascript
const signToken = (_id, id, lv, name, exp) => { // _id add
    jwt.sign({ _id, id, lv, name }, cfg.jwt.secretKey, o, (err, token) => { // _id add
    // ..
}

const getToken = async(t) => {
// ..
  const nt = await signToken(vt._id, vt.id, vt.lv, vt.name, expSec) // _id added
```

# 게시물 API 등록

**be/routes/api/index.js**  
```javascript
router.use('/board', require('./board'))
router.use('/article', require('./article')) // added
router.use('/manage', require('./manage'))
```

article API를 등록합니다.

# 쓰기

**be/routes/api/article/index.js**  
```javascript
router.post('/:_board', (req, res, next) => {
  const _board = req.params._board
  if (!_board) return res.send({ success: false, msg: '게시판이 선택되지 않았습니다' }) // 나중에 400 bad request 처리 예제
  const { title, content } = req.body
  Board.findOne({ _id: _board })
    .then(r => {
      if (!r) return res.send({ success: false, msg: '잘못된 게시판입니다' })
      if (r.lv < req.user.lv) return res.send({ success: false, msg: '권한이 없습니다' })
      const atc = {
        title,
        content,
        _board,
        ip: '1.1.1.1',//req.ip,
        _user: null
      }
      if (req.user._id) atc._user = req.user._id
      return Article.create(atc)
    })
    .then(r => {
      res.send({ success: true, d: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
```

- 이 게시판에 쓸 자격이 있는지 판단합니다.
- 자격이 있다면 게시물을 만듭니다.
- 게시물을 만들 때 req.user._id 가 없다는 것은 손님 레벨이므로 null이 됩니다.

# 읽기

**be/routes/api/article/index.js**  
```javascript
router.get('/:_board', (req, res, next) => {
  const _board = req.params._board

  const f = {}
  if (_board) f._board = _board

  Article.find(f).populate('_user', '-pwd')
    .then(rs => {
      res.send({ success: true, ds: rs, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
});
```

- 게시판이 지정된 게시물들을 읽습니다.
- 게시판이 지정 안되었을 경우(_board: null) 전체(f = {}) 를 불러 올 수도 있습니다.
- 게시물에 연결된 사용자 정보중 비밀번호는 빼고 전송합니다.

# 수정하기

**be/routes/api/article/index.js**  
```javascript
router.put('/:_id', (req, res, next) => {
  if (!req.user._id) return res.send({ success: false, msg: '게시물 수정 권한이 없습니다' })
  const _id = req.params._id

  Article.findOne({ _id })
    .then(r => {
      if (!r) throw new Error('게시물이 존재하지 않습니다')
      if (r._user.toString() !== req.user._id) throw new Error('본인이 작성한 게시물이 아닙니다')
      return Article.findOneAndUpdate({ _id }, { $set: req.body}, { new: true })
    })
    .then(r => {
      res.send({ success: true, d: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
```

- 손님(!req.user._id or req.user.lv ===3 )은 수정이 안됩니다.
- 해당 게시물을 먼저 찾아서 권한 체크를 합니다.
- 본인이 작성한 게시물만 수정됩니다.(_관리자도 수정이 안됨_)
- 수정된 결과를 전송합니다.(findOneAndUpdate)

## findOneAndUpdate에 대해

보통 업데이트를 하면 update가 되었다는 결과가 표현됩니다.

하지만 업데이트하고 나서의 결과값을 받고 싶을 때가 있죠..

프라미스 체인으로 또 파인드를 해도 되긴하지만 이런 것을 줄여주는 함수들이 있습니다.

참고: [https://mongoosejs.com/docs/api.html#Model](https://mongoosejs.com/docs/api.html#Model)

findOneAndUpdate 3번째 항목은 옵션인데 new: true를 줌으로 써 변경된 결과가 나옵니다.

해당 옵션이 없다면 수정 전 결과가 나옵니다.

# 삭제하기

```javascript
router.delete('/:_id', (req, res, next) => {
  if (!req.user._id) return res.send({ success: false, msg: '게시물 수정 권한이 없습니다' })
  const _id = req.params._id

  Article.findOne({ _id }).populate('_user', 'lv')
    .then(r => {
      if (!r) throw new Error('게시물이 존재하지 않습니다')
      if (r._user.toString() !== req.user._id) {
        if (r._user.lv < req.user.lv) throw new Error('본인이 작성한 게시물이 아닙니다')
      }
      return Article.deleteOne({ _id })
    })
    .then(r => {
      res.send({ success: true, d: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
```

- 손님(!req.user._id or req.user.lv ===3 )은 삭제가 안됩니다.
- 삭제가 가능한지 여부를 판단하기위해 찾습니다.
- 본인은 아니지만 권한이 높은 사용자는 삭제가 가능합니다.(_eg: lv1은 lv2,3 게시물을 삭제할 수 있음_)

# 소스

commit no: d395c0482e4332c182d5c1ba27c62c30148858bf

[소스 확인](https://github.com/fkkmemi/nemv3/commit/d395c0482e4332c182d5c1ba27c62c30148858bf)

- 해당 소스로 가려면

```bash
$ git checkout d395c0482e4332c182d5c1ba27c62c30148858bf
// or
$ git checkout d395c0
```

- 마지막 소스로 가려면

```bash
$ git checkout master
```

{% endraw %}

# 영상

{% include video id="5fU2BHJ72Gc" provider="youtube" %}
