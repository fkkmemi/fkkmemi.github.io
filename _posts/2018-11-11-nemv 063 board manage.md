---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 63 게시판 관리 만들기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

일반적으로 운영할 게시판을 만드려면 무엇이 필요한지 생각해봅니다.

{% include toc %}

{% raw %}

# 시나리오

게시판으로 도배된 사이트중 한 곳에 들어가서 구조를 한번 살펴보겠습니다.

![alt boards](/images/nemv/2018-11-12 08-34-44.png)

![alt articles](/images/nemv/2018-11-12 08-40-04.png)

여러개의 게시판들이 있는 [dcinside](https://dcinside.com)라는 사이트입니다.

> 그냥 임의 선정입니다. 5년에 1번 갈까말까 하는 곳임..

게시판 하나 만들어서 유형별로 관리 하는 것 같습니다.

어떤식으로 만든 것인지는 제작자만 알테지만 가볍게 유추해서 비슷하게 만들어 볼 작전을 짭니다.

- boards는 게시판들의 집합
- board는 게시판의 이름(유형)
- articles은 게시물들의 집합: board 하위 관계 요소들
- article은 게시물

여러개의 게시판을 구현하려면 두가지 생각을 해볼 수 있습니다.

- 각각의 저장소에 담는 방법: 콜렉션(모델) 여러개(_중세시대 게시판, 소녀전선 게시판등을 따로 만듬_)
- 한개의 저장소에 담는 방법: 콜렉션(모델) 한개에 유형별 정렬(_전체 게시판에 모든 게시물을 넣고 유형으로 선택_)

장단점이 있습니다만.. 이 중 한개의 저장소에 담는 방법으로 선택 해보겠습니다.

관계형 디비가 아닌 몽고디비라 한 덩어리로 쌓아놓고 잘 찾고 싶네요~(_몇십억 데이터도 0.001초 이내에 찾아 내니 걱정마세요_) 

# 게시판 관리 만들기

먼저 게시물들의 유형을 판단할 게시판 관리를 만들어 보겠습니다.

# 벡엔드

## 모델 만들기

아주 단순하게 만듭니다. 

**be/models/boards.js**  
```javascript
const mongoose = require('mongoose')
const cfg = require('../../config')

mongoose.set('useCreateIndex', true)
const boardSchema = new mongoose.Schema({
  name: { type: String, default: '', index: true, unique: true },
  lv: { type: Number, default: 0 },
  rmk: { type: String, default: '' }
})

module.exports = mongoose.model('Board', boardSchema)
```

- 헷갈릴 수 있으니 같은 게시판명은 피하려고 이름에 유니크를 넣었습니다.
- 권한은 기본값으로 관리자만 쓸 수 있게 만듭니다. 전체 사용가로 바꾸려면 lv: 3로 나중에 변경하면 됩니다.

## 라우터 만들기

관리자만 좌지우지 할 페이지를 만들 것이니 manage/에 넣습니다.

**be/routes/api/manage/index.js**
```javascript
router.use('/board', require('./board'))
```  

**be/routes/api/manage/board/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Board = require('../../../../models/boards')

router.post('/', (req, res, next) => {
  const { name, lv, rmk } = req.body
  Board.create({ name, lv, rmk })
    .then(r => {
      res.send({ success: true, msg: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.get('/', function(req, res, next) {
  Board.find()
    .then(rs => {
      res.send({ success: true, ds: rs, token: req.token })
    })
    .catch(e => {
      res.send({ success: false })
    })
});

router.put('/:_id', (req, res, next) => {
  const _id = req.params._id
  Board.updateOne({ _id }, { $set: req.body})
    .then(r => {
      res.send({ success: true, msg: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.delete('/:_id', (req, res, next) => {
  const _id = req.params._id
  Board.deleteOne({ _id })
    .then(r => {
      res.send({ success: true, msg: r, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

기존 api 재탕에 생성(post)부분만 넣었습니다.(_이제 익숙해 질 때가 되었죠?_)

# 프론트엔드

프론트엔드는 지난 강좌에서 배운 콤포넌트로 나눌 것입니다.

CRUD(Create Read Update Delete) 중 CR은 view에서 하고 UD는 componet 에서 하는 것입니다.

파일이 슬슬 많아지는 추세이니 manage라는 폴더로 관리 합니다.(_다른 것들은 귀찮으니 나중에..._)

# 뷰티파이 업그레이드

뷰티파이가 1.3.7이 되었습니다. 업그레이드는 자유입니다.

```bash
$ cd fe
$ yarn upgrade vuetify
```

## 목록: 부모

**fe/src/views/manage/boards.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-alert
      :value="!boards.length"
      type="warning"
    >
      데이터가 없습니다
    </v-alert>
    <v-layout row wrap>
      <v-flex xs12 sm6 md4 v-for="board in boards" :key="board._id">
        <board-card :board="board" @list="list"></board-card>
      </v-flex>
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
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">게시판 추가</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field
                  label="게시판 이름"
                  hint="당구모임"
                  persistent-hint
                  required
                  v-model="form.name"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-text-field
                  label="게시판 설명"
                  hint="당구를 좋아하는 사람"
                  persistent-hint
                  required
                  v-model="form.rmk"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-select
                  :items="lvs"
                  label="권한"
                  required
                  v-model="form.lv"
                ></v-select>
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
      boards: [],
      dialog: false,
      lvs: [0, 1, 2, 3],
      form: {
        name: '',
        rmk: '',
        lv: 0
      },
      selected: 0,
      sb: {
        act: false,
        msg: '',
        color: 'error'
      }
    }
  },
  mounted () {
    this.list()
  },
  methods: {
    addDialog () {
      this.dialog = true
      this.form = {
        name: '',
        rmk: '',
        lv: 0
      }
    },
    add () {
      if (!this.form.name) return this.pop('이름을 작성해주세요', 'warning')
      this.$axios.post('manage/board', this.form)
        .then((r) => {
          this.dialog = false
          this.list()
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    list () {
      this.$axios.get('manage/board')
        .then(({ data }) => {
          this.boards = data.ds
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

지난 번과 별 다를 것이 없습니다. 함수명만 조금 변경했습니다.

boardCard 부분을 콤포넌트로 만들어서 board를 넘깁니다.

하는 일은 게시판 추가와 목록 갱신 뿐입니다. 

## 상세 내용: 자식

**fe/src/componets/manage/board.vue**  
```vue
<template>
  <v-card>
    <template v-if="!edit">
      <v-card-title primary-title>
        <h3 class="headline mb-0">{{board.name}}</h3>
      </v-card-title>
      <v-divider light></v-divider>
      <v-card-text>
        <div>권한: {{board.lv}}</div>
        <div>설명: {{board.rmk}}</div>
      </v-card-text>

      <v-divider light></v-divider>
      <v-card-actions>
        <v-btn flat color="orange" @click="modeChange(board)">수정</v-btn>
        <v-btn flat color="error" @click="ca=true">삭제</v-btn>
      </v-card-actions>
    </template>
    <template v-else>
      <v-card-title>
        <span class="headline">게시판 수정</span>
      </v-card-title>
      <v-card-text>
        <v-form>
        <v-text-field
          label="게시판 이름"
          :hint="form.name ? '' : '야구모임'"
          persistent-hint
          required
          v-model="form.name"
        ></v-text-field>

        <v-text-field
          label="게시판 설명"
          :hint="form.rmk ? '' : '야구를 좋아하는 사람'"
          persistent-hint
          required
          v-model="form.rmk"
        ></v-text-field>

        <v-select
          :items="lvs"
          label="권한"
          required
          v-model="form.lv"
        ></v-select>
      </v-form>

      </v-card-text>
      <v-card-actions>
        <v-spacer></v-spacer>
        <v-btn color="green darken-1" flat @click="mod(board)">확인</v-btn>
        <v-btn color="error darken-1" flat @click.native="edit = false">취소</v-btn>
      </v-card-actions>
    </template>

    <v-card-text v-if="ca">
      <v-alert v-model="ca" type="warning">
        <h4>정말 진행 하시겠습니까?</h4>
        <v-btn color="error" @click="del(board)">확인</v-btn>
        <v-btn color="secondary" @click="ca=false">취소</v-btn>
      </v-alert>
    </v-card-text>
    <v-card-text v-if="ma.act">
      <v-alert v-model="ma.act" :type="ma.type" dismissible>{{ma.msg}}</v-alert>
    </v-card-text>
  </v-card>
</template>
<script>

export default {
  props: [ 'board' ],
  data () {
    return {
      ca: false,
      ma: {
        act: false,
        msg: '',
        type: 'error'
      },
      lvs: [0, 1, 2, 3],
      form: {
        name: '',
        lv: 0,
        rmk: ''
      },
      edit: false
    }
  },
  methods: {
    modeChange (b) {
      this.edit = true
      this.form = {
        name: b.name,
        lv: b.lv,
        rmk: b.rmk
      }
    },
    mod (board) {
      if (board.name === this.form.name && board.rmk === this.form.rmk && board.lv === this.form.lv) return this.pop('변경한 것이 없습니다.', 'warning')
      this.$axios.put(`manage/board/${board._id}`, this.form)
        .then((r) => {
          if (!r.data.success) throw new Error(r.data.msg)
          board.name = this.form.name
          board.rmk = this.form.rmk
          board.lv = this.form.lv
          this.edit = false
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    del (board) {
      this.$axios.delete(`manage/board/${board._id}`)
        .then((r) => {
          if (!r.data.success) throw new Error(r.data.msg)
          this.$emit('list')
        })
        .catch((e) => {
          this.pop(e.message, 'error')
        })
    },
    pop (m, t) {
      if (this.ma.act) return
      this.ma.act = true
      this.ma.msg = m
      this.ma.type = t
      setTimeout(() => {
        this.ma.act = false
      }, 6000)
    }
  }
}
</script>
```

키(_id)를 가지고 있는 자식은 혼자 수정과 삭제가 가능합니다.

부모로 보낼 때는 삭제하고 난 후 목록 갱신(this.$emit('list')) 정도 입니다.

부모의 다이얼로그(dialog)를 재활용 할 수도 있지만.. 컨셉이 별로 입니다.

자식이 할 수 있는 일은 최대한 자식 혼자 하는 것이 좋습니다.(_부모와 자식간에 통신 여러번 하면 안쓰는 것이 낫습니다._)

혼자 처리하기 위해서 한 것들

- 수정모드를 만들어서 토글합니다.(template v-if v-else)

- 스낵바(v-snackbar) 대신에 알럿(v-alert)를 이용해 카드 하단에 각종 내용을 뿌립니다. 

## 라우터 등록

**fe/src/router.js**  
```javascript
// ..
{
  path: '/manage/boards',
  name: 'manageBoards',
  component: () => import('./views/manage/boards'),
  beforeEnter: pageCheck
},
// ..
```

## 메뉴 등록

**fe/src/App.vue**
```javascript
// ..
{
  icon: 'settings',
  title: '게시판관리',
  to: {
    path: '/manage/boards'
  }
}
// ..
```

# 마치며

자식 콤포넌트 분리로 조금 복잡해 보입니다.. 

하지만 한 페이지 코드가 너무 길어지면 유지보수가 힘들어 집니다.

이제 수정과 삭제에 문제 있을 때는 자식만 확인하면 되니까요~

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/2320b52c2747df399a9a94070eaceb1066fb0045)

{% endraw %}

# 영상

{% include video id="fpUUnnY905A" provider="youtube" %}
