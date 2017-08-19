---
layout: single
title: Mongo dump & restore
category: mongoDB
tag: [database, mongo]
comments: true
---

Mongodb는 분명히 좋은 백업 솔루션이 있겠지만..

아직 습득하지 못했기 때문에.. 기초적인 방법으로 백업을 받고 복구 하는 방법에 대해 알아보겠다..

crontab 등으로 매 일 자정에 다른 서버에 덤프를 해놓는 것이 목표..

{% include toc %}

## 사용

다양한 방법으로 자료를 백업, 복구 할 수 있다..

아래의 링크를 확인하면 관련 자료를 얻을 수 있다..

[https://docs.mongodb.com/manual/reference/program/](https://docs.mongodb.com/manual/reference/program/)

db, collection 별로 혹은 특정 쿼리로 파일 in/out이 가능하며 두 서버를 연결하여 파이프 형태로 바로 전달해 줄 수도 있다..
 
> eg : or로 piping  
mongodump --host db1.com --archive --db test --port 27017 | mongorestore --host db2.com --archive --port 27018    
db1.com의 test db를 db2.com에 바로 전달한다..

목표는 백업이기 때문에 파일로 저장 하고 복원하는 방법만 서술하겠다.

### 1. mongodump

```text
mongodump -v --host dbc:27017 --out /data/bk
```

/data/bk 폴더에 dbc:27017에 있는 모든 자료를 저장한다

--achive 옵션을 사용하면 압축된 결과가 나온다

### 2. mongorestore

```text
mongorestore -v --host localhost:27017 /data/bk --objcheck
```

현재 서버에 모든 자료를 복원한다.

## 결론

Mongo backup 솔루션이 훌륭한 게 있을텐데 고전적인 방법 밖에 쓸 수 밖에 없는 안타까운 현실

