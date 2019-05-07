---
layout: single
title: NUXT로 혼자 웹사이트 만들기 7 닷엔브(dotenv)로 파이어베이스 프로젝트 정리하기
category: nuxt
tag: [nuxt,node,vue,vuetify,plugin,firebase,firestore,dotenv]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

넉스트 프로젝트에서 닷엔브로 파이어베이스를 사용하기 위한 설정을 해보겠습니다.

# 개요

공개프로젝트를 배포할때 가장 먼저 확인할 것이 있습니다.

바로 환경설정 변수입니다.

주로 config, config.json, global.js, eco.json 같은 파일들이 될 수 있습니다.

크게 두가지 이유로 사용됩니다.

- 공개되면 안되는 정보: 데이터베이스 URL, 관리자 정보, 각종키등
- 재사용 정보: 프론트에서 사용할 각종 API key들

웹에서 사용할 구글맵, 리캡차등의 api 정보는 웹에서 사용하기 때문에 이미 드러내놓고 사용하기 때문에 어짜피 비밀정보가 아닙니다.(_난독화하고 숨겨봐야 다 뚫립니다._)

그래서 프론트에서 사용할 api등은 주로 도메인(리퍼러)이나 서버 주소등으로 막게됩니다.

웹에서 사용할 환경변수는 코드 재사용의 편리함 때문에 존재하는 것입니다.

하지만 백엔드 환경변수는 노출이 되면 안됩니다.
 
실수로 데이터베이스 접근 URL 이나 사용자명 같은 것을 하드코딩해서 소스에 넣으면 큰일납니다.

구현 방법은 간단합니다.

깃허브에 올리지 않도록 깃무시파일(.gitignore) 에 보안이 필요한 파일을 적어두고 코드에서 require('/config.json')등으로 로드해서 사용하면 끝입니다.

그 중 가장 일반적인 방법인 **dotenv** 모듈을 이용해서 프로젝트를 깃허브에 배포해보겠습니다.

# 닷엔브(dotenv) 설치

```bash
$ yarn add dotenv
```

# 닷엔브 파일(.env) 만들기

**.env**  
```text
FIREBASE_APIKEY=key
FIREBASE_AUTHDOMAIN=domain
FIREBASE_PROJECTID=pid
DB_URL=xxx.com
```

닷엔브는 기본값으로 .env파일을 로드합니다.

> 물론 다른 파일도 당연히 로드 가능합니다. 하지만 귀찮아짐..

**.gitignore**  
```text
# dotenv environment variables file
.env
```

보편적이기 때문에 이미 깃무시(.gitignore)로 지정되어 있습니다.(_혹시 없다면 꼭 추가_)

# 닷엔브 파일 읽기

닷엔브에 등록되어있는 키와 값은 process.env.x 로 읽을 수 있습니다.

**server/index.js**  
```javascript
require('dotenv').config()

console.log(process.env.FIREBASE_APIKEY, process.env.FIREBASE_AUTHDOMAIN)
```

이렇게 백엔드에서는 잘 읽을 수 있습니다.

하지만 프론트에서는 읽을 수 없습니다.

# 프론트에서 읽기: 참고용

프론트에서도 읽고 싶다면 넉스트에서 제공하는 @nuxjs/dotenv 를 설치하면 됩니다.

> 설치는 하지마시고 참고만 하시기 바랍니다.

```bash
$ yarn add @nuxjs/dotenv
```

설치 후 모듈에 추가해주면

**nuxt.config.js**  
```javascript
  modules: [
    // Doc: https://axios.nuxtjs.org/usage
    '@nuxtjs/axios',
    '@nuxtjs/dotenv',
    'vuetify-dialog/nuxt'
    // ['vuetify-dialog/nuxt', { property: '$dialog' }] // 'vuetify-dialog/nuxt'
  ],
```

프론트에서 읽을 수 있습니다.

그런데 공개되면 안되는 정보까지 노출이 됩니다.

process.env.DB_URL 같은 것도 나오게 됩니다.

다행하게도 console.log(process.env) 를 하게되면 웹팩이 알아서 자식요소가 나오지 않게({}) 만들어주기 때문에 프론트에서 공개하면 안되는 정보는 코딩 안하면 그만입니다.

하지만 찝찝하기 때문에.. 이 방법은 사용하지 않을 것입니다.  

```bash
$ yarn remove @nuxtjs/dotenv
```

# 프론트에서 읽기: 실제 적용

프론트에서 사용할 것들만 오픈해주는 것이 좋습니다.

**nuxt.config.js**  
```javascript
require('dotenv').config()

module.exports = {
  mode: 'universal',
  // ..
  env: {
    FIREBASE_APIKEY: process.env.FIREBASE_APIKEY,
    FIREBASE_AUTHDOMAIN: process.env.FIREBASE_AUTHDOMAIN,
    FIREBASE_PROJECTID: process.env.FIREBASE_PROJECTID,
    abcd: 1234
  }
}
```

이렇게 하면 프론트에서만 노출 시킬 환경변수를 지정해서 사용이 가능합니다.

# 파이어스토어 전역으로 사용하기

**plugins/etc.js**  
```javascript
import Vue from 'vue'
import moment from 'moment'
import firebase from 'firebase/app'
import 'firebase/firestore'

Vue.prototype.$moment = moment
if (!firebase.apps.length) {
  const config = {
    apiKey: process.env.FIREBASE_APIKEY,
    authDomain: process.env.FIREBASE_AUTHDOMAIN,
    projectId: process.env.FIREBASE_PROJECTID
  }
  firebase.initializeApp(config)
  Vue.prototype.$db = firebase.firestore()
  Vue.prototype.$db.settings({
    timestampsInSnapshots: true
  })
}
```

> 나중에 분리해야겠지만.. 귀찮아서 etc.js에 다 때려 박습니다.

앞에 if (!firebase.apps.length) 조건은 이미 로드되어 있을 경우를 막아 놓은 것입니다.(_개발시 필요_)

이제 어느 페이지에서든 this.$db로 파이어스토어를 가지고 놀 수 있습니다.

# 프론트에서 테스트해보기

**pages/test.vue**  
```vue
<template>
  <v-card>
    <v-card-title>
      hihihi
    </v-card-title>
    <v-card-text>
      <v-textarea v-model="text" />
    </v-card-text>
    <v-card-actions>
      <v-btn @click="write">
        write
      </v-btn>
      <v-btn @click="read">
        read
      </v-btn>
    </v-card-actions>
  </v-card>
</template>
<script>
export default {
  data() {
    return {
      text: 'wwww'
    }
  },
  methods: {
    async write() {
      try {
        const r = await this.$db.collection('users').add({
          first: 'Ada',
          last: 'Lovelace',
          born: 1815
        })
        this.text = `Document written with ID: => ${r.id}`
      } catch (e) {
        await this.$dialog.notify.error(e.message)
      }
    },
    async read() {
      try {
        const rs = await this.$db.collection('users').get()
        const ss = []
        rs.forEach(r => {
          ss.push(`${r.id} => ${JSON.stringify(r.data())}`)
        })
        this.text = ss.join('\n')
      } catch (e) {
        await this.$dialog.notify.error(e.message)
      }
    }
  }
}
</script>
```

> 쓰기/읽기에 권한을 전부 주었기 때문에 잘 동작하지만 테스트 후에는 꼭 읽기/쓰기를 막아야합니다.

# 파이어베이스 초기화하기

지난 강좌에서 처럼 firebase init을 최상위(루트/) 디렉토리에서 해줍니다.

5가지의 선택 체크박스(호스팅, 파이어스토어, 펑션등등)가 있는데 그냥 전부 체크해줍니다.

복잡하지만.. 이제부터 백엔드, 프론트, 파이어베이스 관련툴들이 한집에서 살게됩니다.

**.gitignore**  
```text
# firebase
public
.firebaserc
``` 

- public: 계속 바뀌는 생성 디렉토리(generate path)인 public은 깃무시를 해줘야겠죠~
- .firebaserc: 프로젝트아이디 정보가 있으므로 역시 깃무시 해줍니다.

# 배포 커맨드 추가하기

한번에 파일 생성하고 배포까지 하게 실행 명령을 추가합니다.

**package.json**  
```json
  "scripts": {
    // ..
    "precommit": "npm run lint",
    "firebase:deploy": "nuxt generate && firebase deploy"
  },
```

&&를 두개 넣어서 앞의 명령인 파일 생성이 끝나고 배포가 되게 변경합니다. 

```bash
$ yarn firebase:deploy
```

# 배포 후 디비 접근 문제

배포를 하고 나서 프론트에서 버튼을 눌러보면 디비 액세스 오류가 납니다.

이유는 파이어스토어 룰이 같이 배포되기 때문입니다.

**firestore.rules**  
```javascript
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth!=null;
    }
  }
}
```

read, write가 인증이 된 상태가 안되었으니 당연히 접근이 안되는 것이죠.

![alt console allow](/images/nuxt/2019-05-07_20.35.25.png)

파이어베이스 콘솔로 가서 테스트를 위해 읽기 쓰기를 잠시 풀어 주면 됩니다.

그리고 다시 잠그시길 바랍니다.(_재수 없으면 악성유저가.. 해당 api로 부하를 줘서 과금이 발생할 수 있습니다._)

어짜피 나중에 인증된 유저만 사용하게 변경해야하니 룰은 그대로 두는 것이 좋습니다.

# README.md 만들어보기

지금까지 한 것이 다 기억나십니까?

별 잡다한 애들이 한집에 살기 때문에 더더욱 복잡하죠..

그래서 공개든 비공개든 README.md or HISTORY.md는 꼭 작성해두는 것이 좋습니다.

마크다운 1~2시간만 투자하면 충분히 원하는 설명 넣을 수 있습니다.

단순한 기술, 커맨드 설명이기 때문에 영어로 작성하는 것도 어렵지 않습니다.

> 가급적 영어로 공개하면 다른 나라 개발자들도 편하게 사용해볼 수 있겠죠?

[마크다운 참고](/github/markdown/){: .btn .btn--success}

```markdown
    # memi nuxt lecture
    
    ## Install tools
    
    - node v8
    - yarn
    - firebase-tools
    
    ## .env File setting
    
    eg)
    ```text
    FIREBASE_APIKEY=yourKey
    FIREBASE_AUTHDOMAIN=yourDomain
    FIREBASE_PROJECTID=yourProjectId
    ``` 
    
    guide: [https://firebase.google.com/docs/firestore/quickstart](https://firebase.google.com/docs/firestore/quickstart)
    
    ## Build Setup
    
    ``` bash
    # install dependencies
    $ yarn
    
    # serve with hot reload at localhost:3000
    $ yarn dev
    
    # build for production and launch server
    $ yarn build
    $ yarn start
    
    # generate static project
    $ yarn generate
    ```
    
    ## Firebase Setup
    
    ```bash
    # login
    $ firebase login
    
    # init
    $ firebase init
    ```
    
    Select all and enter default
    
    ## deploy
    
    ```bash
    $ yarn firebase:deploy
    ```
```

이렇게 작성해두면 깃헙 리포 전면에 뜨기 때문에 프로젝트를 이해하기가 좋습니다.

성의 없긴 하지만.. 이정도만 해둬도 나중에 까먹을 경우, 설치는 가능할 것 같습니다.

# 설치해서 확인할 경우

```bash
# download
$ git clone https://github.com/fkkmemi/nuxt.git

# move
$ cd nuxt

# install dep.
$ yarn

# firebase
$ firebase init # 모두 체크 후 기본값

# .env 파일 작성

# 개발 모드 구동
$ yarn dev

# 혹은 호스팅서버로 배포
$ yarn firebase:deploy
```

# 소스

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

# 영상

{% include video id="" provider="youtube" %}
