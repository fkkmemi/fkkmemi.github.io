---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 80 구글 애널리틱스로 사이트 접속 현황 파악하기
category: nemv
tag: [nemv,node,express,mongoose,vue,vuetify,google,analytics]
comments: true
sidebar:
  nav: "nemv6"
---

구글 애널리틱스로 사이트 분석을 해보겠습니다.

{% include toc %}

# 개요

구글 애널리틱스는 말그대로 분석입니다. 사이트를 접속을 통계내고 분석해줍니다.

구글 애널리틱스로 구글은 빅데이터 제공해줘서 좋고 사이트 운영자는 무료로 분석해줘서 좋은 기능입니다.

리캡챠 만큼 서로 시너지를 낼 수 있는 기능 같습니다.

사용자 통계는 지금 단계에서는 시기상조일 수 도 있지만, 도메인만 있으면 무료로 제공되며 구현이 간단하기 때문에 먼저 적용해봅니다.

> 개인적으로는 페이지 분석은 구글 애널리틱스로 판단하고 api쪽에 미들웨어를 넣어서 api를 사용 횟수를 디비에 넣어서 트랙킹 하는 것이 좋습니다.

# 사이트 등록

구글 애널리틱스에 접속해서 사이트를 추가합니다.(_구글 계정만 있으면 OK_)

[https://analytics.google.com](https://analytics.google.com)

![alt ga](/images/nemv/2018-11-28_10.55.27.png) 

등록 후 계정 ID를 얻게 됩니다.

> fkkmemi.com의 계정 ID는 93372301 이네요

# 모듈 설치

구글 설치 안내에서는 fe/public/index.html 에 코드 서너줄 넣으면 완성이지만 싱글페이지라 안될 수도 있고 더 깔끔하게 처리하기 위해 모듈을 설치합니다.

역시 npm에 뷰로 만들어진 깔끔한 모듈이 검색됩니다.

[https://www.npmjs.com/package/vue-analytics](https://www.npmjs.com/package/vue-analytics)

```bash
$ cd fe
$ yarn add vue-analytics
```

프론트에 설치합니다.

# 설정파일에 아이디 추가

```javascript
module.exports = {
  recaptchaSiteKey: '6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI',
  analyticsID: 'UA-93372301-1'
}
```

한 소스로 다른 곳에서 이용해야하기 때문에 설정파일에 추가합니다.

# 코드 적용하기

페이지 트랙킹 예제를 보고 메인에 삽입합니다.

[https://github.com/MatteoGabriele/vue-analytics/blob/HEAD/docs/page-tracking.md](https://github.com/MatteoGabriele/vue-analytics/blob/HEAD/docs/page-tracking.md)

> vue-analytics 공식홈에는 귀찮게 링크만 걸려 있네요..

**fe/src/main.js**  
```javascript
// ..
import VueRecaptcha from 'vue-recaptcha'
import VueAnalytics from 'vue-analytics'
// ..

Vue.prototype.$cfg = cfg

// ..
Vue.use(VueAnalytics, {
  id: cfg.analyticsID,
  router,
  autoTracking: {
    pageviewOnLoad: false
  }
})
```

예제중 오토트랙킹 코드를 넣어봤습니다.

모두 저장하고 git commit & push를 해줍니다.

# 서버 적용하기

서버 설정파일에도 애널리틱스 아이디를 추가합니다.

```bash
$ cd /var/www/nemv/source
$ vi fe/config/index.js 
# i로 추가하고 :wq 로 저장하고 나옴
$ sh build.sh && pm2 log
```

소스코드를 최신화하고 vue-analytics도 설치하고 재시작하게 됩니다.

# 적용 확인하기 

![alt apply](/images/nemv/2018-11-28_11.21.05.png)

페이지를 클릭하면 구글 애널리틱스 실시간에서 확인이되면 끝입니다.

SPA임에도 불구하고 잘 트랙킹하는 것을 확인할 수 있습니다.

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/b675fa100c0df0172f1cb62a31ef2b064cec964a)

# 영상

준비중

{% include video id="" provider="youtube" %}

