---
title: "CentOS7 VM Setup"
permalink: /docs/centos7-vm-setup/
excerpt: "KT VM 새로 이미지 만들 때 정리"
last_modified_at: 2018-03-22T13:26:00+09:00
redirect_from:
  - /theme-setup/
---

간만에 개발용 vm을 만들어야해서 다시 기록해본다.

# password 변경

```bash
$ passwd
```

# firewall setting

기본적으로 ssh는 이미 열려있다.. *안그럼 들오지도 못하니까..*

나머지는 다 닫힌채로 방화벽 활성상태

사용할 80, 443, 27017을 열어준다.

```bash
$ firewall-cmd --zone=public --add-service=http --permanent
$ firewall-cmd --zone=public --add-service=https --permanent
$ firewall-cmd --zone=public --add-port=27017/tcp --permanent
$ firewall-cmd --reload
$ cat /etc/firewalld/zones/public.xml
<?xml version="1.0" encoding="utf-8"?>
<zone>
  <short>Public</short>
  <description>For use in public areas. You do not trust the other computers on networks to not harm your computer. Only selected incoming connections are accepted.</description>
  <service name="dhcpv6-client"/>
  <service name="http"/>
  <service name="ssh"/>
  <service name="https"/>
  <port protocol="tcp" port="27017"/>
</zone>
``` 

> IP white listing(대역 허용)은 이미 상단에 다 되어 있기 때문에 이정도만 세팅한다.

# package update

KT의 경우 커널은 업데이트 하지말라고 해서 추가..(18.8.9)

처음 시작할 때는 커널업데이트가 되는데 예전 것을 업데이트하면 망하는 것 같다.

```bash
$ yum update --exclude=kernel* -y
```

# hard disk add

번들로 들어 있는 80기가는 테스트디비 저장용으로 사용

```bash
$ fdisk -l
$ fdisk /dev/xvdb

# partition set
n enter
p enter
enter
enter
enter
ctrl+c

# mount
$ mkfs.ext4 /dev/xvdb
$ mkdir /data
$ chmod 777 /data
$ mount -t ext4 /dev/xvdb /data

# regist mount

$ ls -l /dev/disk/by-uuid #uuid copy

# fstab modify
$ vi /etc/fstab
# i 하단에 추가
UUID=copieduuid /data                   ext4    defaults        1 2
# :wq 저장후 나감
```

# node.js install

```bash
$ curl --silent --location https://rpm.nodesource.com/setup_9.x | sudo bash -
$ yum -y install nodejs
$ node -v
v9.9.0
$ npm -v
5.6.0
```

# mongoDB install

## repo make

```bash
$ vi /etc/yum.repos.d/mongodb-org-3.6.repo
```

```yaml
[mongodb-org-3.6]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.6/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.6.asc
```

## mongodb-org install

```bash
$ yum install -y mongodb-org
```

## storage make

```bash
$ mkdir /data/mongo
$ chown mongod:mongod mongo
```

## mongod.conf file change

```bash
$ vi /etc/mongod.conf
```

```yaml
dbPath: /data/mongo #/var/lib/mongo
bindIp: 0.0.0.0 # 127.0.0.1
```

## service start

```bash
$ service mongod start
Redirecting to /bin/systemctl start  mongod.service
```

## disable-transparent-hugepages

```bash
$ vi /etc/init.d/disable-transparent-hugepages
```

```yaml
#!/bin/bash
### BEGIN INIT INFO
# Provides:          disable-transparent-hugepages
# Required-Start:    $local_fs
# Required-Stop:
# X-Start-Before:    mongod mongodb-mms-automation-agent
# Default-Start:     2 3 4 5
# Default-Stop:      0 1 6
# Short-Description: Disable Linux transparent huge pages
# Description:       Disable Linux transparent huge pages, to improve
#                    database performance.
### END INIT INFO

case $1 in
  start)
    if [ -d /sys/kernel/mm/transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/transparent_hugepage
    elif [ -d /sys/kernel/mm/redhat_transparent_hugepage ]; then
      thp_path=/sys/kernel/mm/redhat_transparent_hugepage
    else
      return 0
    fi

    echo 'never' > ${thp_path}/enabled
    echo 'never' > ${thp_path}/defrag

    re='^[0-1]+$'
    if [[ $(cat ${thp_path}/khugepaged/defrag) =~ $re ]]
    then
      # RHEL 7
      echo 0  > ${thp_path}/khugepaged/defrag
    else
      # RHEL 6
      echo 'no' > ${thp_path}/khugepaged/defrag
    fi

    unset re
    unset thp_path
    ;;
esac
```

```bash
$ chmod 755 /etc/init.d/disable-transparent-hugepages
$ chkconfig --add disable-transparent-hugepages
$ reboot
```

## security

### admin account setting
 
```bash
$ mongo
> use admin
> db.createUser(
    {
        user: "adminid",
        pwd: "password",
        roles: [ { role: "userAdminAnyDatabase", db: "admin" } ]
    }
  );
Successfully added user: {
	"user" : "adminid",
	"roles" : [
		{
			"role" : "userAdminAnyDatabase",
			"db" : "admin"
		}
	]
}
# ctrl+c
> exit
$ service mongod restart
```

### db account setting

```bash
$ mongo
> use dbname
> db.createUser(
    {
        user: "dbid",
        pwd: "dbpassword",
        roles: [ { role: "readWrite", db: "dbname" }, { role: "dbAdmin", db: "dbname" } ]
    }
  );
# ctrl+c
Successfully added user: {
	"user" : "dbid",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "dbname"
		},
		{
		    "role" : "dbAdmin",
		    "db" : "dbname"
		}
	]
}
> exit
```

### mongod.conf file change for security

```bash
$ vi /etc/mongod.conf
```

**add security**  
```yaml
security:
  authorization: enabled
```

```bash
$ service mongod restart
```

### test

```bash
$ mongo -u "adminid" -p --authenticationDatabase "admin"
MongoDB shell version v3.6.3
Enter password:
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.3
> show dbs;
admin   0.000GB
config  0.000GB
local   0.000GB
```

# git ssh regist

## github.com > personal setting
![git setting](/images/server/2018-03-22 11-36-14 git ssh.png)

## generate and copy key
```bash
$ ssh-keygen -t rsa -b 4096 -C "aaa@bbb.com"

# copy key
$ cat ~/.ssh/id_rsa.pub
```

## github.com > personal setting > new key button
![git new key](/images/server/2018-03-22 11-51-43 git ssh key.png)

## regist
```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (111.222.111.222)' can't be established.
RSA key fingerprint is xx:xx:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,111.222.111.222' (RSA) to the list of known hosts.
Hi aaa! You've successfully authenticated, but GitHub does not provide shell access.
```

## git clone

web source 가 있을 곳 위치에 복사

```bash
$ mkdir /var/www
$ cd /var/www
$ git clone git@github.com:fkkmemi/projectname.git
```

# client ssh connect

서버접속시 키 복사(암호를 입력하지 않기 위함)

```bash
$ ssh-copy-id account@serverurl -p12345
```

# git update

기본 깔려있는 git v1은 pm2 deploy 문제가 있음

```bash
$ yum install http://opensource.wandisco.com/centos/7/git/x86_64/wandisco-git-release-7-2.noarch.rpm
$ yum install git
$ git --version
git version 2.14.1
```

# yarn install

package install update

```bash
$ curl --silent --location https://dl.yarnpkg.com/rpm/yarn.repo | sudo tee /etc/yum.repos.d/yarn.repo
$ yum install yarn
```

# pm2 install

node run

```bash
$ npm i pm2 -g
$ pm2 install pm2-logrotate
$ pm2 startup

# after run
$ pm2 save

# log retain 7day
$ pm2 set pm2-logrotate:retain 7
```