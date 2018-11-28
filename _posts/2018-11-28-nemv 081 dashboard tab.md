---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 81 대시보드 게시판 다르게 표현하기
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify]
comments: true
sidebar:
  nav: "nemv6"
---

게시판 데이터를 활용해서 다른 표현을 해보도록 하겠습니다.

{% include toc %}
{% raw %}

# 개요

이제부터 대시보드를 만들어보려합니다.

대시보드는 여러가지 데이터들을 보기 좋게 표현한 페이지입니다.

**구글에서 대시보드로 검색한 이미지들**  
![alt google dashboard](/images/nemv/2018-11-28_18.32.03.png)

대시보드 안에 들어가는 것들을 위젯이라 합니다.

뷰티파이에서는 v-card를 사용한 콤포넌트로 위젯의 역할을 대신하게 되겠죠..

이제부터 대시보드에 위젯을 하나씩 채워가면서 대시보드를 완성하려 합니다.

그중 게시판 데이터를 활용해서 테이블이 아닌 다른 형태로 표현할 수 있다는 것을 예로 보여드리려고 합니다.

# 게시판 데이터 만들기

![alt link board](/images/nemv/2018-11-28_18.36.14.png)

link 라는 이름의 게시판을 관리자만 사용하도록 하게 만들어 추가합니다.

게시물들을 추가합니다. 

![alt link article](/images/nemv/2018-11-28_18.38.03.png)

# 대시보드 껍데기 만들기

**fe/src/views/dashboard/index.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout wrap row>
      <v-flex xs12 sm6>
        <link-card></link-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import linkCard from '@/components/dashboard/linkCard'

export default {
  components: { linkCard }
}
</script>
```

대시보드로 사용할 껍데기만 만들어 놓고 콤포넌트 linkCard를 넣을 자리를 마련해둡니다.

# 콤포넌트 만들기

저 다섯개의 데이터를 v-tab을 이용해서 표현해보겠습니다.

참고: [https://vuetifyjs.com/ko/components/tabs](https://vuetifyjs.com/ko/components/tabs)

**fe/src/components/dashboard/linkCard.vue**  
```vue
<template>
  <div>
    <v-toolbar color="cyan" dark tabs>
      <v-toolbar-title>{{board.title}}</v-toolbar-title>
      <v-tabs
        slot="extension"
        v-model="tab"
        color="cyan"
      >
        <v-tabs-slider color="yellow"></v-tabs-slider>

        <v-tab v-for="article in articles" :key="article._id">
          {{ article.title }}
        </v-tab>
      </v-tabs>
    </v-toolbar>

    <v-tabs-items v-model="tab">
      <v-tab-item v-for="article in articles" :key="article._id">
        <v-card flat>
          <v-card-text v-html="article.content"></v-card-text>
        </v-card>
      </v-tab-item>
    </v-tabs-items>
    <v-btn @click="test">test</v-btn>
  </div>
</template>
<script>
export default {
  data () {
    return {
      board: {
        name: '로딩중...',
        title: '로딩중...',
        rmk: '무엇?'
      },
      tab: null,
      loading: false,
      params: {
        draw: 0,
        search: '',
        skip: 0,
        sort: '_id',
        order: 1,
        limit: 10
      },
      articles: []
    }
  },
  mounted () {
    this.getBoard()
  },
  watch: {
    tab (t) {
      console.log(t)
      if (this.articles.length) this.read(t)
    }
  },
  methods: {
    test () {
      this.articles[0].content = Math.random()
    },
    getBoard () {
      this.$axios.get(`board/read/link`)
        .then(({ data }) => {
          console.log(data)
          if (!data.success) throw new Error(data.msg)
          this.board = data.d
          this.list()
        })
        .catch((e) => {
          if (!e.response) this.$store.commit('pop', { msg: e.message, color: 'warning' })
        })
    },
    list () {
      if (!this.board._id) return
      this.loading = true
      this.params.draw += 1

      this.$axios.get(`article/list/${this.board._id}`, { params: this.params })
        .then(({ data }) => {
          if (!data.success) throw new Error(data.msg)
          data.ds.forEach((v) => {
            v.content = ''
          })
          this.articles = data.ds
          this.loading = false
        })
        .catch((e) => {
          if (!e.response) this.$store.commit('pop', { msg: e.message, color: 'warning' })
          this.loading = false
        })
    },
    read (i) {
      const atc = this.articles[i]
      this.loading = true
      this.$axios.get(`article/read/${atc._id}`)
        .then(({ data }) => {
          if (!data.success) throw new Error(data.msg)
          console.log(data)
          atc.content = data.d.content
          atc.cnt.view = data.d.cnt.view
          this.loading = false
        })
        .catch((e) => {
          if (!e.response) this.$store.commit('pop', { msg: e.message, color: 'warning' })
          this.loading = false
        })
    }
  }
}
</script>
``` 

게시판에 있는 스크립트를 그대로 가져와서 뷰티파이 공식홈 예제중 하나를 가져와서 v-tab에 표시해본 것입니다.

머스태시 태그({{}}) 로는 html 표현이 안됩니다.
 
그래서 v-html을 사용해서 내용(article.content)을 담았습니다.

주의 할 것은 리스트를 불러올 때 article.content = '' 처리를 해준 것인데 이게 없다면 v-tab에서 렌더링이 안됩니다.

다른 콤포넌트도 항상 주의 하실 것이 대부분 datas: [] 로 초기화하고 렌더링이 안되는 문제가 있습니다.

바로 멤버(datas[n].xxx)가 정해지지 않았을 때 렌더링에 문제가 생기는데요.. 

안되면 멤버에 키와 값을 넣어주면 됩니다.

탭을 누를 때마다 watch에서 tab의 인덱스를 받을 수 있습니다.(0~N)

해당 인덱스로 게시물 읽기를 할 수 있는 것입니다.

# 결과

![alt vtab](/images/nemv/2018-11-28_18.46.19.png) 

# 사용해보기

[https://fkkmemi.com](https://fkkmemi.com)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/9f0652f1ff96e35a6fb662ce174eed5de23b714b)

{% endraw %}

# 영상

{% include video id="CFfguiqheS0" provider="youtube" %}

