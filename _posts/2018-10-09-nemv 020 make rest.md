---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 20 RESTful한 api 만들기
category: nemv
tag: [nemv,api,express,vue,axios]
comments: true
sidebar:
  nav: "nemv"
---

REST(Representational State Transfer)는 구글링해보면 잘 나와 있지만 대체 뭔소린지 알아먹기가 힘드실 겁니다.

사실 uri(주소창에 /로 이어지는 경로)로 통신하는 방법을 "이렇게 하는 게 어때?" 정도의 가이드라인일 뿐입니다.

그래서 RESTful하다는 형용사에 겁 먹을 필요 없습니다.

실제 사용해보면 별 것도 없습니다.

# CRUD와 비교표

결국 통신으로 읽고 쓰고 수정하고 지울 뿐이죠..

CRUD를 HTTP로는 이렇게 하자는 게 결국 REST죠

| name	| desc. |	db  | http |
| --- | --- | --- | --- |
| Create	| 생성 |	INSERT | POST  |
| Read(또는 Retrieve)	 | 읽기(또는 인출) | 	SELECT | GET |
| Update | 갱신 | UPDATE | PUT |
| Delete(또는 Destroy)	| 삭제(또는 파괴) | 	DELETE | DELETE |   
    
# 왜 REST REST 난리인가

지금은 REST가 대중적으로 정립이 된 개념이지만.. 

자유도가 높은 웹세상에서 과거에는 엉망으로 써왔기 때문이죠..

put delete는 사용조차 하지 않았고.. 사용자 추가를 get으로 하거나 post로 데이터를 읽어오거나 한것이죠..

뭐 강제성은 없으니까요..

최근에는 어떤 강좌, 책을 읽어봐도 비슷하게 작성되서 프로토콜이 필요 없을 정도이죠..

# REST api 만들기

get post put delete만 다룹니다.

사실 업데이트는 put patch 개념이 조금 다른데.. 뭐 나중에 필요를 느끼시면 알아보고 메쏘드 하나 추가하시면 됩니다.

# 프론트 요청 ui 만들기

레스트 테스트는 크롬 확장앱인 포스트맨이 최고입니다.

근데 전 안씁니다..

굳이..

공부도 할겸 직접 만듭시다.

# 영상

{% include video id="yVCP-nohVSc" provider="youtube" %}  


