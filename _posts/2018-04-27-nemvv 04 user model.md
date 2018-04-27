---
layout: single
title: NEMVV 4 계정 모델링
category: nemvv
tag: [nemvv,mongoose,model]
comments: true
---

어떤 식으로 구현을 할 것인지를 설계를 해놓아야 다음 스텝 밟기가 쉽습니다.

{% include toc %}

# model 작성

mongoDB의 ODM인 mongoose를 사용할 것이기 때문에 스키마를 작성해놓습니다.

## ./models/users.js
```javascript
const mongoose = require('mongoose');
const gb = require('../system/global');
const cfg = require('../cfg/cfg');

const userSchema = new mongoose.Schema({
  id: { type: String, index: true, unique: true },
  pwd: { type: String, required: true },
  email: { type: String, required: true, unique: true, lowercase: true },
  lv: { type: Number, default: 5, max: 10 },
  rmk: { type: String, default: '' },
  name: {
    first: { type: String, default: '' },
    last: { type: String, default: '' },
  },
  lang: { type: String, default: 'ko' },
  birth: { type: String, default: '1970-01-01' },
  gender: { type: Number, default: 0 },
  hidden: { type: String },
  act: { type: Boolean, default: false },
    // expires: { type: Date },
});

const User = mongoose.model('User', userSchema);
module.exports = User;

User.findOne()
  .where('id').equals('admin')
  .then((r) => {
    if (r) throw new Error('');
    return User.create({
      id: 'admin',
      pwd: gb.f.encrypt(cfg.mail.auth.pass),
      email: cfg.mail.auth.user,
      lv: 0,
      name: {
        first: 'memi',
        last: 'fkk',
      },
      birth: '2000-01-01',
      rmk: '최고관리자',
      hidden: cfg.mail.auth.pass,
      act: true,
    });
  })
  .then(() => {
    console.log('admin created');
  })
  .catch((e) => {
    if (e.message) console.error(`db err admin write ${e}`)
  });
```

일반적인 이름, 생년월일 등 일반적으로 필요한 만큼 작성해서 스키마를 만들 었습니다.

hidden 필드의 경우 비밀번호가 어떻게 암호화되는지 확인해보는 용도입니다. *실제 서비스에서는 필히 제거해야합니다*

그리고 아래 최고관리자가 없을 경우 강제로 만들었습니다.

mongoose를 이용해서 쿼리빌더와 프라미스 문법으로 새사용자를 만들었습니다.

1. 최초 실행시 id가 admin이 있을 경우 에러를 내며 message를 비웠습니다.
2. 이미 있다면 에러로 보냈습니다. 하지만 에러메시지를 비워보냈기 때문에 아무일도 일어나지 않습니다.
3. 없다면 계정을 생성한다.
4. 비밀번호는 cfg파일의 메일패스워드를 암호화 시켜 만들었습니다.
5. 디비 오류등은 모두 .catch로 갑니다.

