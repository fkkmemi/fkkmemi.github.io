---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 90 알림창 쉽게 구현하기(vuetify-dialog)
category: nemv
tag: [nemv,node,vue,vuetify,vuetify-dialog]
comments: true
sidebar:
  nav: "nemv7"
---

모던웹(NEMV) 제작 하기 보너스편 시작합니다.

이제부터는 추가적으로 알면 쓸모 있는 것들 위주로 정리해봅니다.

{% include toc %}

# 알림창에 관하여

```javascript
alert('헬로월!')
```

모든 브라우저에서 지원하는 기본 오브 기본 알림창이죠.. 주로 얼럿이라고 하는..

투박한 것도 문제지만 확인을 누르지 않으면 코드가 아래로 향하지 않습니다.

그래서 뷰티파이에서는 스낵바(v-snackbar)라는 것을 이용해서 마치 안드로이드의 토스트 같은 알림 효과를 간편하게 구현해 볼 수 있었습니다.

하지만 연속으로 스낵바가 중첩이 안되는 문제가 있습니다.

그리고 여러 페이지에서 써야되는데 페이지마다 스낵바를 만들어 둘 수도 없어서 App.vue에 만들어 놓고 vuex를 사용해서 어거지로 돌렸습니다.

지금까지의 문제를 해소할 수 있는 방법은 여러가지가 있지만.. 

가장 간편한 방법이 있습니다.

# vuetify-dialog

일반적인 고민은 항상 많은 이가 고민합니다...

그렇기 때문에 고민을 해결하는 사람도 많습니다.

바로 그 고민해결사는..

이것입니다.

[https://www.npmjs.com/package/vuetify-dialog](https://www.npmjs.com/package/vuetify-dialog)

직접 아래 시연해보기를 누르시면 바로 감이 잡힐 것입니다. 

[시연해보기](https://ppx57r3nnj.codesandbox.io/){: .btn .btn--success}

정말 고마우신 분이 편리하게 만들어 놓았습니다.

결과가 프라미스라서 더더욱 좋습니다.

# 설치

## 모듈 설치

```bash
$ cd fe
$ yarn add vuetify-dialog
```

## 등록하기

**fe/src/main.js**  
```javascript
import VuetifyDialog from 'vuetify-dialog'

Vue.use(VuetifyDialog)
```

> 뒤에 콤마로 기본 옵션을 줄 수도 있지만 전 디폴트를 항상 선호하기 때문에 기본으로 등록해봅니다~  
자세한건 해당 모듈 페이지에서...

## 시험하기

**fe/src/views/test/lv3.vue**  
```vue
<template>
  <v-container fluid :grid-list-md="!$vuetify.breakpoint.xs">
    <v-layout wrap row>
      <v-flex xs12 sm6>
        <v-card>
          <v-card-title>vuetify-dialog시험</v-card-title>
          <v-card-actions>
            <v-btn color="warning" @click="confirm">
              누르지마세요
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>      
    </v-layout>
  </v-container>
</template>
<script>

export default {
  methods: {
    confirm: async function () {
      const r = await this.$dialog.confirm({ title: '위험', text: '그래도 누를 것입니까?' })
      if (!r) return this.$dialog.notify.info('취소 완료')
      this.$dialog.notify.error('핵폭탄 발사', { position: 'bottom-right' })
    }
  }
}
</script>
```

아무페이지에서나 this.$dialog로 접근이 가능합니다.

> 실행해보면 크롬 개발자 콘솔에 뻘건줄이 올라가면서 잘 안 되는 경우가 있습니다.  
v-dialog, v-card등등 register가 안되었다는 내용이 등장합니다.  
왜 그럴까요? 

## 아라카르트(A la crate)는 무엇일까..

사실 저도 뭔지 잘 모릅니다. (_현재 리포(저장소)에서는 설치를 했는지 기억이 안나네요..._)

지금껏 아무생각 없이 v-card니 v-form등의 뷰티파이 태그를 맘대로 사용했을 것입니다.

아라카르트를 설치했다면 아마도 그건 아라카르트가 자동 등록 대행 업체였던 것입니다.

.vue에서 사용하고 있는 요소들을 찾아서 사용하는 만큼만 등록해서 사용이되었다는 것입니다.

뷰티파이 요소 전부를 사용하면 무거우니까 그런 것이겠죠~ 

그런데 vuetify-dialog에 들어 있는 요소들 까지 아라카르트가 알아내지는 못했나봅니다~

## 다시 만들어보기

궁금해서 아라카르트를 설치해서 프로젝트를 새로 만들어 보겠습니다.

```bash
$ vue create newtest
? Please pick a preset: Manually select features
? Check the features needed for your project: Babel, Router, Vuex, Linter
? Use history mode for router? (Requires proper server setup for index fallback 
in production) Yes
? Pick a linter / formatter config: Standard
? Pick additional lint features: (Press <space> to select, <a> to toggle all, <i
> to invert selection)Lint on save
? Where do you prefer placing config for Babel, PostCSS, ESLint, etc.? In dedica
ted config files
? Save this as a preset for future projects? No
$ cd newtest
$ vue add vuetify
? Choose a preset: Configure (advanced)
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use custom properties (CSS variables)? No
? Select icon font Material Icons
? Use fonts as a dependency (for Electron or offline)? No
? Use a-la-carte components? Yes
? Select locale English
```

아라카르트를 설치하고 위와 같은 환경에서 vuetify-dialog를 구동한 결과

```text
[Vue warn]: Unknown custom element: <v-card-actions> - did you register the component correctly? For recursive components, make sure to provide the "name" option.
```

역시 이런 에러가 잔뜩 뜨면서 동작이 안됩니다.

## 필수 요소 수동 등록하기

그래서 사용해야할 요소들은 수동으로 등록해주면 됩니다.

뷰티파이를 등록한 곳을 찾아서 콤포넌트들을 등록해줍니다.

**fe/src/plugins/vuetify.js**  
```javascript
import Vue from 'vue'
import Vuetify, {
  VSnackbar,
  VIcon,
  VDialog,
  VCard,
  VCardTitle,
  VCardText,
  VCardActions,
  VSpacer,
  VBtn,
  VToolbar,
  VToolbarTitle,
  VAlert
} from 'vuetify/lib'
import 'vuetify/src/stylus/app.styl'

Vue.use(Vuetify, {
  iconfont: 'md',
  components: {
    VSnackbar,
    VIcon,
    VDialog,
    VCard,
    VCardTitle,
    VCardText,
    VCardActions,
    VSpacer,
    VBtn,
    VToolbar,
    VToolbarTitle,
    VAlert
  }
})
``` 

참 많이도 사용하네요~

휴 이제 잘 되네요

![alt dialog](/images/nemv/2019-04-18_15.43.47.png)

잘되니 지우면 되죠~

```bash
$ cd ..
$ rm -rf newtest
```

> 항상 의심나면 직접 새로 시작해서 시험해보세요~

# 결론

사실 비슷한 모듈을 만들고 있는 중이었는데.. 역시 강호엔 고수들이 많습니다~

저도 시간날 때 쓸만한 모듈 만들면 npm에 등록해 봐야겠습니다~

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3)

# 시험해보기

[https://fkkmemi.com/test/lv3](https://fkkmemi.com/test/lv3)

# 영상

{% include video id="" provider="youtube" %}
