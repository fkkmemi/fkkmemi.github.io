---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 22 몽고디비 설치 확인
category: nemv
tag: [nemv,mongo,mongodb,sheel]
comments: true
sidebar:
  nav: "nemv2"
---

몽고디비 셸로 설치 확인하기

{% include toc %}

# 디비 생성

```bash
$ mongo
> use nemv
switched to db nemv
```

nemv를 이용한다고 한 것이지 사실 생성된 것은 아닙니다.

# 데이터 넣기

데이터를 넣고 나면 디비가 생성됩니다.

```bash
> db.test.insertOne({a:1});
> show dbs;
> db.test.find();
```
test라는 컬렉션에 데이터를 하나 쓴 것이며 nemv라는 디비가 생성되어 있는 것이 확인됩니다.

데이터가 잘 있는 것도 확인이 됩니다.

이정도 확인 되었으면 이제 시커먼 터미널 셸에서 접속할 일은 없습니다.

이제부터는 노드에서 편리하게 데이터를 읽고 쓸 수 있습니다.

# 영상

{% include video id="SamQqfZ8RRE" provider="youtube" %}  


