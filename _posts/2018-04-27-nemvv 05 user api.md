---
layout: single
title: NEMVV 5 계정 api
category: nemvv
tag: [nemvv,api]
comments: true
---

설계된 모델을 이용해 xhr요청이 왔을때 데이터를 보내줄 수 있는 api를 만들어보겠습니다.

{% include toc %}

# api 구조

필요한 api는

- routes
    - api
        - auth
            - register: 회원가입
            - sign: 로그인,아웃
        - data
            -user: 계정확인등
    - check.js: middleware, 토큰체크

정도가 되겠습니다.

# 회원가입

**./routes/api/auth/register/ctrls.js**
```javascript
const User = require('../../../../models/users');
const gb = require('../../../../system/global');
const cfg = require('../../../../cfg/cfg');

exports.add = (req, res) => {
  const { id, email, pwd, name, birth } = req.body;

  if (id === undefined) return res.send({ success: false, msg: 'param err id' });
  if (email === undefined) return res.send({ success: false, msg: 'param err email' });
  if (pwd === undefined) return res.send({ success: false, msg: 'param err pwd' });
  if (name === undefined) return res.send({ success: false, msg: 'param err name' });

  const usr = {
    id: id,
    email: email,
    pwd: gb.f.encrypt(pwd),
    hidden: pwd,
    name: name,
    birth: birth,
  };

  const u = new User(usr);

  u.save()
    .then((r) => {
      let msg = '<h4>아래 링크를 클릭하면 계정이 활성화 됩니다.</h4>';
      msg += '<a href="http';
      if (cfg.web.https.use) msg += 's';
      msg += '://';
      msg += cfg.web.host;
      msg += '/api/auth/sign/act/';
      msg += r._id;
      msg += '">회원 가입 확인</a>';

      console.log(msg);

      gb.f.mail(
        cfg.mail.auth.user,
        r.email,
        `${cfg.web.host} 가입 확인 메일`,
        msg
      );
      res.send({ success: true });
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
};
```

- 받은 POST 요청으로 디비에 추가합니다.
- 중복되거나 잘못된 처리는 모델에 unique처리가 되어있으므로 u.save().catch가 잡아냅니다.
- 디비 저장후 가입 확인 메일을 보냅니다.
- 메일내용 링크 : eg) https://xxx.com/api/auth/sign/act/mongoId

# 가입체크

처음 가입한 회원은 act: false입니다. 로그인시 act가 true가 아니면 리젝상황으로 프론트를 작업해야합니다.

결국 메일 확인을 누르고 act를 true로 만들게 합니다.

**./routes/api/auth/sign/ctrls.js**

```javascript
exports.act = (req, res) => {
  const _id = req.params._id;

  if (_id === undefined) return res.redirect('/#/register');

  User.update({ _id: _id }, { $set: { act: true } })
    .then(() => { res.redirect('/#/sign'); })
    .catch(() => { res.redirect('/#/register'); })
};
```

- https://xxx.com/api/auth/sign/act/12345 라는 요청이 왔을때
- _id는 12345 입니다.
- 해당 아이디를 찾아서 act만 true로 변경하고 로그인 페이지로 이동합니다.
- 에러상황일때는 사실 다른 처리가 필요합니다. 적당히 수정하시면 됩니다.

> { _id: _id, pwd: pwd } 같은 코드는 뭔가 멍청해보이는데 es6에서는 { _id, _pwd } 로 줄여서 사용 가능하니 편한 방법으로 사용하면 됩니다.

# 로그인

**./routes/api/auth/sign/ctrls.js**

```javascript
exports.in = (req, res) => {
  const { id, pwd } = req.body;

  if (id === undefined) return res.send({ success: false, msg: 'param err id' });
  if (pwd === undefined) return res.send({ success: false, msg: 'param err pwd' });

  let usr = {};
  User.findOne()
    .where('id').equals(id)
    .then((r) => {
      if (!r) throw new Error('id not exists');
      if (gb.f.encrypt(pwd) !==  r.pwd) throw new Error('password diff');
      if (!r.act) throw new Error('email not confirmed');

      usr = {
        _id: r._id,
        id: r.id,
        email: r.email,
        name: r.name,
        lv: r.lv,
      };

      const secret = req.app.get('jwt-secret');
      const p = new Promise((resolve, reject) => {
        jwt.sign(
          usr,
          secret,
          {
            expiresIn: cfg.web.jwt.expiresIn,
            issuer: cfg.web.host,
            subject: 'user-token'
          }, (err, token) => {
            if (err) reject(err);
            resolve(token);
          })
      })
      return p;
    })
    .then((tk) => {
      res.set('WWW-Authenticate', tk);

      res.send({ success: true, d: usr });
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
};
```

- id 유무를 판단후 없으면 에러로 보냅니다.
- 들어온 패스워드를 암호화 시켜서 비교후 틀리면 에러로 보냅니다.
- 활성화 안되어 있는 경우 에러로 보냅니다.
- 프라미스를 사용해 유저정보가 담긴 데이터를 유효기간 만큼 존재할 토큰으로 만듭니다.
- 프론트는 받아서 쿠키든 어디든 로컬에 저장합니다.
- 토큰으로 만든 데이터를 헤더중 WWW-Authenticate에 저장합니다.
- 저장된 헤더와 함께 데이터가 usr 정보를 보냅니다.

> WWW-Authenticate는 원래 다른용도로 알고 있는데. 적당한 헤더를 찾지못해서 한번 써봤습니다.
데이터에 실어서 줄 수도 있는데 헤더에 실은 이유는 사용중 토큰 재발급 문제입니다.
예를 들어 사용자가 api로 이것저것 요청하고 있다 인증시간이 얼마 안남았을 때 미들웨어가 토큰을 줘야하는데 그 사이 하던 요청에 대한 응답을 주고 싶었기 때문입니다.
재발급 토큰을 헤더에 실어서 보내면 하던 작업이 멈춰지지 않습니다.
사실 뭔가 맘에 안드는데 혹시 좋은 아이디어가 있으면 의견 주세요..

> 코드 스타일은 개취인데 저같은 경우 예외상황부터 처리합니다.
현재 코드 스타일에 관심이 있으시면 문서를 참고하세요 > [예외처리 관련](/docs/exception-definition/)

# 로그아웃

**./routes/api/auth/sign/ctrls.js**

```javascript
exports.out = (req, res) => {
  res.send({ success: true });
};
```

사실 로그아웃은 api 요청을 할 필요가 없습니다.

프론트에서 저장된 토큰 파기하면 끝이니까요.

서버입장에서 사용자 행적 트랙킹을 위해 만들었습니다.

미들웨어에서 요청 받아서 디비에 저장하면 됩니다.