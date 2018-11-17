---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 68 게시판 게시물 쓰고 읽기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

게시물(article) API CRUD중 게시판 정보가 필요한 CR(Create Read)를 프론트에서 확인해보겠습니다.

{% include toc %}

{% raw %}

# 게시물 확인하기

**fe/src/views/board/anyone.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12>
        <v-card>
          <v-img
            class="white--text"
            height="70px"
            src="https://cdn.vuetifyjs.com/images/backgrounds/vbanner.jpg"
          >
            <v-container fill-height fluid>
              <v-layout fill-height>
                <v-flex xs6 align-end flexbox>
                  <span class="headline">{{board.name}}</span>
                </v-flex>
                <v-flex xs6 align-end flexbox>
                  <span>{{board.rmk}}</span>
                </v-flex>
              </v-layout>
            </v-container>
          </v-img>
        </v-card>
      </v-flex>
      <v-flex xs12 sm6 md4 v-for="article in articles" :key="article._id">
        {{article}}
      </v-flex>
    </v-layout>

    <v-btn
      color="pink"
      dark
      small
      absolute
      bottom
      right
      fab
      @click="addDialog"
    >
      <v-icon>add</v-icon>
    </v-btn>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">글 작성</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12>
                <v-text-field
                  label="제목"
                  persistent-hint
                  required
                  v-model="form.title"
                ></v-text-field>
              </v-flex>
              <v-flex xs12>
                <v-textarea
                  label="내용"
                  persistent-hint
                  required
                  v-model="form.content"
                ></v-textarea>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="green darken-1" flat @click="add()">확인</v-btn>
          <v-btn color="red darken-1" flat @click.native="dialog = false">취소</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model="sb.act"
    >
      {{ sb.msg }}
      <v-btn
        :color="sb.color"
        flat
        @click="sb.act = false"
      >
        닫기
      </v-btn>
    </v-snackbar>
  </v-container>
</template>
<script>
import boardCard from '@/components/manage/boardCard'

export default {
  components: { boardCard },
  data () {
    return {
      board: {
        name: '로딩중...',
        rmk: '무엇?'
      },
      articles: [],
      dialog: false,
      lvs: [0, 1, 2, 3],
      form: {
        title: '',
        content: ''
      },
      sb: {
        act: false,
        msg: '',
        color: 'error'
      }
    }
  },
  mounted () {
    this.get()
  },
  methods: {
    addDialog () {
      this.dialog = true
      this.form = {
        title: '',
        content: ''
      }
    },
    get () {
      this.$axios.get('board/아무나')
        .then(({ data }) => {
          if (!data.success) throw new Error(data.msg)
          this.board = data.d
          this.list()
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    add () {
      if (!this.form.title) return this.pop('제목을 작성해주세요', 'warning')
      if (!this.form.content) return this.pop('내용을 작성해주세요', 'warning')
      this.$axios.post(`article/${this.board._id}`, this.form)
        .then((r) => {
          this.dialog = false
          this.list()
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    list () {
      this.$axios.get(`article/${this.board._id}`)
        .then(({ data }) => {
          this.articles = data.ds
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    pop (m, c) {
      this.sb.act = true
      this.sb.msg = m
      this.sb.color = c
    }
  }
}
</script>
```

- 게시판 정보를 받은 후(get())에 해당 게시판 _id로 게시물 리스틀 가져옵니다(list()).
- 우측 하단에 fab 버튼으로 다이얼로그를 띄우고 확인을 누르면 전송합니다.(add())

> 이미 많이 강좌에서 진행했던 부분이니 길게 설명은 하지 않습니다.

# 결과

![alt result](/images/nemv/스크린샷 2018-11-15 오후 2.59.40.png)

이렇게 간단한 머스태시 태크로 해당 데이터를 텍스트로 확인해볼 수 있습니다.

> 콤포넌트로 나눠서 작업하려하다가 복잡도가 증가할 것 같아서 나중으로 미룹니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/72e32ac00de7b6d479095f09c9bac4393c111ce4)

{% endraw %}

# 영상

{% include video id="-phfRjhhLlM" provider="youtube" %}
