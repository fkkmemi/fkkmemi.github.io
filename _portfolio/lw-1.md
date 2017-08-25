---
title: "LW(LOOP Web) V1"
excerpt: "디자인 개선을 위한 노력"
author_profile: false
header:
  teaser: /images/pf/lw1/3.png
sidebar:
  - title: "Date"
    text: "11.2015"
  - title: platform
    text: "**Server** : *cloud vm Window2012*<br>
    **Web server** : *IIS*<br>
    **Communcation server** : *Window Application*<br>
    **Server script** : *PHP*<br>
    **Database** : *MySQL*<br>
    **Raw data** : *File*<br>
    **front-end** : jquery ui, wordpress<br>
    **devices** : *DTG, GPS-tracker*<br>"
gallery:
  - url: /images/pf/lw1/1.png
    image_path: /images/pf/lw1/1.png
    alt: "main"
  - url: /images/pf/lw1/2.png
    image_path: /images/pf/lw1/2.png
    alt: "login form"
  - url: /images/pf/lw1/3.png
    image_path: /images/pf/lw1/3.png
    alt: "recent data"
  - url: /images/pf/lw1/4.png
    image_path: /images/pf/lw1/4.png
    alt: "track map"
  - url: /images/pf/lw1/5.png
    image_path: /images/pf/lw1/5.png
    alt: "track raw"
  - url: /images/pf/lw1/6.png
    image_path: /images/pf/lw1/6.png
    alt: "report"
  - url: /images/pf/lw1/7.png
    image_path: /images/pf/lw1/7.png
    alt: "setting devices"
  - url: /images/pf/lw1/8.png
    image_path: /images/pf/lw1/8.png
    alt: "setting companies"
  - url: /images/pf/lw1/9.png
    image_path: /images/pf/lw1/9.png
    alt: "setting users"
---

워드프레스 커스터마이징으로 리뉴얼 도전

{% include gallery caption="워드프레스 콜라보" %}

{% include toc %}

## 개요

단순 구현에만 목적을 두었던 것인데 역시 상용화 하기엔 디자인 부터 무리가 있었다.

html,css 모두 수준이하였던 나는 고민에 빠져있었다.. 

어느날 워드프레스 블로그 테마를 구경하다가 번뜩이는 아이디어가 떠올랐다..

바로 '미려한 워드프레스 페이지 UI에 안에 컨텐츠를 담으면 어떨까?'

당장 20불짜리 어울리는 테마를 구입하고 사건은 시작되었다.. 

## 구현

페이지 안에 php 코드를 쓰기 위해 플러그인을 설치하여 php조각을 포함(include)하여 구현하였다.

{% include figure image_path="/images/pf/lw1/wp1.png" alt="wordpress edit php" caption="페이지에 php 조각을 넣음" %} 

반응형 테마였기 때문에 모바일에서도 잘 동작했다.

## 문제

시작은 좋았으나 곳곳에서 문제가 터지기 시작하는데..

- 무거움  
    페이지 로드가 느렸다.. 물론 내가 만든 조각 php코드도 엉망이었겠지만  
    빈페이지 자체가 느린 걸 봐선 워드프레스 본연의 문제 같았다.
- css  
    워드프레스의 목적처럼 단순 블로그라면 문제될 것이 없지만..  
    고객은 의외로 단순하며 심플한 주문을 한다. eg) 글짜 크기, 색상등  
    사용자 css를 오버라이드 해서 구현해야하는데 다 엮여있어서 조금만 수정해도 엉망이 되기 일쑤이다.  
    덕분에 css study는 많이 했지만..
- javascript  
    글 안에 javascript를 포함하려면 php와 마찬가지로 플러그인을 설치해야하는데..  
    조금 틀린건 javascript 조각은 페이지 로드 완료 후 실행되어있는데  
    글에 삽입하는 것이 아닌 플러그인 세팅쪽 메뉴에가서 페이지마다 다르게 복잡하게 넣어야했다..  

{% include figure image_path="/images/pf/lw1/wp2.png" alt="wordpress edit javascript" caption="페이지에 javascript 조각을 넣음 극혐.." %} 

- 배포  
    당시 deploy에 대한 개념이 전혀 없었는데..  
    *ftp 접속해서 직접 코드를 수정하고 svn에 커밋하고..*  
    테스트 서버든 다른 서버든 배포하려면 워드프레스를 설치해야했다..  
    문제는 각종 플러그인들 설치와 계정 추가 등등 버전 차이도 나고 그야말로 멘붕,극혐..  
- 백업
    php조각파일들과 mysql 테이블들을 주기적으로 백업해야했는데 글들에는 키값들이 있어서 덮어쓰기도 안되고..  
    백업 툴이 있긴 했지만 복잡하고 지저분했다..
    
## 평가

유지보수하기 너무 힘든 상황이었지만..  
내부적인 문제는 개발자의 고충일 뿐..  
안은 죄다 썩어있었지만 겉에서 보기엔 그럭저럭 두어달 작업한 것 치곤 괜찮다는 편이었다..

## 의미

다양한 워드프레스의 문제 때문에..  정신적으로 힘들었지만..

jquery라는 것을 처음 보고 jquery-ui로 그럭저럭 괜찮은 버튼,표등 나와서 적용하는 즐거움이 있었다..

ajax라는 것을 처음 알고 되도록 페이지 전환이 없게 코딩하느라 애를 썼다.
    
## 결론

워드프레스는 블로그 저작툴이다.. 용도에 맞게 써야한다..