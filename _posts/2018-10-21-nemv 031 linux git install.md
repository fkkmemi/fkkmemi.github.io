---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 31 클라우드 서버 깃 설치 및 테스트
category: nemv
tag: [nemv,server,toast,cloud]
comments: true
sidebar:
  nav: "nemv3"
---

깃 설치하고 ssh방식으로 소스를 다운로드 받는 방법입니다.

**중요: 2018.10.23 연결 변경**
  
이제 토스트에서 새로운 인스턴스 생성시 루트로 접근을 못하게 막아놓았습니다..

```bash
$ ssh -i nemvKey.pem centos@x.x.x.x
$ sudo -i
```

centos라는 일반 유저로 접속한 후에 sudo -i 루트로 다시 접근해야합니다..

# 깃 설치

```bash
$ yum -y install http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-2.noarch.rpm
$ yum -y install git
$ git --version
git version 2.18.0
```

깃을 최신버전으로 설치합니다.

# 서비스할 디렉토리

일반적으로 /var/www 에 서비스 됩니다.

```bash
$ mkdir /var/www
$ cd /var/www
```

해당 위치로 가서 소스 다운로드를 합니다.

# 깃허브 연결하기

기존에 https로 클론을 받아서 소스를 다운로드 받았습니다.

공개 자료이기 때문에 아이디 암호 없이 그냥 내려 받은 것이죠~

비공개 소스일 경우에는 아이디/암호도 매번 넣어야합니다.
 
무엇보다 나중에 pm2를 이용해 서버에 배포할 때 ssh연결로만 가능합니다.

조금 귀찮지만 한번만 ssh로 깃헙에 개발 피씨와 배포용 리눅스 서버의 키를 저장해두면 나중에 매우 편리합니다.

참고: [https://help.github.com/articles/connecting-to-github-with-ssh/](https://help.github.com/articles/connecting-to-github-with-ssh/)

## ssh 키 등록하러 가기

github.com > personal setting 에 가서 하면 됩니다.

![git setting](/images/server/2018-03-22 11-36-14 git ssh.png)

## 키 생성

참고: [https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/](https://help.github.com/articles/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent/)

```bash
$ ssh-keygen -t rsa -b 4096 -C "aaa@bbb.com"

# copy key
$ cat ~/.ssh/id_rsa.pub
```

키를 만들고 값을 복사해놓습니다.


## 키 값 붙혀넣기

github.com > personal setting > new key button

![git new key](/images/server/2018-03-22 11-51-43 git ssh key.png)

## 깃헙에 연결

```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (111.222.111.222)' can't be established.
RSA key fingerprint is xx:xx:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,111.222.111.222' (RSA) to the list of known hosts.
Hi aaa! You've successfully authenticated, but GitHub does not provide shell access.
```

안해도 되는 작업이긴 한데 언젠간(다음 깃헙 연결시) yes 한번 눌러줘야해서 미리 해둔다고 생각하면 됩니다.

# 소스 내려받기

이제 드디어 ssh를 이용한 소스 다운로드가 가능합니다~

```bash
$ cd /var/www
$ git clone git@github.com:fkkmemi/nemv3.git
```

# 영상

{% include video id="90FVGaIVhk4" provider="youtube" %}   




