---
title: "LW V3"
excerpt: "완전 새로운 도전"
author_profile: false
header:
  teaser: /images/pf/lw3/3.png
sidebar:
  - title: "Date"
    text: "5.2016"
  - title: platform
    text: "**Server** : cloud vm CentOS<br>
    **Web server** : node express<br>
    **Communcation server** : node<br>
    **Server script** : node jade<br>
    **Database** : mongoDB<br>
    **Raw data** : mongoDB<br>
    **front-end** : bootstrap<br>
    **devices** : *DTG, GPS-tracker, mobile(android)*<br>"
gallery:
  - url: /images/pf/lw3/1.png
    image_path: /images/pf/lw3/1.png
    alt: "main"
  - url: /images/pf/lw3/2.png
    image_path: /images/pf/lw3/2.png
    alt: "login form"
  - url: /images/pf/lw3/3.png
    image_path: /images/pf/lw3/3.png
    alt: "recent data"
  - url: /images/pf/lw3/4.png
    image_path: /images/pf/lw3/4.png
    alt: "track map"
  - url: /images/pf/lw3/5.png
    image_path: /images/pf/lw3/5.png
    alt: "track raw"
  - url: /images/pf/lw3/6.png
    image_path: /images/pf/lw3/6.png
    alt: "report"
  - url: /images/pf/lw3/7.png
    image_path: /images/pf/lw3/7.png
    alt: "setting devices"
  - url: /images/pf/lw3/8.png
    image_path: /images/pf/lw3/8.png
    alt: "setting companies"
  - url: /images/pf/lw3/9.png
    image_path: /images/pf/lw3/9.png
    alt: "setting users"

---

node.js, mongoDB, mobile app으로 관제를 구현

{% include gallery caption="node의 시작" %}

{% include toc %}

## 시작 전

모바일 앱 개발을 시작하여 한달만에 안드로이드로 위치정보를 기존 사이트 프로토콜에 맞춰 전송하는 데 성공하였다..

원래 윈도우 어플리케이션 코딩스타일이 클래스 중심에 UI와 비즈니스 쓰레드코드는 분리해서 코딩했기 때문에 c++ to java에 그다지 이질감이 없었다..

딱 위치전송 초데이터 까지만 했다..
  
백그라운드 동작은 제약 때문에 쉽지 않았다..
  
UI가 맘에 안들었고 플레이스토어 배포까지 하려니 혼자서는 무리가 있음을 깨닫고 가능성만 열어둔채 소흘해진다..(벡터아이콘 같은 사소한게 발목을 잡는다)

근본적인 이유는 사실 ios 개발이 더 하고 싶었고..  
> 핸드폰도 데스크탑도 랩탑도 전부 애플인데..  안드로이드 태블릿,폰등 지원해주긴 했지만..

당시 apple 개발자 등록이 지연되서 안드로이드를 먼저 했을 뿐..

## 시작

기존 사이트는 후임에게 넘겨두고... 재밌는 것을 찾아 해메이다 node.js, mongoDB 조금씩 손대기 시작한다..

하다보니 너무 마음에 들어서 새로운 솔루션을 만들려고 할때 마침..

새로운 프로젝트가 떨어졌다..

안드로이드 앱으로 위치정보와 비콘위치를 수집해서 데이터를 서버로 날리고 웹과 앱에서 열람이 가능하며 비콘이 중간에 디텍이 떨어지면 푸쉬 알림까지 되야하는 프로젝트...

당연히 기존 사이트에 기능을 추가하길 모두가 바랬지만..

모든 것에 자신감이 넘치던 이때 아예 새롭게 linux, node, mongo, android등으로 3개월 이내에 만들겠다고 키노트 선포하고..

고나 헬로 가게 되는데...

## 구현

#### 리눅스

윈도우에 서버를 구성하는 게 왠지 후져보이기도 했다... 거품(괜한 UI) 없이 라이트한 리눅스가 이치에 맞다고 생각했다.. 

제대로 서버를 구성하기는 처음해보지만 OS는 어짜피 사용자가 편하게 쓰게 하기위한 시스템이라고 생각하고 쉬울 거라고만 생각했다..

그것은 오산.. 

mongodb, nodejs 까는 것 자체도 힘들었고..

기본 명령어도 잘 모르는 데다가 일부 vi로 컨픽 잡는건 고역이었다.. 

#### 데이터베이스

무식하게 접근해서 무식한 컬렉션(테이블)이 나왔다..

컬렉션 구상만 이리해보고 저리해보고 괜히 새롭게 만들다 시간만 날리고 스킬도 오르지 않았다..

noSQL은 다량의 로데이터에 무적일 줄알았지만.. 키도 제대로 설정안되어있는 상태에서는 퍼포먼스가 현저히 떨어졌다..

#### 수신서버

[https://nodejs.org/](https://nodejs.org/) 레퍼런스를 하나씩 정독하며 소켓서버를 만드는데..

윈도우 어플리케이션 서버만 하다가 처음으로 상용 서버를 만들어야되는데...  
>  파이썬이나 루비는 연습만 조금 해본 정도..

node의 net module과 javascript의 특성등을 이해하지 못해 파싱에 어려움이 많았다..

#### 라우터

node express의 라우터를 제대로 인지하지 못한 채 막 시작했더니 url이 꼬이고..

#### 렌더러

node express의 기본 html 제네레이터인 jade를 익히는 것도 쉽지 않았다..

#### 안드로이드

다른 것들은 괴로웠지만.. 할만했다..

안드로이드가 어려웠던게 아니라 이것도 하고 저것도 할 만큼 시간이 많지 않았던 것이다..

## 문제

토악질을 해가며 만들었고 결국에 구현은 대부분 하긴 했다..

하지만 산출물에는 영혼이 없었고 역시 이익으로 남을 수도 없는 구현체일 뿐이었다..

무어의 법칙처럼 가속해온 개발력은 이때 처참히 무너져 내렸다.. 

## 결론

과도한 욕심히 부른 개발을 해보고 나서 많은 것을 깨닫고..

잠시 릴렉스하기로 했다..