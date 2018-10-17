---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 21 몽고디비 설치
category: nemv
tag: [nemv,mongo,mongodb]
comments: true
sidebar:
  nav: "nemv2"
---

몽고디비 로컬에 설치해보겠습니다.

백엔드가 진정 의미 있게 해주는 것이 바로 디비입니다.

윈도우도 해보고 싶은데.. 시간상 다음에 해보겠습니다.

단순 설치이니 크게 어렵지 않습니다.

# 맥에서 할 경우 brew 이용

```bash
$ brew update
$ brew install mongodb
```

# 몽고디비 데이터가 저장될 공간 만들어줌

```bash
$ sudo mkdir -p /data/db
```

/data/db가 아마도 기본 저장 경로인 것 같습니다.

보안상 sudo를 써야 만들어집니다.

# 권한 상승

```bash
$ sudo chmod 777 /data/db
```

777 말고 다른 좋은 권한이 있을 것 같은데 일단 돌아가는게 중요해서 돌아가게 해놨습니다.

# 실행

```bash
$ mongod
```

터미널창을 띄어놓고 작업하는 형태입니다.

# 실행 확인

```bash
$ mongo
> show dbs;
```

접속 후 디비들을 확인

# 영상

처음 설치해보는 것이라 많이 버벅이니 잘 스킵해서 보세요~

{% include video id="t4YYBStBUro" provider="youtube" %}  


