---
layout: single
title: Electron 5 일렉트론뷰로 우아한 데스크탑 앱 만들기 업데이트(vue-cli3)
category: electron
tag: [electron,talk,idea,nodejs,vue,vuetify,vuecli3]
comments: true
sidebar:
  nav: "electron"
---

vue-cli3가 업데이트 되어서 기존 설치법(vue-cli2)이 아예 막혀버렸습니다.

> 기존 설치법 지워짐: https://github.com/vuetifyjs/electron

물론 기존 설치법으로도 방법은 있지만 새로운 방법으로 정상적으로 수행되게 만들어보겠습니다.

# vue-cli3 란

vue-cli 라는 것 자체가 뷰로 새로운 프로젝트를 만들어주고 관리할 수 있는 터미널 툴이라고 할 수 있는데요.

2에서 3버전으로 바뀌면서 너무 확 바껴버렸죠..

> USB-C 타입만 달려서 제작년 출시한 맥북 느낌이었습니다.(아직 준비 안되었는데...) 

과감하게 바꾼 이유는 희생을 감수하더라도 편의와 효율을 생각했기 때문일 것 같습니다.

# 2 and 3 차이

자세히는 모르지만 간략하게 설명해보면

## vue-cli2

```bash
$ vue init 어느리포/어느소스 내프로젝트
```

소스 별로 각각 다른 설치 요소들을 가지고 있습니다.

문제는 예를 들어 린트 종류를 고르는 선택문이 일렉트론을 설치할 때도 뷰티파이를 설치할 때도 물어보는 것입니다.

## vue-cli3
 
```bash
$ vue create 내프로젝트
``` 

자질구레한 설정을 깔끔하게 다 내포하고 있습니다.

웹팩 린트 바벨등 신경쓰지 않아도 알아서 다 깔립니다.

> 생산성이 강조되는 시대에 바벨 웹팩 설정 공부하는 시간은 더이상 필요 없는 것이죠.

기본적인게 다 깔리고 플러그인 형태로 설치되는 것입니다.

문제는 핫한 플랫폼이 아니면 아직 플러그인이 없다는 것입니다..

다행히도 일렉트론과 뷰티파이는 핫하기 때문에 있습니다.

```bash
$ vue add electron-builder
$ vue add vuetify
```

일렉트론 빌더 참고: [https://www.npmjs.com/package/vue-cli-plugin-electron-builder](https://www.npmjs.com/package/vue-cli-plugin-electron-builder)

# 설치하기

## 프로젝트 생성하기

```bash
$ vue create el

Vue CLI v3.2.1
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback 
in production) Yes
? Pick a linter / formatter config: Standard
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedica
ted config files
? Save this as a preset for future projects? No


Vue CLI v3.2.1
✨  Creating project in /Users/fkkmemi/tmp/el.
🗃  Initializing git repository...
⚙  Installing CLI plugins. This might take a while...

yarn install v1.12.3
info No lockfile found.
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...

success Saved lockfile.
✨  Done in 14.71s.
🚀  Invoking generators...
📦  Installing additional dependencies...

yarn install v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...

success Saved lockfile.
✨  Done in 5.60s.
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project el.
👉  Get started with the following commands:

 $ cd el
 $ yarn serve

```

Router, Vuex 설치하고 eslint standard로 설정해서 만들었습니다.

여기까지 하면 뷰 기본화면이 뜹니다.

## 일렉트론 설치하기

```bash
$ cd el
$ vue add electron-builder

📦  Installing vue-cli-plugin-electron-builder...

yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...
success Saved lockfile.
success Saved 139 new dependencies.
info Direct dependencies
└─ vue-cli-plugin-electron-builder@1.0.0-rc.10
info All dependencies
├─ align-text@0.1.4
├─ amdefine@1.0.1
# ..
├─ xmldom@0.1.27
├─ yaku@0.16.7
├─ yauzl@2.4.1
└─ zip-stream@1.2.0
✨  Done in 7.29s.
✔  Successfully installed plugin: vue-cli-plugin-electron-builder

? Choose Electron Version ^3.0.0

🚀  Invoking generator for vue-cli-plugin-electron-builder...
📦  Installing additional dependencies...

yarn install v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...
success Saved lockfile.
$ electron-builder install-app-deps
  • electron-builder version=20.38.4
  • no native production dependencies
✨  Done in 8.49s.
⚓  Running completion hooks...

✔  Successfully invoked generator for plugin: vue-cli-plugin-electron-builder
   The following files have been updated / added:

     src/background.js
     .gitignore
     package.json
     yarn.lock

   You should review these changes with git diff and commit them.
```

별도의 선택 없이 그대로 깔립니다.

## 뷰티파이 설치하기

```bash
$ vue add vuetify

📦  Installing vue-cli-plugin-vuetify...

yarn add v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...
success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
└─ vue-cli-plugin-vuetify@0.4.6
info All dependencies
└─ vue-cli-plugin-vuetify@0.4.6
$ electron-builder install-app-deps
  • electron-builder version=20.38.4
  • no native production dependencies
✨  Done in 5.89s.
✔  Successfully installed plugin: vue-cli-plugin-vuetify

? Choose a preset: Configure (advanced)
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use custom properties (CSS variables)? No
? Select icon font Material Icons
? Use fonts as a dependency (for Electron or offline)? Yes
? Use a-la-carte components? Yes
? Select locale English

🚀  Invoking generator for vue-cli-plugin-vuetify...
📦  Installing additional dependencies...

yarn install v1.12.3
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 📃  Building fresh packages...
success Saved lockfile.
$ electron-builder install-app-deps
  • electron-builder version=20.38.4
  • no native production dependencies
✨  Done in 8.21s.
⚓  Running completion hooks...

✔  Successfully invoked generator for plugin: vue-cli-plugin-vuetify
   The following files have been updated / added:

     src/assets/logo.svg
     src/background.js
     src/plugins/vuetify.js
     .gitignore
     package.json
     src/App.vue
     src/components/HelloWorld.vue
     src/main.js
     src/views/Home.vue
     yarn.lock

   You should review these changes with git diff and commit them.
```

일렉트론을 위해 오프라인 폰트저장하는 것만 선택해주고 나머지는 기본값 처리하였습니다.

# 실행하기

## 개발용

```bash
$ yarn electron:serve
```

## 배포용

```bash
$ yarn electron:build
```

# 결과

![alt result](/images/electron/2018-12-27_10.01.20.png)

# 마치며

[지난 글](/electron/electron-04-vuecli3/)

이미 지난번에 포스팅 해두었는데 깜박하고 글을 또 썼네요..

뭐.. 둘 다 참고하세요~
