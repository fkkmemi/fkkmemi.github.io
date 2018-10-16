---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 8 익스프레스와 뷰티파이 연동
category: nemv
tag: [nemv,node,express,vue,vuetify]
comments: true
sidebar:
  nav: "nemv"
---

http://localhost:8080은 뷰 개발모드입니다.

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

# 정리

- 서버의 화면 처리는 정적 라우터(fe/dist)만 있으면 된다.
- 화면 안에서 내부 요청은 html이 아닌 API로 데이터(JSON)만 건네준다.
- 라우터에 등록되어 있지 않거나 에러가 나면 app.js 최하단의 에러처리 라우터로 간다.   

# 영상

{% include video id="uQk6ozqxQYQ" provider="youtube" %}   


 