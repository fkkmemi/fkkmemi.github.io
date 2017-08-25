---
title: "LW(LOOP Web) Beta"
excerpt: "처음 웹을 시작할 때"
author_profile: false
header:
  teaser: /images/pf/lwb/2.png
sidebar:
  - title: "Date"
    text: "10.2015"
  - title: platform
    text: "**Server** : cloud vm Window2012<br>
    **Web server** : IIS<br>
    **Communcation server** : Window Application<br>
    **Server script** : PHP<br>
    **Database** : MySQL<br>
    **Raw data** : File<br>
    **front-end** : html<br>
    **devices** : DTG, GPS-tracker<br>"
gallery:
  - url: /images/pf/lwb/1.png
    image_path: /images/pf/lwb/1.png
    alt: "login form"
  - url: /images/pf/lwb/2.png
    image_path: /images/pf/lwb/2.png
    alt: "recent data"
  - url: /images/pf/lwb/3.png
    image_path: /images/pf/lwb/3.png
    alt: "day data"
  - url: /images/pf/lwb/4.png
    image_path: /images/pf/lwb/4.png
    alt: "recieve server"
---

단순한 목표를 가지고 접근해서 만들어봤다.  
바로 데이터를 받아서 브라우저에서 데이터를 표로 보고 지도로 경로를 확인하는 것..  
> 아무도 시킨 적은 없다.. 단독 프로젝트였다.

기존에 '윈도우 어플리케이션 개발자'로는 웹이 너무 부러웠었다.  
이유는 접근성...  
아무리 윈도우 어플을 잘만들어봤자 설치라는 불편한 절차가 하나 들어가고..  
신뢰할 수 있는 어플을 만들고 배포하는 것은 또 다른 일이기 때문이다.  
하지만 웹은 브라우저만 있으면 된다.  
당시 윈도우 어플리케이션으로 소켓 서버 송수신과 데이터베이스 연동(백엔드)은 이미 마스터를 했기 때문에..  
apache,php,javascript만 좀 다루면 목표에 접근할 것 같았다.  
> 정적인 사이트는 어릴 적 해본 경험이 있었고, 워드프레스 조금 만져본 상태에서 시작했다.

{% include gallery caption="완성된 첫 동적 사이트" %}

{% include toc %}

## 개요

처음부터 제대로 할 리는 없다...

시행착오의 연속에서 경험을 얻고 특성을 이해해야 비로소 다음 스테이지로 갈 수 있다.

## 웹서버

아파치를 깔고 싶었다.. 웬지 그래야 될 것 같아서... 하지만 굳이 윈도우서버인데 IIS를 쓰면 될 것 같았다.  
IIS서버 구동 후 루트에 default.htm 에 '헬로' 쓰고 마무리.

## PHP

#### 설치

윈도우서버에서 패키지 매니저같은 기능이 있었는데 php 관련 선택하고 리붓하니 php가 안정적으로 깔렸던 것 같다.

#### 세션

폐쇄적이었던 윈도우 어플과는 달리 웹은 자유로운 80포트라는 문이 늘 열려있기 때문에...  
이용자격을 분리해서 주기 위해서는 로그인이라는 게 필요했다.  
그래서 php구동과 동시에 알아본 것이 바로 세션..  
나만이 로그인해서 볼 수 있는 사이트가 되어서 너무 기뻤다... :)  

#### 데이터베이스 연동

처음엔 세션 내용을 하드코딩으로 만들었다..  
```javascript
if($session.uid == 'admin' && $session.pwd == '1111122222') else if (session.u...
```  
데이터베이스에 id,pwd 딱 두개의 컬럼의 users라는 테이블을 만들고 php로 읽기/쓰기가 되는지 확인 끝.

#### html

아는 거라고는 body table tr td p a 정도였는데  
table과 a를 사용하여 메뉴를 만들었다.  
문제는 모든 페이지마다 똑같은 top 내용이 하나 바뀔때마다 몇개의 파일을 수정해야했는데 include top의 방법을 알아내고 기뻤다.. :) 

## javascript

GET요청 파라메터로 php렌더링을 해버렸기 때문에 항상 페이지 리프레쉬 상태라 깊게 들어갈 필요는 없었지만...  
하면 할 수록 거를 수 없는 것이었다..   

#### google-map

문제의 구글맵.. 정말 고생 많이했다..  
윈도우 어플에서도 다뤘었지만 그때는 웹브라우저를 래핑한 창에 COM으로 파일을 조작하는 형태였다..  
> 실상 C++로 변수를 밀어넣었다고 해야하나...

네이티브 웹에서는 많은 지식이 요구 되었다.  
구글맵 레퍼런스를 읽어봤자 소용없었다.  javascript자체를 모르니..  
지금은 주언어이지만 당시엔 javascript 자체의 특성을 파악하기도 힘들었다..    
마침내 폴리라인, 마커 정도 원하는 위치에 드로잉이 가능하게 되었다..

#### jquery

지금은 datatables 마저 촌스러워서 빼고 싶은 마음이지만 당시 jquery table은 혁신이었다.  
윈도우 어플에서 자주 쓰던 StringGrid와 거의 같았다.  
내맘데로 원하는 텍스트를 $ 몇줄 쓰면 바꾸는 것도 놀라웠다.  
디비의 테이블 결과를 표에 출력하고 만족..

## 마치며

처음엔 취미로 했었는데..  
마침 외국관제사업이 시작되었다..  
외주업체를 써서 작업을 하는데 시간이 너무 오래 걸려 모두 걱정이 될 무렵..

'짜잔' 하고 이런 정도는 시험삼아 해봤습니다.. 라고 보여주었다.  

그 후 외주업체와 나와 각각 따로 개발을 했으며..  
약 한달간의 씨름 끝에 완성되었는데..  
결국 외주업체는 시간안에 완성하지 못해서 내 것이 채택되어버렸다..

상업적으로 쓸 수 있는 모양새가 아니었지만..  
데이터 누락도 없었고 100대 이하인데다가 의외로 아재감성이 먹혀서 두달 정도는 썼던 것 같다.
