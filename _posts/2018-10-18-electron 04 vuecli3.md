---
layout: single
title: Electron 4 일렉트론뷰로 우아한 데스크탑 앱 만들기 vue-cli3 업그레이드
category: electron
tag: [electron,talk,idea,nodejs,vue,vuetify]
comments: true
sidebar:
  nav: "electron"
---

한동안 일렉트론으로 뭔가 만들지 않았습니다.

그 이유는 vue-cli3를 지원하지 않아서였는데요.

일렉트론 하겠다고 vue-cli2로 다운그레이드 하는 것은 성미에 안맞기 때문이었습니다.

이제 공식사이트에 나왔으니 업그레이드 갑니다.

# 기존 설치법

참고: [https://github.com/vuetifyjs/electron](https://github.com/vuetifyjs/electron)

```bash
# Install vue-cli and scaffold boilerplate
npm install -g vue-cli
vue init vuetifyjs/electron my-project

# Install dependencies and run your app
cd my-project
yarn # or npm install
yarn run dev # or npm run dev
``` 

vue-cli2는 저런식(vuetifyjs/electron)으로 묶음 상품을 설치하는 방식이었죠.

# 신규 설치법

vue-cli3가 웹팩이니 린트니 뭐니 시작시 선택으로 가게 되면서 설치가 매우 깔끔해 졌습니다. 

아래 공식 사이트에 가보면 cordova 까지 업데이트 되어서 매우 모바일개발이 하고 싶어지네요~

참고: [https://github.com/vuetifyjs/vue-cli-plugin-vuetify](https://github.com/vuetifyjs/vue-cli-plugin-vuetify)

**공식사이트의 기본적인 순서**  
```bash
$ vue create my-app
$ vue add electron-builder
$ vue add vuetify
$ yarn serve:electron
```

# 실제 설치 확인

중간에 고르는 것들 때문에 직접 한번 설치해봤습니다.

**실제 설치**  
```bash
$ vue create eltr

# 선택한 것들
Vue CLI v3.0.4
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback 
in production) Yes
? Pick a linter / formatter config: Standard
? Pick additional lint features: Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedica
ted config files
? Save this as a preset for future projects? No
success Saved lockfile.
✨  Done in 4.39s.
⚓  Running completion hooks...

📄  Generating README.md...

🎉  Successfully created project eltr.
👉  Get started with the following commands:

# 바로 일렉트론 설치
$ cd eltr
$ vue add electron-builder

# 일렉트론 설치 결과
  • electron-builder version=20.28.4
  • no native production dependencies
✨  Done in 9.25s.
⚓  Running completion hooks...

✔  Successfully invoked generator for plugin: vue-cli-plugin-electron-builder
   The following files have been updated / added:
   
# 뷰티파이 설치
$ vue add vuetify

# 선택한 것
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use custom properties (CSS variables)? No
? Select icon font md
? Use fonts as a dependency (for Electron or offline)? Yes
? Use a-la-carte components? No
? Use babel/polyfill? No
? Select locale en
```

## 선택한 것들

- vue create 시 일렉트론은 SPA로 설치해야하므로 Router, vuex를 선택
- vue create 시 eslint로 악습관 교정해야해서 standard 선택
- vue add vuetify 시 오프라인에서도 동작해야하기 때문에 Yes로 선택
- vue add vuetify 시 구형부라우저 신경쓸 일이 없으므로  babel/polyfill은 No로 선택

나머지는 다 기본값으로 하면 됩니다.

# 구동

당연한 것이지만 꼭 데탑 어플리케이션이 아닌 웹으로 써도 무방합니다.

**웹 개발용**  
```bash
$ yarn serve
```

**데스크탑 앱 개발용**  
```bash
$ yarn serve:electron
```

# 실행

![alt electron](/images/electron/스크린샷 2018-10-18 오전 11.23.36.png)

# vue-cli3 넘어가야할 이유

vue-cli2로 생성한 파일 디렉토리 구조에서 vue-cli3가 미묘하게 바뀌었는데 훨씬 합리적입니다.

한가지 예를 들면

- 기존: vue-cli2
    - src/router/index.js
- 현재: vue-cli3
    - src/router.js

router 디렉토리에 여러 가지를 넣어서 설계할 이유가 별로 없어서 router.js로 구조만 변경한 것입니다.

확실히 합리적이죠?

# 마치며

기존 vue-cli2로 만드신 분은 vue-cli3로 만든 곳에 비즈니스코드만 다시 넣어서 사용하세요~
