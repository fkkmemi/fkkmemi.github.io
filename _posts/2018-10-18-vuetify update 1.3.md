---
layout: single
title: Vuetify(뷰티파이) 1.3 릴리즈 소식
category: talk
tag: [talk,idea,news]
comments: true
---

기다리던 뷰티파이가 드디어 1.3 릴리즈가 오늘 떴습니다.

기념으로 간단히 리뷰를 해보겠습니다.

{% include toc %}

# 추가된 것들

![alt vuetify 1.3](/images/talk/스크린샷 2018-10-18 오후 5.40.42.png)

- v-treeview
- v-timeline
- v-item-group

맘에 드는 3가지네요. 그간 그룹버튼 왜 없을까 했는데... 

# 업데이트 1.3

**package.json**  
```javascript
"vuetify": "^1.3.0",
```

```bash
$ yarn
```

package.json을 1.3으로 고치고 얀! 하면 끝납니다.

# Groups

## Button groups

![alt vuetify Button groups](/images/talk/스크린샷 2018-10-18 오후 6.18.50.png)

부트스트랩의 그룹버튼과 같습니다~ 고대하던 콤포죠..

하필 예제를 에디트 버튼 위주로 해놨는데 색상 넣고 다른 아이콘 넣으면 또 다른 느낌입니다.

v-model이 인덱스 역할이 되어 있어서 라디오 선택기능으로 써도 좋습니다.

저는 주로 추가/삭제/수정 3버튼을 묶어서 사용할 것 같네요~

## Item groups

![alt vuetify Item groups](/images/talk/스크린샷 2018-10-18 오후 6.19.08.png)

라디오 선택을 카드 같은 것으로도 할 수 있다 정도 되겠네요~

## windows

![alt vuetify windows](/images/talk/스크린샷 2018-10-18 오후 6.19.18.png)

기존에 있던 탭기능을 이런식으로 쓸 수 있다 정도입니다.

탭모양이 지겨울 때 한번 써봐야겠네요.

# Timeline

![alt vuetify Timeline1](/images/talk/스크린샷 2018-10-18 오후 6.19.36.png)

타임라인 npm에서 이상한 거 받아서 어거지로 사용했었는데 지우고 이거 써야겠네요..

고객 상담 채팅용으로 쓰는 것도 괜찮을 것 같아요~

![alt vuetify Timeline2](/images/talk/스크린샷 2018-10-18 오후 6.19.53.png)

사용자 행동패턴을 시간순으로 배열하는 것에 어울릴 것 같네요

# Treeview    

![alt vuetify Treeview](/images/talk/스크린샷 2018-10-18 오후 6.20.22.png)

이런 트리뷰 스타일은 윈도우 프로그램 느낌나서 좀 올드한 느낌이 있죠..

예제처럼 주소록 보다는 3개이상의 계층이 있는 복잡한 트리가 어울릴 것 같습니다..

# 마치며

리액트 파다 말았는데 그 에너지 다른 데 쓰고 뷰나 계속 해야겠습니다.

점유율이 너무 낮아서 고민했던것인데 성장 트렌드로 보면 거의 3년간 1위 먹고 있는 뷰를 지켜봐야겠어요~

뷰티파이 덕분에 저 같은 css바보들이 UI 작업하기 너무 편리하네요~

공식홈 로드맵의 약속을 항상 지키는 뷰티파이 개발진들을 응원합니다.

> 베타는 앞에 호스트 next만 붙혀주면 됩니다.  
참고: [https://next.vuetifyjs.com/ko/getting-started/quick-start](https://next.vuetifyjs.com/ko/getting-started/quick-start)
