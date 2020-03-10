---
layout: single
title: Flutter Tip - 개발 이슈 모음(2020.3.6)
category: flutter
tag: [flutter, android]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

# 개요

파이어베이스 기반으로 플러터를 개발하면서 문제가 되었던 부분을 공유해봅니다.

최근 개발 생태계가 급변하고 있기 때문에.. 한 두달만 지나도 안되는 모듈이나 보안 이슈로 디프리케이트 되는 상황이 많습니다.

지난 강의도 환경에 따라(버전) 이미 동작이 안되고 있을 수도 있습니다.

> 특히 안드로이드쪽은 제가 테스트를 많이 안해서 오류가 많았습니다.  
> 슬프지만.. 개발자는 역시 항상 변화에 적응해야합니다.

그래서 중간 중간 트러블슈팅 결과를 포스팅해봅니다.

**항상 날짜에 유의하세요~**

# 현재 버전 정보

```bash
[✓] Flutter (Channel stable, v1.12.13+hotfix.8, on Mac OS X 10.15.3 19D76,
    locale ko-KR)
 
[✓] Android toolchain - develop for Android devices (Android SDK version 28.0.3)
[✓] Xcode - develop for iOS and macOS (Xcode 11.3.1)
[✓] Android Studio (version 3.5)
[✓] VS Code (version 1.42.1)
[✓] Connected device (1 available)
```

# 안드로이드 관련

## multidex

안드로이드에서 파이어스토어를 사용할 때 무조건 에러로 나오기 때문에 그냥 해놓으시면 됩니다.

참고: [mergeDexDebug](https://fkkmemi.github.io/flutter/flutter-multidex/)

## androidx

androidx 라는 최신 라이브러리는 플러터 프로젝트 생성시 옵션인데.. 어짜피 파이어베이스 관련 앱의 경우 하다보면 결국 마이그레이션하라고 에러 뜹니다.

해결 방안은 2가지 입니다.

### 기존 소스 업데이트

참고: [https://flutter-ko.dev/docs/development/androidx-migration](https://flutter-ko.dev/docs/development/androidx-migration)

### 새로 만들기

```dart
$ flutter create --androidx new_project
```

제가 내린 결론은 그냥 새로 만드는 것이 좋습니다.

안드로이드 스튜디오에서 마이그레이션해보니 매우 느리고 잘 동작이 안되었던 것 같습니다.

## fcm(firebase cloud message) 관련

파이어베이스의 푸시 알림 관련 기능을 사용할 때 문제가 있었습니다.

현재 버전으로는 공식 가이드대로 안되어서 아래 링크처럼 변경해야 동작합니다.

참고: [/flutter/flutter-fcm/](/flutter/flutter-fcm/)

# ios 관련

## 모듈 설치

모듈은 pubspec.yaml에 추가하면 끝이지만 실제로는 잘 동작하지 않을 때가 많습니다.

주로 버전을 바꿔 테스트 할 때 인데요..

플러터의 모듈은 결국 ios 내부적으로는 pod이라는 시스템으로 구현됩니다.

그래서 초기화를 시켜야 할 때..

```bash
$ flutter clean
$ cd ios
$ rm Podfile.lock
$ pod install
```

이렇게 해서 해결이 될 때가 있습니다.

## firebase_auth 문제

firebase_auth 모듈의 최신버전이 ios에서 동작을 안합니다.

다양한 에러가 나오는데 아래는 그 중 일부입니다.

eg) 'UserAgent.h' file not found

참고 [https://github.com/flutter/flutter/issues/35670](https://github.com/flutter/flutter/issues/35670)

결국 구글링을 해서 해결을 본 상태입니다.

```yaml
dependencies:
  flutter:
    sdk: flutter
  cupertino_icons: ^0.1.2
  # firebase_auth: ^0.15.5+2
  firebase_auth: 0.15.3
  google_sign_in: ^4.1.4

dependency_overrides:
  firebase_core: 0.4.4
```

firebase_core과 firebase_auth 버전을 낮추니 정상 작동합니다.

저렇게 버전 테스트를 할 때마다 아래와 같은 행위가 동반됩니다.

정말 크리티컬한 문제라고 보는데 여전히 안되고 있습니다.

다음 패치를 기다리는 수 밖에는 답이 없는 상황입니다.

> 가급적 최신모듈만 쓰는 제게는 이번 기회에 버전이 정말 중요하다는 것을 깨달았습니다. 웹개발 할 때는 거의 못 겪던 일이라...  
> 이 문제 해결만 반나절은 날린 것 같습니다.

## 모듈설치

디버그중 pubspec.yaml에 뭔가 추가했다면 귀찮지만 디버그를 종료하고 다시 빌드 하는 것이 정신건강에 좋습니다.

## 라이트닝 케이블

가끔 접속이 안된다고 메세지가 뜰 때가 있는데 뒤집어서 꼽아서 해결한 적이 있습니다.

아마도 통신 127.0.0.1:12345 어쩌고 접속 안된다고 뜰 때입니다.

정품 케이블만 쓰는데도 케이블이 후천적 불량이 날 수 있으니 확인해봐야합니다.

# 마치며

당연히 이런 문제가 있을 것이란 감안하고 선정한 플러터이지만.. 실제 문제가 발생했을 때는 포기하고 싶을 정도로 트러블슈팅이 어려웠습니다.

저처럼 주로 프로토타입, 장치연결용으로 간단하게 쓸 프로젝트에는 버틸만한 수준이지만.. 규모가 큰 프로젝트에서는 조금 위험할 것 같습니다.

다만 규모가 매우 큰 조직이라면 네이티브와 섞어 쓰면 매우 효과적일 수 있습니다.

어짜피 플러터라는 것이 다트로 코딩한 것을 각 플랫폼 언어로 제네레이트 하는 것입니다.

각 플랫폼에서 서비스 코드를 만든 후 플러터에서 해당 서비스에 연결해서 사용하고 화면표출부만 처리하면 매우 좋은 시스템이 될 수 있다고 생각합니다.

> 백그라운드에서 동작할 앱은 네이티브 서비스 코드가 필수 입니다..

결국 네이티브를 어느정도로 다룰 수 있으면서 플러터를 사용하면 괜찮다는 것입니다..

버전 호환 문제는 규모가 크던 작던 심각하게 짜증나기 때문에 플러터를 이탈할 수도 있을 것 같습니다.

그래도 java, swift, kotlin 같은 것을 dart라는 언어 하나로 코딩하는 것과 화면코딩의 편리함은 충분한 장접이 틀림 없지만, 프로젝트 규모가 클 때는 신중한 선택을 해야할 것 같습니다.

{% include video id="OF8YjAV2nM8" provider="youtube" %}