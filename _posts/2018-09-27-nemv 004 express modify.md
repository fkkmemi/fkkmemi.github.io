---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 4 익스프레스 수정
category: nemv
tag: [nemv,node,express]
comments: true
sidebar:
  nav: "nemv1"
---

생성된 익스프레스 소스를 수정해서 웹서버 동작 확인해보겠습니다.

{% include toc %}

# 익스프레스 구동 원리 확인

노드의 실행은 곧 node 어쩌고.js 인데 어떻게 실행되는 것일까요?

expressjs.com 에선 이렇게 하라고 하죠

```bash
$ DEBUG=be:* npm start
```

그러면 3000포트로 http서버가 열려서 브라우저에서 http://localhost:3000 을 입력하면 됩니다.

npm start라는 것은 무엇일까요?

그 비밀은 package.json이라는 파일에 있습니다.

파일을 열어보면

```javascript
"scripts": {
  "start": "node ./bin/www"
},
```

이 부분이 npm start 입니다.

결국 node ./bin/www 가 되는 것이죠.

특이하게도 확장자가 없이 만들어 놨는데 결국 자바스크립트 파일 입니다. 

http서버를 여는 부분일 뿐이고 실제 중요한 파일은 app.js가 담당합니다.

# 익스프레스 수정

**app.js**  
```javascript
var express = require('express');
var path = require('path');

var index = require('./routes/index');
var users = require('./routes/users');

var app = express();

// view engine setup
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

app.use(express.static(path.join(__dirname, 'public')));

app.use('/', index);
app.use('/users', users);

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // render the error page
  res.status(err.status || 500);
  res.render('error');
});

module.exports = app;
```

- path라는 모듈을 쓰는 이유는 윈도우, unix등의 호환 문제 때문입니다. 
    window: \bin\www  
    unix: /bin/www
- route의 [index, users]라는 파일을 각 [/,/users] 로 라우팅을 한것입니다.  
    localhost:3000  
    localhost:3000/users 
- 만일 없는 패스를 지정하면 어떻게 될까?  
    localhost:3000/xxx -> 많이 들어본 404 페이지가 나옵니다.
    
# pug란 무엇일까?

index.js를 보면 res.render('index', { title: 'Express' }); 항목은

views/index.pug를 사용해 파일(index.html)을 만들었다고 생각하면 됩니다.

pug는 html을 편리하게 쓸 수 있는 툴이라고 보시면 됩니다.

주로 서버사이드에서 판단해서 페이지를 다르게 주고 싶을 때 사용하게 됩니다.(_php와 비슷합니다._)

현재 프로젝트에서는 vue를 이용해 정적인 페이지를 사용할 것이기 때문에 필요가 없습니다.

어떤 동작을 하는지 정도만 파악하면 될 것 같습니다.

# 영상

{% include video id="9bhcV0_PPB0" provider="youtube" %}   


 