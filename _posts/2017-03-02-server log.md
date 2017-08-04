---
layout: post
title: Server log
category: server
tag: [node, talk]
comments: true
---

## 개요

서버를 구성하여 러닝 시키는 것은 간단하지만 운영과 유지보수가 어려운데, 그 이유중 하나가 보안이다.

클라이언트와 달리 서버는 늘 정적인 어딘가에 위치하고 있기 때문에 침입자들은 다양한 시도를 하며 서버의 정보를 탈취하려고 한다.

서버 로깅을 통해 어떤 공격들이 있는지 기록해본다.

## Log

- phpmyadmin 공격  
가장 자주 볼 수 있는 로그로 php서버에 깔려 있는 phpmyadmin에 접속해 database를 해킹 시도한다 

    > phpmyadmin은 mysql web client다 root권한으로 디비에 접속해 쿼리등을 통해 데이터 열람,수정,백업등을 할 수 있는 편리한 도구다  
    편리한 만큼 편리하게 털어갈 수 있다.

웹툴이기 때문에 get request로 들어오는데 accout form으로 세션 인증을 받아야하므로 brute force 공격일 가능성이 높다

> 브루트 포스 공격이란? 무차별 대입공격이란 뜻으로 성공할 때까지 가능한 모든 조합의 경우의 수를 시도해 원하는 공격을 시도하는 것으로 대표적인 예로 크렉 등 소프트웨어를 이용하여 password를 추측하는 방법이다.

서버를 구성한지 하루밖에 안되었지만 공격이 들어온다.

[2017-3-2]

![alt sys](/images/server_log/1.png)

[2017-3-7]

![alt sys](/images/server_log/2.png)

[2017-3-8]

![alt sys](/images/server_log/3.png)