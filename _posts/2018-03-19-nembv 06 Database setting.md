---
layout: single
title: NEMBV 6 Database setting
category: nembv
tag: [nembv,mongodb,atlas]
comments: true
---

이제 api와의 연동할 MongoDB를 세팅 해보도록하겠다.

이미 사용중인 디비가 있다면 다음 챕터로 이동. 

mongoDB를 이용할 것인데 무거운 디비를 로컬에 까는 것 보다는 무료 호스팅 서버를 이용하도록 하겠다.

다양한 클라우드 서비스가 있지만 그중 몽고디비가 직접 운영하는 아틀라스를 사용해보겠다.

[https://www.mongodb.com/cloud/atlas](https://www.mongodb.com/cloud/atlas) 에서 get started free를 이용하면 무지 느리지만 그래도 돌아가는 서버를 써볼 수 있다.

가입을 한 후 대시보드가 뜨는데

새로운 디비 생성과 접근을 위해 계정을 새로 만들어 보도록 한다.

### mongoDB setting

#### atlas 계정 생성

- Security -> MongoDB Users -> ADD NEW USER button click

![계정 생성 1](/images/nembv/2018-03-19 15-50-04 Security.png)

- user name : nembv 입력, 패스워드 입력 Rad and Write 권한 선택후 Add User

![계정 생성 2](/images/nembv/2018-03-19 15-51-08 Security.png)

- nembv@admin 생성 확인

![계정 생성 3](/images/nembv/2018-03-19 16-41-07 Security.png)

#### atlas 접속

- connect click

![계정 접속 1](/images/nembv/2018-03-19 17-11-39 Clusters.png)

- ip 허용목록에 본인 아이피(Add current ip address) 혹은 범위 추가

![계정 접속 2](/images/nembv/2018-03-19 15-36-27 Clusters.png)

- mongo shell 설치 안내 페이지 이동

![계정 접속 3](/images/nembv/2018-03-19 15-37-13 Clusters.png)

- os 별 설치 방법대로 설치 ( *디비 설치가 아닌 shell 설치니 긴장 풀것* ) : 설치되있다면 다음

![계정 접속 4](/images/nembv/2018-03-19 15-37-55 Clusters.png)

- 3.4 버튼을 눌러서 --username은 nembv 로 --password는 설정한 것으로 변경 후 해당 내용 카피 ( *3.6은 접속설정이 매우 심플해졌지만, 상용버전부터 3.6으로 설치된다* ) 

![계정 접속 5](/images/nembv/2018-03-19 15-35-52 Clusters.png)

- 터미널을 열어 붙혀넣기

![계정 접속 6](/images/nembv/2018-03-19 16-42-51 bash.png)

- 접속 확인 서버 버전과 프롬프트가 나오면 성공

```bash
MongoDB server version: 3.4.13
```

#### 데이터베이스 생성 및 테스트

- 현재 데이터베이스를 확인해본다.

```bash
> show dbs; # or show databases;
admin  0.000GB
local  5.985GB
```

현재는 관리 디비만 보인다.

- 디비를 만든다.

```bash
> use nembv;
switched to db nembv
> show dbs;
admin  0.000GB
local  5.985GB
```

사용하겠다고 하고 다시 리스트를 확인했으나 그대로이다. 

> 사실 use 등을 이용할 필요 없다. 백엔드사이드에서 디비 선정해서 써버리면 자동 생성되는데 지금 시각적으로 보여주기 위함이다. 

- 데이터를 써본다.

```bash
> db.hello.insert({make:'gogo'});
WriteResult({ "nInserted" : 1 })
> show dbs;
admin  0.000GB
local  5.985GB
nembv  0.000GB
```

nembv db에 hello라는 collection에 make:'gogo'라는 내용아 딤긴 도큐먼트를 추가 한것이다.

- 데이터를 확인.

```bash
> db.hello.find();
{ "_id" : ObjectId("5aaf6a883248872586b6ee49"), "make" : "gogo" }
```

이것으로 더이상 복잡한 shell을 통해 무언가를 할필요는 없다 이후 부터는 백엔드에서 mongoose로 편리하게 작업할 것이다.

> 추가로 제대로 디비를 확인하고 싶으면 NoSQLBooster 라는 상용 프로그램을 추천한다. roboMongo라는 무료 프로그램도 있긴 하지만..  
제대로 프로젝트가 진행된다면 NoSQLBooster가 훨씬 시간 이득이다.