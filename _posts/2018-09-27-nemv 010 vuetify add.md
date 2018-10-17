---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 10 뷰티파이 메뉴 추가
category: nemv
tag: [nemv,node,express,vue,vuetify]
comments: true
sidebar:
  nav: "nemv1"
---

뷰티파이 기본 설치된 상태에서 페이지 추가해서 브라우저로 확인해보도록 하겠습니다.

기본적으로 터미널에서 fe 디렉토리에 가서 yarn serve 해두시고 브라우저와 코드를 같이 보면 좋습니다.

# 뷰티파이 메뉴 추가

**fe/src/app.vue**  
```vue
<template>
<v-list-tile
          value="true"
          v-for="(item, i) in items"
          :key="i"
          :to="item.to"
        >
</template>
<script>
export default {
  name: 'App',
  data () {
    return {
      clipped: false,
      drawer: true,
      fixed: false,
      items: [
        {
            icon: 'home',
            title: '홈',
            to: {
              path: '/'
            }
        },
        {
            icon: 'face',
            title: '사용자관리',
            to: {
              path: '/user'
            }
        }
      ],
      miniVariant: false,
      right: true,
      rightDrawer: false,
      title: 'Vuetify.js'
    }
  }
}
</script>
```

위와 같이 items를 늘려주고 html 요소중 v-list-tile에 :to를 지정해주면 됩니다.

:to는 뷰라우터가 처리해주는 것입니다. 

뷰라우터 공식 문서를 보면 알 수 있습니다.

[https://router.vuejs.org/kr/guide/#javascript](https://router.vuejs.org/kr/guide/#javascript)

뷰라우터를 너무 심도있게 공부할 필요는 없습니다.

저정도 기능말고 쓸 것이 없기 때문입니다. 

:to 말고도 v-for key등이 뭔지 모르지만 다음에 배우고 이번 챕터는 메뉴가 잘 추가된 것만 확인하고 넘어갑니다.

# 아이콘 바꾸기

[https://material.io/tools/icons](https://material.io/tools/icons) 에서 이름만 복사해서 사용하면 됩니다.

![alt material.io](/images/nemv/스크린샷 2018-10-08 오후 8.58.28.png)

# 영상

자세한 것은 영상을 보며 확인하세요~

{% include video id="tsGryR6NPEc" provider="youtube" %}   

 