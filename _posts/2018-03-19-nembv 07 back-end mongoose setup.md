---
layout: single
title: NEMBV 7 Back-end mongoose setup
category: nembv
tag: [nembv,nodejs,mongodb,mongoose]
comments: true
sidebar:
  nav: "nembv"
---

> 이 강좌는 종료되었습니다.  
새로운 강좌로 시작하세요~  
[모던웹(NEMV) 제작 강좌](/nemv/){: .btn .btn--success}  

이제 백엔드에서 디비에 직접 연결해보도록 한다.

기본적인 mongoClient도 있으나 래핑한 mongoose를 이용하도록 하겠다.

{% include toc %}

# mongoose 설치

- mongoose를 설치하고 package.json에 기록한다.

```bash
$ npm i mongoose --save
```

- connection string

mongodb에 접속하기 위한 문자열은 아래와 같다.

mongodb://id:password@shell연결시들어간샤드주소들/디비이름?ssl=true&replicaSet=Cluster0-shard-0&authSource=admin

- 연결 확인 : app.js 하단에 아래의 코드를 넣고 npm start를 해본다.

```javascript
// ..중략
  res.send({ success: false, msg: err.message });
});

const mongoose = require('mongoose');
const url = 'mongodb://nembv:비밀번호@cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017/nembv?ssl=true&replicaSet=Cluster0-xxx&authSource=admin';
mongoose.connect(url, (err) => {
  if(err) return console.error(err);
  console.log('mongoose connected');
});

module.exports = app;
```

mongoose conncected 가 뜨면 연결 성공

# config file

디비 연결스트링은 비밀번호가 들어 있기 때문에 코드에 밖기에는 어색하고 다른 디비로 변경시에도 불편하다.

그래서 git에 관리되어지지 않는 cfg/cfg.js 를 만들어서 로드해보도록 하겠다.

- 우선 깃에서 무시되도록 cfg/ 등록

.gitignore

```yaml
# cfg files
cfg/
```

- /cfg/cfg.js

```javascript
module.exports = {
    db: {
        url: 'mongodb://nembv:비밀번호@cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017/nembv?ssl=true&replicaSet=Cluster0-xxx&authSource=admin'
        // url : "mongodb://xxx.com:27170/xxx"
        // url : 'mongodb+srv://id:pwd@cluster0-xxx.net/yyy' // 3.6이상
    },    
    web: {
      // 추후 http, https, port등 
    },
};
```

> cfg.json 보다 module이 나은 이유는 위처럼 주석 처리도 가능하고 따옴표등도 통일성 있게 가능하며 computed된 작은 함수들도 넣을 수 있기 때문

- db 연결 실제 사용

```javascript
const mongoose = require('mongoose');
const cfg = require('./cfg/cfg');

if (!cfg) {
  console.error('./cfg/cfg.js file not exists');
  process.exit(1);
}

mongoose.connect(cfg.db.url, (err) => {
  if (err) return console.error(err);
  console.log('mongoose connected');
});
```
cfg 파일이 없을 경우에 대한 예외 처리를 해준다. 

커넥션 풀은 한번 연결하면 매번 연결 종료가 필요 없으며 장시간 미연결시 끊어지지만 요청에 의해 쿼리가 들어가면 자동 연결된다.

- README.md 작성  
    cfg.js 파일은 git에 없기 때문에 분명 까먹는다. [마크다운](/github/markdown/) 문법도 익힐겸 작성해놓는다.
    
```markdown
# nembv
Node Express Mongo Bootstrap Vue Stack

## config file definition

    **cfg/cfg.js**

    ```javascript
    module.exports = {
      db: {
        url: 'mongodb://nembv:비밀번호@cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017,cluster0-xxx.mongodb.net:27017/nembv?ssl=true&replicaSet=Cluster0-xxx&authSource=admin',
        // url : "mongodb://xxx.com:27170/xxx"
        // url : 'mongodb+srv://id:pwd@cluster0-xxx.net/yyy' // 3.6이상
      },
      web: {
        // 추후 http, https, port등 
      },
    };
    ```
``` 


