---
layout: single
title: mariaDB(mySQL) Server 설치
category: server
tag: [database,mariadb]
comments: true
---

간만에 mysql 쓸 일이 있어서 기억을 저장해봅니다.

그런데 mysql 대신 mariaDB를 사용할 것입니다.


mariaDB는 mysql의 창시자가 밖에 나가서 새로 만든 디비입니다.

mysql은 현재 오라클 것이죠.. 라이센스 또한 유료입니다.

공룡기업의 주특기죠.. 경쟁사 제품 사들여서 멍충이 만들고 자사 제품 독과점 만들기.. 

오라클은 mysql을 유료 전환하면서 다 떠나갈까봐 암묵적으로 무료로 사용하는 사용자들을 좌시하고 있지만, 결국 유료고 언젠 난리 떨지 모릅니다.

mariaDB라고 무료는 아닙니다. 사용시 서비스할 내용을 잘 파악해야합니다.

저는 용도가 딱 로컬라이징에 노-배포이기 때문에 무료로 사용할 수 있습니다.


mariaDB는 사실상 커맨드도 mysql과 같이 쓸 수 있어서 복사본 같습니다.

성능은 mariaDB가 훨 빠르다고 자사 소개 사이트와 구글링하면 많이 나옵니다.

그리고 무엇보다 오라클은 왠지 밉상이기 때문입니다.

그래서 mariaDB을 씁니다. 

RDBMS는 AWS rds등에서 지원하기 때문에 굳이 깔아서 쓰고 싶진 않았으나..

특정한 환경이 필요해서 한번 설치해봅니다... 

{% include toc %}

# 레퍼런스 찾기

생각보다 centOS에서 yum으로 설치하는 방법 찾기가 어려웠습니다.

찾은 링크는 아래와 같습니다.

[https://mariadb.com/kb/en/library/yum/](https://mariadb.com/kb/en/library/yum/)

> 사실 다운로드 받아서 압축풀고 어쩌고 하면되는데..  
전 리눅스를 잘 다루지 못합니다.  
그래서 이것저것 설치할 때도 해당 리소스의 레퍼런스 사이트를 참고해서 그대로 설치합니다..  
가급적 yum 같은 패키지 인스톨러로 설치합니다..  
리눅스는 제가 만든 서비스를 돌아가게 해주는 운영체제일 뿐이지..  
잘 다뤄야 된다는 생각을 해본 적이 없기 때문입니다.
맥은 webstorm, 윈도우는 지뢰찾기, linux는 각종 서버만 쓰면 됩니다.. 

# repo에 등록하기

리포파일을 만듭니다.

```bash
$ vi /etc/yum.repos.d/MariaDB.repo
```

아래 내용을 넣어줍니다.

**/etc/yum.repos.d/MariaDB.repo**  
```bash
[mariadb]
name = MariaDB
baseurl = http://yum.mariadb.org/10.1/centos7-amd64
gpgkey=https://yum.mariadb.org/RPM-GPG-KEY-MariaDB
gpgcheck=1
```

# 설치하기 

서버와 클라이언트를 설치합니다.

```bash
$ sudo yum install MariaDB-server
```

근데 잘 안되네요 미러사이트에 접속을 할 수 없답니다..

그래서 위 사이트에 코멘트중 맨 첫번째를 보니 클린 한번 해보라네요...

저 같은 사람이 많은가 봅니다.

```bash
$ yum clean all
```

그리고 다시 설치하니 잘 진행되네요..

# 실행하기

maria라고 치니 안먹네요..

기존 mysql 명령이 그대로 먹는 답니다.

```bash
$ systemctl start mariadb
$ mysql
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 2
Server version: 10.1.34-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>exit
Bye
```

잘 되네요..


# root 접속

root로 접속 후 아직 비밀번호를 넣지 않았으므로 그냥 엔터 누릅니다.

```bash
$ mysql -u root -p mysql
Enter password:
```

여기서 뒤에 붙는 mysql은 디비명 입니다. 사용자 세팅 같은 정보들이 들어있습니다.

test디비로 가려면 mysql -u root -p test 이렇게 해야죠.

# 동작 확인

설치 후 기본 데이터베이스가 뭐가 있는 지 확인해봅니다.(_사실 아는 명령어가 이것 뿐임.._)

```bash
> show databases;
  +--------------------+
  | Database           |
  +--------------------+
  | information_schema |
  | mysql              |
  | performance_schema |
  | test               |
  +--------------------+
  4 rows in set (0.00 sec)
```

# 비밀번호 변경

새로운 비번을 넣어주고 적용합니다.

```bash
> update user set password=password('새로운 비번 어렵게') where user='root';
> flush privileges;
> exit
Bye
```

# 권한 부여

localhost에게 test DB 아래의 모든 테이블을 사용하게 합니다.

```bash
> GRANT ALL privileges ON test.* TO root@localhost IDENTIFIED BY '위에 지정한 패스워드';
> flush privileges;
```

> 전체로 하려면 test.* -> ```*.*```

# root로 재접속하기

이제 정식으로 localhost 접속해봅니다.

```bash
$ mysql -u root -p test
Enter password:
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 7
Server version: 10.1.34-MariaDB MariaDB Server

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [test]>
``` 

잘 되네요..

# 원격 접속: 방화벽

이제 제 피씨에서 VM에 접속해야 됩니다.

아마도 로컬호스트만 접속하게 해놨을 테죠..

우선 3306포트를 방화벽에서 열어줘야합니다. 왠지 mysql이라는 서비스명이 있을 것 같아서 아래처럼 해봤습니다.

```bash
$ firewall-cmd --zone=public --add-service=mysql --permanent
$ firewall-cmd --reload
```

별 에러 없는 것을 보니 잘 되는 것 같습니다.

# 원격 접속: 설정파일

이제 바인드 아이피를 찾아서 0.0.0.0으로 바꿔야될 겁니다.

바로 /etc/my.cnf 에 그런 설정이 있을 것 같네요

열어보니 이상하게 텅텅 비어있습니다.

```bash
#
# This group is read both both by the client and the server
# use it for options that affect everything
#
[client-server]

#
# include all files from the config directory
#
!includedir /etc/my.cnf.d
```

왠지 includedir가 의미하는 것이 저 디렉토리 밑에 다 있나보네요.

가봤더니 5개의 파일이 있습니다.

그 중 server.cnf를 열어보니

```bash
#bind-address=0.0.0.0
```

주석 처리 되어있네요. 다 열려있단 소립니다.

괜히 왔습니다.

# 원격접속 사용자 추가

다시 root로 접속해봅니다.

```bash
$ mysql -u root -p mysql
> GRANT ALL privileges ON test.* TO root@'1.2.3.%' IDENTIFIED BY '위에 지정한 패스워드';
> flush privileges;
```

1.2.3.%는 1.2.3.1~ 254 까지 허용한다는 말입니다.

예시에는 root인데 따로 만들어 주는 것이 당연히 좋겠죠?

밖에서 해보니 잘 되네요.

![alt sequel](/images/server/2018-08-07 15-54-14 maria.png)

맥에서 무료로 쓸 수 있는 sequel Pro 입니다. 

# 재시작시 다시 구동

서버라 그런 것인지 다행이 아무것도 안해도 그냥 됩니다..
