---
layout: single
title: NEMVV 2 전역파일
category: nemvv
tag: [nemvv,global,nodemailer,crypto]
comments: true
---

전역적으로 쓰고 싶은 기능을 모아봤습니다.

구성은 취향차이일 뿐이니 다른 방법을 쓰셔도 됩니다.

소스는 [https://github.com/fkkmemi/nemvv.git](https://github.com/fkkmemi/nemvv.git)에서 확인 할 수 있습니다.

{% include toc %}

# 주요 역활

- 전역변수: v 에 모두 담았습니다.

- 전역함수: f 에 모두 담았습니다.

# 함수들

## cfg.check

혹시나 cfg.js파일이 구버전이거나 잘못 작성되었을때 예외처리 용도

## encrypt

```javascript
  encrypt : (p) => {
    return crypto.createHmac('sha1', cfg.web.secret_key).update(p).digest('base64');
  },
```

cfg의 시크릿키를 이용해 암호화 합니다.

## mail

```javascript

  const transporter = nodemailer.createTransport(cfg.mail);

  mail: (from, to, subject, html) => {
    const m = {
      from: from,
      to: to,
      subject: subject,
      html: html,
    };
    transporter.sendMail(m, function(err, info){
      if (err) return console.error(err);
      console.log('Email sent: ' + info.response);
    });
  },
```

nodemailer를 이용해 메일을 보내는 함수입니다.

yarn add nodemailer를 이용해 설치할 수 있습니다.

google smtp를 사용할 수도 있는데 구글에서 레거시 전송을 체크해야합니다.

사실 저도 어디서 설정하는지 모르는데 구글로 해보니 메일이 왔습니다. 위험요소가 감지되었다고.. 그 링크를 타고가면 레거시 전송을 활성화할 수 있습니다.

# 테스트

playGround.js에 코드를 넣어서 테스트해봅니다.

## ./playGround.js
```javascript
const gb = require('./system/global');
exports.test = {
  model: () => {
    let s = '';
    s = gb.f.encrypt('1234');

    console.log(s);
    gb.f.mail('from@a.com', 'to@b.com', 'hi', '<h3>ok?</h3>');
  },
};
```