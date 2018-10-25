---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 32 클라우드 서버 디비 설치 및 테스트
category: nemv
tag: [nemv,server,toast,cloud]
comments: true
sidebar:
  nav: "nemv3"
---

서버가 개발했을 때와 같이 동작하게 하려면 얀, 몽고디비를 설치해야합니다.

**중요: 2018.10.23 연결 변경**
  
이제 토스트에서 새로운 인스턴스 생성시 루트로 접근을 못하게 막아놓았습니다..

```bash
$ ssh -i nemvKey.pem centos@x.x.x.x
$ sudo -i
```

centos라는 일반 유저로 접속한 후에 sudo -i 루트로 다시 접근해야합니다..

{% include toc %}

# 얀 설치하기

CentOS에 맞는 설치 방법으로 설치합니다.

참고: [https://yarnpkg.com/en/docs/install#centos-stable](https://yarnpkg.com/en/docs/install#centos-stable)

```bash
$ curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
$ sudo yum -y install yarn
$ yarn -v
1.10.1
```

# 백엔드 서버 패키지 설치

```bash
$ cd /vaw/www/nemv3/be
$ yarn
```

yarn 혹은 yarn install로 package.json에 적혀있는 대로 종속 요소들(익스프레스, 코스등)이 설치됩니다.


# 프론트 서버 패키지 설치

```bash
$ cd /vaw/www/nemv3/fe
$ yarn
$ yarn build
```

종속 요소 설치후 백엔드에서 사용할 수 있는 정적인 파일들을 만듭니다.

# 서버 구동

```bash
$ cd /vaw/www/nemv3/be
$ npm start
```

이제 서버를 구동하면 몽고디비 에러가 납니다..

아직 몽고디비를 설치하지 않았죠?

사실 같은 서버에 디비를 놓는 것은 좋지 않지만, 테스트를 위해 설치해봅니다.

# 리눅스 패키지 업데이트

몽고디비를 설치하려는 데 오류가 나서 업데이트 합니다.

리눅스에 내장되어있는 각종 패키지들을 업데이트 하는것인데요.

클라우드 서버에서 커널은 피하고 업데이트 하라고 했으니 빼고 업데이트 합니다.

```bash
$ yum update --exclude=kernel* -y
```

# 몽고디비 설치

참고: [https://docs.mongodb.com/manual/administration/install-on-linux/](https://docs.mongodb.com/manual/administration/install-on-linux/)

## 몽고디비 설치 정보 추가

node같이 curl등으로 다운 받는 방법이 없고 직접 파일을 작성하네요..

```bash
$ vi /etc/yum.repos.d/mongodb-org-4.0.repo
```

**i** 입력후 아래 글을 붙힙니다.

```yaml
[mongodb-org-4.0]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.0/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.0.asc
```

esc 누르고 :wq를 눌러서 저장하고 나옵니다.

![alt yum.repos.d](/images/nemv/스크린샷 2018-10-22 오후 4.25.28.png)

/etc/yum.repos.d/에 가보시면 node나 git등의 정보들도 들어 있음을 확인 할 수 있습니다.

## 몽고디비 설치

```bash
$ yum install -y mongodb-org
```

## 몽고디비 실행

```bash
$ service mongod start
Redirecting to /bin/systemctl start  mongod.service
```

공식 홈대로 실행을 진행했는데 아래 메세지가 의미 하는 것은..

> CentOS7부터 service 대신 systemctl이라는 명령어를 사용한다는 것입니다.  
저도 가급적 systemctl을 주로 이용하는 편이지만, 그냥 공식홈의 룰을 따릅니다. 

## 몽고디비 실행 저장

다시 시작했을 때 자동 실행 가능하게

```bash
$ chkconfig mongod on
```

> 안해도 자동 실행 될 듯 한데.. 뭐 공식홈에서 하라는데로 스텝 진행합니다.

## 몽고디비 확인

```bash
$ mongo
> use nemv
> db.test.insertOne({a:1})
> show dbs;
admin 0.000GB
config 0.000GB
local 0.000GB
nemv 0.000GB
```

의미 없는 데이터를 써보고 디비가 잘 생성되는 지 판단합니다.

# 포트 3000 열어주기

토스트 콘솔에서 Network - Security Groups - nemv 클릭후

![alt 3000 port](/images/nemv/스크린샷 2018-10-22 오후 4.38.40.png)

현재 익스프레스의 기본 포트인 3000번 포트를 추가합니다.

# 앱 실행하기

```bash
$ cd /var/www/nemv3/be
$ npm start
```

# 프론트 절대 경로를 상대 경로로 변경하기

프론트 요청이 절대 경로 localhost:3000를 가르키고 있어서 서버에서는 실행이 안됩니다.

**fe/src/views/user.vue** 파일을 수정합니다.

일괄로 api 연결 주소를 편집

http://localhost:3000/ -> / 

> 추후에는 빌드별로 다르게 동작하게 변경할 것입니다.

깃 커밋, 깃 푸쉬해서 깃헙 서버에 보냄

# 서버에서 변경 사항 받기

이제 ssh 연결이 되어있어서 손쉽게 받을 수 있습니다.

```bash
$ cd /var/www/nemv3
$ git pull
$ cd fe
$ yarn build
$ cd ../be
$ npm start
```

# 브라우저 확인

브라우저에 http://x.x.x.x:3000으로 확인해보면 됩니다.

![alt browser view](/images/nemv/스크린샷 2018-10-22 오후 4.47.52.png)

# 영상

{% include video id="H-d5IO2IWQw" provider="youtube" %}   




