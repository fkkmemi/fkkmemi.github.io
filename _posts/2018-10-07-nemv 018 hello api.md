---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 18 api 설정하기
category: nemv
tag: [nemv,node,api,express]
comments: true
sidebar:
  nav: "nemv2"
---

깃헙에 올린 새 프로젝트에서 api 설정하는 것을 다시 정리해봅니다.

새로운 저장소니 지난 강좌를 가다듬는 정도로 보시면 됩니다.

{% include toc %}

{% raw %}

# 기존 소스 코드 정리

- pug 부분 삭제
- api 추가

# 예외 처리

**be/api/index.js** 
```javascript
var createError = require('http-errors'); //상단에 추가

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});
```

모르는 url, 예를 들어 api/abc 등이 들어오면 app.js에 있는 에러 라우터로 이동시킵니다.

바로 응답을 주지 않고 next에 createError를 실어서 에러 핸들러로 보내는 것이죠..


**be/app.js**  
```javascript
// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.send({ msg: err.message })
});
```

html 페이지를 주는 것이 아닌 JSON 데이터를 던져주는 것입니다.

{% endraw %}

# 소스

깃헙 소스는 아래 링크에 있습니다.

[https://github.com/fkkmemi/nemv3](https://github.com/fkkmemi/nemv3)

# 영상

영상에서 저장을 안하고 안된다고 실수하는 부분 양해바랍니다~

> 에디터에 자동저장 기능이 없어서 매번 해메이는 중..

{% include video id="uYsIADfSMNk" provider="youtube" %}  



