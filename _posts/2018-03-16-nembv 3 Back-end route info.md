---
layout: single
title: NEMBV 3 Back-end route 이해
category: nembv
tag: [nembv,express,nodejs,pug]
comments: true
---

express의 라우트 개념을 알고 있으면 다음 챕터로 이동하면 된다.

지난번의 http://localhost:8080 로 표출된 화면은 프론트 개발용도이며 실제 사용할 서버는 익스프레스의 http://localhost:3000 이다.

현재 디렉토리는 

```bash
/..
/bin
/fe
/public
/route
app.js  
package.json

```
    
이런 구성일 것이다.

현재 루트 디렉토리 에서 **npm start** 입력후 http://localhost:3000 으로 들어가보면 아래와 같은 화면인데 어떻게 작동 된 것인지 알아보겠다. 

![browser](/images/nembv/Express 2018-03-16 13-53-54.png)  

### node 사용법

node의 사용법은 간단히 

```bash
$ node 파일명
```

이다

파일을 하나 만들어 보자

**a.test**  
```javascript
const a = 3, b = 2;

console.log('= ' + (a + b));
```

그후 

```bash
$ node a.test
= 5
```

### express 구동원리

마찬가지로 'npm start' 는 곧 'node bin/www' 이기 때문에 www라는 파일로 실행을 한것이다.

www파일은 굳이 건들 필요가 없고 중요 부분만 발췌해봤다.

**bin/www**
```javascript
var app = require('../app');  
var debug = require('debug')('nembv:server'); 
var http = require('http'); 

var port = normalizePort(process.env.PORT || '3000'); // 3000 포트를 사용하겠다는 것이다.
app.set('port', port);

var server = http.createServer(app); 

server.listen(port); 
```

- import나 require를 할때는 .js를 생략해도 된다. 최상단에 있는 app.js를 불러온다
- 3000 포트를 사용하서 웹서비스를 시작 한것이다

결국 3000 포트를 사용하는 단순한 웹서버가 구동된 것이며 중요한 파일은 app.js에 다 있다는 말이다

app.js중 중요한 부분만 짚어보겠다.

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

- path라는 모듈을 쓰는 이유는 윈도우, unix등의 호환 문제 때문이다.  
    window: \bin\www  
    unix: /bin/www
- route의 [index, users]라는 파일을 각 [/,/users] 로 라우팅을 한것이다.  
    localhost:3000  
    localhost:3000/users 
- 만일 없는 패스를 지정하면 어떻게 될까?  
    localhost:3000/xxx -> 많이 들어본 404 페이지가 나온다

#### pug?

index.js를 보면 res.render('index', { title: 'Express' }); 항목은

views/index.pug를 사용해 파일(index.html)을 만들었다고 생각하면된다.

pug는 html을 편리하게 쓸 수 있는 툴이라고 생각하면된다.

주로 서버사이드에서 판단해서 페이지를 다르게 주고 싶을 때 사용한다.

현재 프로젝트에서는 vue를 이용해 정적인 페이지를 사용할 것이기 때문에 필요가 없다.

필요할 경우 하루 정도 스터디하면된다.

#### 정적인 파일

app.use(express.static(path.join(__dirname, 'public')));

이 항목은 public 디렉토리를 정적인 파일들을 담을 수 있는 그릇으로 만들어준다.

public 디렉토리에 아래의 index.html을 만들어 넣어보자.

**public/index.html**  
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    hi?
</body>
</html>
```

```bash
$ npm start
```

http://localhost:3000을 들어가보면 hi?가 있다는 것을 알 수 있다.

그러면 원래 있던 welcome to express는 어떻게 된것일까?

#### 라우트 우선순위

라우트 우선순위는 선언 순이며 그중 하나가 응답을 해버리면 더이상 아래로 내려가지 않는다.

현재 라우트 순서는

1. public
2. /
3. /users
4. /알수 없는 페이지

와 같다.

public에 index.html을 만들어 버렸기 때문에(응답해버림) 2번 항목으로 내려가지 못한것이다.