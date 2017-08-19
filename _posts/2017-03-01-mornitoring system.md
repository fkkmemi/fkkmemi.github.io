---
layout: single
title: Monitoring System
category: server
tag: [server, node, mongo]
comments: true
---

서버를 구성할 때 가장 유의해야할 것은 유지보수라고 할 수 있다.

이미 구성되어진 서버 플로우를 차후에 바꾸는 것은 어렵다.

> 돈이면 안되는 일이 없긴 하지만 비용을 최소화하기 위함이다.

모니터링 시스템을 구성하기 위한 scale-out 서버 설정을 직접해보도록 하겠다.

## 구성도

![alt sys](/images/monitoring_system/1.png)

1. comm(데이터수신서버) 2대 
 - communication server tcp socket data를 수신 받고 db에 저장
 - by node.js

2. web(웹서버) 2대
 - 사용자로 부터 https요청을 받아서 처리함
 - by node.js

3. db(데이터베이스서버) 3대
 - comm, web 서버의 데이터 저장소
 - 3대는 데이터를 분산해서 가져간다(sharding)
 - by mongoDB

4. lb(로드밸런서) 2대
 - comm, web 서버의 수평확장을 위한 트래픽 분산 시스템
 - type : round robin   
   
> Round robin 방식은 Client 의 요청을 순차적으로 순환을 하여 Load Balancing 을 하는 방식입니다.

## 흐름

- device들은 lb(comm)에 접속한다.

- lb(comm)는 타겟 comm vm을 정해준다.  

> 타겟이 3개의 서버일 경우, 마지막 접속이 0이면 1로 1이면 2로 2면 0으로

- comm은 db로 연결하여 현재 데이터가 유효한지 판단 후 저장

- db는 데이터를 서로 나눠 가진다  

> 3개의 db vm에 60개의 데이터를 쓰면 각 db vm에 20개씩 나눠 가진다.

- user들은 lb(web)에 https request 한다.

- lb(web)는 타겟 web vm을 정해준다.
 
- 정해진 web vm에 https request 한다.  

> https bridge mode로 lb가 인증서를 가지고 있지 않다.  
각 web vm이 인증서 복사본을 가지고 있고 각각 구동한다.

- db에서 데이터를 찾아서 https response 한다.

## 구성

기본적으로 web,comm vm들이 상대경로로 독립실행 가능하게 작성되어 있어야한다.

> 복제해서도 실행될 수 있어야된다.(수평확장 고려)  
eg)    
req.redirect('https://xxx.com/info') X  
req.redirect('../info') O 

### vm : comm

- vm(cs0)생성 

- cs0 서버 설정을 한 후 이미지 생성을 한다.

[이미지명 cs0-init]

![alt image](/images/monitoring_system/2.png)

- cs1이란 호스트명으로 복제하여 시작한다.

![alt start](/images/monitoring_system/3.png)

- 편의를 위해 cs0.xxx.com, cs1.xxx.com 각각 도메인을 연결하여 접속한다.

### lb : comm

- Load balancer를 추가한다.

![alt lbadd](/images/monitoring_system/4.png)

- cs0와 cs1의 호스트를 등록하고 사용할 포트를 지정한다.

> 포트는 한개만 가능하며, 다른 포트를 등록해야한다면 lb를 추가해야한다.  
[참고 : https://ucloudbiz.olleh.com/manual/ucloud_loadbalancer_user_manual.pdf](https://ucloudbiz.olleh.com/manual/ucloud_loadbalancer_user_manual.pdf)

- 각각의 서버에 상태를 확인할 수 있다.

![alt status](/images/monitoring_system/5.png)

- cs0, cs1에 ssh 접속해본다
 
- console에 접속 로그를 작성하여 확인해 보면

![alt log](/images/monitoring_system/6.png)

- 어떤 아이피가 계속 접속하고 끊고를 매초마다 반복하는 것이 보인다.

> tcp socket의 health 상태를 주기적으로 체크하는 것이며 lb의 타겟선정을 위한 행위라고 할 수 있다.  
그러므로 이런 잦은 소켓 접속에 대비한 프로그램을 작성해야한다.

### db : sharding

- [세팅 : mongo-sharding](/mongo-sharding-1/)

### vm : web

- vm(ws0)생성

- ws0 서버 설정을 한 후 이미지 생성을 한다.

> 각각의 서버에는 복제한 인증서와 키를 가지고 서버 실행을 해둔다.

- ws1이란 호스트명으로 복제하여 시작한다.

- 편의를 위해 ws0.xxx.com, ws1.xxx.com 각각 도메인을 연결하여 접속한다.

> 위의 cs 설정과 동일하다.

### lb : web

- lb : comm 과 같이 세팅한다.

> 이때 설정 Load balancer type을 https(bridge) 로 설정하고 well-known port 인 443 포트를 지정하면 된다.


## 검증

- 아래와 같이 vm cs0,cs1,ws0,ws1 4개를 띄어 놓는다.

![alt 4ea](/images/monitoring_system/7.png)

- cs0,cs1,ws0,ws1을 껏다 켰다 해보고 특정한 곳에 부하를 주고 lb가 switching이 되는지 확인 해본다.

## 결론

사실 처음에는 소규모의 데이터를 운용할 것이기에 lb의 존재 자체가 오버헤드인데 나중에 lb를 추가하기가 여러가지로 불편하고, 서비스의 연속성을 가지며 변경하긴 어렵다.
 
lb가 제일 중요한 타겟 서버 아이피를 가져야되기 때문이다.

아무래도 단순하게 접근하기에는 aws보다는 훨씬 설정이 간편하게 느껴진다. 

{% include toc %}

