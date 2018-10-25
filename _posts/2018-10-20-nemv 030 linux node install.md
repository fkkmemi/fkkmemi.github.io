---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 30 클라우드 서버 노드 설치
category: nemv
tag: [nemv,server,toast,cloud]
comments: true
sidebar:
  nav: "nemv3"
---

리눅스 서버에 모던웹을 서비스할 수 있도록 요소들을 설치해보겠습니다.

**중요: 2018.10.23 연결 변경**
  
이제 토스트에서 새로운 인스턴스 생성시 루트로 접근을 못하게 막아놓았습니다..

```bash
$ ssh -i nemvKey.pem centos@x.x.x.x
$ sudo -i
```

centos라는 일반 유저로 접속한 후에 sudo -i 루트로 다시 접근해야합니다..

# 노드 설치

CentOS node 설치 참고: [https://nodejs.org/en/download/package-manager/#enterprise-linux-and-fedora](https://nodejs.org/en/download/package-manager/#enterprise-linux-and-fedora)

```bash
$ curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -
$ yum -y install nodejs
```

- 최신버전 정보를 curl로 다운로드
- yum을 통해 설치 (_-y 는 중간중간 물어보는 질문에 다 yes로 넘어가겠다는 것입니다._)

## 노드 설치 확인

```bash
$ node -v
10.12.0
$ npm -v
6.4.1
```

설치 확인은 버전으로 확인하면 됩니다.

# 영상

{% include video id="kn1H15zCAsM" provider="youtube" %}   




