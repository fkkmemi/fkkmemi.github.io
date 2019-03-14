---
layout: single
title: Electron native module 사용하기
category: talk
tag: [talk,idea,electron,tip,serialport,vue,iconv-lite]
comments: true
---

electron-vue(vue-cli2)로 제작되었던 앱을 vue-cli3플러긴 일렉트론 버전(vue add electron-builder)으로 마이그레이션을 하다보니 생각보다 괴로운 부분이 많았습니다..

몇몇 모듈들이 사용되지 않아서 간단하게 팁을 공유합니다.

제가 사용하고 싶은 모듈은 serialport 라는 모듈입니다.

하지만 잘 안됩니다.

바로 웹팩 때문일 듯합니다..

이럴 때는 바로 제작사 홈피로 달려가야죠..

[https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/guide.html#native-modules](https://nklayman.github.io/vue-cli-plugin-electron-builder/guide/guide.html#native-modules) 

바로 이 곳에 해답이 있습니다.

```javascript
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      // List native deps here if they don't work
      externals: ['my-native-dep'],
      // If you are using Yarn Workspaces, you may have multiple node_modules folders
      // List them all here so that VCP Electron Builder can find them
      nodeModulesPath: ['../../node_modules', './node_modules']
    }
  }
}
```

이런 식으로 하라고 하네요..

계속 에러 나는 모듈들(serialport, iconv-lite) 가 잘 작동하네요~

```javascript
// vue.config.js
module.exports = {
  pluginOptions: {
    electronBuilder: {
      // List native deps here if they don't work
      externals: ['serialport, iconv-lite'],
      // If you are using Yarn Workspaces, you may have multiple node_modules folders
      // List them all here so that VCP Electron Builder can find them
      nodeModulesPath: ['../../node_modules', './node_modules']
    }
  }
}
```

최상단에 vue.config.js 파일을 만들면 끝납니다~

# 여담

사실 너무 안되서 웹팩머지도 학습하고 깃헙이나 스택오버플로도 들락거렸는데..

**해메이게 했던 각종 이슈 중 한 링크**  
[https://github.com/ashtuchkin/iconv-lite/issues/204](https://github.com/ashtuchkin/iconv-lite/issues/204)

항상 이런 문제는 역시 제작자가 제일 잘 알고 있습니다.
