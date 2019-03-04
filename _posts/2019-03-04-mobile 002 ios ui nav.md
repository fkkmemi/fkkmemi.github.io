---
layout: single
title: 모바일앱(iOS Android) 둘 다 만들기 - 2 iOS UI 네비게이션 콘트롤러
category: mobile
tag: [mobile,ios,talk,idea,navigation]
comments: true
sidebar:
  nav: "mobile"
---

스토리보드로 네비게이션 콘트롤러를 구현해봅니다. 

# 싱글뷰앱 선택

![alt pr cr](/images/mobile/2019-02-28_19.58.15.png)

새로 프로젝트를 만듭니다.

그러면 뷰컨트롤러 달랑 있는 프로젝트가 만들어집니다.

# 네비게이션 콘트롤러 추가

![alt pr cr](/images/mobile/2019-03-04_09.30.30.png)

뷰컨트롤러를 선택하고 Editor > Embed in > Navigation controllor 누르면 뷰컨트롤러 앞에 네비게이션 콘트롤러가 생깁니다.

# 네비게이션 콘트롤러 확인

![alt pr cr](/images/mobile/2019-03-04_11.45.36.png)

지금은 플레이를 눌러도 아무일도 일어나지 않습니다.

# 네비게이션 타이틀 및 버튼 추가

![alt pr cr](/images/mobile/2019-03-04_11.48.35.png)

식별할 수 있게 타이틀을 적어두고 상세페이지를 연결할 버튼을 하나 올려놓습니다.

# 뷰컨트롤러를 추가

![alt pr cr](/images/mobile/2019-03-04_11.54.45.png)

우측 상단에 홈버튼모양? 을 누르면 UI 라이브러리가 팝업됩니다.

그 중 뷰컨트롤러를 선택후 드래그해서 바닥에 놓습니다.

# 버튼 연결

![alt pr cr](/images/mobile/2019-03-04_13.45.51.png)

만든 버튼을 우클릭 드래그해서 새로 만든 뷰컨트롤러에 놓습니다.

버튼이 새로운 뷰컨트롤러에 연결된 것을 확인 할 수 있습니다.

그리고 자동으로 상단에 전 위치가 표시(메인페이지)됩니다.

실행해보면 페이지 전환이 잘 되는 것을 확인할 수 있습니다.

# 네비게이션 아이템

![alt pr cr](/images/mobile/2019-03-04_14.45.33.png)

네비게이션 콘트롤러로 구성하면 네비게이션 아이템이 상단에 있는데 좌우에 버튼을 넣을 수 있습니다.

# 결론

가장 좋은 예는 iOS의 설정입니다.

각 씬(페이지)와의 관계에 대한 콘트롤러인것이죠.

그 밖의 방법으로 추가하는 방법은 영상을 참고하시기 바랍니다.

# 영상

{% include video id="UlNy-4_j8bA" provider="youtube" %}
