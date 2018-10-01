---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 5 익스프레스 라우터
category: nemv
tag: [nemv,node,express]
comments: true
sidebar:
  nav: "nemv"
---

생성된 익스프레스 소스에 라우터, 페이지를 추가해봅니다.

# 익스프레스 라우터 추가

http://localhost:3000/users에 가면

respond with a resource 라는 텍스트가 있습니다.

그 이유는

**app.js**  
```javascript
var usersRouter = require('./routes/users');
app.use('/api', usersRouter);
```

**routes/users.js**  
```javascript
router.get('/', function(req, res, next) {
  res.send('respond with a resource');
});
```

이렇게 라우터가 구현되어 있기 때문입니다.

라우터 추가는 비슷하게 따라해보면 되겠죠?

**app.js**  
```javascript
var set = require('./routes/set');
app.use('/api', set);
```

**routes/set.js**  
```javascript
router.get('/', function(req, res, next) {
  res.send('나는 셋라우터입니다.');
});
```

http://localhost:3000/set 으로 가면 라우터 추가가 잘 된 것을 확인할 수 있습니다.

# 스태틱라우터란: 정적인 파일

app.use(express.static(path.join(__dirname, 'public')));

이 항목은 public 디렉토리를 정적인 파일들을 담을 수 있는 그릇으로 만든 것입니다.

public 디렉토리에 아래의 index.html을 만들어 넣게 되면?

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

http://localhost:3000을 들어가보면 hi?가 있다는 것을 확인 할 수 있습니다.

그러면 원래 있던 welcome to express는 어떻게 된것일까요?

# 라우트 우선순위

라우트 우선순위는 선언 순이며 그중 하나가 응답을 해버리면 더이상 아래로 내려가지 않습니다.

http프로토콜 자체가 요청과 응답이 한 쌍으로 이루어져있습니다.

요청이 왔을 때 두번 응답할 수 없습니다.

응답후 이미 연결은 종료된 상태이기 때문이죠..

현재 라우트 순서는

1. public
2. /
3. /users
4. /알수 없는 페이지

와 같습니다.

public에 index.html을 만들어 버렸기 때문에(응답해버림) 2번 항목으로 내려가지 못한것입니다.

위의 라우터중 갈 곳이 없다면 맨 마지막 라우터 즉 알 수 없는 페이지인 404로 가게 됩니다. 

```javascript
// catch 404 and forward to error handler
app.use(function(req, res, next) {
  next(createError(404));
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.render('error');
});
```

views/error.pug를 렌더링 하게 되는 것이죠.

# 영상

{% include video id="IjUVBIOLSZ4" provider="youtube" %}   


 