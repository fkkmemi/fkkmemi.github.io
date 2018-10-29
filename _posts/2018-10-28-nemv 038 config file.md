---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 38 깃헙에서 안보이는 설정파일 만들기
category: nemv
tag: [nemv,mongodb,atlas,cloud,node]
comments: true
sidebar:
  nav: "nemv4"
---

설정파일을 사용하는 방법에 대해 알아보겠습니다.

{% include toc %}

# 개요

몽고디비 문자열이 공개 저장소에 그대로 박혀 있으면 안되기 때문에 저장소에 푸쉬하지 말아야됩니다.

결국 깃 소스에서 관리되지 않으면서 서버별로 다른 값을 주기 위해 필요합니다.

# 파일 만들기

**config/index.json**  
```javascript
{
  "dbUrl": "mongodb+srv://nemv:password@cluster0-4ugnm.mongodb.net/nemv"
}
```

**config/index.js**  
```javascript
module.exports = {
  dbUrl: 'mongodb+srv://nemv:password@cluster0-4ugnm.mongodb.net/nemv',
  // dbUrl: 'mongodb://localhost:27017/nemv',
}
```

2개의 설정파일은 똑같이 작동합니다.

하지만 2개의 설정파일중 당연히 js파일이 편리함을 알 수 있습니다. 

json은 주석처리도 할 수 없고 문자열은 꼭 쌍따옴표가 요구되기 때문입니다.

# 깃무시(gitignore)

프로젝트 상단에 .gitignore 라는 파일이 있습니다.

무시할 폴더나 파일들을 저장하는 장소입니다.

```javascript
# config
config/
```

앞에 #표시는 주석이고 맨 아래에 config/ 을 넣어주면 깃에 포함하지 않는 것입니다.

![alt git ignore](/images/nemv/스크린샷 2018-10-29 오후 4.18.42.png)

깃저장소에 config 과 node_modules 가 없는 것이 확인됩니다.
 
# 파일 읽어서 디비 연결
 
**be/app.js**   
```javascript
const cfg = require('../config')
console.log(cfg)

mongoose.connect(cfg.dbUrl, { useNewUrlParser: true }, (err) => {
  if (err) return console.error(err)
  console.log('mongoose connected!')
```

이제 설정파일로 디비 연결이 가능합니다.

# 설명서 만들기

config파일을 실수로 안만들었다면 바로 에러가 나버리겠죠..

파일 존재유무로 예외처리 해줄 수는 있지만 의미가 없죠.. 디비 연결도 없이 할 것이 없으니..

그래서 README.md라는 파일에 최소 구동법 정도를 적어 두는 것이 좋습니다.

나중되면 다 까먹기 때문입니다..

**README.md**  
```markdown
# nemv3
node express mongo vue

## config 파일 세팅 방법

**config/index.js**  
    ```javascript
    module.exports = {
      dbUrl: 'mongodb://localhost:27017/nemv',
    }
    ```  
이런식으로 디비 연결 문자열을 작성해야 웹서버가 정상 구동됨.
```

마크다운 문법을 조금 익혀야 되는데.. #과 코드 작성하는 법만 익히면 됩니다.

참고: [마크다운 간단 사용 방법](/github/markdown/) 

# 설명서 출력된 화면

![alt readme](/images/nemv/스크린샷 2018-10-29 오후 4.33.46.png)

# 영상

{% include video id="r4pRbPDQCo0" provider="youtube" %}   




