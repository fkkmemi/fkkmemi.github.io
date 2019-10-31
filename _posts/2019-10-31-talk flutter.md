---
layout: single
title: Flutter로 크로스플랫폼 앱 만들어보기
category: mobile
tag: [mobile, flutter, firebase]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

모바일 개발 플랫폼인 플러터(flutter)를 선택하게된 계기와 설치 후기를 남겨봅니다.

# 네이티브 앱의 고뇌

네이티브 앱이란 iOS는 xcode로 objective-c or swift를 Android는 android studio로 java or kotlin으로 개발하는 것을 말합니다.

> 사실 제 취향은 공식 가이드라인에 따라서 맞춰가는 네이티브 앱을 더 좋아합니다. 

개발자에게 중요한 것은 항상 시간입니다.

java에서 kotlin 익히는데도 엄청난 에너지를 소비했는데 iOS의 swift도 해야되다보니 극심한 피로도를 느꼈습니다.

물론 대단한 앱을 만든다면 그 에너지가가 가치가 있겠지만 별 기능 없는 간단한 앱을 만드는 상황일 때가 문제입니다.

# 크로스플랫폼 앱의 선정

그래도 네이티브앱을 해야된다고 생각한 것은 크로스플랫폼 앱에 대한 실망이 매우 컸기 때문입니다.

1~2년전 테스트 당시 다양한 크로스플랫폼을 테스트 했었는데 지금 잘나가는 react-native를 건너 뛴게 사실 매우 후회됩니다.

> 당시 제가 vue.js를 하고 있어서 react는 건너 뛰었습니다..

그당시 문제는 iOS의 경우 노치부분이 짤리거나 아웃풋이 swift가 아닌 objective-c 이거나 빌드 워닝도 몇백개가 떠있는 찜찜함이 있었습니다.

하지만 개발생태계는 항상 라이브하기 때문에 현시점에서 크로스플랫폼 앱을 다시 알아봤습니다.

![alt cross-plf](/images/mobile/2019-10-31_10.11.27.png)

널리 알려진 크로스플랫폼들을 검색어 기준으로 트렌드를 확인해보니 눈에 띄게 급상승 하는 플러터(flutter)를 확인 할 수 있습니다.

물론 검색어 기준이라 점유율과는 거리가 멉니다. 그래도 핫하다는 지표는 될 수가 있습니다.

예전 vue.js 처럼 초반에 깃헙 스타를 엄청 먹고 점유율은 사실 그리 높지 않았던 기억이 있어서 망설여지기는 했습니다.

# 플러터에 대한 고민

고민은 한가지 였습니다.

바로 dart라는 신종 언어로 작성해야 된다는 것입니다.

안타까운 마음이 드는 것은 제 개발 생태계에 유일한 타언어가 되는 것이기 때문입니다.

![alt platforms](/images/mobile/2019-10-31_10.50.30.png)

제가 만들고 있는 iot 서비스에 대한 간략한 구성도입니다.

서비스를 제대로 하기 위해서는 다양한 기술들이 들어갈 수 밖에 없습니다.

저는 엄청 프로그램 고수도 아니고 경험도 많지 않지만 이 모든 것을 다 할 수 있는 것은... 바로 javascript 언어 하나로 위의 구성 대부분을 제작 했기 때문입니다.

저는 원래 임베디드 개발 엔지니어입니다.

개발 변화를 보면

- Firmware > RT-OS > Linux(node.js)
- php > ~~ruby~~ > ~~python~~ > jade(node.js)
- html > jquery > vue.js
- java > swfit, kotlin > dart???
- mssql > mysql(maria) > ~~dynamo~~ > mongo > firestore
- c++builder, delphi > vs c#, c++ > electron.js

결국은 javascript로 다 몰아버리고 서비스의 융합을 위해 파이어베이스로 브릿지 역할을 하게끔 변화를 준것입니다.

> 사실 javascript(ecma-script, node, vue.js)도 그다지 많이 아는 편은 아니어서 팀원들에게 물어봐가면서 하는 편입니다.

그래서 react-native로 가는 게 맞을 것 같기도 했지만.. 구글이 만든 플러터를 믿어보기로 했습니다~

# 플러터 설치

공식홈에 설치 방법이 너무 자세하게 나와있어서 macOS 카탈리나 설치시에 문제 되었던 부분만 짚고 넘어갑니다.

> 윈도우 설치 방법 또한 잘 나와있으니 따라해보면 될 것 같습니다.  
다만 윈도우의 경우 iOS 개발은 안될 것 같습니다.

공식문서: [https://flutter-ko.dev/docs/get-started/install](https://flutter-ko.dev/docs/get-started/install)

설치는 다운로드 받고 압축 풀어서 압축 푼 경로의 패스를 지정해주면 완료가 됩니다.

homebrew나 npm등으로 설치가 안되는 것은 큰 실망을 안겨주었습니다.

> 저 같은 unix바보는 관리도 안되고 복잡한 패스 설정을 매우 혐오합니다.

**설치 시에 권한문제 경고창이 떠오르는데, 그 때 아래 붉은 박스안에 항목이 생기면 허용해주면 됩니다.**

![alt settings](/images/mobile/2019-10-31_11.32.56.png)

# 플러터 의존요소 설치

xcode와 android studio, vs code를 설치하고 모든 업데이트를 다 설치해줍니다.

어짜피 편집은 vs code로 하지만 결국 iOS, android 빌드는 해당 툴에서 하기 때문입니다.(_android studio에서도 편집이 가능합니다._)

아래 flutter doctor 명령어를 입력해서 모두 체크박스가 뜨면 성공입니다.

```bash
$ flutter doctor
Doctor summary (to see all details, run flutter doctor -v):
[✓] Flutter (Channel stable, v1.9.1+hotfix.6, on Mac OS X 10.15.1 19B88,
    locale ko-KR)
 
[✓] Android toolchain - develop for Android devices (Android SDK version
    28.0.3)
[✓] Xcode - develop for iOS and macOS (Xcode 11.1)
[✓] Android Studio (version 3.5)
[✓] VS Code (version 1.39.2)
[!] Connected device
    ! No devices available
```

vs code의 경우 plugin의 plutter만 찾아서 설치해주면 필요한 요소들과 디버깅툴이 자동으로 설치됩니다.

체크가 안 뜨고 문제가 있는 경우, 공식홈에 가이드에 따라 문제 되는 항목을 설치해주면 됩니다.

# vscode에서 첫번째 앱 만들기

공식홈에 자세히 나와 있지만 그림이 없어서 이해가 안가실 분들을 위해 설명드립니다.

- vscode 설정: [https://flutter-ko.dev/docs/get-started/editor?tab=vscode](https://flutter-ko.dev/docs/get-started/editor?tab=vscode)
  
## 앱 위치 지정

보기(view) -> 명령 팔레트(command palette)

![alt settings](/images/mobile/2019-10-31_11.46.28.png)

Flutter: New Project를 선택후 앱 이름을 적고 원하는 위치를 선택하면 자동으로 의존 요소를 알아서 설치해줍니다.

## 시뮬레이터 혹은 실제 장치 선정

실제 물리 장치(iPhone, android)의 경우 공식홈의 가이드를 따라 xcode나 android studio를 열어서 수행해야 할 것들이 있습니다.

기기에 배포: [https://flutter-ko.dev/docs/get-started/install/macos#ios-기기에-배포](https://flutter-ko.dev/docs/get-started/install/macos#ios-기기에-배포)

장치는 vs code의 우측하단을 눌러서 지정할 수 있습니다.

![alt settings](/images/mobile/2019-10-31_11.55.17.png)

처음 시뮬레이터를 로드할 때 보안경고창이 계속 뜨니 보안 및 개인 정보 보호에서 허용을 몇번 눌러줘야합니다.

## 디버그 지정하기

좌측에 벌레 모양을 누르고 톱니바퀴를 누르면 설정이 완료 됩니다.

![alt settings](/images/mobile/2019-10-31_11.57.15.png)

## 실행하기

벌레모양 탭에서 좌측 상단에 디버그 옆 플레이 버튼을 누르면 시작됩니다.

![alt play](/images/mobile/2019-10-31_13.08.00.gif)

소소한 내용들은 핫-리로딩되어 바로 반영됩니다.

# 플러터 연습하기

기본적인 구동 원리를 느꼈다면 차근차근 UI 요소를 핸들링 해보는 것이 좋습니다.

## 공식홈 따라서 첫번째 앱 완성하기

공식홈 링크: [https://flutter-ko.dev/docs/get-started/codelab](https://flutter-ko.dev/docs/get-started/codelab)

매우 친절하게 원리와 함께 리스트형식의 데이터를 표시하려면 어떤 방식인저 설명해줍니다..

> 물론 자세히 설명해줘도 dart는 뭔소린지 잘은 모르겠지만.. 흐름은 파악됩니다.

## 예제들로 실행해보기

간단한 예제들: [https://github.com/nisrulz/flutter-examples](https://github.com/nisrulz/flutter-examples)

간단한 예제들을 보면서 /lib/main.dart를 바꿔봅니다.

## 모듈 설치

공식 홈: [https://pub.dev](https://pub.dev)

기존 패키지 매니져들(npm, gem, pip 등)처럼 아직 양이 많지는 않지만.. 구글 자작(안정적) 모듈이 많아서 괜찮은 것 같습니다.

## API 문서 보면서 차근차근 만들어가기

공식 문서: [https://api.flutter.dev/index.html](https://api.flutter.dev/index.html)

문서를 보며 제일 상단에 위치한 AppBar 부터 조금씩 튜닝해봅니다.

## 파이어베이스 연동

공식 홈: [https://firebaseopensource.com/projects/firebaseextended/flutterfire/](https://firebaseopensource.com/projects/firebaseextended/flutterfire/)

오프라인 모바일앱만 해서는 할 수 있는 것이 별로 없습니다.

서버가 있어야 푸쉬도 받을 수 있고, 인증도 받고 데이터도 읽고 쓸 수 있습니다.

그 모든 다리 역할을 해주는 파이어베이스를 이용하면 간단합니다.

vue & firebase 강좌: [vf 강좌](/vf/)

# 플러터 장점과 단점

장단을 운운하기에는 너무 초입이지만.. 굳이 느낀 점은..

## 장점

- 개발환경: 핫리로딩과 vscode의 플러그인은 환상적
- 모듈 설치: pubspec.yaml 파일에 원하는 플러그인을 써주고 저장만하면 자동 설치
- 디자인: 구글의 머터리얼 디자인 최적화

## 단점

- 설치: 인스톨러가 없어서 귀찮음
- 언어: dart.. 완전 새로 공부해야함
- 안정성?: 대규모 앱에서는 아직 네이티브 앱을 해야되지 않을까요?

# 결론

많은 것을 만들고 운영하고 있기 때문에.. 전부 돌아가는 수준 정도의 얕은 지식 밖에 없습니다. 다 잘하는 것은 말이 안되죠...

당연히 프로그래밍 실력은 매우 저질 수준입니다.

저는 개발자지만 영업이 더 중요하다고 생각합니다.

아무리 잘 만들어봐야 못 팔면 끝이기 때문에.. 엔드 유저에게 비춰질 화면을 포장하고 물리적인 가능성을 증명하는데 노력을 기울입니다.

그래서 우선 만들고 시작합니다.

만들었기 때문에 개선을 할 수 있는 여지가 있고 말을 할 거리가 생깁니다.


모바일앱을 외주를 준다고 해도 해볼만한 가치는 있다고 생각합니다. 

개발에 대한 대략적인 느낌을 가지고 있어야 외주 스케줄 관리와 구현 방향을 바로 잡을 수 있기 때문입니다.

[최근 개발 동향](/talk/rest-mobile/) 에서도 언급했었지만.. 

이 시대의 개발자라면 최근 개발 트렌드를 전부 따라가는 지는 못하더라도 흐름은 파악해야한다고 생각합니다.

무엇이든 어렵게 생각하면 시작이 잘 안됩니다..

최근에는 개발자들을 위한 가이드들이 훌륭하기 때문에 무엇이든 잡고 시작하시기 바랍니다.

# 영상

영상은 실제 동작하는 것을 검증하는 참고용이니 꼭 볼 필요는 없습니다.

> 영상은 그저 의사 전달이 빠르기 때문에 그때그때 생각을 적어두는 용도입니다.

{% include video id="AmkvQ2KUPxo" provider="youtube" %}
