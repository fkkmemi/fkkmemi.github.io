---
layout: single
title: NUXT로 혼자 웹사이트 만들기 8 파이어베이스(firebase) 펑션스(functions) 시작하기
category: nuxt
tag: [nuxt,node,vue,vuetify,plugin,firebase,firestore,functions]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스 펑션스를 이용해서 API를 간단히 구현해봅니다.

# 개요

파이어베이스 펑션스(firebase functions)는 말은 거창하지만 사실 익스프레스 서버를 임대해주는 것입니다.

최근에는 데이터베이스도 클라우드공룡들이 다 돌려주기 때문에(aws rds 같은..) 서버 같은 거 필요 없이(serverless) 개발자는 읽고/쓰기에만 집중하면 되는 것입니다.

> 기존 모던웹(NEMV) 강좌를 보고 오신분들은 익스프레스 대체로 생각하면 됩니다.

결국 RESTful API만 구현되는 서버환경을 만들어 주는 것이기 때문에 사용할 API들만 추가하면 됩니다.

그 추가한 API 들이 functions가 되는 것입니다.

복잡한 서버환경(특히 리눅스, 방화벽등)을 전혀 몰라도 되는 것입니다.

# 왜 필요한가?

정적인 프론트에서 이제 직접 파이어베이스 디비인 파이어스토어에 직접 CRUD를 할 수 있습니다.

굳이 백엔드 같은 귀찮은 것이 왜 필요할까요?

아마도 확장성과 편의성 때문인 것 같습니다.

웹 프론트에서의 CRUD도 중요하지만 시대의 흐름은 이제 웹, 앱, 임베디드기기등 다양한 곳에서 입출력이 되고 있습니다.

최근에는 왠만한 기기는 http가 다 되는 세상이기 때문에 각각 다양한 프로토콜은 데이터 유형은 한곳으로 수렴하고 있습니다.

바로 통신프로토콜: RESTful API + 데이터 유형:JSON 이라는 것이죠~

> 예전에는 tcp, soap 부터 시작해서 다양한 레이어, 프로토콜, xml 같은 조합이 있었습니다.. 업체별로 뒤죽박죽이었죠..

지금은 대동단결 REST+JSON이라 백엔드만 잘 구성해 놓으면 다양한 기기에서 동일한 개발 경험을 가질 수 있는 것입니다.

백엔드는 클라이언트 입장이 되어 다른 곳으로부터 데이터도 가져올 수도 있기 때문에 데이터 브릿지 역할을 할 수도 있습니다.

# 제한 및 가격

참고: [https://firebase.google.com/docs/functions/quotas?authuser=0](https://firebase.google.com/docs/functions/quotas?authuser=0)

파이어베이스의 서비스들이 대부분 트래픽이나 양이 적을 때는 무료고 과하게 많아질 경우 과하게 과금이 되니 주의 해야합니다.(_오작동 등으로 무한 요청을 한다던지.._)

> 참고로 호스팅은 아시아 섭이 있지만 펑션스가 아직은 국내에 서버가 없습니다. 그래서 느린 것 같습니다.

우선 OS(리눅스, 윈도우)를 돌려서(VM) 노드 익스프레스로 돌리는 것 보다는 싸야 의미가 있다고 봅니다.

데이터 양이 늘면 어쩔지 모르겠지만, 아무튼 초기 비용이 안나가는 것이 큰 장점인 것 같습니다.

# 파이어베이스 펑션스 설치

먼저 콘솔로 이동해서 펑션스를 사용한다고 해줍니다.

![alt console ff](/images/nuxt/2019-05-20_16.29.33.png)

## 파이어베이스 도구 설치

안내 문구에 아래와 같이 설명이 되어 있는데..

```bash
$ npm install -g firebase-tools
```

맥의 경우 sudo를 안붙히고 설치하면 로컬테스트시 매번 sudo를 넣어줘야하는 것 같습니다.

```bash
$ sudo npm uninstall -g firebase-tools # 지난번 설치했다면~
$ sudo npm install -g firebase-tools # 다시.. 설치
```

## 파이어베이스 펑션스 설치

```bash
$ mkdir fftest && cd "$_"
$ firebase init # 펑션스 혹은 전부 선택 후 설치
```

> 어짜피 테스트를 위해 또 만들어 보는 것이니 계속 설치해보고 느낌을 보는 것이 좋습니다.

## 펑션스 코드 첫 코드로 실행해보기

펑션스 디렉토리를 살펴보면 functions아래에 node_modules도 있고 package.json 도 있습니다.

그것은 곧 functions 디렉토리 자체가 하나의 프로젝트(앱)이라는 것을 확인할 수 있습니다.

**functions/index.js**  
```javascript
const functions = require('firebase-functions');

// Create and Deploy Your First Cloud Functions
// https://firebase.google.com/docs/functions/write-firebase-functions

exports.helloWorld = functions.https.onRequest((request, response) => {
 response.send("Hello from Firebase!");
});
```

달랑 하나 있는 index.js가 다 주석처리가 되어있는데 주석을 해제하고 실행해봅니다.

```bash
$ firebase serve --only functions
✔  functions: Using node@8 from host.
✔  functions: Emulator started at http://localhost:5000
i  functions: Watching "/Users/fkkmemi/lectures/fftest/functions" for Cloud Functions...
i  functions: HTTP trigger initialized at http://localhost:5000/memi-nuxt/us-central1/helloWorld
i  functions: Beginning execution of "helloWorld"
i  Your code has been provided a "firebase-admin" instance.
i  Your code does not appear to initialize the 'firebase-admin' module, so we've done it automatically.
   - Learn more: https://firebase.google.com/docs/admin/setup
i  functions: Finished "helloWorld" in ~1s
```

브라우저에서 위의 링크를 눌러보면 잘 동작함을 확인 할 수 있습니다.

파이어 펑션스만 하고 싶다면 링크가 단축되어 있기 때문에 내려가서(cd functions) 노시면 됩니다.

**functions/package.json**  
```json
   "scripts": {
    "serve": "firebase serve --only functions",
    "shell": "firebase functions:shell",
    "start": "npm run shell",
    "deploy": "firebase deploy --only functions",
    "logs": "firebase functions:log"
  },
```

이 내용은 즉

```bash
$ cd functions
$ npm run serve
```

요렇게 해도 되는 것이죠

이것으로 helloword 라는 api 하나가 생긴 것입니다.(_1000개 까지 무료 제공_)

# 파이어베이스 펑션스 익스프레스 처럼 꾸며보기

기본 예시로 나온 문법은 파이어베이스 네이티브 같습니다.

이것을 익스프레스 느낌으로 바꿔서 실험해보겠습니다.

참고: [https://firebase.google.com/docs/functions/http-events?authuser=0](https://firebase.google.com/docs/functions/http-events?authuser=0)

먼저 구글 레퍼런스 내용을 확인해봅니다.

**구글 레퍼런스**  
```javascript
const express = require('express');
const cors = require('cors');

const app = express();

// Automatically allow cross-origin requests
app.use(cors({ origin: true }));

// Add middleware to authenticate requests
app.use(myMiddleware);

// build multiple CRUD interfaces:
app.get('/:id', (req, res) => res.send(Widgets.getById(req.params.id)));
app.post('/', (req, res) => res.send(Widgets.create()));
app.put('/:id', (req, res) => res.send(Widgets.update(req.params.id, req.body)));
app.delete('/:id', (req, res) => res.send(Widgets.delete(req.params.id)));
app.get('/', (req, res) => res.send(Widgets.list()));

// Expose Express API as a single Cloud Function:
exports.widgets = functions.https.onRequest(app);
```

많이 보던 익스프레스의 app.js와 유사합니다.

cors의 경우 다른 서버의 요청을 받아 들이기 위해 기본 채택이 되어 있는 것 같습니다.(_localhost:3000 -> localhost:5000_)

그런데 너무 불친절합니다. myMiddleware, Widgets는 예제에 없기 때문입니다.(_초보자들을 고려안함.._)

api는 쓰면 쓸 수록 난잡해지기 때문에 절대 index.js 에서 해결할 수 없는 상황이 되기 때문에 파일 분리도 필요한데 어디에도 설명이 없습니다.

## 돌아가게 만들어보기

우선 익스프레스로 돌아가게 만들어봅니다.

```javascript
const functions = require('firebase-functions');

const express = require('express');
const cors = require('cors');

const app = express();

// Automatically allow cross-origin requests
app.use(cors({ origin: true }));

// Add middleware to authenticate requests

// build multiple CRUD interfaces:
app.get('/:id', (req, res) => res.send(`get id = ${req.params.id}`));
app.post('/', (req, res) => res.send('post'));
app.put('/:id', (req, res) => res.send('put'));
app.delete('/:id', (req, res) => res.send('del'));
app.get('/', (req, res) => res.send('get /'));

// Expose Express API as a single Cloud Function:
exports.widgets = functions.https.onRequest(app);
```

있지도 않은 myMiddleware, Widgets 제거한 것이 끝입니다.

이렇게 하고 실행해봅니다. 

```bash
$ firebase serve --only functions
# or
$ cd functions && npm run serve
```

- 요청: http://localhost:5000/memi-nuxt/us-central1/widgets/wgw
- 응답: get id = wgw

이제 이렇게 exports.aaa, exports.bbb, exports.xxx 등으로 만들어 나가면 끝인 거죠~

> 당연히 몽구스 등도 설치(npm i mongoose --save)해서 사용 가능합니다.

## 여러개의 파일로 나누기

index.js 하나로는 너무 산만하기 때문에 펑션별로 파일을 나누면 됩니다.

**functions/abc.js**  
```javascript
const functions = require('firebase-functions');
const express = require('express');

const app = express();

app.get('/:id', (req, res) => res.send(`abcget id = ${req.params.id}`));
app.get('/', (req, res) => res.send('abcget /'));

module.exports = functions.https.onRequest(app);
// or module.exports = app
```

이렇게 여러개를 만들어두고 index.js서 매치시켜주면 끝입니다.

**functions/index.js**  
```javascript
exports.widgets = functions.https.onRequest(app);
exports.abc = require('./abc'); // or exports.abc = functions.https.onRequest(require('./abc')); 
exports.def = require('./def');
// more ...
```

- 요청: http://localhost:5000/memi-nuxt/us-central1/abc/1231rd
- 응답: abcget id = 1231rd

방법은 여러가지인데 취향것 구현만 되면 되는 것이죠~

# 클라이언트에서 요청해보기

기존에 만들어 두었던 프로젝트에서 액시오스로 간단히 요청해봅니다.

**pages/test.vue**  
```vue
<template>
      <v-btn @click="test">
        test
      </v-btn>
</template>
<script>
// ..
    async test() {
      const { data } = await this.$axios.get(
        'http://localhost:5000/memi-nuxt/us-central1/abc'
      )
      console.log(data)
      this.text = data
    }
</script>
```

cors 설정이 되어있어서 localhost:3000 -> localhost:5000으로 잘 작동하는 것을 볼 수 있습니다.

# 소스

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

# 영상

{% include video id="" provider="youtube" %}
