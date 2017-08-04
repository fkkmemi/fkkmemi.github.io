---
layout: single
title: Mongo Sharding 2 - config,router 구성
category: mongoDB
tag: [database, mongo]
comments: true
sidebar:
  nav: "mongos"
---

## 개요

원리는 만들어 놓았던 shardsvr들에 연결하기 위해 router를 통해야하는데 그 config meta 정보를 configsvr에 담는 것이다.
 
> 대략 이런 구성이라고 보면 된다  
client -> router -> configsvr -> shards[?] 

## 구성

### configsvr

각 서버의 conf 파일을 만들어 아래의 내용으로 작성한다.

```text
vi /data/mongo/configsvr.conf
```

```yaml
# configsvr.conf

systemLog:
  destination: file
  logAppend: true
  path: /data/mongo/log/configsvr.log

storage:
  dbPath: /data/mongo/configsvr
  journal:
    enabled: true

processManagement:
  fork: true  # fork and run in background
  pidFilePath: /data/mongo/pid/configsvr.pid  # location of pidfile

net:
  port: 27019
#  bindIp: 127.0.0.1  # Listen to local interface only, comment to listen on all interfaces.

replication:
  replSetName: configReplSet

sharding:
  clusterRole: configsvr
```

shardsvr구성과 거의 같지만 여러개의 replset을 묶어줄 세트가 필요하고

clusterRole: configsvr 이란 옵션을 넣어줘야한다. 

> 3.x 이전 버전엔 없던 것 같다.

각서버 작성후 옵션을 --configsvr를 넣어서 실행한다.

```text
mongod --configsvr -f configsvr.conf
mongo dbc:27019
```

이제 replset를 만들어줘야하는데 shard 구성했을때의 rs method를 통해 멤버를 추가하면 된다..

> shard구성할때 귀찮게 reconfig 했었는데. 이번엔 멤버 추가와 함께 initiate를 해보겠다..

```text
rs.initiate( { _id: "configReplSet", configsvr: true, members: [ { _id: 0, host: "dbc:27019" }, { _id: 1, host: "dbs1:27019" } ] } )
rs.status()
```

shardsvr test와 마찬가지로 primary secondary를 껏다 켜보면서 잘 바뀌는지 테스트 해보면 된다.

### router

mongos 라고하는 프로세스로 분산되어있는 서버들의 연결을 담당한다.

```text
vi /data/mongo/mongos.conf
```

```yaml
# mongos.conf
systemLog:
  destination: file
  logAppend: true
  path: /data/mongo/log/mongos.log

processManagement:
  fork: true
  pidFilePath: /data/mongo/pid/mongos.pid

net:
  port: 27017
#  bindIp: 127.0.0.1  # Listen to local interface only, comment to listen on all interfaces.

sharding:
  configDB: configReplSet/dbc:27019,dbs1:27019
```

> configDB라는 세팅 외에는 별 특별한 것이 없다.  
기존엔 configsvr들의 ip만 컴마로 주욱 썼는데 3.x 이후 부터 replset를 명시하고 호스트:포트 방식으로 작성법이 바뀐것 같다.  
기존처럼 192.168.111.111, 192.168.111.112 이런식으로 작성하면 에러가 난다.  
추후 로드밸런서로 공인망 부하를 줄이기 위해 mongos역시 두대 다 세팅하였다.    
일반적인 상황이면 접근하고 싶은 서버에서 하나만 구성하면 된다.

```text
mongos -f /data/mongo/mongos.conf
```

실행해보면 워닝이 뜨는데 configsvr 3대를 권장한다는 메세지가 뜬다.

> 권장사항은 왠지 거부하기가 두렵기 때문에 다음 서버 수평확장 때 컨픽서버도 하나 더 세팅해보겠다.

라우터의 포트를 27017로 했기 때문에 그냥 mongo 만 치면 접속된다

그래서 아래와 같이 접속해보면 된다.

```text
mongo
mongo dbc
mongo dbs1
mongo dbc:27017
```

> 워닝이 뜰 경우 (Access control is not enabled for the database.)  
rdbms처럼 계정을 쓰라는 얘기다  
mongos.conf에 auth관련 세팅을해서 실행하라는 건데...  
이건 나중에 세팅해보기로하겠다  
[참고:https://medium.com/@raj_adroit/mongodb-enable-authentication-enable-access-control-e8a75a26d332#.ebau7ien8](https://medium.com/@raj_adroit/mongodb-enable-authentication-enable-access-control-e8a75a26d332#.ebau7ien8)

sh method로 shard들을 추가해야한다

```text
sh.addShard("rs0/dbc:27018")
sh.addShard("rs0/dbs1:27018")
```

복제셋/샤드주소 이렇게 추가하면된다. 다른 복제셋/샤드주소 도 얼마든지 추가 가능하다

다른 커맨드는 아래를 참조한다..

[https://docs.mongodb.com/manual/reference/method/js-sharding/](https://docs.mongodb.com/manual/reference/method/js-sharding/)

더 디테일한 세팅이나 서버 추가는 공식사이트를 확인하여 변경하면된다.

> 이제 확인해볼 차례다 세팅이 완료 된 상황이기 때문에..  
더이상 mongo shell로 접근해서 피곤한 command 레퍼런스 찾아보기는 안해도 된다.    
다양한 visual client가 있기 때문에 이제부터는 mongobooster라는 프로그램으로 마우스 콕콕 누르며 확인하면 된다..

[mongobooster]

![alt mongobooster](/images/mongo_sharding/6.png)


> 사실 이정도 말고 아는 것도 없고, 알 필요도 없기 때문이다..  
front-back end 개발을 해야하는 상황에 DBA수준의 관리 능력은 가질 여력이 안된다..   
mongos 2대 shards 1set 확인 되었음..  
이제 read/write 장애발생 상황은 QA에 맡기고 싶지만, 없기 때문에 여러가지 셀프테스트를 해보면 된다.. :(

## 결론

간단한 설정 몇개로 심플한 구성은 완료가 되었다.

mongodb.com 에서는 서버 한대를 쓰더라도 replSet를 구성하기를 권하고있다.
 
사실 한대로 무언가를 한다는 것 자체가 테스트용도라는 것이다.

어짜피 해야될 구성 replSet, sharding을 고려해서 설계하는 것이 좋을 것 같다.

> 역시 설정이 너무 복잡함.. 여전히 복제와 샤딩이 정확히는 이해가 안감(3대중 한대가 죽어도 데이터가 있는 것 외에는 아는 것이 없음)

점수 : 7점

## 의견

개발자로 살며 늘 생각하지만 출제자의 의도를 파악하는 것이 중요하다..

stackoverflow.com등의 정보는 고맙지만 배껴 쓰고 더 혼란이 가중되었던 적이 많다..

특히 deprecated 된 항목이 많아서 갱신된 최신 레퍼런스 매뉴얼을 참고해야한다..

> nodejs를 할꺼면 nodejs.org, mongo를 할꺼면 mongodb.com, android를 할꺼면 google.com, angularjs를 할꺼면 angularjs.org