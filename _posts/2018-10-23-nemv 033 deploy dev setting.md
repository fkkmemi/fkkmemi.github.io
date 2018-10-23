---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 33 배포를 위한 개발환경 설정
category: nemv
tag: [nemv,deploy,yarn,env]
comments: true
sidebar:
  nav: "nemv3"
---

지금까지 3000포트를 사용하여 테스트를 했습니다.

3000은 사실 개발 포트죠~ xyz.com:3000 같이 사이트 운영을 할 수는 없습니다.

실제 배포할 서버는 포트는 http는 80 https는 443포트로 운영해야 되는 것이죠~

그래서 실행시 개발 모드인지 배포 모드인지 골라서 실행해야하며.. 코드에도 개발, 배포 모드마다 조금 다르게 해줘야 할 것들을 설정해야합니다. 

개발과 배포 외에도 많은 것(테스트, 엔드투엔드등)들이 있지만 2가지만 하겠습니다.

{% include toc %}

# 백엔드 서버 모드 변경 실행

node는 process.env.NODE_ENV 라는 전역변수로 현재 상태를 판단할 수 있습니다.

app.js등에서 console.log(process.env.NODE_ENV) 해두고 be/npm start 를 하면 undefined 라고 나옵니다.

**be/package.json**  
```javascript
  "scripts": {
    "start": "node ./bin/www"
  },
```

다시한번 백엔드의 package.json을 보면 실제로는 node ./bin/www 를 진행 하고 있습니다.

npm start가 아닌 직접 노드로 실행해보겠습니다.

## NODE_ENV 지정해서 실행

```bash
$ cd be
$ NODE_ENV=xxMode node ./bin/www
```

이제 console.log(process.env.NODE_ENV) 로 xxMode 가 출력되는 것을 알 수 있습니다.

## 최상단 package.json 수정하기

최상단에 있는 package.json파일이 현재 비어 있는 상태일 것입니다.

**현재 디렉토리**  
- be
- fe
    - **package.json** -> 바로 이 파일 
    - README.md
    - ...
    
be와 fe의 package.json 처럼 좀 있어보이게 만들고 실행도 좀 간편하게 만드는 것이 좋겠죠?
    
## 실행 방법 2가지와 부가 정보 입력

**package.json**  
```javascript
{
  "name": "nemv",
  "version": "0.0.1",
  "scripts": {
    "dev": "NODE_ENV=development node ./be/bin/www",
    "pr": "NODE_ENV=production node ./be/bin/www"
  },
  "dependencies": {}
}
```

npm start 대신에 dev 와 pr을 정의해봤습니다.

이제 npm start 처럼 npm dev라고 치면 될까요?

안되죠? npm 에서 정해놓은 커맨드 중에서 하라는 메세지가 뜹니다.

그렇지 않은 저런 커스텀 커맨드는 run을 붙혀야 합니다.

## 실행해 보기

```bash
$ npm run dev
$ npm run pr
```

npm run dev 라고 하면 잘 되죠~

하지만 우리는 yarn을 쓰기로 했습니다.

yarn은 run을 써도 되고 빼도 되는 것이죠~

이렇게 yarn의 장점이 하나 더 있네요~

```bash
$ yarn dev
$ yarn pr
```

이제 모드 별로 다르게 시작해야겠죠~

- 실제 서버: yarn pr
- 개발용: yarn dev

## 포트 설정

**be/bin/www**  
```javascript
console.log(process.env.PORT)
var port = normalizePort(process.env.PORT || '3000');
console.log(port)
```

웹서버가 시작되는 www파일을 보면 process.env.PORT를 사용한 다는 것을 알 수 있습니다.

process.env.PORT를 찍어보면 아무것도 없습니다. 그래서 없을 경우 ``` || ``` 로 3000번을 쓴다는 것입니다.

기본값 3000처리를 익스프레스 개발자가 이렇게 만들었구나 정도로 보시면 됩니다.

웬지 process.env.NODE_ENV 처럼 실행할 때 지정할 것 같은 느낍이 나죠?

```bash
$ PORT=4000 node ./be/bin/www
```

이렇게 실행해보니 console.log(process.env.PORT)이 4000이 잘 찍히는 것을 알 수 있습니다.

**package.json**  
```javascript
{
  ...
    "dev": "NODE_ENV=development node ./be/bin/www",
    "pr": "NODE_ENV=production PORT=80 node ./be/bin/www"
  ...
}
```

개발 모드는 따로 지정을 안해도 3000포트니 그대로 두고 배포 모드는 PORT=80을 추가했습니다.

## cors설정

지난번 localhost:8080 에서 localhost:3000에 연결하기 위해 cors 설정을 해두었는데..

개발시에만 쓰기 위해 NODE_ENV로 사용 유무를 변경해줍니다.

**be/app.js**  
```javascript
if (process.env.NODE_ENV !== 'production') app.use(cors())
```

이렇게 해두면 배포모드 일 때는 cors를 사용하지 않고 개발모드나 아무렇게나 런 했을 때 cors를 사용하게 됩니다.

# 프론트의 모드

다행히 프론트의 vue-cli는 이미 2가지 모드가 이미 갖추어져 있습니다.

계속 쓰고 있던 yarn serve(개발) yarn build(배포) 인데요..

코드 안에서 전역 변수로 확인 할 수 있습니다.

다행히 백엔드와 전역변수 명이 같습니다.

fe/src/main.js에 console.log(process.env.NODE_ENV) 이 코드를 넣고 크롬 개발자 콘솔에서 확인이 가능합니다.

하지만 더 좋은 방법은 직접 화면에서 확인하면 됩니다~

![alt node env](/images/nemv/스크린샷 2018-10-23 오후 4.58.21.png)

yarn serve(개발) yarn build(배포) 가 눈에 확 들어옵니다.

## API 경로를 모드 별로 변경

**src/main.js**  
```javascript
Vue.prototype.$apiRootPath = process.env.NODE_ENV !== 'production' ? 'http://localhost/api/' : '/api/'
```

프로토타입 $apiRootPath를 선언해둡니다.

> 솔직히 왜 $를 붙히는 지는 잘 모릅니다.  
그냥 다른 예제에 이런식이라 대세를 따릅니다.

**src/views/user.vue**  
```javascript
// axios.get('http://localhost:3000/api/user')
// axios.get('/api/user')
axios.get(`${this.$apiRootPath}user`)
```

**src/App.vue**  
```javascript
      ...
      rightDrawer: false,
      title: this.$apiRootPath // 'Vuetify.js'
    }
```

이제 모드 별로 알아서 api 요청을 하게 되었습니다~

## 실행 결과

![alt apiRootPath](/images/nemv/스크린샷 2018-10-23 오후 6.05.48.png)

# 정리

테스트를 하다보니 yarn이니 yarn build니 매우 귀찮은 부분이 많습니다..

그래서 리눅스의 연속 실행을 알아두면 좋습니다.

```bash
$ mkdir bbb && cd bbb
```

이런 식으로 디렉토리를 만들고 바로 이동이나 실행이 가능합니다.

> &&가 아닌 & 일 경우는 백그라운드 실행이라 백&프론트를 둘다 켤수 있으나 헷갈립니다..  
로그도 볼 수 없고 작업관리자를 관리해야합니다.

package.json의 스크립트를 응용해서 만들어 보았습니다.

**package.json**  
```javascript
{
  "name": "nemv",
  "version": "0.0.1",
  "scripts": {
    "dev": "NODE_ENV=development node ./be/bin/www",
    "serve": "cd fe && yarn serve",
    "pr": "cd fe && yarn && yarn build && cd ../be && yarn && NODE_ENV=production PORT=80 node ./bin/www"
  },
  "dependencies": {}
}
```

이제 매번 cd be 나 fe로 갈 필요 없이 프로젝트 최상단에서 실행을 하면 됩니다.

## 로컬에서(개발)

최상단 package.json으로 백&프론트를 각각 아래와 같이 입력하면 됩니다.

```bash
$ yarn dev
```

```bash
$ yarn serve
```

약간 편리해졌죠?

## 실제 서버에서(배포)

yarn pr은 && 를 이용해서 순차적으로 처리해서 실행합니다.

1. cd fe: 프론트엔드 디렉토리로 이동
2. yarn: 종속요소 설치(혹시 프론트에서 패키지가 추가되었을 수도 있으니)
3. yarn build: 코드 수정 부분 반영
4. cd ../be: 백엔드 디렉트로리로 이동
5. yarn: 종속요소 설치(혹시 백엔드 패키지가 추가되었을 수도 있으니)
6. NODE_ENV=production PORT=80 node ./bin/www: 배포모드로 실행

### 실제 서버에 적용하기

이제 :3000 없이 동작하는 서버를 직접 적용해봅니다.

### 클라우드 서버 보안 그룹 설정에서 룰 80포트 추가

### 소스 디렉토리에서 소스 업데이트

```bash
$ cd /var/www/nemv3
$ git pull
``` 

### 실행

```bash
$ yarn pr
```

# 영상

{% include video id="" provider="youtube" %}   




