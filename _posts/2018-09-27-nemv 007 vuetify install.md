---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 7 뷰티파이 설치
category: nemv
tag: [nemv,vue,vuetify]
comments: true
sidebar:
  nav: "nemv1"
---

뷰티파이 설치에 대해 알아보기

아래 사이트에 나온 정보대로 설치해보겠습니다.

[https://vuetifyjs.com/ko/getting-started/quick-start](https://vuetifyjs.com/ko/getting-started/quick-start)

{% include toc %}

# yarn 이란

yarn은 패키지 매니저라고 하는 것입니다. npm이랑 같지만 빠릅니다.

그런데 최근에는 npm 보다 yarn을 선호하는데요 설치가 어렵지 않으니

아래 사이트에서 설치 하면 됩니다.

[https://yarnpkg.com/en/](https://yarnpkg.com/en/)

```bash
$ yarn -v
```

yarn 버전을 확인해서 잘 나오면 설치 성공입니다.

# vue cli3 설치

뷰로 만든 사이트를 쉽게 만들어 주는 커맨드라인인터페이스라고 합니다.

뷰씨엘아이3, 뷰컴3 아무렇게나 불러도 됩니다.

```bash
$ yarn global add @vue/cli
$ vue --version
```

vue 버전이 잘 나오면 설치 성공입니다.

# vue cli로 프론트엔드 만들기

be 디렉토리에서 만들어봅니다.

```bash
$ vue create fe
```

이렇게 하고나면 고를 수 있는 메뉴가 생기는데요

```bash
? Please pick a preset: (Use arrow keys)
❯ default (babel, eslint)
  Manually select features
```
이중 디폴트로 하게되면 아주 미니멀한 설치가 됩니다.

하지만 저는 이것 저것 좀 필요한 것이 있어서 매뉴얼로 가봅니다.

```bash
? Please pick a preset: Manually select features
? Check the features needed for your project:
 ◉ Babel
 ◯ TypeScript
 ◯ Progressive Web App (PWA) Support
 ◉ Router
❯◉ Vuex
 ◯ CSS Pre-processors
 ◉ Linter / Formatter
 ◯ Unit Testing
 ◯ E2E Testing
```

화살표 위아래와 스페이스로 설치할 것들을 고를 수 있습니다.

저는 라우터와 vuex를 추가해봤습니다.

뷰라우터는 싱글페이지앱을 만들기 위해, 뷰이엑스는 저장소를 위한 것인데 나중에 구현할때 다시 설명하겠습니다.

대략 엔터 치고나니 이런 형태네요

```bash
Vue CLI v3.0.1
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback
in production) Yes
? Pick a linter / formatter config: Standard
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedica
ted config files
? Save this as a preset for future projects? (y/N)
```

이제 1~2분 기다리면 설치가 끝납니다. 

뷰컴3는 바로 가지가지로 방법이 많았던 설치를 하나로 묶은 것입니다.

항상 눈으로 보는 거보다 엔터 꽝꽝 때리면서 직접보면 이해가 빠릅니다.

설치가 다 되고나면 역시나 고맙게도 안내 메세지를 출력해줍니다.

```bash
  Successfully created project test.
  Get started with the following commands:

 $ cd fe
 $ yarn serve
```

저대로 해줍니다.

```bash
 DONE  Compiled successfully in 4432ms                                  10:18:34


  App running at:
  - Local:   http://localhost:8080/
  - Network: http://10.0.1.2:8080/

  Note that the development build is not optimized.
  To create a production build, run yarn build.
```

서버가 구동 되었다네요..

> 밑의 Note 내용은 현재는 개발용도 서버지만 yarn run build를 하면 진짜서버에서 돌아가도록 압축 해준다는 것입니다.

브라우저를 열어서 http://localhost:8080/ 들어가봅니다.

![alt vue](/images/nemv/2018-08-23 10-29-58 test.png)

화면이 나옵니다..

반갑다는 메세지가 뜨지만, 그런데 뭔가 허전하네요..

바로 뷰티파이를 아직 설치하지 않은 것이죠..

ctrl+c를 눌러서 서버를 꺼버리고 뷰티파이를 설치합니다.

**뷰티파이 설치**

필독!: [2018 10월 이후 뷰티파이 설치하시는 분들](/talk/vuetify-update-cli/)

```bash
$ vue add vuetify
```

또 뭔가 물어보네요.. 그냥 다 엔터 때려줍니다.

```bash
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use a-la-carte components? No
? Use babel/polyfill? Yes
```

다시 서버를 구동해볼까요?

```bash
$ yarn serve
```

![alt vuetify](/images/nemv/2018-08-23 10-37-37 test.png)

브라우저마다 틀리지만 기존 페이지를 캐쉬로 가지고 있어서 새로운 탭이나 윈도우를 변경해야 확인이 될 수 있습니다.

드디어 뷰티파이한 화면을 볼 수가 있네요~

> 뷰티파이 1.3.2 버전부터 아쉽게도 메뉴가 포함된 시작화면을 제공하지 않습니다.. 왜 바꿨을까요... 아쉽네요..  
이제 [https://github.com/fkkmemi/nemv3][https://github.com/fkkmemi/nemv3] 의 소스에서 fe/src/App.vue를 참고해서 가져오셔야합니다.

# 뷰티파이 아이콘에 대해

아래 사이트에서 아이콘 명으로 뷰티파이 아이콘을 사용할 수 있습니다.

[https://material.io/tools/icons/?style=baseline](https://material.io/tools/icons/?style=baseline)

# 영상

{% include video id="wHa9w_BB3yQ" provider="youtube" %}   


 