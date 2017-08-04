---
layout: post
title: Mongo Sharding 3 (add servers)
category: mongoDB
tag: [database, mongo]
comments: true
---

## 개요

configsvr 권장 3세트와 shardsvr를 더 추가해서 수평확장(scale-out)이 잘 이루어지는지 확인

## 구성

### shardsvr add

아래 링크와 같이 호스트명 dbs2 shardsvr를 추가한다.

[shard add : https://fkkmemi.github.io/mongo-sharding/](https://fkkmemi.github.io/mongo-sharding/)

기본적으로는 primary에서만 추가를 할 수 있기 때문에 기존 두 서버중 아무 shardsvr나 들어가서 rs.status() 를 확인하여 primary를 찾아내고 작업하면 된다.

> 물론 rs.reconfig(v,{force:true}) 하면 secondary에서도 가능하지만 권장사항을 지키는 것이 좋다

```text
mongo dbc:27018 //or mongo dbs1:27018
rs.status()
rs.add("dbs2:27018")
rs.status()
```

확인해보면 rs0 라는 replSet은 3개가 되었다.

### configsvr add

아래 링크와 같이 dbs2에서 configsvr를 구성,저장하고 실행한다.

[configsvr add : https://fkkmemi.github.io/mongo-sharding-2/](https://fkkmemi.github.io/mongo-sharding-2/)

```text
mongod --configsvr -f configsvr.conf
```

지난번엔 멤버를 한번에 등록했지만 이번엔 한 대만 추가한다.

```text
rs.add("dbs2:27019")
rs.status()
```

### mongos modify

dbc, dbs1의 mongos.conf 파일을 열어서 configDB에 dbs2:27019를 추가한다.


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
  configDB: configReplSet/dbc:27019,dbs1:27019,dbs2:27019
```

설정파일을 바꾼 후 재시작한다.

```text
mongo
>use admin
>db.shutdownServer()
>exit
mongos -f /data/mongo/mongos.conf
```

dbs2의 mongos는 안해도 되지만 추후 로드밸런싱 대비 라우터를 추가한다.

이제 3곳의 서버에 모두 연결 가능하다.

[daemon 확인]

```yaml
ps -ef|grep mongo
mongo     2445     1  0 09:06 ?        00:01:12 mongod --shardsvr -f shardsvr.conf
mongo     3734     1  0 09:37 ?        00:01:07 mongod --configsvr -f configsvr.conf
mongo    14243     1  0 13:56 ?        00:00:00 mongos -f mongos.conf
```

![alt server](/images/mongo_sharding/7.png)


매번 커맨드를 쳐서 데몬을 실행시키기는 힘들기 때문에 sh 파일을 만들어 놓는게 좋다

[mongo.sh]

```text
vi /data/mongo/mongo.sh
```

```yaml
#!/bin/bash
mongod --shardsvr -f /data/mongo/shardsvr.conf
mongod --configsvr -f /data/mongo/configsvr.conf
mongos -f /data/mongo/mongos.conf
```

[권한상승]

```text
chmod 777 /data/mongo/mongo.sh
```

[run]

```text
su - mongo //if mongo 계정이 아니라면.. 꼭 mongo계정으로 실행해야한
sh /data/mongo/mongo.sh
```

[boot and run]

root계정으로 로그인한 후

```text
vi /etc/rc.d/rc.local
```

아래 한줄을 추가한다

```yaml
su - mongo -c '/data/mongo/mongo.sh'
```

3서버에 read/write와 장애 상황을 만들어서 Self QA Test를 진행해보면 된다

## 성능시험

기존 서버에 있는 약 1억개의 실제 운영되는 데이터를 가져왔다.

![alt count](/images/mongo_sharding/8.png)

한 컬렉션에서 하루치를 2880개를 찾아내는데 0.02초.

![alt 1day](/images/mongo_sharding/9.png)

15필드의 데이터를 프로젝션(불필요한 필드 제거) 없이 반나절 맵에 뿌리는데 0.8초

![alt web](/images/mongo_sharding/10.png)

과정은 

1. map init
2. xhr(ajax) get request
3. node router(api/data/id)
4. mongo query(findById{id})
5. response(json)
6. map data push
7. map poly+marker

집(giga)에서 하면 대부분 0.3초 이내에서 끝난 것 같다.

## 요약

**cloud server 활용**

- 기존 3서버(vm)중 하나를 configsvr, mongos 관련 내용을 제거하고 이미지로 만든다
- 호스트 dbs(n) 으로 이미지를 이용해 vm을 생성
- primary shard에 연결해서 dbs(n) 추가

## 결론

복잡했던 3configsvr만 구성해 놓으면 추가는 쉬움

noSQL답데 너무 빅데이터 엔터 시장만 고려한 것 같은 많은 수의 서버 권장사항 ...

마이너버전 업데이트인데도 deprecated된 항목이나 바뀐 것이 너무 많다..

> 항상 새로운 것에 도전하는 것이 좋지만 이번엔 새버전이 두렵다..

점수 8점

## 의견

사실 끝이 아니다...
 
DBA라면 이제 시작일 수 있는 아주 기초적인 것만 한 상태이다.

arbiter 설정도 해야하고, 백업 플랜도 짜야하지만...

여기까지 하고 이제 화려한 front-end로 넘어가야할 시기인 것 같다..

> 더 파면 가성비가 안나온다..


**이전 [2편 : config, router](/mongo-sharding-2/)**