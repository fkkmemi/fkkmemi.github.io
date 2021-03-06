---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 15 뷰티파이 레이아웃
category: nemv
tag: [nemv,vue,vuetify]
comments: true
sidebar:
  nav: "nemv2"
---

모던웹, 즉 현대적인 웹에서 가장 큰 부분을 담당하는 반응형에 대한 이야기입니다.

한 코드로 데스크탑, 태블릿, 모바일등이 적절하게 나오게 프로그래밍 하는 것이죠.

바로 뷰티파이의 그리드 시스템을 이용하면 되는 것입니다.

아래 링크를 참고해서 강좌를 진행 합니다.

[https://vuetifyjs.com/ko/layout/grid](https://vuetifyjs.com/ko/layout/grid)

{% include toc %}

# 그리드 시스템을 위한 3요소

v-container / v-layout / v-flex

항상 이런 3 계층으로 이루어져야합니다.


```vue
<v-container fluid grid-list-md>
  <v-layout row wrap>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="red">
        a
      </v-card>
    </v-flex>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="blue">
        b
      </v-card>
    </v-flex>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="red">
        c
      </v-card>
    </v-flex>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="blue">
        d
      </v-card>
    </v-flex>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="red">
        e
      </v-card>
    </v-flex>
    <v-flex xs12 sm6 md4 lg3 xl2>
      <v-card color="blue">
        f
      </v-card>
    </v-flex>
  </v-layout>
</v-container>
```


[https://vuetifyjs.com/ko/layout/breakpoints](https://vuetifyjs.com/ko/layout/breakpoints)

위 링크를 참조해보면.

**12가 너비의 맥스**인 것이고(_bootstrap등 다른 프레임도 마찬가지입니다._)

xs12의 경우 600px이하(곧 모바일)에서는 12개를 다 쓰겠다는 것 곧 너비를 다쓴다는 것입니다.

xs6로 해두면 12 나누기 6 = 2개가 나오겠죠.

실제 구현 모습..

- xs: mobile  
![alt xs](/images/electron/2018-07-06 10-45-14 xs.png)
- sm: tablet  
![alt sm](/images/electron/2018-07-06 10-45-30 sm.png)
- md: notebook  
![alt md](/images/electron/2018-07-06 10-45-46 md.png)
- lg: desktop  
![alt lg](/images/electron/2018-07-06 10-46-01 lg.png)

특이한 것은 옆에 사이드 메뉴가 있죠? lg사이즈에서 v-navigation-drawer가 살아나는 것을 알수 있습니다.
- xl: 4k monitor  
모니터가 후져서 이건 테스트가 안되네요...

사실 처음 디자인 운운했던것 중 가장 큰 요소가 바로 이 그리드 시스템입니다.

정렬만 제대로 해주면 왠만한 UI 추가해도 잘 어울립니다.

vuetify가 이미 설계가 잘 되어있기 때문에 저 3요소만 잘 이용해주면 배치의 문제는 끝난 것입니다.

# 아톰에디터에 터미널 추가하기

Preferences/install 에서 터미널을 찾아서 설치하면 편리합니다.

![alt terminal](/images/nemv/스크린샷 2018-10-08 오후 3.53.19.png)

우측 하단에 + 버튼을 눌러서 yarn serve등을 해주면 좋습니다.

# mounted로 시작 테스트하기

페이지 구동시 들어갑니다.

**fe/src/views/About.vue**  
```vue
<script>
export default {
  mounted () {
    console.log('page start')
  }
}
</script>
```

페이지 수정 후 저장하고 브라우저에서 콘솔을 보며 확인하면 편리합니다. 

# v-text-area로 디버그 내용 뿌려보기

**fe/src/views/About.vue**  
```vue
<template>
  <v-container>
    <v-layout>
      <v-textarea
        outline
        v-model="ta"
      </v-textarea>
    </v-layout>
  </v-container>
</template>
<script>
export default {
  name: 'about',
  data () {
    return {
      ta: 'debug'
    }
  },
  mounted () {
    const a = { a: 1, b: 2 }
    this.ta = JSON.stringify(a)
    console.log('page start')
  }
}
</script>
```

이제부터는 this.ta를 이용해 콘솔 대신 textarea에 이것저것 표시해볼수 있게 되었습니다.

# $vuetify.breakpoint 확인해보기

$vuetify라는 전역변수를 살펴볼 수 있습니다.
 
**fe/src/views/About.vue**  
```javascript
  mounted () {
    this.ta = JSON.stringify(this.$vuetify.breakpoint)
  }
```

현재 브라우저 화면이 어떤 상태인지 전역 뷰티파이 변수로 확인할 수 있습니다.

# v-for 조건으로 개수 변경하기

간단하게 요소를 복사할 때 숫자를 넣어서도 가능합니다.

```vue
<v-flex xs12 sm6 md4 v-for="x in 4">
  {{x}} 
</v-flex>
```

주의해야할 것은 x는 0이 아닌 1부터 시작합니다. 

# 영상

이번 강좌는 코드보다는 영상을 봐야 이해가 빠릅니다..

{% include video id="NGYAoSv1k3E" provider="youtube" %}  



