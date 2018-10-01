---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 6 뷰티파이란
category: nemv
tag: [nemv,vue,vuetify]
comments: true
sidebar:
  nav: "nemv"
---

뷰티파이라는 CSS프레임워크에 대해 간략히 소개합니다.

# 뷰

뷰(vue.js)는 자바스크립트 프레임워크? 라는 것인데요.

angular, react, vue가 요새 잘나가는 것들인데요.

시작을 너무 어렵게 생각하시는 분들에게 권하고 싶습니다.

리액트던 뷰던 그냥 시작하면 생각보다 어렵지 않습니다.

그저 시작이 어려울 뿐이죠..

추후 리액트로 넘어가려고 준비중인데 다 비슷한 느낌입니다.

# 뷰 맛보기

아래 사이트에 가보면 어떤 느낌인지 첫 코드에서 알 수 있습니다.

[https://kr.vuejs.org/v2/guide/index.html](https://kr.vuejs.org/v2/guide/index.html)

```vue
<div id="app">
  {{message}}
</div>
<script>
var app = new Vue({
  el: '#app',
  data: {
    message: 'Hello Vue!'
  }
})
</script>
```

Hello Vue!가 html에 자동으로 나온다는 것이죠.

둘러보면서 뷰를 공부해도 되지만.. 역시 지루하기 때문에 바로 화면을 구현하며 필요할때마다 뷰를 다뤄보기로 하겠습니다.

# 뷰티파이

뷰로 머터리얼 디자인을 적용한 CSS 프레임워크입니다.

아래 제작사 사이트에 예제가 잘 나와있으니 하나씩 적용해보면 됩니다.

[https://vuetifyjs.com](https://vuetifyjs.com)

사이트에 들어가서 브라우저 너비를 줄이고 키우고 하게되면 반응형 웹이라는 것이 어떤 느낌인지 알 수 있습니다.

UI Components 라는 메뉴에서 각 콤포넌트들을 보고 직접 코드에 삽입해보면 느낌을 알 수 있습니다.

화면을 꾸며보면서 뷰를 공부하는 것이 좋습니다.

# 영상

{% include video id="BRRfWRvY9AI" provider="youtube" %}   


 