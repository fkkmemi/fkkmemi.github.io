---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 4 익스프레스 수정
category: nemv
tag: [nemv,node,express]
comments: true
sidebar:
  nav: "nemv"
---

생성된 익스프레스 소스를 수정해서 웹서버 동작 확인해보겠습니다.

# 익스프레스 구동 원리 확인

노드의 실행은 곧 node 어쩌고.js 인데 어떻게 실행되는 것일까요?

expressjs.com 에선 이렇게 하라고 하죠

```bash
$ DEBUG=be:* npm start
```

그러면 3000포트로 http서버가 열려서 브라우저에서 http://localhost:3000 을 입력하면 됩니다.

npm start라는 것은 무엇일까요?

그 비밀은 package.json이라는 파일에 있습니다.

파일을 열어보면

```javascript
"scripts": {
  "start": "node ./bin/www"
},
```

이 부분이 npm start 입니다.

결국 node ./bin/www 가 되는 것이죠.

특이하게도 확장자가 없이 만들어 놨는데 결국 자바스크립트 파일 입니다. 

http서버를 여는 부분일 뿐이고 실제 중요한 파일은 app.js가 담당합니다.

# 익스프레스 수정

_수정중_

# 영상

{% include video id="9bhcV0_PPB0" provider="youtube" %}   


 