---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 50 사용자 및 페이지 관리하기
category: nemv
tag: [nemv,express,vue,vuetify,vuex,mongoose]
comments: true
sidebar:
  nav: "nemv4"
---

사용자를 자동으로 만들어 주긴 하지만 권한등을 코드에서 제어하기 불편합니다.

그래서 화면을 만들어서 사용자 및 페이지 권한을 관리할 수 있는 시스템을 만들어 보겠습니다. 

{% include toc %}

{% raw %}

# 만들기 전에

이제 응용편이기 때문에 기본적인 설명은 하지 않습니다.

아래 열거한 내용들은 숙지하셔야 강좌 진행이 가능합니다.

- 백엔드: 모델 만들기, route/* 만들기
- 프론트엔드: 화면(*.vue) 등록, 라우터 등록, 메뉴 등록

# 시나리오

사용자는 이제 부터 4가지 권한을 가집니다.

**권한 레벨**  
- 0: 관리자(개발자 및 모든 권한을 가진 사용자)
- 1: 슈퍼유저(회원가입 후 승격된 사용자)
- 2: 일반유저(회원가입 후 기본 사용자)
- 3: 손님(토큰 없이 요청하는 사용자)

페지지 레벨과 사용자 레벨이 맞아야 진입이 되는 것입니다.

eg) 손님은 일반페이지에 들어갈 수 없음, 슈퍼유저는 일반페이지, 손님페이지는 들어가지만 관리자페이지에 못들어감

> 숫자 0을 최고권한으로 둔 이유는 그래야 계층을 밑으로 더 늘리기가 편해서 입니다.

페이지와 유저의 레벨을 관리할 수 있는 솔루션을 구축해놔야 테스트가 편합니다.

> 없으면 일일히 백엔드 껏다키면서 User.updateOne Page.deleteMany() 등을 해줘야합니다..

# 모델 만들기

## 사용자 모델 변경

**be/models/users.js**  
```javascript
const mongoose = require('mongoose')
const cfg = require('../../config')

mongoose.set('useCreateIndex', true)
const userSchema = new mongoose.Schema({
  name: { type: String, default: '' },
  age: { type: Number, default: 1 },
  id: { type: String, default: '', unique: true, index: true },
  pwd: { type: String, default: '' },
  lv: { type: Number, default: 2 }, //add
  inCnt: { type: Number, default: 0 }, //add
  retry: { type: Number, default: 0 }
})

const User = mongoose.model('User', userSchema)
// User.collection.dropIndexes({ name: 1})

User.findOne({ id: cfg.admin.id })
  .then((r) => {
    // console.log(r)
    if (!r) return User.create({ id: cfg.admin.id, pwd: cfg.admin.pwd, name: cfg.admin.name, lv: 0 })
    // if (r.lv === undefined) return User.updateOne({ _id: r._id }, { $set: { lv: 0, inCnt: 0 } }) // 임시.. 관리자 계정 레벨 0으로..
    return Promise.resolve(null)
  })
  .then((r) => {
    if (r) console.log(`admin:${r.id} created!`)
  })
  .catch((e) => {
    console.error(e.message)
  })

module.exports = User
```

사용자 레벨과 로그인 횟수를 추가했습니다.

## 페이지 모델

**be/models/pages.js**  
```javascript
const mongoose = require('mongoose')
const cfg = require('../../config')

mongoose.set('useCreateIndex', true)
const pageSchema = new mongoose.Schema({
  name: { type: String, default: '', index: true },
  inCnt: { type: Number, default: 0 },
  lv: { type: Number, default: 0 }
})

module.exports = mongoose.model('Page', pageSchema)
```

- 이름(name)은 '/home' 같은 url을 등록하는 것입니다.
- 페이지 레벨과 페이지 조회수를 추가했습니다.

# API 만들기

관리용 루트를 추가해서 세분화 했습니다.

**구조도**

- api
    - manage
        - user
        - page

## api/

**be/routes/api/index.js**  
```javascript
router.use('/sign', require('./sign'))
router.use('/manage', require('./manage'))
```

## api/manage/

**be/routes/api/manage/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();

router.use('/user', require('./user'))
router.use('/page', require('./page'))

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

manage/user or manage/page 만 받겠다는 것이죠.

## api/manage/user

**be/routes/api/manage/user/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const User = require('../../../../models/users')

router.get('/', function(req, res, next) {
  User.find()
    .then(r => {
      res.send({ success: true, users: r })
    })
    .catch(e => {
      res.send({ success: false })
    })
});

router.put('/:_id', (req, res, next) => {
  const _id = req.params._id
  User.updateOne({ _id }, { $set: req.body})
    .then(r => {
      res.send({ success: true, msg: r })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.delete('/:_id', (req, res, next) => {
  const _id = req.params._id
  User.deleteOne({ _id })
    .then(r => {
      res.send({ success: true, msg: r })
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

지난번 만든 user CRUD와 거의 비슷합니다.

생성은 이제 회원가입에서 해야하므로 제거했고 회원 수정에서 req.body를 통째로 넣어서 아무값이나 수정하게 만들었습니다.

## api/manage/page

**be/routes/api/manage/page/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Page = require('../../../../models/pages')

router.get('/', function(req, res, next) {
  Page.find()
    .then(r => {
      res.send({ success: true, pages: r })
    })
    .catch(e => {
      res.send({ success: false })
    })
});

router.put('/:_id', (req, res, next) => {
  const _id = req.params._id
  Page.updateOne({ _id }, { $set: req.body})
    .then(r => {
      res.send({ success: true, msg: r })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})

router.delete('/:_id', (req, res, next) => {
  const _id = req.params._id
  Page.deleteOne({ _id })
    .then(r => {
      res.send({ success: true, msg: r })
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

위에 user를 복사해서 page로 변경한 파일입니다.

# 프론트 만들기

## 라우터 등록

**fe/src/router.js**  
```javascript
import Vue from 'vue'
import Router from 'vue-router'
import axios from 'axios'
import Home from './views/Home.vue'

Vue.use(Router)

Vue.prototype.$axios = axios

export default new Router({
  mode: 'history',
  base: process.env.BASE_URL,
  routes: [
    {
      path: '/',
      name: 'lv0',
      component: () => import('./views/lv0')
    },
    {
      path: '/lv1',
      name: 'lv1',
      component: () => import('./views/lv1')
    },
    {
      path: '/lv2',
      name: 'lv2',
      component: () => import('./views/lv2')
    },
    {
      path: '/lv3',
      name: 'lv3',
      component: () => import('./views/lv3')
    },
    {
      path: '/user',
      name: '사용자',
      component: () => import('./views/user')
    },
    {
      path: '/page',
      name: '페이지',
      component: () => import('./views/page')
    },    
    {
      path: '/block',
      name: '차단',
      component: () => import('./views/block')
    },
    {
      path: '/test',
      name: 'test',
      component: () => import('./views/test')
    },
    {
      path: '/sign',
      name: '로그인',
      component: () => import('./views/sign')
    },
    {
      path: '*',
      name: 'e404',
      component: () => import('./views/e404')
    }
  ]
})
```

- 자주 쓰는 axios를 프로토로 등록 해두었습니다. (_this.$axios 로 다른 컴포넌트에서 사용가능_)
- lv0~3 까지 테스트옹 페이지를 등록했습니다.

## 레벨테스트용 페이지들

**fe/src/views/lv0.vue**  
```vue
<template>
  <v-container>
    <v-alert type="info" :value="true">
      레벨 0 관리자
    </v-alert>
  </v-container>
</template>
```

나머지 lv1~3도 글자만 수정해서 만들면 됩니다.

## 사용자 관리

**fe/src/views/user.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6 md4 v-for="user in users" :key="user._id">

        <v-card>
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">{{user.id}}</h3>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-title primary-title>
            <div>
              <div>이름: {{user.name}}</div>
              <div>권한: {{user.lv}}</div>
              <div>나이: {{user.age}}</div>
              <div>로그인 횟수: {{user.inCnt}}</div>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-actions>
            <v-btn flat color="orange" @click="putDialog(user)">수정</v-btn>
            <v-btn flat color="error" @click="delUser(user._id)">삭제</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">User Profile</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field
                  label="이름"
                  hint="홍길동"
                  persistent-hint
                  required
                  v-model="userName"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-select
                  :items="userLvs"
                  label="권한"
                  required
                  v-model="userLv"
                ></v-select>
              </v-flex>
              <v-flex xs12 sm6>
                <v-select
                  :items="userAges"
                  label="나이"
                  required
                  v-model="userAge"
                ></v-select>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="putUser">수정</v-btn>
          <v-btn color="blue darken-1" flat @click.native="dialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model="snackbar"
    >
      {{ sbMsg }}
      <v-btn
        color="pink"
        flat
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </v-container>
</template>
<script>

export default {
  data () {
    return {
      users: [],
      dialog: false,
      userAges: [],
      userLvs: [],
      userAge: 0,
      userLv: 0,
      userName: '',
      snackbar: false,
      sbMsg: '',
      putId: ''
    }
  },
  mounted () {
    for (let i = 10; i < 40; i++) this.userAges.push(i)
    for (let i = 0; i < 4; i++) this.userLvs.push(i)
    this.getUsers()
  },
  methods: {
    getUsers () {
      this.$axios.get(`${this.$apiRootPath}manage/user`)
        .then((r) => {
          this.users = r.data.users
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    putDialog (user) {
      this.putId = user._id
      this.dialog = true
      this.userName = user.name
      this.userLv = user.lv
      this.userAge = user.age
    },
    putUser () {
      this.dialog = false
      this.$axios.put(`${this.$apiRootPath}manage/user/${this.putId}`, {
        name: this.userName, lv: this.userLv, age: this.userAge
      })
        .then((r) => {
          this.pop('사용자 수정 완료')
          this.getUsers()
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    delUser (id) {
      this.$axios.delete(`${this.$apiRootPath}manage/user/${id}`)
        .then((r) => {
          this.pop('사용자 삭제 완료')
          this.getUsers()
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    pop (msg) {
      this.snackbar = true
      this.sbMsg = msg
    }
  }
}
</script>
```

지난 강좌에서 만들어두었던 파일을 불필요한 부분 제거하고 그대로 사용했습니다.

권한 조정할 수 있는 기능 하나 추가된 것이 다입니다.

## 페이지 관리

**fe/src/views/page.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-alert
      :value="!pages.length"
      type="warning"
    >
      데이터가 없습니다
    </v-alert>
    <v-layout row wrap>
      <v-flex xs12 sm6 md4 v-for="page in pages" :key="page._id">
        <v-card>
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">{{page.name}}</h3>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-title primary-title>
            <div>
              <div>권한: {{page.lv}}</div>
              <div>진입 횟수: {{page.inCnt}}</div>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-actions>
            <v-btn flat color="orange" @click="putDialog(page)">수정</v-btn>
            <v-btn flat color="error" @click="delPage(page._id)">삭제</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">페이지 수정</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field
                  label="페이지 이름"
                  hint="게시판"
                  persistent-hint
                  required
                  v-model="pageName"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-select
                  :items="pageLvs"
                  label="권한"
                  required
                  v-model="pageLv"
                ></v-select>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="putPage">수정</v-btn>
          <v-btn color="blue darken-1" flat @click.native="dialog = false">Close</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
      v-model="snackbar"
    >
      {{ sbMsg }}
      <v-btn
        color="pink"
        flat
        @click="snackbar = false"
      >
        Close
      </v-btn>
    </v-snackbar>
  </v-container>
</template>
<script>

export default {
  data () {
    return {
      pages: [],
      dialog: false,
      pageLvs: [],
      pageLv: 0,
      pageName: '',
      snackbar: false,
      sbMsg: '',
      putId: ''
    }
  },
  mounted () {
    for (let i = 0; i < 4; i++) this.pageLvs.push(i)
    this.getPages()
  },
  methods: {
    getPages () {
      this.$axios.get(`${this.$apiRootPath}manage/page`)
        .then((r) => {
          this.pages = r.data.pages
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    putDialog (page) {
      this.putId = page._id
      this.dialog = true
      this.pageName = page.name
      this.pageLv = page.lv
    },
    putPage () {
      this.dialog = false
      this.$axios.put(`${this.$apiRootPath}manage/page/${this.putId}`, {
        name: this.pageName, lv: this.pageLv
      })
        .then((r) => {
          this.pop('페이지 수정 완료')
          this.getPages()
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    delPage (id) {
      this.$axios.delete(`${this.$apiRootPath}manage/page/${id}`)
        .then((r) => {
          this.pop('페이지 삭제 완료')
          this.getPages()
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    pop (msg) {
      this.snackbar = true
      this.sbMsg = msg
    }
  }
}
</script>
```

사용자 관리를 조금 바꾸어서 만들었습니다.

## 메뉴 등록

**fe/src/App.vue**  
```javascript
export default {
  name: 'App',
  data () {
    return {
      drawer: null,
      items: [
        {
          icon: 'home',
          title: 'lv0',
          to: {
            path: '/'
          }
        },
        {
          icon: 'home',
          title: 'lv1',
          to: {
            path: '/lv1'
          }
        },
        {
          icon: 'home',
          title: 'lv2',
          to: {
            path: '/lv2'
          }
        },
        {
          icon: 'home',
          title: 'lv3',
          to: {
            path: '/lv3'
          }
        },
        {
          icon: 'face',
          title: '사용자관리',
          to: {
            path: '/user'
          }
        },
        {
          icon: 'face',
          title: '페이지관리',
          to: {
            path: '/page'
          }
        }
      ],
      title: this.$apiRootPath
    }
  },
  mounted () {
  },
  methods: {
    signOut () {
      this.$store.commit('delToken')
      this.$router.push('/')
    }
  }
}
```

간단하게 메뉴 아이템을 수정했습니다.

# 결과

![alt result](/images/nemv/스크린샷 2018-11-02 오후 7.11.44.png)

- 각 lv0~3 페이지가 잘 열리는 지 확인
- 유저 수정 삭제가 가능한지 확인

코드양이 많을 뿐 그전 강좌를 이해하셨다면 충분히 이해 가능하실 겁니다~

{% endraw %}

# 영상

{% include video id="7FHHTvrmBU8" provider="youtube" %}   




