---
layout: single
title: iOS 개발 - 개발환경 세팅
category: iOS
tag: [iOS,iPhone]
comments: true
---

갑자기 뜬금 없이 아이폰 앱이 만들고 싶어서 한번 시작해봅니다..

할 일도 많은데 갑자기 땡기는 걸 거부할 수가 없네요..

우선 개발환경을 만들어보려합니다.

일년전에 했었는데 다 까먹어서 기록할 겸 정리해봅니다.    

{% include toc %}

# 개발자 등록

작년에 구글링해서 일단 회사명의로 개발자 등록은 시켜놨습니다. 한 십만원 날라갔네요..

워낙 자료가 많아서 이부분은 패스 하겠습니다.

# 앱을 빌드하기 위한 과정

매우 귀찮은 절차를 진행해야합니다..

## UDID 복사

디바이스를 등록하려면 UDID라는게 필요합니다.

아이튠즈에 아이폰을 연결하고 시리얼을 누르면 확인 할 수 있습니다.

우클릭해서 카피 해둡니다.

![alt 1](/images/ios/1.png)

## 디바이스 추가

카피했던 UDID를 붙혀넣습니다.
 
![alt 2](/images/ios/2.png)

## 디바이스 추가 확인

![alt 3](/images/ios/3.png)

## 인증서 만들기 1

응용 프로그램 > 유틸리티 > 키체인 접근

키체인 접근 이라는 앱을 실행하고

인증기관에서 인증서 요청을 클릭합니다.

![alt 4](/images/ios/4.png)

## 인증서 만들기 2

![alt 5](/images/ios/5.png)

## 인증서 만들기 3

![alt 6](/images/ios/6.png)

## 인증서 등록 1

![alt 7](/images/ios/7.png)

## 인증서 등록 2

![alt 8](/images/ios/8.png)

## 인증서 등록 3

생성된 인증서를 선택합니다.

![alt 9](/images/ios/9.png)

## 인증서 등록 4

키를 다운로드 받습니다.

![alt 10](/images/ios/10.png)

## 인증서 등록 5

다운받은 키 확인

![alt 11](/images/ios/11.png)

## 앱 등록 1

xCode에서 새프로젝트 만든후 제네럴에서 번들 아이디를 복사해둡니다.

![alt 12](/images/ios/12.png)

## 앱 등록 2

사이트에서 앱 추가 버튼 누름

![alt 13](/images/ios/13.png)

## 앱 등록 3

앱 설명 아무렇게나 넣고 아까 복사해둔 번들아이디를 붙혀넣습니다.

![alt 14](/images/ios/14.png)

## 앱 등록 4

등록

![alt 15](/images/ios/15.png)

## 앱 등록 5

등록 확인

![alt 16](/images/ios/16.png)

## 프로파일 등록 1

예전에 만들어 둔 것이 만료가 되어 있네요..

추가 버튼을 누름

![alt 17](/images/ios/17.png)

## 프로파일 등록 2

개발자 선택하고 다음

![alt 18](/images/ios/18.png)

## 프로파일 등록 3

원하는 앱 선택 후 다음

![alt 19](/images/ios/19.png)

## 프로파일 등록 4

원하는 사람 선택 후 다음..

![alt 20](/images/ios/20.png)

## 프로파일 등록 5

장비 선택 후 다음

![alt 21](/images/ios/21.png)

## 프로파일 등록 6

프로필 이름 지정 후 다음

![alt 22](/images/ios/22.png)

## 프로필 등록 7

프로필 다운로드

![alt 23](/images/ios/23.png)

## 프로필 등록 8

더블 클릭해서 설치

![alt 24](/images/ios/24.png)

# 괜히 유아이 몇개 넣어서 빌드

Main.storyboard을 더블클릭 한 후에..

우측 하단의 유아이 몇개 드래그엔드랍으로 화면에 넣습니다.

아이폰을 언락 시키고 재생버튼(빌드)를 클릭하면 아이폰에서 바로 실행됩니다.

![alt 25](/images/ios/25.png)

# 최종 결과

잘 되네요..

![alt 26](/images/ios/26.png)

