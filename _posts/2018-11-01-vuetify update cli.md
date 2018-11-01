---
layout: single
title: Vuetify(뷰티파이) cli 변경 문제
category: talk
tag: [talk,idea,news]
comments: true
---

뷰티파이가 1.3.2 에서 갑자기 설치되는 기본 홈 모양을 바꿨습니다...

2018 10월 23(?)일 경부터 이런데요..

뷰 기본 홈 모양과 거의 흡사합니다..

굳이 왜.. 바꿨는 지 모르겠습니다.

{% include toc %}

# 뷰 설치 기본 홈

```bash
$ vue create test
$ yarn serve
```

![alt vue](/images/nemv/2018-08-23 10-29-58 test.png)

뷰의 장점을 못 살린 페이지 입니다 ...

뷰만의 동적이고 쉬운 느낌이 들지 않습니다. 20세기 사이트 같습니다.

# 뷰티파이 설치 기본 홈

```bash
$ vue add vuetify
```

설치하기는 vue-cli 3부터 vue add로 바뀌었습니다.

# 뷰티파이 1.3.2 이전 버전

```bash
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use a-la-carte components? No
? Use babel/polyfill? Yes
```

질문이 딱 네가지 기본값 쳐서 나온 페이지는 아래와 같습니다.

![alt vuetify](/images/nemv/2018-08-23 10-37-37 test.png)

머터리얼 디자인이 무엇인지 알 수 있는 콤포넌트들(툴바와 네비게이션 드라워, 반응형웹)을 사용해서 설명하기가 좋았습니다.

# 뷰티파이 1.3.2 부터

질문이 바뀌었습니다.

```bash
? Choose a preset: (Use arrow keys)
❯ Default (recommended) 
  Prototype (rapid development) 
  Configure (advanced) 
```

우선 셋중 하나를 고르라는데요.. 

Configure로 할때만 아래의 질문이 옵니다.

```bash
? Choose a preset: Configure (advanced)
? Use a pre-made template? (will replace App.vue and HelloWorld.vue) Yes
? Use custom theme? No
? Use custom properties (CSS variables)? No
? Select icon font Material Icons (default)
? Use fonts as a dependency (for Electron or offline)? No
? Use a-la-carte components? Yes
? Select locale English (default)
```

다 엔터 기본값으로 내려봤습니다.

바벨폴리필은 하기 싫을 때도 있는데 물어보지도 않네요.. 

이제 a-la-carte 가 기본 설치가 되었네요? 

모듈 선택해서 쓰는 것으로 알고 있는데..

코드로 콤포넌트 일일히 지정해서 넣어야해서 귀찮았는데 아마 자동으로 해줘서 기본값으로 넣어 놓은것 아닐까요?

# 신뷰티파이(1.3.2 이상) 결과

```bash
$ yarn serve
```

![alt new vuetify](/images/nemv/스크린샷 2018-11-01 오후 9.59.52.png)

위의 모드 3개 다해봐도 결과는 마찬가지 입니다.

아무것도 없어요.. 뷰 기본 홈이랑 비슷합니다.

씁쓸한 기본홈이네요..

# 강좌용 원래 홈 소스

제 깃헙 첫 커밋 데이터를 읽어서 하시면 됩니다.

깃헙 리포 주소: [https://github.com/fkkmemi/nemv3/blob/30ab077714df0567679d79e7b2915e25bc46b401/fe/src/App.vue](https://github.com/fkkmemi/nemv3/blob/30ab077714df0567679d79e7b2915e25bc46b401/fe/src/App.vue)

뷰티파이 1.3.2 이상 버전 설치하신 분들은 아래 App.vue로 교체해주세요.

**fe/src/App.vue**  
```vue
<template>
  <v-app>
    <v-navigation-drawer
      persistent
      :mini-variant="miniVariant"
      :clipped="clipped"
      v-model="drawer"
      enable-resize-watcher
      fixed
      app
    >
      <v-list>
        <v-list-tile
          value="true"
          v-for="(item, i) in items"
          :key="i"
        >
          <v-list-tile-action>
            <v-icon v-html="item.icon"></v-icon>
          </v-list-tile-action>
          <v-list-tile-content>
            <v-list-tile-title v-text="item.title"></v-list-tile-title>
          </v-list-tile-content>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-toolbar
      app
      :clipped-left="clipped"
    >
      <v-toolbar-side-icon @click.stop="drawer = !drawer"></v-toolbar-side-icon>
      <v-btn icon @click.stop="miniVariant = !miniVariant">
        <v-icon v-html="miniVariant ? 'chevron_right' : 'chevron_left'"></v-icon>
      </v-btn>
      <v-btn icon @click.stop="clipped = !clipped">
        <v-icon>web</v-icon>
      </v-btn>
      <v-btn icon @click.stop="fixed = !fixed">
        <v-icon>web</v-icon>
      </v-btn>
      <v-toolbar-title v-text="title"></v-toolbar-title>
      <v-spacer></v-spacer>
      <v-btn icon @click.stop="rightDrawer = !rightDrawer">
        <v-icon>menu</v-icon>
      </v-btn>
    </v-toolbar>
    <v-content>
      <router-view/>
    </v-content>
    <v-navigation-drawer
      temporary
      :right="right"
      v-model="rightDrawer"
      fixed
      app
    >
      <v-list>
        <v-list-tile @click="right = !right">
          <v-list-tile-action>
            <v-icon>compare_arrows</v-icon>
          </v-list-tile-action>
          <v-list-tile-title>Switch drawer (click me)</v-list-tile-title>
        </v-list-tile>
      </v-list>
    </v-navigation-drawer>
    <v-footer :fixed="fixed" app>
      <span>&copy; 2017</span>
    </v-footer>
  </v-app>
</template>

<script>

export default {
  name: 'App',
  data () {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      items: [{
        icon: 'bubble_chart',
        title: 'Inspire'
      }],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  }
}
</script>
```

# 마치며

익스프레스는 4.16버전인가 1년 넘게 업이 안되고 있습니다.

익스프레스 개발진이 나가 만든 코아(koa.js)로 넘어가 볼까 많은 고민을 했지만.. npm 위클리 다운로드 수가 너무 적어서 관두었습니다.

아무리 새로운 것을 좋아하는 저라도.. async 지원 말고는 크게 욕심이 나는 부분이 없었습니다.

웹서버 하는 역할이 별게 없긴 하죠..

다른 모듈들의 버전업은 무시무시합니다.

정말 자고 일어나면 릴리즈 넘버 하나씩 올라가드니 오늘 뷰티파이 1.3.5가 되어 있네요..

노드는 11버전이 나왔고요..

몽고도 4 버전의 변화라고 엄청 메일이 오네요..

뷰(vue.js)가 의외로 2.5를 오래 유지하고 있는데.. 어느샌가 뷰3가 나오겠죠..

버전업 따위 겁내지 않고 늘 최신버전으로 갈아탔는데.. 요샌 좀 무섭긴 합니다~

버전업으로 인한 리스크는 조금 있지만.. 개선된 모습을 보면서 더 큰 즐거움과 아직 트렌드 따라가고 있는 느낌을 가져봅니다~