---
layout: single
title: NEMBV 10 Front-end api test
category: nembv
tag: [nembv,nodejs,vue,api,bootstrap,cors]
comments: true
sidebar:
  nav: "nembv"
---

front-end에서 최소한의 RESTful api 요청/응답을 받을 수 있는 페이지를 만들어보도록 하겠다.

> 이하 'front-end': 'fe', 'back-end': 'be'로 설명하겠다.

이제 /fe 디렉토리를 살펴봐야할 때다.

시험을 하려면 be, fe server를 둘다 켜야한다.   
```bash
$ npm start # be server on
$ cd fe
$ npm run dev # fe server on
```

> fe server가 켜질때 브라우저가 자동으로 켜지는데 불편하면 config/index.js 중 dev.autoOpenBrowser를 false로 변경하면된다.  
기본브라우저가 사파리인데 개발용도는 역시 크롬이 좋기 때문이다.

{% include toc %}

{% raw %}

# 화면표시 동작 순서
fe가 브라우저에 켜지면 'Welcome to Your Vue.js App' 이 표시되는데

index.html > src/main.js > app.vue > src/components/hello.vue 정도라고 보면된다.

## fe router test
이제 라우터를 수정하고 테스트 페이지를 만들어서 test.vue와 hello.vue를 스위칭 해보겠다.

![test vue](/images/nembv/2018-03-20 11-37-42 test vue.png)

### test.vue를 추가
**fe/src/components/test.vue**  

```vue
<template>
  <div>
    <h1>{{ msg }}</h1>
  </div>
</template>

<script>
export default {
  name: 'test',
  data() {
    return {
      msg: '여긴 테스트 페이지임',
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

- ``` {{ msg }} ``` 는 code highlight vue를 아직 안받아서 띄어 쓴것이 다 원래는 붙여야한다.  
vue.js의 시작하기 첫장에 나오는 예제다. *선언적 렌더링*

> [https://kr.vuejs.org/v2/guide/index.html](https://kr.vuejs.org/v2/guide/index.html) vue는 한글화가 너무 잘되어있다.

### 라우트 포지션을 추가
**fe/src/router/index.js**  
```javascript
import Vue from 'vue';
import Router from 'vue-router';
import Hello from '@/components/Hello';
import test from '@/components/test';

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: '/',
      name: 'Hello',
      component: Hello,
    },
    {
      path: '/test',
      name: 'test',
      component: test,
    },
  ],
});
```

### fe route 시험
localhost:8080/ -> hello  
localhost:8080/#/test -> test

여기서 localhost:8080/test를 눌러보면 localhost:8080/test#/ 으로 이동하면서 hello로 가는 것을 볼 수 있다.

뷰라우터는 곧 해시태그 다음을 라우팅 한다는 것이다.

그리고 localhost:8080/#/abc 같이 없는 곳을 해보면 뷰 로고만 둥 떠있는 것을 알수 있다.

그 이유는 app.vue만 로드하고 그 하위 레이어에는 라우트 포인트를 지정하지 않아서 아무 표시를 할 수 없는 것이다.

### test를 위해 라우트 편하게 변경
**fe/src/router/index.js**  
```javascript
import Vue from 'vue';
import Router from 'vue-router';
import Hello from '@/components/Hello';
import test from '@/components/test';

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: '/hello',
      name: 'Hello',
      component: Hello,
    },
    {
      path: '/',
      name: 'test',
      component: test,
    },
  ],
});
```

### axios 추가
일반적으로 jquery사용할때 $(ajax)를 많이 이용했었다.  
vue에 jquery를 깔아도 되지만.. pure하게 vue를 즐겨야 되기 때문에(*사실 부트스트랩에 의존요소로 미니jquery가 깔려있긴하다*)
fe에 xhr 요청을 하기 위한 axios를 추가해본다.

/fe 에서 설치해야한다.  
```bash
$ npm i axios --save
```

axios는 이제 모든 페이지서 사용될 것이기 때문에 전역 선언을 한다.

**fe/src/main.js**  
```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue';
import BootstrapVue from "bootstrap-vue";
import App from './App';
import router from './router';
import axios from 'axios'; // add

import 'bootstrap/dist/css/bootstrap.min.css';
import 'bootstrap-vue/dist/bootstrap-vue.css';

Vue.prototype.$axios = axios; // add
Vue.use(BootstrapVue);
Vue.config.productionTip = true;

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App },
});
```

- 이제 다른 페이지(컴포넌트)에서 this.$axios로 어디서든 사용 가능하다.

### test button 추가

이제 약간 fe엔지니어 느낌 나는 일들을 해야한다.

css framework로 bootstrap을 사용하는데 이제 화면을 구성할 때 항상 두 곳을 자습서라 생각하고 자주 둘러봐야한다.

1. [https://bootstrap-vue.js.org/docs](https://bootstrap-vue.js.org/docs)  
2. [https://getbootstrap.com/docs/4.0/getting-started/introduction/](https://getbootstrap.com/docs/4.0/getting-started/introduction/)    

주로 1번 링크를 많이 참조해야하지만 몇몇 클래스 정의에 대한 문서는 2번에서 찾을 수 있다.

우선 카드그룹 4개로 버튼과 결과를 받을 폼을 만들어 보겠다.

카드 컴포넌트는 [https://bootstrap-vue.js.org/docs/components/card](https://bootstrap-vue.js.org/docs/components/card) 보면서 용도에 맞게 쓰면 된다.

**fe/src/components/test.vue**  
```html
<template>
  <div>
    <b-card-group deck class="m-3">
      <b-card header="POST">
        <b-form-textarea v-model="txtPost" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendPost"
                  variant="primary">전송</b-button>
      </b-card>
      <b-card header="GET">
        <b-form-textarea v-model="txtGet" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendGet"
                  variant="success">전송</b-button>
      </b-card>
      <b-card header="PUT">
        <b-form-textarea v-model="txtPut" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendPut"
                  variant="warning">전송</b-button>
      </b-card>
      <b-card header="DELETE">
        <b-form-textarea v-model="txtDelete" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendDelete"
                  variant="danger">전송</b-button>
      </b-card>
    </b-card-group>

  </div>
</template>

<script>
export default {
  name: 'test',
  data() {
    return {
      txtPost: '',
      txtGet: '',
      txtPut: '',
      txtDelete: '',
    };
  },
  methods: {
    sendPost() {
      this.txtPost = 'pppp';
    },
    sendGet() {
      this.txtGet = 'ggggg';
    },
    sendPut() {
      this.txtPut = 'uuuuu';
    },
    sendDelete() {
      this.txtDelete = 'ddddd';
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

아마 vue를 사용해본적이 없다면 난해할 것이다.. bootstrap과 같이 설명해야 하기 때문에 글이 좀 길다.

> 백엔드 엔지니어는 장황한 설명에 당황하지 말고 잘 버티길..  
사실 몇가지 패턴만 익히면 어느정도 사용하는데 불편함이 없어진다.

- b-card-group: deck or column 두종류가 있으며 위의 링크에 가면 알 수 있다.
- class m-3: margin을 동서남북 3 준다는 것이다. **bootstrap을 쓴다면 이제 style 속성은 가급적 사용을 자제하고 만들어진 클래스를 사용해야 와꾸가 맞아 이쁘니 주의!**
- b-card: 기존 3.X 버전의 panel 과 역활이 같다. 글만 있으면 썰렁할 때 박스 쳐주는 용도임.. b-card-group 없이도 사용 가능하다.
- b-form-textarea: [https://bootstrap-vue.js.org/docs/components/form-textarea](https://bootstrap-vue.js.org/docs/components/form-textarea) 보고 만든것..
- v-model: txtPost의 값이 변하면 같이 바뀐다. vue를 쓰는 이유=binding
- class mb-3: margin bottom 3
- b-button: @click으로 아래 methods 이벤트 핸들러에 연결되었다.
- variant: 부트스트랩을 대표하는 상징적인 색에 대한 강조 표시인데 일반적으로 'success,warning,danger,primary,info' 등이 있었는데 4버전에서 'secondary' 등 몇개가 추가된 듯 하다.

**출력 화면**  
![테스트페이지 1](/images/nembv/2018-03-20 13-30-41 fe.png)

### test button axios 연결

test를 위해 sendGet()만 시험해봤다.

**fe/src/components/test.vue methods**  
```javascript
sendGet() {
  this.$axios.get('http://localhost:3000/api/data/company')
    .then((res) => {
      this.txtGet = JSON.stringify(res.data);
    })
    .catch((err) => {
      this.txtGet = JSON.stringify(err);
    });
},
```

- sendGet() 부분만 변경해본 것인데 네트워크 에러 가 났다. 크롬 콘솔에 자세한 내용이 쓰여 있는데

내용은 -Failed to load http://localhost:3000/api/data/company: No 'Access-Control-Allow-Origin' header is present on the requested resource. Origin 'http://localhost:8080' is therefore not allowed access.-

이다 be server:3000 과 fe server:8080이 다르기 때문에 보안상 허용하지 않는 것이다.

실제 배포된 서버(npm run build)에서는 잘 될것이다.

이것은 express에 cors를 사용하면 해결이 된다.

개발용과 배포용이 필요하기 때문에 설정값으로 만들어 둔다.

**cfg/cfg.js**  
```javascript
  module.exports = {
    db: {
      url: 'mongodb://address',      
    },
    web: {
      cors: true, //개발용
    },
  };
```

**cors 설치**
```bash
$ cd .. # back-end에서 해야한다.
$ npm i cors --save
```

be server에 routing 하기전 추가한다. 그래서 약간의 정리를 했다.

**app.js**  
```javascript
var express = require('express');
var path = require('path');
var favicon = require('serve-favicon');
var logger = require('morgan');
var cookieParser = require('cookie-parser');
var bodyParser = require('body-parser');
const mongoose = require('mongoose');
const cfg = require('./cfg/cfg');
const pg = require('./playGround');

if (!cfg) {
  console.error('./cfg/cfg.js file not exists');
  process.exit(1);
}

var app = express();

if(cfg.web.cors) app.use(require('cors')());

app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
app.use(logger('dev'));
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
app.use(cookieParser());
app.use(express.static(path.join(__dirname, 'fe', 'dist')));
app.use(express.static(path.join(__dirname, 'public')));

app.use('/api', require('./routes/api'));

// catch 404 and forward to error handler
app.use(function(req, res, next) {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

// error handler
app.use(function(err, req, res, next) {
  // set locals, only providing error in development
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};

  // render the error page
  res.status(err.status || 500);
  res.send({ success: false, msg: err.message });
});

mongoose.connect(cfg.db.url, (err) => {
  if (err) return console.error(err);
  console.log('mongoose connected');
  pg.test.model();
});

module.exports = app;
```

반영하려면 be, fe server를 둘다 다시 켜본다.   
```bash
$ npm start # be server on
$ cd fe
$ npm run dev # fe server on
```

잘 동작되는 것을 확인 했다면 코드를 정리한다.

**fe/src/components/test.vue**  
```html
<template>
  <div>
    <b-card-group deck class="m-3">
      <b-card header="POST">
        <b-form-textarea v-model="txtPost" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendPost"
                  variant="primary">전송</b-button>
      </b-card>
      <b-card header="GET">
        <b-form-textarea v-model="txtGet" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendGet"
                  variant="success">전송</b-button>
      </b-card>
      <b-card header="PUT">
        <b-form-textarea v-model="txtPut" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendPut"
                  variant="warning">전송</b-button>
      </b-card>
      <b-card header="DELETE">
        <b-form-textarea v-model="txtDelete" class="mb-3"
                         placeholder="이곳에 결과 값이 나옴"
                         :rows="3"
                         :max-rows="6">
        </b-form-textarea>
        <b-button @click="sendDelete"
                  variant="danger">전송</b-button>
      </b-card>
    </b-card-group>

  </div>
</template>

<script>
export default {
  name: 'test',
  data() {
    return {
      txtPost: '',
      txtGet: '',
      txtPut: '',
      txtDelete: '',
    };
  },
  methods: {
    sendPost() {
      this.$axios.post('http://localhost:3000/api/data/company')
        .then((res) => {
          this.txtPost = JSON.stringify(res.data);
        })
        .catch((err) => {
          this.txtPost = JSON.stringify(err);
        });
    },
    sendGet() {
      this.$axios.get('http://localhost:3000/api/data/company')
        .then((res) => {
          this.txtGet = JSON.stringify(res.data);
        })
        .catch((err) => {
          this.txtGet = JSON.stringify(err);
        });
    },
    sendPut() {
      this.$axios.put('http://localhost:3000/api/data/company')
        .then((res) => {
          this.txtPut = JSON.stringify(res.data);
        })
        .catch((err) => {
          this.txtPut = JSON.stringify(err);
        });
    },
    sendDelete() {
      this.$axios.delete('http://localhost:3000/api/data/company')
        .then((res) => {
          this.txtDelete = JSON.stringify(res.data);
        })
        .catch((err) => {
          this.txtDelete = JSON.stringify(err);
        });
    },
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

**출력 화면**  
![테스트페이지 2](/images/nembv/2018-03-20 14-42-19 fe.png)

### bootstrap mobile

bootstrap의 장점중 제일 중요한 것은 모바일 지원인데

모바일 지원을 위해서는 기본 골격 meta tag에 viewport를 추가해야한다.

**fe/index.html**  
```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8">
    <meta name="viewport" content="width=device-width, initial-scale=1, shrink-to-fit=no">
    <meta http-equiv="content-type" content="text/html; charset=UTF-8">
    <title>nembv project</title>
  </head>
  <body>
    <div id="app"></div>
    <!-- built files will be auto injected -->
  </body>
</html>
```

**추가후 모바일 화면**  
![테스트페이지 3](/images/nembv/2018-03-20 16-15-36 nembv project.png)

{% endraw %}
