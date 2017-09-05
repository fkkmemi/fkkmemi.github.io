---
layout: single
title: linux nfs server
category: linux
tag: [linux,nfs,server]
comments: true
---

서버를 운영할 때 file server와 같은 물리적인 데이터를 담아놓을 공간은 항상 필요하다

이번에 서버당 데이터를 몇백기가 쌓아야하는 상황이 되어서 로컬이 아닌 외부 넓직한 서버에 쌓아놓기로 해봤다.

smb로 공유는 해봤지만 아무래도 linux<>linux는 nfs가 맞을 것 같아서 기록해본다..

## 설치

CentOS 7 환경에서 설치

### 서버

```text
# yum -y install nfs-utils
``` 

**mount할 빈 공간 만듬(읽기 쓰기 가능하)**  
```text
# mkdir /data/nfs
# chmod 777 /data/nfs
```

**허용 아이피대역, 권한 설정**  
```text
# vi /etc/exports
/data/nfs 192.168.0.*(rw,sync) sse(rw,sync) 111.222.111.222(ro,no_all_squach)
```

기본값이 sync인데 그외의 옵션은 아래와 같다

- rw : 읽기, 쓰기 가능
- ro : 읽기만 가능
- secure : 클라이언트 마운트 요청시 포트를 1024 이하로 한다.
- noaccess : 액세스 거부
- root_squach : 클라이언트의 root가 서버의 root권한을 획득하는 것을 막는다
- no_root_squash : 클라이언트의 root와 서버의 root를 동일하게 한다
- sync : 파일 시스템이 변경되면 즉시 동기화한다.
- all_squach : root를 제외하고 서버와 클라이언트의 사용자를 동일한 권한으로 설정한다.
- no_all_squach : root를 제외하고 서버와 클라이언트의 사용자들을 하나의 권한을 가지도록 설정한다. 

**showmount 설치**  
```text
# yum -y install showmount
```
 
**서버 확인**    
```text
# showmount -e localhost
```

**서버 구동**  
```text
# systemctl restart nfs-server
```

**서버 자동 구동**  
```text
# systemctl enable nfs-server
```  

**방화벽 설정**  
```text
# firewall-cmd --permanent --add-service=nfs
# firewall-cmd --permanent --add-service=mountd
# firewall-cmd --permanent --add-service=rpc-bind
# firewall-cmd --reload
```

**클라이언트 접속 확인**  
```text
# exportfs -v
```

### 클라이언트

**마운트 디렉토리 생성**  
```text
# mkdir /data/xxx
```

**서버 상태 확인**  
```text
# showmount -e 192.168.0.33
```

**마운트**
```text
# mount -t nfs 192.168.0.33:/data/nfs /data/xxx
```

**자동 마운트**
```text
#vi /etc/fstab
192.168.0.33:/data/nfs /data/xxx   nfs     default    0 0
```

## 결론

마운트 해서 써보니 첫 연결이 느렸다.. vi a.txt 를 입력하고 나면 한참후에 에디터가 떳다..

두번째 연결부터는 잘되는데.. 테스트가 좀더 필요할듯..