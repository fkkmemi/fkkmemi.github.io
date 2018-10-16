---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 9 뷰티파이 수정
category: nemv
tag: [nemv,node,express,vue,vuetify]
comments: true
sidebar:
  nav: "nemv"
---

뷰티파이 기본 설치된 파일을 수정해서 브라우저로 확인해보기

# 뷰티파이 구조 살펴보기

화면 변경/추가를 위한 최소한의 구조를 알아봅니다.

- dist: 빌드된 파일들
- public: 이미지등의 공용 자료들과 맨 처음 로드되는 index.html이 있습니다.
- src/views: 페이지들
- app.vue: 메뉴 포함된 시작 화면
- main.js: 시작 스크립트들

# 아톰에서 뷰도우미 설치하기

아톰에서 Preferences/install 에 쓸만한 것들 2개를 설치해봤습니다.

- .vue 파일 색상 및 코드 보기 좋게 해주기
![alt vue-language](/images/nemv/스크린샷 2018-10-08 오후 3.50.22.png)

- 에디터 하단에 터미널 띄우기
![alt terminal](/images/nemv/스크린샷 2018-10-08 오후 3.53.19.png)

# 뷰 간단한 설명

html 코드와 script의 데이터 연동 정도만 생각하면 됩니다.

# html 레이아웃

요약하면 index.html 안에 app.vue(메뉴등의 테두리) 안에 views/home.vue 가 있는 것입니다.

# 뷰 라우터

router.js에 등록 되어 있는 파일만 열립니다.

현재 등록된 페이지는 home.vue, about.vue 2개 입니다.

- src/views/home.vue: http://localhost:8080/ 

- src/views/about.vue: http://localhost:8080/about

# 영상

자세한 것은 영상을 보며 확인하세요~

{% include video id="WQ_GDbG-mvA" provider="youtube" %}   

 