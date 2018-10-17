---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 12 뷰티파이 콤포넌트 사용해보기
category: nemv
tag: [nemv,node,vue,vuetify]
comments: true
sidebar:
  nav: "nemv1"
---

뷰티파이의 콤포넌트(버튼, 칩)를 사용해서 화면 처리를 위한 연습을 해봅니다. 

{% include toc %}
{% raw %}

# 뷰 연습할 파일 만들기

파일을 하나 만들어서 뷰를 연습해봅시다.

**fe/src/views/default.vue**  
```vue
<template>
</template>
<script>
export default {
  name: 'default',
  data () {
    return {
    }
  },
  methods: {}
}
</script>
<style scoped>
</style>
```

뷰 파일은 항상 위와 같은 구조로 만들면 좋습니다.

html, script, style이 한세트이죠

- 페이지 내에서 동적인 변환이 필요 없다면 html인 탬플릿(<template>) 부분만 작성
- 페이지 내에서 동적인 변환이 필요 하다면 script 코드 작성 추가
- 페이지 내의 특별한 스타일이 필요 하다면 style 코드 작성 추가, 정의 된 style을 재정의(오버라이드) 할 수 있습니다.

# mustache 태그 사용해보기

턱수염 같이 생겨서 머스터시 태그라고 불리웁니다. 

{{ }} 모양이 비슷하죠? 전 발음이 귀찮아서 머스태그라고 합니다.
 
[https://kr.vuejs.org/v2/guide/#시작하기](https://kr.vuejs.org/v2/guide/#시작하기)

뷰 기본안내서 맨 첫페이지 이기도 합니다.

**fe/src/views/help.vue**  
```vue
<template>
  <div class="about">
    <h1>{{title}}</h1>
  </div>
</template>
<script>
export default {
  name: 'help',
  data () {
    return {
      title: 'Vuetify.js'
    }
  }
}
</script>
```

이런식으로 간단히 뷰의 머스태그를 확인할 수 있습니다.

# 이벤트 만들기

어떤 프로그램이든 이벤트가 중요합니다.

페이지가 표시된 후 타이머라도 만들어 놓지 않은 이상 아무일도 안일어나죠..

대부분 사용자 버튼등을 클릭하고 처리하는 과정을 이벤트라고 합니다.

이벤트를 처리하는 과정을 이벤트 핸들러 혹은 리스너라고 합니다.

간단하게 이벤트를 구현해보면

**fe/src/help.vue**  
```vue
<!-- html 부분 -->
<button @click="test">test</button>

<script>
// 이벤트 핸들러 추가
    methods: {
      test () {
        console.log('test done')
      }
    }
</script>
```

뷰에서 이벤트는 골뱅이 @ 로 연결해줍니다.

간단하게 이벤트 구현을 크롬 콘솔에서 확인 해볼 수 있습니다.

# 뷰티파이 버튼 만들기

html의 기본 버튼 대신에 뷰티파이의 미려한 버튼으로 테스트 해보겠습니다.

[https://vuetifyjs.com/ko/components/buttons](https://vuetifyjs.com/ko/components/buttons)

에 예제대로 하면 끝입니다.

**fe/src/help.vue**  
```vue
<v-btn color="primary" @click="test">test</v-btn>
```

훨씬 아름다운 버튼이죠?

# 뷰 바인드 해보기

머스태그 많이 아닌 html 속성도 뷰로 다이내믹하게 변경이 가능합니다.


**fe/src/help.vue**  
```vue
<!-- html 부분 -->
<v-btn :color="btnColor" @click="test">test</v-btn>
<!-- 스크립트 부분 -->
<script>
export default {
  data () {
    return () {
      title: 'xxx',
      btnColor: 'success'
    }
  },
  methods: {
    test () {
      this.title = 'nnnnnnn',
      this.btnColor = 'warning'
    }
  }
}
</script>
```

test 버튼을 누르면 색상과 타이틀이 변하는 것을 확인 할 수 있습니다.

v-btn의 컬러가 처음엔 'success' 지만 버튼을 누를 때 'warning'으로 변경되게 했습니다.

data에 선언된 변수들은 html에서는 그냥 쓰면 되지만, script 내부에서는 this를 붙혀서 참조하는 것을 알 수 있습니다.

# 뷰티파이 색상에 관하여

기본적으로 최근 css 프레임워크들은 강조되는 몇가지 의미의 색상을 구분 해놓습니다.

프레임워크들은 정의대로 버튼이나 경고를 띄우기를 바랍니다.

- primary: 중요, 주로 짙은 파랑
- info: 정보자료, 주로 하늘색
- success: 성공, 주로 초록계통
- warning: 경고, 주로 오렌지계통
- error: 위험, 주로 빨간색

그 밖에도 secondary등이 있으나 주로 위 5가지가 제일 많이 사용합니다.

그밖에 색상 정의는 뷰티파이 공식홈 중 아래 링크를 참고 하면 됩니다.

[https://vuetifyjs.com/ko/style/colors](https://vuetifyjs.com/ko/style/colors)

가급적 정의된 색상을 사용해야 머터리얼한 느낌을 가질 수 있습니다~ 

eg) color="red lighten-4" color= "blue lighten-2" 

# 뷰로 반복문 해보기

뷰티파이의 칩(v-chip)을 사용해서 반복해서 여러개를 출력해보겠습니다.

칩 또한 뷰티파이 공식홈에서 간단하게 사용법을 익힐 수 있습니다.

[https://vuetifyjs.com/ko/components/chips](https://vuetifyjs.com/ko/components/chips)

```vue
<!-- html 부분 -->
<v-chip v-for="chip in chips" :color="chip.cl">{{chip.name}}</v-chip>

<!-- script 추가 -->
<script>
data () {
  chips: [
    {
      cl: 'primary',
      name: 'abc'
    },    
    {
      cl: 'red',
      name: 'bbb'
    },    
    {
      cl: 'green',
      name: 'ccc'
    }
  ]
}
</script>

```

간단히 v-for로 data에 선언된 것들을 여러개 출력해 볼 수 있습니다.

# 마치며

뷰와 뷰티파이를 이용해 간단히 예제를 만들어봤습니다.

제가 생각한 뷰 스터디는 이정도면 충분하다 생각합니다.

뷰라는 것은 결국 스크립트와 html의 조화 정도로만 생각하면 되기 때문입니다.

개념이 중요한 것이지 뷰의 장대한 기능들을 다 알 필요는 없습니다.

다른 기능은 필요할 때 추가하고 스터디해도 충분합니다.

{% endraw %}

# 영상

{% include video id="0k70tiZiupE" provider="youtube" %}   

 