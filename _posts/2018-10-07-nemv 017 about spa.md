---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 17 SPA란
category: nemv
tag: [nemv,spa,talk]
comments: true
sidebar:
  nav: "nemv"
---

웹사이트를 처음 만드시는 분들도 있기 때문에 SPA에 대해 간략하게 설명해보겠습니다.

{% include toc %}

{% raw %}

# 웹사이트 가정

어떤 웹사이트가 3개의 페이지를 가지고 있다고 생각해봅니다.

- index: url을 입력했을 때 처음 나오는 페이지
- user: 사용자 정보가 나오는 페이지
- set: 어떤 정보를 읽고 저장하는 페이지

# 순수한 웹방식

![alt web1](/images/nemv/2018-10-07 13-33-38 about SPA.png)

브라우저로 각 페이지에 요청 합니다.

이 때 서버는 각 3개의 html 파일을 가지고 있습니다.

비슷한 페이지도 많은 데 서버는 자원 낭비가 될 수도 있죠..

# 처리기를 통한 웹방식

![alt web2](/images/nemv/2018-10-07 13-33-54 about SPA.png)

서버에서 파일 하나로 3개의 다른 html을 보낼 수 있습니다.

전처리기를 통해서 말이죠.

이런 식이죠..

**super.xxx**
```html
<html>
<body>
  <img src="/bigbigbig.jpg" alt="엄청나게 큰 그림">
  {{ if (req === main) return 'main 입니다'}}
  {{ if (req === user) return 'user 입니다'}}
  {{ if (req === set) return 'set 입니다'}}
</body>
</html>
```

이제 클라이언트가 3번의 요청을 하지만..

http://xxx.com/index.xxx?main  
http://xxx.com/index.xxx?user  
http://xxx.com/index.xxx?set

서버는 super.xxx 파일 하나로 3개의 페이지를 전송할 수 있습니다.

그런데 위에 이미지 bigbigbig.jpg 역시 3번 전송하겠네요.. 늘 같은 그림인데 낭비죠?

> 물론 브라우저 캐쉬가 자동으로 다운 받지 않게는 해줄 수도 있지만, 브라우저마다 틀리고 여튼 느립니다.

# SPA(Single Page Application)

![alt web3](/images/nemv/2018-10-07 13-34-07 about SPA.png)

사이트 접속시 전부 다운로드 받습니다.

http://xxx.com/  
http://xxx.com/user  
http://xxx.com/set

3번의 요청은 이미 받아놓은 것에서 표시됩니다.

위의 SPA가 아닌 경우처럼 큰 이미지를 또 다운로드 받을 필요는 없겠죠?

페이지 내부에서 필요한 정보는 api를 통해 필요한 만큼만 받아 옵니다.

예를 들면 user 페이지(껍데기)는 표시되었고 그 안의 실제 정보들은 api를 통해 텍스트로 받는 것이죠(_<html>이런거 없이 데이터만.._)

eg) 
http://xxx.com/user 로 빈 페이지 표시
http://xxx.com/api/user/all 로 사용자 전체 표시

api가 필요 없는 페이지의 경우 처음 한번만 내려받았다면 오프라인으로도 사이트가 굴러갑니다~


{% endraw %}

# 영상

{% include video id="lhuvP3oVBK8" provider="youtube" %}  



