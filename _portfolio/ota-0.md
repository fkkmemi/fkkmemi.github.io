---
title: "OTA V1"
excerpt: "NEMVV로 제작한 웹"
author_profile: false
header:
  teaser: /images/pf/ota/3.png
sidebar:
  - title: "Date"
    text: "5.2018"
  - title: platform
    text: "**Server** : *cloud vm CentOS*<br>
    **Web server** : *node express*<br>
    **Communcation server** : *node*<br>
    **Server script** : *node*<br>
    **Database** : *mongoDB*<br>
    **Raw data** : *mongoDB*<br>
    **front-end** : *vue, vuetify*<br>
    **devices** : *DTG, GPS-tracker*<br>"
gallery:
  - url: /images/pf/ota/1.png
    image_path: /images/pf/ota/1.png
    alt: "1"
  - url: /images/pf/ota/2.png
    image_path: /images/pf/ota/2.png
    alt: "2"
  - url: /images/pf/ota/3.png
    image_path: /images/pf/ota/3.png
    alt: "3"
  - url: /images/pf/ota/4.png
    image_path: /images/pf/ota/4.png
    alt: "4"
  - url: /images/pf/ota/5.png
    image_path: /images/pf/ota/5.png
    alt: "5"
  - url: /images/pf/ota/6.png
    image_path: /images/pf/ota/6.png
    alt: "6"
  - url: /images/pf/ota/7.png
    image_path: /images/pf/ota/7.png
    alt: "7"
  - url: /images/pf/ota/8.png
    image_path: /images/pf/ota/8.png
    alt: "8"
  - url: /images/pf/ota/9.png
    image_path: /images/pf/ota/9.png
    alt: "9"
  - url: /images/pf/ota/10.png
    image_path: /images/pf/ota/10.png
    alt: "10"
  - url: /images/pf/ota/11.png
    image_path: /images/pf/ota/11.png
    alt: "11"
  - url: /images/pf/ota/12.png
    image_path: /images/pf/ota/12.png
    alt: "12"
  - url: /images/pf/ota/13.png
    image_path: /images/pf/ota/13.png
    alt: "13"
  - url: /images/pf/ota/14.png
    image_path: /images/pf/ota/14.png
    alt: "14"
  - url: /images/pf/ota/15.png
    image_path: /images/pf/ota/15.png
    alt: "15"
  - url: /images/pf/ota/21.png
    image_path: /images/pf/ota/21.png
    alt: "21"
  - url: /images/pf/ota/22.png
    image_path: /images/pf/ota/22.png
    alt: "22"
  - url: /images/pf/ota/23.png
    image_path: /images/pf/ota/23.png
    alt: "23"
  - url: /images/pf/ota/24.png
    image_path: /images/pf/ota/24.png
    alt: "24"
  - url: /images/pf/ota/25.png
    image_path: /images/pf/ota/25.png
    alt: "25"
  - url: /images/pf/ota/26.png
    image_path: /images/pf/ota/26.png
    alt: "26"
  - url: /images/pf/ota/27.png
    image_path: /images/pf/ota/27.png
    alt: "27"
  - url: /images/pf/ota/28.png
    image_path: /images/pf/ota/28.png
    alt: "28"
  - url: /images/pf/ota/29.png
    image_path: /images/pf/ota/29.png
    alt: "29"
  - url: /images/pf/ota/30.png
    image_path: /images/pf/ota/30.png
    alt: "30"
  - url: /images/pf/ota/31.png
    image_path: /images/pf/ota/31.png
    alt: "31"
  - url: /images/pf/ota/32.png
    image_path: /images/pf/ota/32.png
    alt: "32"
  - url: /images/pf/ota/33.png
    image_path: /images/pf/ota/33.png
    alt: "33"
  - url: /images/pf/ota/34.png
    image_path: /images/pf/ota/34.png
    alt: "34"
  - url: /images/pf/ota/35.png
    image_path: /images/pf/ota/35.png
    alt: "35"

---

[NEMVV](/nemvv/nemvv-00-intro/) 강좌를 진행하면서 뭔가 이용할 만한 것 없을까 고민하다가

3주동안 만들어서 완성 시켰다.

{% include gallery caption="기기 관리 사이트 데스크탑 / 모바일 스크린샷" %}

새로운 플랫폼 vue, vuetify의 조합은 역시나 빠른 생산성을 검증하였다.

# 이룬 것들

{% include toc %}

## JWT 인증  
  
기존에는 서버에서 사용자 정보를 세션으로 저장하고 핸들링했는데 클라이언트에 토큰을 주고 api 처리를 하는 방식이다.

클라는 매 요청마다 엑세스 토큰을 들고 덤비기 때문에 사용자 식별이 용이하고 서버 부하도 적다.(_다만 복잡도가 문제_)

강좌를 진행하며 jwt(json web token)등도 다루고 실험도 했지만 실제 적용하긴 처음이다.  

사실 작은 규모인 이런 사이트는 전작처럼 세션처리면 충분할 것이지만..  추후를 대비해 트렌드를 따라가 본 정도?

그래도 사용자 계정 저장되는 정도의 의미는 있기는 하다.. 

## SPA(싱글페이지 어플리케이션)

일반적인 사이트는 매 페이지마다 페이지 리프레쉬를 한다.(_이미지고 뭐고 죄다 싹 긁어오는 행위를 한다_) 

SPA는사이트 내에서 페이지를 눌러도 화면만 변경되는 방식이다.(_화면상 어느 특정 부위만 변경된다고 보면된다. 브라우저에서 유튜브를 보듯.._)

전작에서도 이미 ajax로 구현했던 것이다. 

이번엔 vue-router를 통해 좀 더 강력하게 개념을 가다듬고 정비했다.

## PWA(프로그레시브 웹 어플리케이션)

쉽게 말해서 웹앱이라고 하는 것이다.  

앱의 사용자 경험을 웹에 옮기는 어떤 철학 같은 것이라 딱히 법이 있는 것은 아니다.

기존에도 PWA형으로 구현했지만 이번에는 개발시 모바일화면 부터 시작했다.(_현장 대응을 고려한.._)

위에 스샷과 같이 동일페이지가 모바일, 태블릿, 데스크탑이 다르게 동작한다.

## vue.js

쉽게 말하면 스크립트를 편하게 쓰기 위한 도우미..

양방향 바인딩은 놀랍다.. 코드와 화면이 같이 움직인다..

학습커브도 낮고 자료도 한글로 잘 정리되어 있어서.. 정말 고맙게 잘 사용했습니다. 감사합니다.

## vuetify.js

위에 언급한 PWA를 만들 기 위한 CSS framework이다

기존 쓰던 부트스트랩이 삼국시대면 뷰티파이는 조선시대다..

그만큼 격차가 크게 느껴졌으며 vue와 결합해서 매우 유연하게 페이지 작성이 되었다.  

## ECMAScript 2018

promise chain 전체 도입으로 전작의 콜백지옥 완전 탈출..

소켓서버 수신부도 전면 프라미스로 변경했다.

이제는 웬만한 모듈들은 죄다 프라미스로 리턴을 줘서 역시 시대의 흐름을 안따라갈 수가 없다.

그 밖에 선보인 신기능도 심심풀이로 몇가지 넣었는데 기억이 안날정도로 대세에 미미한 기능들..

## ESLint(airbnb)

맞춤법, 띄어쓰기, 변수 사용들을 바로잡아주는 도우미다.

처음에는 너무 피곤하고 귀찮게해서 꺼버리고 싶었으나 인내로 버텨냈더니 손에 익은 데다가 다 작성하고 나면 코드가 아름답다. 

다음 프로젝트부턴 ESLint(standard)로 바꿨다.(_에어비엔비 써보니 별로.._)

# 버린 것들

## IE(인터넷 익스플로러8, 9, 10, 11)

익스는 모든버전 포기.. 그래도 리디렉션 시켜서 엣지나 크롬설치창 유도정도는 해줬다

물론 바벨 폴리필로 익스11까지는 되긴 하는데.. 저질 익스11 맞출려고 퍼포먼스 떨어트리고 싶지 않았다.(_그냥 크롬 깔으시오_)

## pug(jade)

*.pug는 코딩하기 힘든 *html을 쉽게 작성하고 사용자 요청시엔 html로 보내주는 역활..  

서버사이드 html 제네레이터를 과감히 버렸다.

서버는 api만 나머지는 모두 vue로 처리..

# 결론

페이지 수가 많이 되지는 않지만.. 

3주동안..

수십번 디비 구조를 변경, 네이밍을 바꾸고

수십번 함수 구조와 위치를, 공용 모듈을 재정의 하고..

수십번 api를 만들고 지우고..

수십번 레퍼런스사이트를 들락날락하고..

수십번 버튼을 만들고 콘솔에 찍어가며..

수십번 빌드해서 태어난 것이다.. 

그리고.. 과거 수만번의 시행착오가 시간을 줄인 것..