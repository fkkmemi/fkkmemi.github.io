---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 8 익스프레스와 뷰티파이 연동
category: nemv
tag: [nemv,node,express,vue,vuetify]
comments: true
sidebar:
  nav: "nemv"
---

익스프레스(백엔드서버)와 뷰티파이 연동하기

# 프론트 빌드하기

```bash
$ cd fe
$ yarn build
```

빌드를 하면 fe/dist에 정리된 파일들이 나옵니다.

# 백엔드서버의 퍼블릭 위치를 프론트엔드 내용으로 변경 

정적으로 지정된 기본 public 디렉토리를  위의 빌드된 디렉토리 위치로 옮겨줍니다.

**app.js**  
```javascript
// app.use(express.static(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, 'fe', 'dist')));
```

이제 http://localhost:3000을 들어가면 프론트 페이지가 나옵니다.

# 백엔드 라우팅에 대한 설명

# API 서버 만들기

# 영상

{% include video id="uQk6ozqxQYQ" provider="youtube" %}   


 