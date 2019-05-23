---
layout: single
title: NUXT로 혼자 웹사이트 만들기 10 파이어베이스(firebase) 파이어스토어(firestore) 페이징(paging)처리하기
category: nuxt
tag: [nuxt,node,vue,vuetify,firebase,firestore,paging]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어스토어를 이용해서 페이징처리를 해봅니다.

> 반드시 페이징 처리 기본을 학습하고 오셔야합니다.  
참고: [모던웹(NEMV) 혼자 제작 하기 3기 - 72 게시판 페이징과 정렬 처리하기](/nemv/nemv-072-board-paging/)

{% raw %}
# 개요

시작하기 앞서 페이징에 필요한 요소가 전부 있는지 확인해야합니다.

- 요청시
    - 스킵(skip): 건너 뛰기
    - 리미트(limit): 개수
    - 오더(order): 정렬을 무엇으로 할지
    - 정렬(sort): 오름차(asc) 내림차(desc)
- 응답시
    - 데이터들(datas[]): 실제 받는 데이터
    - 총데이터 개수(total): 전체 데이터 개수
    
어떠한 언어든 플랫폼이든 이 요소들만 있으면 페이징이 됩니다.

# 문제점

파이어스토어 이용시 바로 문제점이 나옵니다.

## skip

파이어스토어에는 skip offset등으로 불리우는 건너뛰기 기능이 없습니다.

비슷한 startAt startAfter endAt 같은 것이 있습니다.

참고: [https://firebase.google.com/docs/firestore/query-data/query-cursors?authuser=0](https://firebase.google.com/docs/firestore/query-data/query-cursors?authuser=0)

위의 예제를 가져와 보면

```javascript
var startAt = db.collection('cities')
    .orderBy('population')
    .startAt(1000000);
```

불러온 데이터 개수의 1000000만큼을 스킵하는 것이 아닙니다.

1000000은 명시된 값입니다. 1000000을 건너 뛰는 것이 아닌 그 값 부터 시작이란 것입니다.

이것을 활용해서 게시판 로직을 구현하려면 모든 데이터에 연번이 있어야합니다..

## total

검색된 데이터의 전체 개수 입니다.

100개의 게시물을 5개씩 보여주려면 100/5 인 20페이지라는 값을 얻어내야합니다.

그 20페이지라는 것을 얻기위해 꼭 토탈: 100이라는 값을 얻어내야합니다.

하지만 아무리 찾아도 totalCount를 나타내는 링크가 보이지 않습니다.

> 있으면 제보 바랍니다

## 컨셉?

위의 참고의 페이징 예제를 보면

```javascript
var first = db.collection('cities')
    .orderBy('population')
    .limit(3);

var paginate = first.get()
    .then((snapshot) => {
      // ...

      // Get the last document
      var last = snapshot.docs[snapshot.docs.length - 1];

      // Construct a new query starting at this document.
      // Note: this will not have the desired effect if multiple
      // cities have the exact same population value.
      var next = db.collection('cities')
          .orderBy('population')
          .startAfter(last.data().population)
          .limit(3);

      // Use the query for pagination
      // ...
    });
```

처음 것을 읽어서 다음 다음 버튼으로 처리하는 것을 볼 수 있습니다.

이전, 다음 같은 버튼이나.. 스크롤등으로 내려갈때 얻어 올 수 있는 기능이 되는 것이죠..

아마도 파이어스토어는 고전적인 게시판류를 고려해서 만들지 않았나봅니다..

## 방법

사실 전체 카운트의 경우 디비에서 색인을 하고 있는 것입니다.

데이터가 삽입되거나 제거될때 어디다 개수를 적어두는 것이죠..

그래서 1억개의 데이터라도 안뒤지고 Document.count() 하면 바로 나올 수 있는 것이죠..

그래서 이러한 일을 직접 해주면 됩니다.

귀찮지만 데이터마다 연번을 넣어주면 마지막 값을 읽어서 개수를 확인하고 정렬도 해결이되죠..(_연번은 고유해야하니.. id로 써도 됩니다_)

## 준비중..

{% endraw %}

# 소스

본문 내용과 정확히 일치하진 않으니 참고용으로 보시면 됩니다.

깃허브 링크: [https://github.com/fkkmemi/nuxt](https://github.com/fkkmemi/nuxt)

# 영상

{% include video id="" provider="youtube" %}
