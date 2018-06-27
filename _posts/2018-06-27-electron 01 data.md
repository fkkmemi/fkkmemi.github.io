---
layout: single
title: Electron 1 일렉트론뷰로 우아한 데스크탑 앱 만들기 데이터 다루기
category: electron
tag: [electron,talk,idea,nodejs,vue,vuetify]
comments: true
sidebar:
  nav: "electron"
---

이번에는 데스크탑 앱으로 데이터를 다뤄보겠습니다.

예전 윈도우7 이전시절 윈도우 프로그램 만들 때는 아무 걱정 없이 아무데나 파일을 쓸 수 있었습니다.(_7이상 이래봤자 UAC 확인 버튼 한번 누르면 마찬가지긴 합니다.._)

당연히 그런 보안으로는 exe 파일 한번 잘못 눌렀다가 랜섬 걸리기 딱 좋죠

저시절 보안으로 랜섬웨어를 만든다면 아마도 

```javascript
const char Keys[] = "this is my secret key 어쩌구 저꺼구 길고 긴 키";

recursiveFiles("C:\\"); // 시작할때

String recursiveFiles(String &p) {
    // 재귀 호출로 하위 디렉토리 다 돌면서 파일만 색출
    changeFile(any_file, &Keys);
}

bool encodeFile(String& o, unsigned char* ks) {
    if (systemPath(o)) return; // "C:\windows" 이런 곳은 걸르는 함수 만듬..
    fileRead(o, rs) { // bytes 단위로 연다.
        fileDelete(o); // 데이터 열람했으니 과감히 지움
        for (int i = 0; i < rs.length; i++) rs[i] += ks[i % sizeof(ks)]; // 데이터 꼬아놓음

        String cpath = chageFileName(o); // 지랄 맞은 파일명으로 변경
        fileWrite(cpath, rs); // 지랄 맞은 파일명과 꼬인 데이터로 파일 생성
    }
}

// bool decodeFile( // ... 푸는 것도 만듬..
```

대충 이렇게 만들 것 같습니다.

> 간만에 느낌대로 c로 휘갈겨 본겁니다.. 파일콘트롤은 매우 위험하다는 예시

윈도우 프로그램 처음 배울 때는 바탕화면에 한가득 이상한 파일을 채운다거나... 

> 그 밖에도 재미로.. 창을 닫으면 또생기는 짜증나는 장난감들을 만들어서 주위 친구들에게 선물로 주기도 했었죠.. 

파일콘트롤은 매우 위험하기 때문에 현대의 앱들은 개념을 명확히 두고 인증되지 않은 앱은 엄청난 경고 패널티가 주어집니다(_물론 예전에도 개념 제시는 했습니다. 강제는 안했지만_)

구동하는 앱의 위치(program files\anyApp 같은..)와 해당 앱이 파일을 쓸 수 있는 위치(%APPDATA%\anyApp)가 정해져 있습니다.

이제 해당 앱이 파일을 쓸 수 있는 위치에 읽고 쓰고 해보겠습니다.

{% include toc %}

{% raw %}

# 디버그 기초

지난번 프로젝트에서 다시 ```yarn run dev``` 를 입력해서 앱을 띄어봅니다.

우측에 콘솔창이 있습니다.

이제 콘솔창을 보면서 개발해보도록 하겠습니다.

# 파일 컨트롤

앱 개발자는 사용자 데이터를 보관 할 곳을 알아야합니다.

## 사용자 데이터 보관 위치

[https://electronjs.org/docs/api/app](https://electronjs.org/docs/api/app)

일렉트론의 app 요소에서 찾을 수 있었습니다.

> 참고로 전 구글링을 별로 좋아하지 않습니다.  
우선은 각 제작자가 만든 도큐를 봅니다.
스택오버플로우등에서 운 좋게 원하는 것을 찾을 수도 있지만..  
비정규스러운 코드나 당장을 피하기 위한 코드들이 더 많아서 혼란이 가중될 수 있습니다.

바로 app.getPath(name) 인데요

지난번 작성한 test.vue 파일로 어떤 결과가 나오는지 눈으로 확인 해봅시다.

**src/renderer/components/test.vue**  
```javascript
  import * as fs from 'fs'

  export default {
    name: 'test',
    data: () => ({
    }),
    mounted () {
      console.log(this.$electron.remote.app.getPath('appData'))
      // 결과: /Users/xxx/Library/Application Support
    },
    methods: {}
  }
```

> mounted 라는 함수는 해당 페이지가 로드된 후 실행 됩니다.  
test.vue를 열어두고 뭔가 수정하고 콘솔로 확인하기가 좋습니다.

우측 콘솔 확인 해보면맥에서는 사용자 계정 폴더에 Library/Application Support 라는 곳을 쓰라는 것인가보네요

해당 디렉토리로 가보겠습니다.

각종 쓰고 있는 앱들이 있습니다.

그중 cd elecapp으로 가보면 웬지 건들고 싶지 않은 폴더, 파일(Cache, Cookies등)이 이것저것 만들어 져있습니다.

![alt ls](/images/electron/2018-06-27 09-35-40 bash ls.png)


## 파일 써보기

이제 우리가 쓰고 읽을 곳은 저곳이기 때문에 다시한번 조합해서 작성해 봅니다.

**src/renderer/components/test.vue**  
```javascript
  import fs from 'fs'
  
  export default {
    name: 'test',
    data: () => ({
    }),
    mounted () {
      const p = this.$electron.remote.app.getPath('appData') + '/elecapp/test.txt'
      fs.writeFile(p, 'abcd', (err) => {  
        if (err) console.log(err)
      })
    },
    methods: {}
  }
```

test.txt에 abcd가 잘 써져있는 것을 확인 할 수 있습니다.

그런데 저 경로는 윈도우에서 먹을까요?

맥, 리눅스등은 슬래쉬(/) 단위지만 윈도우는 역슬래쉬(\\)로 되어 있습니다.

그래서 path라는 모듈이 필요한 것입니다.

```javascript
  import fs from 'fs'
  import path from 'path'

  export default {
    name: 'test',
    data: () => ({
      
    }),
    mounted () {
      const p = path.join(this.$electron.remote.app.getPath('appData'), 'elecapp', 'test.txt')
      fs.writeFile(p, 'qwert', (err) => {
        if (err) console.log(err)
      })
    },
    methods: {}
  }
```

이제 윈도우, 맥 호환 가능한 저장소를 얻은 것 같습니다.

## 파일 쓰고 읽기 페이지로 최종 테스트

이제 파일을 하나 더 만들어서 제대로 테스트 해보겠습니다.

지난번 처럼 app.vue, router/index.js 에 testFile을 추가합니다.

**src/renderer/router/index.js**  
```javascript    
    ...
      component: require('@/components/test').default
    },
    { // 추가
      path: '/testFile',
      name: 'testFile',
      component: require('@/components/testFile').default
    },
```

**src/renderer/app.vue**  
```javascript
  { icon: 'title', title: 'test', to: '/test' },
  { icon: 'attachment', title: 'testFile', to: '/testFile' } // 추가
```

**src/renderer/components/testFile.vue**  
```vue
<template>
  <v-layout row wrap>
    <v-flex xs12 sm6>
      <v-card>
        <v-card-title>test1 Actions</v-card-title>
        <v-card-text>
          <v-text-field
              id="testing"
              name="input-1"
              label="파일에 쓸 내용 작성"
              v-model="test1.text"
          ></v-text-field>
        </v-card-text>
        <v-card-actions>
          <v-btn color="primary" @click="test1save">
            <v-icon left>save</v-icon>
            파일저장
          </v-btn>
          <v-btn color="warning" @click="test1remove">
            <v-icon left>remove</v-icon>
            파일삭제
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
    <v-flex xs12 sm6>
      <v-card>
        <v-card-title>test1 Read</v-card-title>
        <v-card-text>
          {{test1.read}}
        </v-card-text>
        <v-card-actions>
          <v-btn color="success" @click="test1read">
            <v-icon left>attachment</v-icon>
            파일읽기
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
    <v-snackbar
        :timeout="snackbar.timeOut"
        top
        v-model="snackbar.act"
        :color="snackbar.color"
    >
      {{ snackbar.text }}
      <v-spacer></v-spacer>
      <v-btn flat color="white" @click.native="snackbar.act = false">닫기</v-btn>
    </v-snackbar>
  </v-layout>
</template>

<script>
  import fs from 'fs'
  import path from 'path'

  export default {
    name: 'testFile',
    data: () => ({
      snackbar: {
        act: false,
        text: '',
        color: 'success',
        timeOut: 5000
      },
      test1: {
        text: '1234a가나',
        read: ''
      },
      appPath: ''
    }),
    mounted () {
      this.appPath = path.join(this.$electron.remote.app.getPath('appData'), 'elecapp', 'test1.txt')
    },
    methods: {
      pop (msg, cl, t) {
        this.snackbar.act = true
        this.snackbar.text = msg
        this.snackbar.color = cl
        this.snackbar.timeOut = t
      },
      test1save () {
        fs.writeFile(this.appPath, this.test1.text, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.pop('잘 써졌음', 'success', 3000)
        })
      },
      test1remove () {
        fs.unlink(this.appPath, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.pop('잘 지워짐', 'success', 3000)
        })
      },
      test1read () {
        fs.readFile(this.appPath, (err, r) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.test1.read = r
          this.pop('잘 읽음', 'success', 3000)
        })
      }
    }
  }
</script>

<style scoped>
</style>
```  

> 고급 개발자분들을 위해 fs.promises(node V10)로 구현하려 했더니 일렉트론은 node V8 버전을 쓰네요.. 아쉽..

**실행 결과**

![alt success](/images/electron/2018-06-27 11-19-46 elecapp.png)

파일 저장을 누른 후 파일 읽기를 눌렀더니 잘 읽어집니다.

![alt fault](/images/electron/2018-06-27 11-21-49 elecapp.png)

파일 삭제를 누른후 파일 읽기를 누르면 에러가 뜹니다.

- appPath라는 변수를 시작할때 저장해 놓습니다.
- 정해놓은 파일(appPath)을 쓰고 읽고 지우는 행위입니다. 
- 추한 알러트(alert())를 대신하여 저번에 썼던 pop method를 그대로 긁어왔습니다.
- vuetify 관련 콤포는 다음에 설명드리겠습니다.

# 디비 컨트롤

사실 파일을 가지고 별로 할 일이 없습니다. 결국 디비를 사용하려 하는 것인데요..

파일 컨트롤을 앞서 구현해 본 이유는 디비도 결국 어딘가의 파일로 쓰기/읽기를 할 뿐인데요..

물리적으로 어디에 저장되어 있는 지 알고 넘어가는 것이 좋기 때문입니다.(_rdbms도, nosql도 결국엔 어딘가에 파일이 덩어리로 있습니다._)

noSQL lite버전인 neDB라는 것을 사용해서 읽고 쓰기를 시험해보겠습니다.(_rdbms를 주로 쓰시는 분은 sqlite로 테스트 해보시면 됩니다. 이치는 다 같습니다_)

## 설치

[https://www.npmjs.com/package/nedb](https://www.npmjs.com/package/nedb)

```bash
$ yarn add nedb
```  

## 전역 컨트롤 등록

여러페이지에서 쓰고 싶기 때문에 핵심부인 main.js에 정의해봅니다.

**src/renderer/main.js**  
```javascript
import Vue from 'vue'
import axios from 'axios'
import Vuetify from 'vuetify'
import { remote } from 'electron' // 추가 
import path from 'path' // 추가
import 'vuetify/dist/vuetify.css'

import App from './App'
import router from './router'
import store from './store'

Vue.use(Vuetify)
if (!process.env.IS_WEB) Vue.use(require('vue-electron'))
Vue.http = Vue.prototype.$http = axios
Vue.config.productionTip = false

// 추가 시작
const dbPath = path.join(remote.app.getPath('appData'), 'elecapp', 'test.db')
const Datastore = require('nedb')
const db = new Datastore({ filename: dbPath })
db.loadDatabase((err) => {
  if (err) console.log(err)
})
Vue.prototype.$db = db
// 추가 끝

/* eslint-disable no-new */
new Vue({
  components: { App },
  router,
  store,
  template: '<App/>'
}).$mount('#app')
```

- 디비저장소의 경로를 얻기위해 electron, path등을 임포트했습니다.
- neDB 공식홈에 있는 내용을 기반으로 연결했습니다.  
> 현재 eslint standard를 사용하기 때문에 에러나지 않도록 정도만 바꾼 겁니다.  
- new Datastore() 로 파일명을 지정 안하면 저장 없이 인메모리 방식으로 구동됩니다.
- Vue.prototype.$db 를 했으므로 이제부터 어느 페이지에서든 this.$db로 엑세스 가능합니다. 

## 디비페이지 만들기

위에 testFile처럼 testDB 라는 페이지를 만듭니다.

라우터와 메뉴에 잘 추가하신후 뷰파일을 만듭니다.

**src/renderer/components/testDB.vue**  
```vue
<template>
  <v-layout row wrap>
    <v-flex xs12 sm6>
      <v-card>
        <v-card-title>등록 카드</v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid">
            <v-text-field
                v-model="form.name"
                :rules="rule.name"
                :counter="10"
                label="이름"
                required
            ></v-text-field>
            <v-text-field
                v-model="form.email"
                :rules="rule.email"
                :counter="100"
                label="E-mail"
                required
            ></v-text-field>
            <v-btn
                color="primary"
                :disabled="!valid"
                @click="formSubmit"
            >
              <v-icon left>save</v-icon>
              등록
            </v-btn>
            <v-btn @click="formClear">
              <v-icon left>undo</v-icon>
              초기화</v-btn>
          </v-form>
        </v-card-text>
      </v-card>
    </v-flex>
    <v-flex xs12 sm6>
      <v-card>
        <v-card-title>디비 확인 카드</v-card-title>
        <v-card-text>
          {{content}}
        </v-card-text>
        <v-card-actions>
          <v-btn color="success" @click="read">
            <v-icon left>attachment</v-icon>
            파일읽기
          </v-btn>
          <v-btn color="error" @click="remove">
            <v-icon left>clear</v-icon>
            전부 지우기
          </v-btn>
        </v-card-actions>
      </v-card>
    </v-flex>
    <v-snackbar
        :timeout="snackbar.timeOut"
        top
        v-model="snackbar.act"
        :color="snackbar.color"
    >
      {{ snackbar.text }}
      <v-spacer></v-spacer>
      <v-btn flat color="white" @click.native="snackbar.act = false">닫기</v-btn>
    </v-snackbar>
  </v-layout>
</template>

<script>
  export default {
    name: 'testFile',
    data: () => ({
      snackbar: {
        act: false,
        text: '',
        color: 'success',
        timeOut: 5000
      },
      valid: true,
      form: {
        name: '',
        email: '',
        rmk: ''
      },
      rule: {
        name: [
          v => !!v || '이름은 꼭 써야합니다.',
          v => (v && v.length <= 10) || '이름은 10글자 이하여야합니다.'
        ],
        email: [
          v => !!v || '이메일은 꼭 써야합니다.',
          v => (v && v.length <= 100) || '이름은 100글자 이하여야합니다.',
          v => /^\w+([.-]?\w+)*@\w+([.-]?\w+)*(\.\w{2,3})+$/.test(v) || '유효한 이메일 주소가 아닙니다.'
        ]
      },
      content: ''
    }),
    mounted () {
    },
    methods: {
      pop (msg, cl, t) {
        this.snackbar.act = true
        this.snackbar.text = msg
        this.snackbar.color = cl
        this.snackbar.timeOut = t
      },
      formSubmit (e) {
        if (!this.$refs.form.validate()) return this.pop('값이 유효하지 않습니다', 'error', 60000)
        this.$db.insert(this.form, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.pop('저장 성공', 'success', 5000)
          this.formClear()
        })
      },
      formClear () {
        this.$refs.form.reset()
      },
      read () {
        this.$db.find({}, (err, r) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.content = r
        })
      },
      remove () {
        this.$db.remove({}, { multi: true }, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.pop('모두 삭제 성공', 'success', 5000)
        })
      }
    }
  }
</script>

<style scoped>
</style>
```

### html

[https://vuetifyjs.com/ko/components/forms](https://vuetifyjs.com/ko/components/forms)

위의 예제를 조금 수정해서 form.name, form.email과 양방향 바인딩되게 하였습니다.

뷰티파이의 폼 밸리데이션 기본 예제도 조금 넣어봤습니다.

유효하지 않으면 등록 버튼에 불이 안들어오고 에러메세지가 써지는 정도죠.. 

> 기본일 뿐이고 사실 vee-validate등으로 유효성 판단하시면 매우 편리합니다.

그 밖에 ref 라는 태그를 이용해서 스크립트 쪽에서 참조해서 여러가지 용도로 사용 가능합니다.

### script

- formSubmit  
ref tag로 유효성 안맞으면 내뱉고  
this.form(form.name, form.email, form.rmk) 통째로 추가 합니다.
- read  
this.$db.find({}) 라는 것은 전체를 배열로 다 가져온다는 것입니다.  
this.$db.find({ name: '가나다'}) 라고 하면 이름이 가나다인 것만 가져옵니다.
- remove  
this.$db.remove({})는 원래 mongoDB에서는 싹다 날리는 것을 의미합니다. {}는 노-필터란 얘기니까...  
> 사실 그래서 데이터 여럿 날렸었죠.. 아마도 저같은 바보들 때문에 여러개는 뒤에 옵션{ multi: true }을 주게 만들었나봅니다.
옵션이 없다면 데이터는 하나만 지워집니다.

## 결과 확인

![alt testDB](/images/electron/2018-06-27 13-45-41 elecapp.png)

# 소스 저장소

혹시 따라하며 소스 꼬인 분들이 있을 수도 있어서 깃 리포 주소를 적어 놓습니다..

[https://github.com/fkkmemi/elecapp](https://github.com/fkkmemi/elecapp)

# 결론

이것으로 시스템 자원(파일)을 이용한 앱을 만들 수 있다는 검증이 되었습니다..

다음에는 우아하게 만들기 위한 UI를 짚어보도록 하겠습니다..

{% endraw %}