---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 54 한 소스로 사이트 다르게 사용하기
category: nemv
tag: [nemv,express,vue,vuetify,axios]
comments: true
sidebar:
  nav: "nemv4"
---

현재 소스를 가지고 다른 사람이 사용하려면 코드 수정이 필요합니다.

예를 들어 현재소스를 사용해서

abc.com의 타이틀은 'abc의 블로그'

xxx.com의 타아틀은 'xxx의 뉴스' 

처럼 만드려면 코드를 수정하지 않고는 각각 다르게 사용할 수 없는 것입니다.

[https://github.com/fkkmemi/nemv3](https://github.com/fkkmemi/nemv3) 소스로 다른 사람이 사용하게 타이틀과 하단 카피라이트부분을 동적으로 변경해보겠습니다. 

{% include toc %}

{% raw %}

# 사용자화에 필요한 것들

- 사용자화를 저장할 수 있는 디비 컬렉션
- 프론트에 사용자화 내용을 전달할 api
- 프론트 구동시 사용자화 된 내용을 읽음
- 사용자화를 변경할 수 있는 관리자 페이지

# 프론트 사용자화 할 요소들 정리하기

메뉴 밑 타이틀등은 전부 app.vue에 있습니다.

변경할 것들을 미리 만들어 놓고 로컬로 테스트해봅니다.

**fe/src/App.vue**  
```vue
<!-- html 부분 -->
<v-app :dark="siteDark">
<v-toolbar-title v-text="siteTitle">
<span>&copy; 2017 {{siteCopyright}}</span>

// script data
  data () {
    return {
      drawer: null,
      siteTitle: '기다리는중',
      siteCopyright: '기다리는중',
      siteDark: false,
```

v-app의 dark는 테마입니다. 현재는 dark냐 아니냐 밖에 없습니다. siteDark로 바인딩 시켜놓았습니다.

나머지는 값을 바꿔가면서 테스트 해보면 됩니다.

나중에 site... 변수들이 액시오스를 통해 변경되면 되는 것이죠.

# 모델 만들기

항상 새로운 작업은 모델링 후 생각하는 것이 생각하기 편합니다.

**be/models/sites.js**  
```javascript
const mongoose = require('mongoose')
const cfg = require('../../config')
 mongoose.set('useCreateIndex', true)
const siteSchema = new mongoose.Schema({
  title: { type: String, default: 'NEMV', index: true },
  copyright: { type: String, default: '© 2018 nemv' },
  dark: { type: Boolean, default: false }
})
 const Site = mongoose.model('Site', siteSchema)
 Site.findOne()
  .then(r => {
    if (!r) return Site.create({})
    return Promise.resolve(null)
  })
  .then(r => {
    if (r) console.log(`${r.title} site is created`)
  })
  .catch(e => console.error(e.message))
 module.exports = Site
```

프론트에서 사용할 3가지만 넣고 모델을 만들었습니다.

설정값은 여러개의 데이터일 필요는 없습니다. 

그래서 데이터가 하나도 없을때만 생성해 하도록 만들었습니다.

# api 만들기

**be/routes/api/index.js**  
```javascript
router.use('/sign', require('./sign'))
router.use('/site', require('./site'))
```

아무나 접근 가능한 api로 만듭니다.

**be/routes/api/site/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Site = require('../../../models/sites')
 router.get('/', (req, res, next) => {
  // return res.send({ success: true, d: req.user })
  Site.findOne({})
    .then(r => {
      res.send({ success: true, d: r })
    })
    .catch(e => {
      res.send({ success: false })
    })
});
 router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});
 module.exports = router;
```

단촐하게 겟 요청 하나만 있습니다.

데이터는 하나 밖에 없으니 findOne이고 받은 값 전송하면 끝인 겁니다.

이제 저장후 서버를 재시작합니다.

# 프론트에서 받기

이제 /api/site 에서 받아서 변경하면 끝입니다.

```javascript
  mounted () {
    this.getSite()
  },
  methods: {
    signOut () {
      // localStorage.removeItem('token')
      this.$store.commit('delToken')
      this.$router.push('/')
    },
    getSite () {
      this.$axios.get('/site')
        .then(r => {console.log(r.data.d)
          this.siteTitle = r.data.d.title
          this.siteCopyright = r.data.d.copyright
          this.siteDark = r.data.d.dark
        })
        .catch(e => console.error(e.message))
    }
  }
```

시작할 때 getSite() 하면 site... 값들이 적용됩니다.

# 적용된 모습

![alt site fe](/images/nemv/스크린샷 2018-11-05 오전 11.34.19.png)

타이틀과 하단부가 디비값으로 교체된 것이 확인됩니다.

# 관리 페이지 만들기

이제 저 기본값들을 변경할 수 있는 페이지가 있어야됩니다.

관리자만 사용할 수 있는 manage/ 에 넣어야겠죠?

데이터가 하나 밖에 없는 콜렉션이라 좀 다른 설계를 해야하지만.. 귀찮아 manage/page를 그대로 복사해서 만들어봤습니다.

# 사이트관리 api 만들기

**be/routes/api/manage/index.js**  
```javascript
router.use('/page', require('./page'))
router.use('/site', require('./site'))
```

manage에 site를 등록해줬습니다. 

**be/routes/api/manage/site/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();
const Site = require('../../../../models/sites')
 router.get('/', function(req, res, next) {
  Site.find()
    .then(r => {
      res.send({ success: true, sites: r })
    })
    .catch(e => {
      res.send({ success: false })
    })
});
 router.put('/:_id', (req, res, next) => {
  const _id = req.params._id
  Site.updateOne({ _id }, { $set: req.body})
    .then(r => {
      res.send({ success: true, msg: r })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
 router.delete('/:_id', (req, res, next) => {
  const _id = req.params._id
  Site.deleteOne({ _id })
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

기존 Page를 Site로 변경해준 것이 다입니다.

# 프론트에 사이트관리 추가하기

## 라우터 등록

**fe/src/router.js**  
```javascript
{
  path: '/site',
  name: '사이트',
  component: () => import('./views/site'),
  beforeEnter: pageCheck
},
```

라우터를 등록합니다.

## 메뉴 등록

**fe/src/App.vue**  
```javascript
{
  icon: 'face',
  title: '사이트관리',
  to: {
    path: '/site'
  }
}
```

메뉴를 등록합니다.

## 사이트 관리 페이지 제작

**fe/src/views/site.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-alert
      :value="!sites.length"
      type="warning"
    >
      데이터가 없습니다
    </v-alert>
    <v-layout row wrap>
      <v-flex xs12 sm6 md4 v-for="site in sites" :key="site._id">
        <v-card>
          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">{{site.title}}</h3>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-title primary-title>
            <div>
              <div>하단: {{site.copyright}}</div>
              <div>색상: {{site.dark}}</div>
            </div>
          </v-card-title>
          <v-divider light></v-divider>
          <v-card-actions>
            <v-btn flat color="orange" @click="putDialog(site)">수정</v-btn>
            <v-btn flat color="error" @click="delSite(site._id)">삭제</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
    <v-dialog v-model="dialog" persistent max-width="500px">
      <v-card>
        <v-card-title>
          <span class="headline">사이트 수정</span>
        </v-card-title>
        <v-card-text>
          <v-container grid-list-md>
            <v-layout wrap>
              <v-flex xs12 sm6 md4>
                <v-text-field
                  label="사이트 이름"
                  hint="아무거나"
                  persistent-hint
                  required
                  v-model="siteTitle"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-text-field
                  label="사이트 하단"
                  hint="아무거나"
                  persistent-hint
                  required
                  v-model="siteCopyright"
                ></v-text-field>
              </v-flex>
              <v-flex xs12 sm6>
                <v-switch
                  label="다크모드"
                  v-model="siteDark"
                ></v-switch>
              </v-flex>
            </v-layout>
          </v-container>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click="putSite">수정</v-btn>
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
      sites: [],
      dialog: false,
      siteTitle: '',
      siteCopyright: '',
      siteDark: false,
      snackbar: false,
      sbMsg: '',
      putId: ''
    }
  },
  mounted () {
    this.getSites()
  },
  methods: {
    getSites () {
      this.$axios.get(`${this.$apiRootPath}manage/site`)
        .then((r) => {
          this.sites = r.data.sites
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    putDialog (site) {
      this.putId = site._id
      this.dialog = true
      this.siteTitle = site.title
      this.siteCopyright = site.copyright
      this.siteDark = site.dark
    },
    putSite () {
      this.dialog = false
      this.$axios.put(`${this.$apiRootPath}manage/site/${this.putId}`, {
        title: this.siteTitle, copyright: this.siteCopyright, dark: this.siteDark
      })
        .then((r) => {
          this.pop('페이지 수정 완료')
          this.getSites()
        })
        .catch((e) => {
          this.pop(e.message)
        })
    },
    delSite (id) {
      this.$axios.delete(`${this.$apiRootPath}manage/site/${id}`)
        .then((r) => {
          this.pop('페이지 삭제 완료')
          this.getSites()
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

{% endraw %}

기존 page.vue를 복사해서 시험할 수 있는 정도로만 변경했습니다.

# 결과

## 수정 전

![alt site mod](/images/nemv/스크린샷 2018-11-05 오전 11.51.25.png)

## 수정 후

새로고침해야 변경된 것이 확인 가능합니다.

![alt site2](/images/nemv/스크린샷%202018-11-05%20오전%2011.51.44.png)

# 마치며

별 것 없는 기능이지만 지금까지의 지식으로 이제 응용만 잘하면 되겠다는 생각이 들 것입니다.

# 영상

{% include video id="DnHnZ7xVUPU" provider="youtube" %}   



