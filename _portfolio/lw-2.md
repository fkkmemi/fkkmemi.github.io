---
title: "LW(LOOP Web) V2"
excerpt: "디자인을 시작하게되다"
author_profile: false
header:
  teaser: /images/pf/lw2/3.png
sidebar:
  - title: "Date"
    text: "3.2016"
  - title: platform
    text: "**Server** : *cloud vm Window2012*<br>
    **Web server** : *IIS*<br>
    **Communcation server** : *Window Application*<br>
    **Server script** : *PHP*<br>
    **Database** : mariaDB<br>
    **Raw data** : *File*<br>
    **front-end** : bootstrap<br>
    **devices** : *DTG, GPS-tracker*<br>"
gallery:
  - url: /images/pf/lw2/1.png
    image_path: /images/pf/lw2/1.png
    alt: "main"
  - url: /images/pf/lw2/2.png
    image_path: /images/pf/lw2/2.png
    alt: "login form"
  - url: /images/pf/lw2/3.png
    image_path: /images/pf/lw2/3.png
    alt: "recent data"
  - url: /images/pf/lw2/4.png
    image_path: /images/pf/lw2/4.png
    alt: "track map"
  - url: /images/pf/lw2/5.png
    image_path: /images/pf/lw2/5.png
    alt: "track raw"
  - url: /images/pf/lw2/6.png
    image_path: /images/pf/lw2/6.png
    alt: "report"
  - url: /images/pf/lw2/7.png
    image_path: /images/pf/lw2/7.png
    alt: "setting devices"
  - url: /images/pf/lw2/8.png
    image_path: /images/pf/lw2/8.png
    alt: "setting companies"
  - url: /images/pf/lw2/9.png
    image_path: /images/pf/lw2/9.png
    alt: "setting users"
  - url: /images/pf/lw2/10.png
    image_path: /images/pf/lw2/10.png
    alt: "report"
  - url: /images/pf/lw2/11.png
    image_path: /images/pf/lw2/11.png
    alt: "setting devices"
  - url: /images/pf/lw2/12.png
    image_path: /images/pf/lw2/12.png
    alt: "setting companies"
  - url: /images/pf/lw2/13.png
    image_path: /images/pf/lw2/13.png
    alt: "setting users"
  - url: /images/pf/lw2/14.png
    image_path: /images/pf/lw2/14.png
    alt: "report"
  - url: /images/pf/lw2/15.png
    image_path: /images/pf/lw2/15.png
    alt: "setting devices"
  - url: /images/pf/lw2/16.png
    image_path: /images/pf/lw2/16.png
    alt: "setting companies"
  - url: /images/pf/lw2/17.png
    image_path: /images/pf/lw2/17.png
    alt: "setting devices"
  - url: /images/pf/lw2/18.png
    image_path: /images/pf/lw2/18.png
    alt: "setting companies"
  - url: /images/pf/lw2/19.png
    image_path: /images/pf/lw2/19.png
    alt: "setting users"

---

워드프레스 방출 그리고 디자인 시작 by bootstrap

{% include gallery caption="bootstrap work" %}

{% include toc %}

## 개요

기존 워드프레스형 사이트를 운영하며 고생을 하다가..  

결국 '디자인은 디자이너가 해야지' 라는 생각과 함께 완전히 디자인은 외주 쓸 생각하며 디자인을 내려놓고 다른 것에 힘을 쏟기로 한다..

한참 aws(amazon web service) 구현에 관심이 많을 때.. 지인으로 부터 bootstrap에 관한 이야기를 듣고 알게 된다..

내용을 이해한 순간 바로 결단을 내리고 기존 로컬사본 작업본을 시원하게 날려버리고 시작하게 되었다..

> 어짜피 svn에 있으니..

## 구현

구현은 그저... 매일 같이..

레퍼런스 사이트에 있는 내용을 글짜 하나 안빼 먹고 한땀~한땀 전부 숙지해서 먼저 껍데기를 완성하였다..

[http://bootstrapk.com](http://bootstrapk.com){: .btn .btn--warning} **bootstrap korea**  

> 마침 ajax 기반 노 리프레쉬 페이지에 관심이 있었기 때문에..  
초기 페이지만 php로 디비 뒤져서 렌더링하고 나머진 전부 인페이지 기능 구현을 목적으로 했다..

과감한 결단 후 기존 사이트와 새로 만든 사이트가 다 호환이 되게 만들었으며 각 /a, /b 로 원하는 사이트를 선택하여 자료를 열람할 수 있게 되었다.

## 문제

훌륭한 디자인은 아니었지만.. without 디자이너 치고는 나는 만족했다..

그러나 모바일 페이지 트렌드 때문에 버튼이든 글자든 뭐든 큼지막해 지고 어색함이 물씬 풍겼다..  

뭔가 한국 정서와 맞지가 않는 다는 의견이 많았다..

> m.xxx.com 으로 아예 모바일 페이지를 따로 만들기를 권유하기도 했다..  
레이어, 버튼, 폼 하나하나 모바일/데스크탑 화면 전환해가며 만들어 놨는데..  
대세를 몰라주는 이들에게 조금 실망하기도 했다..  

사실 나 조차도 국내 관제나 기타 어드민 템플릿 사이트중에 부트스트랩을 본 적이 없음..  

> 국내는 2017. 현재에도 여전히 액티브엑스, 플래쉬, 그림 버튼등이 난무한다..

## 결론

만류에도 굽히지 않았다.

어짜피 국내 관제 사이트를 만드는 것도 아니고...

외국은 한참 전에 부트스트랩 도배였으니.. 

이 때부터 더욱더 고집스럽게 나의 길을 걷기로 한다..