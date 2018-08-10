---
title: "CentOS7 VM update"
permalink: /docs/centos7-vm-update/
excerpt: "KT VM 업데이트 때 정리"
last_modified_at: 2018-08-10T13:26:00+09:00
redirect_from:
  - /theme-setup/
---

node등이 낡아서 최신버전으로 업데이트하면서 문제가 일어났다.

어떻게 업데이트해야되는지 저장해본다.

# yum update

```bash
$ yum update --exclude=kernel* -y
```

# node update

[https://nodejs.org/en/download/package-manager/#enterprise-linux-and-fedora](https://nodejs.org/en/download/package-manager/#enterprise-linux-and-fedora)

공식 사이트에서 yum을 이용한 방법은 실제 업데이트가 이루어지지 않았다.

아래의 파일을 지워야 정상 업데이트가 된다. 

```bash
$ rm /etc/yum.repos.d/nodesource-el.repo
```

그후에 공식사이트대로 진행하면 된다.

```bash
$ curl --silent --location https://rpm.nodesource.com/setup_10.x | sudo bash -
$ yum clean all
$ sudo yum -y install nodejs
$ node -v
v10.8.0
$ npm -v
6.2.0
```

# source update

```bash
$ cd /var/..
$ git pull origin master
$ npm i
```

# pm2 update

```bash
$ npm install pm2 -g
$ pm2 update

```