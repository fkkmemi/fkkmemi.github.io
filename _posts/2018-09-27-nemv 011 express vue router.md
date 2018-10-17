---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 11 익스프레스와 뷰라우터
category: nemv
tag: [nemv,node,express,vue,vuetify,router]
comments: true
sidebar:
  nav: "nemv1"
---

뷰 라우터의 히스토리모드 이해와 백엔드 라우터의 연결에 대해 알아보겠습니다.

{% include toc %}
{% raw %}

# 백엔드 라우터 처리

1. /api/? 처리
2. / 처리
3. /? 예외 처리

모던웹(NEMV)에서는 딱 저 3 라우트만 필요합니다

데이터는 1. 에서 처리하는데

/xxx/ 같은 없는 url은 어떻해야할까요?

예외처리인 3. 으로 가서 404 어쩌고를 처리해줘야합니다.

근데 안이쁘죠? 뷰티파이 화면에서 놀다가 갑자기 404 어쩌고 에러 메세지가 가득 담긴 텍스트라니~

그래서 없는 페이지는 2. 에서 처리한후 뷰티파이에 있는 라우터에서 처리해줘야합니다.

# 뷰 라우터 예외처리(404)

우선 프론트를 켜고(yarn serve) http://localhost:8080/xxx 를 쳐보면 어떻게 될까요?

라우에 등록이 안된 xxx라 아무 일도 일어나지 않습니다.

옳은 처리는 xxx는 없는 페이지입니다~ 라고 안내를 해주는 페이지를 보여주는게 좋겠죠?

뷰 라우터 예외처리는 2가지면 됩니다.

1. 없다는 것을 알려줄 페이지
2. 라우터에 예외 케이스 등록

**fe/src/views/e404.vue**  
```vue
<template>
  <div>그런 페이지 없어요~</div>
</template>
```

views/e404.vue라는 파일을 만듭니다.

**fe/src/router.js**  
```javascript
    {
      path: '*',
      name: 'e404',
      component: () => import('./views/e404.vue')
    }
```

라우터에 *을 추가해주면 등록 안된 모든 url은 e404.vue를 표시해줍니다.

# 백엔드에서 뷰 라우터 예외처리로 넘기기

http://localhost:8080/xyz 처리는 프론트 개발모드라 잘되는 것입니다.

http://localhost:3000/xyz 는 전혀 다른 예외처리를 해줘서 문제인 것이죠.

더 쉽게 설명한 뷰라우터 공식자료는 아래 링크를 보시면 됩니다.

[https://router.vuejs.org/kr/guide/essentials/history-mode.html#서버-설정-예제](https://router.vuejs.org/kr/guide/essentials/history-mode.html#서버-설정-예제)

결국 백엔드에서는 잘못된 url 또한 fe/dist를 보내줘야하는 거란 얘기죠..

보낸 후에 프론트에서 판단해서 적절한 표시를 해줘야하는 것인겁니다.

# 백엔드에 히스토리모드 설치

잘못된 url을 모두 fe/dist를 보내주려면 히스토리모드를 설치해주라네요~

위의 공식자료에서 안내하는 모듈을 설치해보겠습니다.

[https://github.com/bripkens/connect-history-api-fallback](https://github.com/bripkens/connect-history-api-fallback)

```bash
$ cd be
$ yarn connect-history-api-fallback
```

**be/app.js**  
```javascript
const history = require('connect-history-api-fallback') // 상단 아무곳이나 추가
app.use('/api', require('./routes/api'))
app.use(history())
app.use(express.static(path.join(__dirname, '../', 'fe', 'dist')));
```

스태틱 라우터 위에 history만 사용한다고 선언하면 끝입니다.

fe 에서 yarn build를 하고 localhost:3000/zxv 등이 이제 잘 처리됨을 확인 할 수 있습니다.

# 백엔드의 예외처리는 언제할까?

현재는 /api, / 밖에 라우트 포인트가 없습니다.

하지만 예외처리 부가 있는데요 그건 언제 써야하냐면..

/api/xxx 등 등록 안된 url에 대한 응답과 각종 오류들을 보내는 역할을 넣어줘야합니다.

{% endraw %}

# 영상

자세한 것은 영상을 보며 확인하세요~

{% include video id="cnAemBVXJtw" provider="youtube" %}   

 