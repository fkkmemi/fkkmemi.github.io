---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 74 공용 알림 메세지 만들기(vuex)
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

모든 페이지에 있는 알림 메세지 처리를 한 곳에서 해보겠습니다. 

{% include toc %}

{% raw %}

# 개요

각 페이지 마다 스낵바(v-snackbar)를 추가해서 알림 메세지를 띄웠는데 코드양도 많아지고 유지보수 측면에서 좋지 않습니다.

> 스낵바를 변경하려면 전체 페이지를 다 손봐야되는 문제가 있죠..

한 곳에서 처리한다면 역시 골격이 되는 App.vue에 만들어 놓고 호출하면 됩니다.

하지만 쉽지 않죠. fe/src/views/board.vue 에서 App.vue에 접근이 안되기 때문입니다.

그래서 지난번 저장소(vuex)의 변이를 통해서 App.vue에 알려줘야합니다.

저장소 관련 강좌: [49 뷰저장소(vuex) 이정도만 쓰자](https://fkkmemi.github.io/nemv/nemv-049-vuex/) 

# 저장소 설정

**fe/src/store.js**  
```javascript
export default new Vuex.Store({
   state: {
     token: localStorage.getItem('token'),
     sb: {
       act: false,
       msg: '',
       color: 'error'
     }
   },
   mutations: {
    // ..
     pop (state, d) {
       state.sb.msg = d.msg
       state.sb.color = d.color
       state.sb.act = false
       if (d.act === undefined) state.sb.act = true
     }
   },
```

스낵바 상태를 위해 sb를 선언하고 변이 pop을 등록했습니다.

# 화면 추가

**fe/src/App.vue**  
```vue
    <v-snackbar
       v-model="$store.state.sb.act"
       :color="$store.state.sb.color"
     >
       {{ $store.state.sb.msg }}
       <v-btn
         flat
         @click="$store.commit('pop', { act: false })"
       >
         닫기
       </v-btn>
     </v-snackbar>
   </v-app>
```

변이된 내용이 있으면 뜨고 닫기를 누르면 act: false를 넘겨서 꺼버릴 수 있습니다.

# 다른 페이지들에서 호출할 때

```javascript
this.$store.commit('pop', { msg: '내용을 작성해주세요', color: 'warning' })
```

이제 변경된 상태를 어디서나 확인 할 수 있습니다.

> 복잡해서 간단한 함수로 빼려고 했는데 유지보수 측면에서 이게 더 나을 것 같습니다. 저장소(vuex)를 사용한다는 것이 명확하니까요..

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/c8a17867e48b13aeeeea39c767fd311d60046b70)

{% endraw %}

# 영상

{% include video id="X4Hsvsu7Y30" provider="youtube" %}