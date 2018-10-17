---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 8 익스프레스와 뷰티파이 연동
category: nemv
tag: [nemv,node,express,vue,vuetify]
comments: true
sidebar:
  nav: "nemv1"
---

전 강좌에서 yarn serve로 실행한 http://localhost:8080은 뷰 개발모드입니다.

실제 구현되어야할 곳은 백엔드가 구동하는 http://localhost:3000에서 돌아가야 합니다.

# 프론트 빌드하기

```bash
$ cd fe
$ yarn build
```

빌드를 하면 fe/dist에 깔끔하게 압축되어 있는 js파일들과 css등의 정리된 파일들이 나옵니다.

![alt fe/dist](/images/nemv/스크린샷 2018-10-16 오후 3.13.09.png)

# 백엔드서버의 퍼블릭 위치를 프론트엔드 내용으로 변경 

정적으로 지정된 기본 public 디렉토리를  위의 빌드된 디렉토리 위치로 옮겨줍니다.

**app.js**  
```javascript
// app.use(express.static(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'fe', 'dist')));
```

기존 public을 fe/dist로 변경해주면 끝납니다.

이제 http://localhost:3000을 들어가면 프론트 페이지가 나옵니다.

# 백엔드 라우팅에 대한 설명

스태틱(static)이란 것은 정적이다, 즉 동적이 아니라는 것입니다.

- 정적인 라우터의 예: /image/1.png /about.html
- 동적인 라우터의 예: /user/xxx /user/yyy

정적인 라우터는 정해진 곳에 파일이 있다면 응답으로 그 파일을 전송하는 것이고,

동적인 라우터는 주소가 변경됨에 따라 다른 파일 혹은 내용을 전송하는 것입니다.

# API 서버 만들기

정적인 파일 라우트 위에 api를 만들어 줍니다.

**app.js**  
```javascript
app.use('/api', require('./routes/api'))
app.use(express.static(path.join(__dirname, 'fe', 'dist')));
```

**routes/api/index.js**  
```javascript
var express = require('express');
var router = express.Router();

router.get('/', function(req, res, next) {
  res.send('나는 api입니다.');
});

module.exports = router;
```

백엔드서버를 다시 구동(npm start) 후, 브라우저에서 http://localhost:3000/api 를 입력해보면 "나는 api입니다."가 잘 나오는 것을 확인 할 수 있습니다.

이제 /api/어쩌고 로 들어오면 우선 처리해주고 /xxy/ 같은 모르는 패스가 들어오면 정적인 라우팅을 해주게 됩니다.

정적인 라우터도 모르는 곳이면 더 아래에 있는 예외처리 라우터로 넘어가서 우리가 흔히 보는 404 에러를 보내주는 것입니다.

# 정리

- 서버의 화면 처리는 정적 라우터(fe/dist)만 있으면 된다.
- 화면 안에서 내부 요청은 html이 아닌 API로 데이터(JSON)만 건네준다.
- 라우터에 등록되어 있지 않거나 에러가 나면 app.js 최하단의 에러처리 라우터로 간다.   

# 영상

{% include video id="uQk6ozqxQYQ" provider="youtube" %}   


 