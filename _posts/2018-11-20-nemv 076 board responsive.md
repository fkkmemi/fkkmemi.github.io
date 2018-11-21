---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 76 반응형 게시판 만들기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

모바일, 태블릿, 데스크탑등의 다양한 장에서 적절하게 표현되게 변경해보겠습니다.

{% include toc %}

{% raw %}

# 뷰티파이 v-container 동적으로 조절하기

```vue
<v-container fluid :grid-list-md="!$vuetify.breakpoint.xs" :class="$vuetify.breakpoint.xs ? 'pa-0' : ''">
```

- fluid를 사용하면 좌우 내용을 꽉 채울 수 있습니다.
- grid-list-md는 flex마다의 간격인데 모바일일 때는 사용하지 않도록 바인드 시켰습니다.
- 클래스도 패딩을 동적으로 할당해서 모바일에서는 패딩을 없애버렸습니다.

패딩 및 마진 참고: [https://vuetifyjs.com/ko/layout/spacing](https://vuetifyjs.com/ko/layout/spacing)

# 상단 검색과 타이틀 변경

```vue
<!-- -->
<v-card>
  <v-card-title class="headline">
    <v-tooltip bottom>
      <span slot="activator">{{board.name}}</span>
      <span>{{board.rmk}}</span>
    </v-tooltip>
    <v-spacer></v-spacer>
    <v-text-field
      v-model="params.search"
      append-icon="search"
      label="검색"
      clearable
      style="width:40px"
    ></v-text-field>
  </v-card-title>
  <v-data-table
  <!-- -->
```

간단하게 v-card와 v-text-field로 모양을 내봤습니다.

# 다이얼로그 반응형

```vue
<v-dialog v-model="dialog" persistent max-width="500px" :fullscreen="$vuetify.breakpoint.xs">
```

다이얼로그의 fullscreen 을 바인드 시켜서 모바일에서는 전체화면으로 보게 변경 했습니다.

# 결과

## 큰 화면

![alt not xs](/images/nemv/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202018-11-20%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%206.38.37.png)

![alt not xs d](/images/nemv/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202018-11-20%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%206.44.39.png)

## 작은 화면

![alt xs](/images/nemv/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202018-11-20%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%206.38.48.png)

![alt xs d](/images/nemv/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA%202018-11-20%20%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE%206.44.44.png)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/ac431c4a3f257c735260c5895aa973df1455e3ae)

{% endraw %}

# 영상

{% include video id="EzTGcDp4YeQ" provider="youtube" %}

