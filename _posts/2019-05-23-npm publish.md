---
layout: single
title: NPM에 모듈 등록해보기
category: npm
tag: [npm,publish,tip,module]
comments: true
sidebar:
  nav: "npm"
toc: true
toc_label: "목차"
toc_icon: "list"
---

[NPM](https://npmjs.com) 에 모듈을 만들어 등록해보겠습니다.

# 개요

개발을 하며 오랜시간 npm에서 오픈소스를 잘 가져다 사용해 왔습니다.

가끔 문제가 발생하거나 소스를 참고하기위해 node_modules를 들어가 볼 때도 있지만.. 만들 생각은 해본적도 없습니다.

이유는 생산성을 위한 것은 잘 만들어진 모듈을 사용하고 비즈니스로직만 잘 만들면 된다는 생각이었습니다.

하지만 프로젝트를 여러개 수행하면서 반복행위가 늘어남을 느낍니다.

주로 some.js 등으로 만든 것을 불러서 사용하긴 하지만..

결국 코드든 파일이든 복붙(복사하기/붙혀넣기) 작업이 많아짐을 느꼈습니다.

그래서 비즈니스로직 외에 공용으로 써도 될만한 기능을 모듈로 배포해보기로 했습니다.

# 회원가입

[https://npmjs.com](https://npmjs.com) 에서 가입하고 이메일 확인만하면 준비완료입니다.

# 모듈작성에 앞서

제대로 만들려면 많은 것을 생각해야합니다.

README.md 파일 부터 시작해서 테스트 시나리오도 넣고 웹팩 번들링으로 출력도 다양하게 해줘야합니다.

항상 느끼는 것이지만 무엇이든 근본부터 쉽게 생각하고 접근해야합니다.

우선 올리고 조금씩 개선하면 되겠죠~

# 모듈작성하기

## 디렉토리 생성

먼저 디렉토리부터 만듭니다.

**temp/memitest1**
```bash
$ mkdir memitest1
$ cd memitest1
```

## 패키지 파일 생성

그리고 패키지 파일(package.json)을 만듭니다.

```bash
$ yarn init
yarn init v1.16.0
question name (memitest1): 
question version (1.0.0): 
question description: memitest
question entry point (index.js): 
question repository url: 
question author: fkkmemi
question license (MIT): 
question private: 
success Saved package.json
✨  Done in 16.70s.
```

yarn init or npm init 기호에 맞게 대충 엔터 넣어서 만들면 됩니다.

**temp/memitest1/package.json**  
```json
{
  "name": "memitest1",
  "version": "1.0.0",
  "description": "memitest",
  "main": "index.js",
  "author": "fkkmemi",
  "license": "MIT"
}
```

그러면 이런 파일이 생성됩니다.

## 파일 작성

**temp/memitest1/index.js**  
```javascript
const strAddHello = (s) => {
  return s + ' hello'
}
const strAddBye = (s) => {
  return s + ' bye'
}
module.exports = {
  strAddHello,
  strAddBye
}
```

원래도 자주 하던 es2015의 js파일 작성법이죠~

이제 모듈이 완성되었습니다~

# 모듈 테스트하기

npm 이나 yarn으로 항상 npmjs에 등록되어 있는 것만 쓰는 것은 아닙니다.(eg: yarn add moment)

임의의 프로젝트를 간단하게 만들어서 설치해보겠습니다.

다른 디렉토리에 프로젝트를 하나 만들고 똑같이 패키지파일을 만들어줍니다.

**temp/npmtest**
```bash
$ mkdir npmtest && cd npmtest
$ yarn init v1.16.0
question name (npmtest): 
question version (1.0.0): 
question description: 
question entry point (index.js): 
question repository url: 
question author: 
question license (MIT): 
question private: 
success Saved package.json
✨  Done in 4.42s.
```

## 모듈 설치하기

현재 위치는

- 모듈: temp/memitest1
- 테스트앱: temp/npmtest

이렇게 됩니다.

```bash
$ yarn add ../memitest1
yarn add v1.16.0
info No lockfile found.
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...
success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
└─ memitest1@1.0.0
info All dependencies
└─ memitest1@1.0.0
✨  Done in 0.10s.
```

이렇게 설치가 된 후에 패키지파일을 확인해보면

**temp/npmtest/package.json**  
```json
{
  "name": "npmtest",
  "version": "1.0.0",
  "main": "index.js",
  "license": "MIT",
  "dependencies": {
    "memitest1": "../memitest1"
  }
}
```

잘 설치가 되어 있는 것을 확인할 수 있습니다.

## 모듈 테스트하기

모듈을 작동시킬 파일을 만들어봅니다.

**temp/npmtest/index.js**  
```javascript
const memitest1 = require('memitest1')
console.log(memitest1.strAddHello('hihi'))
console.log(memitest1.strAddBye('hihi'))
```

이렇게 작성해두고 실행해봅니다.

```bash
$ node .
hihi hello
hihi bye
```

잘 동작되는 것을 확인할 수 있습니다.

# npm에 배포하기

## 로그인하기

```bash
$ npm login
```

사용자 이름, 비밀번호, 이메일주소등을 입력하면 완성입니다.

```bash
$ npm whoami
fkkmemi
```

whoami 명령으로 로그인 확인도 가능합니다.

## 배포하기

그리고 모듈이 있는 디렉토리로 이동합니다.

**temp/memitest1**  
```bash
$ npm publish
npm notice 
npm notice 📦  memitest1@1.0.0
npm notice === Tarball Contents === 
npm notice 142B package.json
npm notice 150B index.js    
npm notice === Tarball Details === 
npm notice name:          memitest1                               
npm notice version:       1.0.0                                   
npm notice package size:  303 B                                   
npm notice unpacked size: 292 B                                   
npm notice shasum:        858177706348d9185b9a97766bb2a534938bfb3f
npm notice integrity:     sha512-PF9MeEkDvKP8P[...]4GUbdfm6GV5iA==
npm notice total files:   2                                       
npm notice 
+ memitest1@1.0.0
```

모듈 위치: https://www.npmjs.com/package/모듈이름

![alt npm](/images/npm/2019-05-23_19.42.21.png)

사이트에 잘 올라가 있는 것을 확인할 수 있습니다.

> 이런 몹쓸 모듈들이 널려있으니 설치시에는 항상 조심해야합니다.  
> 악의적인 생각으로 모듈을 배포할 수도 있기 때문입니다.

## 사용하기

앱이 있던 디렉토리에서 설치해봅니다.

```bash
$ yarn add memitest1
yarn add v1.16.0
[1/4] 🔍  Resolving packages...
[2/4] 🚚  Fetching packages...
[3/4] 🔗  Linking dependencies...
[4/4] 🔨  Building fresh packages...

success Saved lockfile.
success Saved 1 new dependency.
info Direct dependencies
└─ memitest1@1.0.0
info All dependencies
└─ memitest1@1.0.0
✨  Done in 1.58s.
```

## 업데이트하기

모듈을 업데이트 할때에는 패키지파일의 버전을 올려주고 배포하면 끝입니다.

**temp/memitest/index.js**  
```javascript
const strAddHello = (s) => {
  return s + ' hello'
}
const strAddBye = (s) => {
  return s + ' bye'
}
const strAddGood = (s) => {
  return s + ' good'
}
module.exports = {
  strAddHello,
  strAddBye,
  strAddGood
}
```

**temp/memitest/package.json**  
```json
{
  "name": "memitest1",
  "version": "1.0.1",
  "description": "memitest",
  "main": "index.js",
  "author": "fkkmemi",
  "license": "MIT"
}
```

```bash
$ npm publish
npm notice 
npm notice 📦  memitest1@1.0.1
npm notice === Tarball Contents === 
npm notice 142B package.json
npm notice 215B index.js    
npm notice === Tarball Details === 
npm notice name:          memitest1                               
npm notice version:       1.0.1                                   
npm notice package size:  318 B                                   
npm notice unpacked size: 357 B                                   
npm notice shasum:        531fab4540a51851e6667fe7261c08b03411f30a
npm notice integrity:     sha512-b57XRwmMg47p2[...]XUeNaw8kypU2A==
npm notice total files:   2                                       
npm notice 
+ memitest1@1.0.1
```

## 서버에서 내리기

테스트는 좋지만 npm 생태계를 더럽히지는 말아야겠죠~

의미 없는 모듈들이 검색되어 짜증을 유발시키기 때문입니다.

```bash
$ npm unpublish --force
npm WARN using --force I sure hope you know what you are doing.
- npmtest@1.0.0
```

force를 입력하면 다 날려버리지만.. 특정 버전 이하/이상만 지울 수도 있습니다.(_배포하고 나니 문제가 있을 경우_)

참고: [https://docs.npmjs.com/cli/unpublish](https://docs.npmjs.com/cli/unpublish)

참고를 보면 npm의 철학이 잘 묻어 있습니다.

_지우지 말고 왠만하면 디프리케이트(npm deprecate) 를 사용해라_

공개소스로 올려놓고 사람들이 잘 쓰고 있는데 지우는 몹쓸 행동들이 많이 있기 때문인 것 같습니다.

그래서 바로 서버에서 내려가진 않고 24시간의 생각할 시간을 주고나서 지워집니다.

> 제가 배포한 저질모듈은 없어지는 것이 생태계를 돕는 것이기 때문에 가차없이 삭제합니다.

# 마치며

기본적으로 모든 모듈들은 공개(public)입니다.

비공개(private)로 할 경우 조직과 팀으로 운영되며 월 $7 이기 때문에 도입이 만만치 않습니다.

비즈니스로직 같은 것을 패키지매니져로 설치하고 싶다면 위와 같이 특정 장소에 담아두고 공동작업하는 것도 괜찮을 것 같습니다.(/sharedrive/modules/)

위의 예제는 그저 돌아가게만 만든 것이며 실제 제대로 만들어서 공개하려면 엄청난 노력과 정성이 들어갑니다.

아마도 대충 만든 모듈은 몰라서 배포하지 못하는 경우도 있지만.. 민낯이 부끄러워서 공개하지 못하는 경우도 많을 것 같습니다.

제가 nodejs의 발전 요소중 가장 크다고 생각하는 것은 npm 입니다.

하나둘씩 기여해서 만들어진 개발환경이 곧 nodejs 생태계가 된 것이라고 봅니다.

본인에게는 별것 아닌 기능이지만 누군가에게 의미가 있을 수도 있습니다.

# 영상

{% include video id="RrTxE3xOD9o" provider="youtube" %}
