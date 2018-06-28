---
title: "LEMS"
excerpt: "친환경 운전 관리 솔루션"
author_profile: false
header:
  teaser: /images/pf/lems/1.png
sidebar:
  - title: "Date"
    text: "11.2013"
  - title: platform
    text: "**Server** : cloud vm Window2012<br>
    **Communcation server** : LDFS<br>
    **Uploader** : LEU<br>
    **Client viewer** : LEC<br>
    **Launcher** : LP Launcher<br>
    **Update server** : LUS<br>
    **Web server** : IIS<br>
    **Device** : DTG<br>"
gallery:
  - url: /images/pf/lems/1.png
    image_path: /images/pf/lems/1.png
    alt: "config"    
  - url: /images/pf/lems/2.png
    image_path: /images/pf/lems/2.png
    alt: "dtg"
  - url: /images/pf/lems/3.png
    image_path: /images/pf/lems/3.png
    alt: "modem"
  - url: /images/pf/lems/4.png
    image_path: /images/pf/lems/4.png
    alt: "ldfs"
  - url: /images/pf/lems/5.png
    image_path: /images/pf/lems/5.png
    alt: "leu"
  - url: /images/pf/lems/6.png
    image_path: /images/pf/lems/6.png
    alt: "lec 1"
  - url: /images/pf/lems/7.png
    image_path: /images/pf/lems/7.png
    alt: "lec 2"
  - url: /images/pf/lems/8.png
    image_path: /images/pf/lems/8.png
    alt: "lec 3"
  - url: /images/pf/lems/9.png
    image_path: /images/pf/lems/9.png
    alt: "lec 4"
---

상용 관제 서비스 : **원맨쇼**가 극에 달았을때의 작품

차량의 데이터를 이용한 **친환경 운전 모니터링 시스템** 이다.

> 해당 솔루션의 친환경 운전 지수로 실제 인센티브까지 계산하는 곳도 있다.. 
 

**Keynote 일부분**  
{% include gallery caption="lems.keynote 자료에서 발췌" %}

**Keynote중 LEU 동영상 부분만 발췌** 
{% include video id="KJykO3BiLII" provider="youtube" %}

{% include toc %}

## 개요

젊음, 두뇌, 속도, 손맛, 스킬, 경험, 말빨등 개발자로서 가장 자신만만할 때였던 것 같다.

> 모든 것을 혼자했다는 데 의미가 있다.  
지금 보면 창피하고.. 무엇보다 귀찮아서 이렇게는 못할 듯..

## 개발  

약 5개월의 개발 기간의 일들을 나열해보면..

- 서버 : cloud vm 생성,튜닝,관리
- 디바이스 : 차량의 OBD 데이터 낸드에 적재 펌웨어 제작
- 3G모뎀 : 소켓 클라이언트 펌웨어 컨트롤
- wifi모듈 : 소켓 클라이언트 펌웨어 컨트롤
- 프로토콜 : 수신서버<>디바이스<>클라이언트뷰어 제작
- DB : mariaDB table 설계
- 수신서버 : 디비 및 파일 생성 디자인, 윈도우 어플리케이션 제작
- 업로더 : 통신 사용이 안될 경우 파일 업로드 기능, 윈도우 어플리케이션 제작
- 웹서버 : 클라이언트가 파일데이터를 인증포함 GET으로 가져가게 하기 위해
- 클라이언트 뷰 : 디비 접속하여 서머리 파일을 확인하고 점수등 조작, 윈도우 어플리케이션 제작
- 런쳐 : 클라이언트가 실행하기 전 업데이트 서버에 새로운 버전이 있는 지 확인. 있다면 업데이트 함, 윈도우 어플리케이션 제작
- 업데이트 서버 : 런쳐로 부터 버전을 체크하고 실행파일을 내려주는 소켓 수신서버, 윈도우 어플리케이션 제작
- 배포패키지 : installshiled로 dll,bpl등 묶어주는 프로그램

자질구레한 것들까지..

- 개발환경 : IDE, 콤포넌트, svn 선정부터 구매까지, RAD Studio
- UI : 전체적인 디자인은 상용콤포넌트로 코딩했다. by [https://www.tmssoftware.com/site/](https://www.tmssoftware.com/site/)
- 스토리보드 : 제작(keynote)
- 회의 참석 : 직접 의견을 듣고 반영함
- 동영상 : iMovie 제작 

> 특히 디자인과 트렌드를 중시했는데..  
라운딩 처리 및 트레이 아이콘 기능등은 시키지도 않은 일을 한것임.. 

## 동작

- 디바이스가 수신서버에 데이터 전송
- 파일로 올리는 경우 업로더로 데이터 전송
- 수신서버는 디비와 파일로 저장
- 클라이언트 실행
- 런처로 업데이트 감지
- 업데이트 있을 경우 클라이언트 바이너리 갱신
- 클라이언트는 요약정보는 디비로부터, 개당자세한정보는 웹서버로 부터 받아서 처리

## 문제점

- 솔로개발자  
혼자 앞서 나간다고 좋을 때는 그야말로 한때..  
역시나 물려줄 수 없는 일이라 적절한 후임이 없음..
- 실증  
해봤던 것은 금방 지겹고 하기 싫어져서..  급실증..
- 트렌드  
윈도우 어플리케이션 개발자로서 나름의 경지에 도달했는데도 불구하고..  
한참 모바일이 판을 치는 시기여서 윈도우 어플은 왠지 뒤쳐지는 느낌..  

## 기록

치열했던 과거의 열정를 잊기엔 아쉬워서 박제해본다.  
앞으로는 이때 처럼 무계획하게 개발에 임해서는 안된다는 것도 깨달음..  
> 남는 게 없음.. 힘들때 한번씩 보며 위안을 삼아야지.. 
