---
layout: single
title: 모바일앱(iOS Android) 둘 다 만들기 - 1 iOS UI 기초
category: mobile
tag: [mobile,ios,talk,idea]
comments: true
sidebar:
  nav: "mobile"
---

간단히 버튼을 올리고 UI가 동작하는 지 테스트 해봅니다.

{% include toc %}

# 프로젝트 생성

Xcode를 실행하면 런처가 실행됩니다.

![alt pr cr](/images/mobile/2019-02-28_19.54.40.png)

Create a new xcode project를 클릭합니다.

# 싱글뷰앱 선택

![alt pr cr](/images/mobile/2019-02-28_19.58.15.png)

아무것도 없는 앱입니다. 그 밖에 다른 종류들(Master-detail App같은..)은 이 앱에서 기능을 추가한 셈입니다.

# 프로젝트 이름 생성

![alt pr cr](/images/mobile/2019-02-28_20.04.47.png)

대충 이름 정하면 Bundle Identifier 가 완성되는 데 이 이름이 이 앱의 핵심 키워드가 됩니다.

# 적당한 위치 지정

![alt pr cr](/images/mobile/2019-02-28_20.05.20.png)

# 첫 생성 화면

![alt pr cr](/images/mobile/2019-02-28_20.05.56.png)

이 앱의 정보입니다. 

# Button 추가하기

![alt pr cr](/images/mobile/2019-02-28_20.07.22.png)

먼저 좌측에서 Main.storyboard를 클릭하면 앱 디자인 화면이 뜹니다.

우측 상단에 홈버튼모양? 을 누르면 UI 라이브러리가 팝업됩니다.

그 중 버튼을 찾아서 드래그만 해주면 버튼이 화면에 배치 됩니다.

> 그 밖에 UI도 배치하고 놀아보면 됩니다.

좌측 상단에 시뮬레이터중 아무거나 고릅니다.(_저는 iPhone XR로 결정_)

좌측 상단의 플레이버튼을 누릅니다.

# 첫 실행된 모습

![alt pr cr](/images/mobile/2019-02-28_20.09.57.png)
 
버튼 하나 달랑 있는 앱이 시뮬레이터를 통해 실행됩니다.

> 실제 폰에 해보고 싶으면 등록 절차가 필요합니다.

이제 앱이 실행된다는 느낌을 가질 수 있습니다.

더럽혀지지 않은 최고의 순수한 코드로 실행된 앱인 것입니다.  

# 텍스트뷰 추가하기

![alt pr cr](/images/mobile/2019-02-28_20.14.32.png)

버튼의 동작을 느껴보기 위해 표시될 텍스트뷰를 추가합니다.

그리고 좌측 상단에 링2개 모양의 아이콘을 클릭합니다.

그러면 화면과 코드가 같이 보이게 됩니다.

# 텍스트뷰 변수 만들기

![alt pr cr](/images/mobile/2019-02-28_20.18.26.png)

이벤트에 반응할 변수를 선언합니다.

따닥따닥 쳐도 되지만 드래그앤 드롭으로 편하게 추가할 수 있습니다.

텍스트뷰를 선택한 후 우클릭(혹은 컨트롤 좌클릭)한 채로 코드에 드래그합니다.

# 텍스트뷰 변수 지정하기

![alt pr cr](/images/mobile/2019-02-28_20.18.51.png)

connection을 Outlet(받을 곳)으로 지정해주고 Name을 tvLog로 정해봤습니다.(_기호에 맞게_)

# 텍스트뷰 코드 확인

![alt pr cr](/images/mobile/2019-02-28_20.19.06.png)

```swift
@IBOutlet weak var tvLog: UITextView!
```

뭘 의미하는지 모르겠지만 아무튼 변수가 생성되었습니다.

이제 tvLog라는 변수를 건들면 뭔가 작동을 하는 것이죠..

# 버튼 이벤트 만들기

![alt pr cr](/images/mobile/2019-02-28_20.20.19.png)

버튼을 선택 후 우클릭으로 코드에 드래그 해줍니다.

# 버튼 변수 지정하기

![alt pr cr](/images/mobile/2019-02-28_20.20.28.png)

Connection을 Action으로 지정하고 Name을 btnChange 라고 지정합니다.(_기호에 맞게_)

# 버튼 이벤트 코드 확인

![alt pr cr](/images/mobile/2019-02-28_20.20.33.png)

버튼을 누르면 이 곳으로 오는 것이죠~

# 버튼 눌러서 텍스트뷰에 표시하기

![alt pr cr](/images/mobile/2019-02-28_20.22.02.png)

```swift
    @IBAction func btnChange(_ sender: Any) {
        tvLog.text = "hello?"
    }
```

버튼 이벤트 함수 안에 아무 스트링이나 넣어보고 실행해봅니다.

제대로 원하는 글자가 표현됩니다~

# 텍스트필드 추가하기

![alt pr cr](/images/mobile/2019-02-28_20.22.36.png)

이번엔 입력을 받아서 표현하기 위해 텍스트필드를 드래그해서 추가합니다.

# 텍스트필드 변수 지정하기 

![alt pr cr](/images/mobile/2019-02-28_20.23.54.png)

Connection을 Outlet으로 설정하고 Name은 tfEdit로 지정합니다.(_기호에 맞게_)

# 최종확인

![alt pr cr](/images/mobile/2019-02-28_20.25.05.png)

```swift
    @IBAction func btnChange(_ sender: Any) {
        tvLog.text = tfEdit.text
    }
```

텍스트필드의 글자가 텍스트뷰에 잘 표시되는 것을 확인 할 수 있습니다.

# 결론

상당히 유치하지만.. 기본이라고 생각합니다.

아무리 쉬워 보여도 누군가에겐 진입장벽이 되는 시작점이죠..

뭐든 어렵게 생각하면 시작이 안됩니다.

앱스토어에 올리진 못하지만 우선 돌아간 다는 것에 만족을 해봅니다~

# 영상

{% include video id="iICRtWcEbVw" provider="youtube" %}
