---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 19 axios 사용하기
category: nemv
tag: [nemv,api,express,vue,axios]
comments: true
sidebar:
  nav: "nemv2"
---

프론트에서 api를 통해 데이터를 받아서 화면을 변경해보겠습니다.

# api 만들기

**be/api/user/index.js**  
```javascript
var express = require('express');
var createError = require('http-errors');
var router = express.Router();

router.get('/', function(req, res, next) {
  const us = [
    {
      name: '김김김',
      age: 14
    },
    {
      name: '이이이',
      age: 24
    }
  ]
  res.send({ users: us })
});

router.all('*', function(req, res, next) {
  next(createError(404, '그런 api 없어'));
});

module.exports = router;
```

api user를 간단히 만듭니다.

**be/api/index.js**  
```javascript
router.use('/user', require('./user'))
```

상위에 user 라우터를 등록해줍니다.

http://localhost:3000/api/user 로 확인할 수 있습니다.

# axios 설치

프론트가 설치된 fe로 가서 설치합니다.

```bash
$ cd fe
$ yarn add axios
```

# 프론트 페이지 만들기

**fe/src/views/user.vue**  
```vue
<template>
  <div class="about">
    <h1>This is an about page</h1>  
  </div>
</template>
<script>
import axios from 'axios'

export default {
  mounted () {
    axios.get('http://localhost:3000/api/user')
      .then((r) => {
        console.log(r)
      })
      .catch((e) => {
        console.error(e.message)
      })
  }  
}
</script>
```

페이지 내에서 axios를 사용한다고 한 것입니다.

페이지가 열릴때 만들어둔 api/user로 부터 get 요청으로 데이터를 받을 수 있습니다.

# cors 설정

그러나 잘 되지 않습니다.

왜냐하면 localhost:8080이 localhost:3000한테 요청하는 것이기 때문에..

백엔드 서버는 외부 요청을 받아들이지 않습니다.

그래서 fe 소스를 yarn build 한 후에 localhost:3000으로 테스트 해보면 잘 되는 것을 알 수 있습니다.

자기가 자기한테 요청하는 것은 받아주기 때문입니다.

그래서 개발시에 편리하게 디버그하기 위해 백엔드에 cors라는 것을 설치해야 합니다.

## cors 설치

```bash
$ cd ../be
$ yarn add cors
```

## cors 사용

**be/app.js**  
```javascript
const cors = require('cors') // 상단 아무곳이나 추가

app.use(cors()) // api 위에서 사용하겠다고 선언
app.use('/api', require('./routes/api'))
```

이제 다른 서버에서 요청해도 데이터를 보내줄 수 있습니다.

localhost:8080에서도 잘 작동 됨을 확인 할 수 있습니다.

# 뷰티파이 콤포넌트로 확인해보기

**fe/src/views/user.vue**  
```vue
<template>
  <v-container grid-list-md text-xs-center>
    <v-layout row wrap>
      <v-flex xs12 sm6 md4 v-for="u in users">
        <v-chip close>{{u.name}}</v-chip>
        <v-card>
          <v-img
            src="https://cdn.vuetifyjs.com/images/cards/desert.jpg"
            aspect-ratio="2.75"
          ></v-img>

          <v-card-title primary-title>
            <div>
              <h3 class="headline mb-0">{{u.name}}</h3>
              <div>{{u.age}}</div>
            </div>
          </v-card-title>

          <v-card-actions>
            <v-btn flat color="orange">Share</v-btn>
            <v-btn flat color="orange">Explore</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import axios from 'axios'
export default {
  data () {
    return {
      users: []
    }
  },
  mounted () {
    axios.get('http://localhost:3000/api/user')
      .then((r) => {
        this.users = r.data.users
        console.log(r)
      })
      .catch((e) => {
        console.error(e.message)
      })
  }
}
</script>
```

뷰티파이 card 콤포넌트를 이용해서 화면을 채워봤습니다.

# 영상

{% include video id="QckudpVdZS4" provider="youtube" %}  


