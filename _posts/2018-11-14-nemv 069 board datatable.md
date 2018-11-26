---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 69 게시판 뷰티파이 테이블 사용하기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

뷰티파이의 데이터테이블(v-datatable)을 이용해서 화면에 표시해보겠습니다.

{% include toc %}

{% raw %}

# 뷰티파이 데이터테이블(v-data-table)

참고: [https://vuetifyjs.com/ko/components/data-tables](https://vuetifyjs.com/ko/components/data-tables)

예제가 잘 나와있어서 코드를 복사해서 잘 사용하면 됩니다.

# 코드 작성

**fe/src/views/board/anyone.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <!-- <v-flex xs12 sm6 md4 v-for="article in articles" :key="article._id">
        {{article}}
      </v-flex> -->
      <v-flex xs12>
        <v-data-table
          :headers="headers"
          :items="articles"
          :loading="loading">
          <template slot="items" slot-scope="props">
            <td :class="headers[0].class">{{ id2date(props.item._id)}}</td>
            <td :class="headers[1].class">{{ props.item.title }}</td>
            <td :class="headers[2].class">{{ props.item._user ? props.item._user.id : '손님' }}</td>
            <td :class="headers[3].class">{{ props.item.cnt.view }}</td>
            <td :class="headers[4].class">{{ props.item.cnt.like }}</td>
          </template>
        </v-data-table>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>

export default {
  data () {
    return {
      // ..
      articles: [],
      headers: [
        { text: '날짜', value: '_id', sortable: true, class: 'hidden-sm-and-down' },
        { text: '제목', value: 'title', sortable: true },
        { text: '글쓴이', value: '_user', sortable: false },
        { text: '조회수', value: 'cnt.view', sortable: true },
        { text: '추천', value: 'cnt.like', sortable: true }
      ],
      loading: false
      // ..
    }
  },
  // ..
  methods: {
    // ..
    list () {
      if (this.loading) return
      this.loading = true
      this.$axios.get(`article/${this.board._id}`)
        .then(({ data }) => {
          this.articles = data.ds
          this.loading = false
        })
        .catch((e) => {
          this.pop(e.message, 'error')
          this.loading = false
        })
    },
    id2date (val) {
      if (!val) return '잘못된 시간 정보'
      return new Date(parseInt(val.substring(0, 8), 16) * 1000).toLocaleString()
    }
  }
}
</script>
```

먼저 코드를 작성하고 설명합니다.

## headers

상단에 표시될 필드(컬럼) 들을 바인드 합니다. 배열로 되어 있으며 배열 개수만큼 표시됩니다.

데이터테이블의 핵심인 headers를 먼저 정의 해야합니다.

- value는 값의 키 입니다.
- text는 표시될 내용입니다.
- sortable은 클릭해서 정렬이 가능하게 합니다.
- class는 해당 컬럼에 스타일을 적용할 수 있습니다.(_id는 hidden-sm-and-down으로 태블릿 이하에서는 표시되지 않음)  
html td 에 class를 바인딩 해놓은 것을 확인 할 수 있습니다.

## items

실제 데이터 입니다. 현재 articles를 받아서 바인드시켜놨습니다.

테이블의 내용을 채우는 ```<template slot="items" slot-scope="props">``` 의 props가 각 데이터가 됩니다.

td에 props.item.x 가 값입니다.

> 작성 시간, 몽고_id에 대해  
묘하게 눈에 띄는 ```id2date(props.item._id)``` 부분이 있는데요..  
id2time() 이라는 함수는 제가 직접 만든 것입니다.  
몽고디비의 _id의 의미에는 여러가지가 담겨 있는데요..  
그중 앞자리 8자리가 만들어진 시간입니다.  
4바이트 정수(0x12345678)를 헥사스트링으로 하다보니 8자리(12345678)가 된것이죠..  
유닉스 타임스탬프라고 하는 초로 되어 있는 값입니다.  
그래서 1000을 곱해서 미리세컨드로 만들어서 시간 변수로 만든 것입니다.  
결국 작성 시간을 따로 필드 만들지 않고 알아낼 수 있는 것이죠..  
바이트니 자료구조니 사실 설명하려면 너무 깁니다. 이런 부분은 그냥 이런 원리구나 정도로만 판단하고 복사해서 써도 됩니다.

props.item._user 의 경우 손님일 경우 null 이었으므로 조건문으로 맞게 처리해줍니다.

## loading

loading이 true 이면 테이블 상단에 프로그레스바가 표시됩니다.

요청하기 전에 true 로 놓고 응답을 받으면 false 로 놓으면 끝입니다.

이미 요청중일때 또 요청하는 것을 막아주는 코드도 넣어주면 좋겠죠~

# 결과

![alt result](/images/nemv/스크린샷 2018-11-15 오후 3.06.14.png)

6개의 게시물이 자동으로 페이징 처리가 되어서 5개별로 페이지로 표시가 됩니다.

서버사이드 처리가 안되어 있는 것이죠..

현재는 백만개의 게시물은 사용할 수가 없습니다.

데이터 개수가 100개 이하일 경우면 현재 코드처럼 한번에 받아서 내부적으로 돌리는 것이 효율적이고 그 이상이라면 서버사이드처리가 필요합니다. 

서버사이드 적용을 하려면 추가적인 옵션이 더 들어갑니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/9a62dd478b39f8d3a92abd3c5d504e363970739f)

{% endraw %}

# 영상

{% include video id="WwR7hccrt60" provider="youtube" %}
