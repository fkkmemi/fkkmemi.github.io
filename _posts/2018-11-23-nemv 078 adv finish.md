---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 78 고급편 정리
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

지금까지 했던 것들을 복습 겸 정리해봅니다.

{% include toc %}

# 개요

활용해야 할 실제 서버의 프론트는 다 오픈됩니다.

공개소스이기 때문에 백엔드도 오픈되어있죠.

그래서 실제 서버로 가기 위한 최소한의 공사를 했다고 생각하면 됩니다.

# 과정

## 비밀번호 암호화 하기

비밀번호를 평문으로 저장하면 큰일나겠죠?

노드 기본 모듈인 crypto 와 scrypt를 이용해서 단방향 암호화시켜서 비밀번호를 보호합니다.

[바로가기](/nemv/nemv-059-crypto/)

## 뷰 컴포넌트 사용하기

코드, 화면을 모듈화 하여 재사용 합니다.

뷰티파이가 어떻게 이루어져 있는 지 확인해 볼 수 있습니다.

[바로가기](/nemv/nemv-060-vue-component)

## 시간 계산하기: 모먼트(moment.js) 맛보기

날짜/시간 라이브러리중 가장 유명한 moment.js에 대해 간단히 알아봅니다.
 
[바로가기](/nemv/nemv-061-moment/)

## 로그인 시한 제한하기(토큰 갱신)

공공장소에서 로그아웃을 까먹으면 큰일입니다.

토큰에 만료시간을 주고 재발급 토큰을 발급하는 방법을 만들어봅니다.

[바로가기](/nemv/nemv-062-refresh-token/)

## 게시판 관리 만들기

여러 게시판을 한 소스로 만들기 위해 게시판을 설계하고 관리를 해봅니다.

[바로가기](/nemv/nemv-063-board-manage/)

## 게시판 모델 만들기

게시판과 게시물을 이어주기 위한 관계에 대해 생각해보고 설계해봅니다.

[바로가기](/nemv/nemv-064-board-populate/)

## 게시판을 위한 메뉴 구성

뷰티파이의 그룹리스트를 이용해서 메뉴에 상하개념을 추가합니다.

[바로가기](/nemv/nemv-065-board-menu/)

## 게시판 정보 읽기

게시물을 가져오기 위해 먼저 게시판 정보를 읽어서 표현해봅니다.

[바로가기](/nemv/nemv-066-board-read/)

## 게시판 게시물 API 만들기

게시판 데이터를 제공하기 위한 API를 설계하고 만들어봅니다.

| 요청 유형 | 파라메터(params) | 내용(body or query) | 설명 |
| --- | --- | --- | --- |
| Create, POST | _board | 게시물 내용 | 어떤 게시판에 내용을 씀 |
| Read, GET | _board | 없음 | 어떤 게시판의 게시물들을 가져옴 |
| Update, PUT | _id | 수정된 게시물 내용 | 어떤 게시물의 내용을 변경 |
| Delete, DELETE | _id | 없음 | 어떤 게시물의 내용을 삭제 |      

[바로가기](/nemv/nemv-067-board-article-api/)

## 게시판 게시물 쓰고 읽기

게시물(article) API CRUD중 게시판 정보가 필요한 CR(Create Read)를 프론트에서 확인합니다.

[바로가기](/nemv/nemv-068-board-article-vue/)

## 게시판 뷰티파이 테이블 사용하기

뷰티파이의 데이터테이블(v-datatable)을 이용해서 화면에 표시합니다.

[바로가기](/nemv/nemv-069-board-datatable/)

## 게시판 내용 읽기

게시물의 리스트는 용량이 얼마 안되지만, 내용은 엄청 클 수 있습니다.

리스트와 내용을 분리해서 API를 적용해봅니다.

| 요청 유형 | 파라메터(params) | 내용(body or query) | 설명 |
| --- | --- | --- | --- |
| Create, POST | _board | 게시물 내용 | 어떤 게시판에 내용을 씀 |
| Read, GET | list/_board | 없음 | 어떤 게시판의 게시물 목록을 가져옴 new! |
| Read, GET | read/_id | 없음 | 어떤 게시판의 게시물 내용을 가져옴 new! |
| Update, PUT | _id | 수정된 게시물 내용 | 어떤 게시물의 내용을 변경 |
| Delete, DELETE | _id | 없음 | 어떤 게시물의 내용을 삭제 |   

[바로가기](/nemv/nemv-070-board-read/)

## 게시판 내용 수정과 삭제

게시물(article)을 선택하여 UD(Update Delete)를 프론트에서 구현해봅니다.

[바로가기](/nemv/nemv-071-board-ud/)

## 게시판 페이징과 정렬 처리하기

많은 양의 데이터를 처리하기 위해 페이징과 정렬을 구현해봅니다.

[바로가기](/nemv/nemv-072-board-paging/)

## 게시판 재활용하기(뷰라우터)

게시판 코드는 하나지만 뷰라우터를 통해 코드 재활용해서 다른 게시판을 운영합니다.

[바로가기](/nemv/nemv-073-board-multi/)

## 공용 알림 메세지 만들기(vuex)

모든 페이지에 있는 알림 메세지 처리를 한 곳에서 해봅니다.

[바로가기](/nemv/nemv-074-global-snackbar/)

## HTTP 예외처리 제대로 하기

임의로 만든 응답(모든 상태 200)이 아닌 HTTP 공식 에러코드를 사용해 상태(400, 401, 404등)와 함께 응답하게 만듭니다.

[바로가기](/nemv/nemv-075-http-error/)

## 반응형 게시판 만들기

모바일, 태블릿, 데스크탑등의 다양한 장에서 적절하게 표현되게 변경합니다.

[바로가기](/nemv/nemv-076-board-responsive/)

## 구글 리캡챠로 로봇 막아보기(recaptcha)

로봇(매크로)를 이용해 무한 게시물, 혹은 회원가입을 하는 요청을 막아봅니다.

[바로가기](/nemv/nemv-077-google-recaptcha/)

# 마치며

전혀 고급스럽지 않은 고급편이 끝났습니다.

고급편은 고급져서 고급편이 아닌.. 활용편 전 과정이라 이름 지어 본 것입니다.

이제 활용편에서는 코드를 서버에 올려두고 하다 둘씩 기능을 추가 할 것입니다.

서버에 올라갔다는 얘기는 모든 포트(80, 443)가 개방되어 공격의 대상이 되기도 한 것입니다.

완벽하지는 않지만 리캡챠나 토큰등으로 방어하며 서버에 올려볼 수준은 된 것입니다.  


# 사용해보기

테스트용 코드를 실제 서버에 포팅해봤습니다. 

[https://fkkmemi.com](https://fkkmemi.com)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3)

# 영상

{% include video id="-XuKWjEXaVA" provider="youtube" %}

