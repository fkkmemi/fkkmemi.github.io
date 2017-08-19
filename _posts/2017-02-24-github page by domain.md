---
layout: single
title: github page domain 설정
category: github
tag: [web, github]
comments: true
---

기본적으로 jekyll github page 를 이용할 경우 id.github.io 라는 도메인명으로 접속하게 되는데

xxx.com을 소유하고 있다면 

id.github.io -> xxx.com 로 접속되게 가능하다.

> [godaddy.com](https://godaddy.com) 도메인이 있기 때문에 이 기준으로 설정해보겠다.

## 사용

### 1. cname change

사이트 최상단에 있는 CNAME 파일에 소유한 도메인을 추가하여 커밋 푸쉬한다..

![alt cname](/images/dns/1.png)

### 2. dns setting

- 호스트 추가  
    - 호스트 : @
    - 지시 방향 : 192.30.252.153

- cname 추가
    - 호스트 : www
    - 지시 방향 : id.github.io

[세팅결과]

![alt cname](/images/dns/1.png)

[확인]

```text
junhoui-MacBook-Pro:~ fkkmemi$ dig fkkmemi.com +nostats +nocomments +nocmd

; <<>> DiG 9.8.3-P1 <<>> fkkmemi.com +nostats +nocomments +nocmd
;; global options: +cmd
;fkkmemi.com.			IN	A
fkkmemi.com.		3600	IN	A	192.30.252.153
```

[참고]

[http://andrewsturges.com/blog/jekyll/tutorial/2014/11/06/github-and-godaddy.html](http://andrewsturges.com/blog/jekyll/tutorial/2014/11/06/github-and-godaddy.html)

## 결론

아직 ssl setting을 안해서 id.github.io 가 더 있어보이기 때문에 쓰고 싶진 않다.

{% include toc %}
