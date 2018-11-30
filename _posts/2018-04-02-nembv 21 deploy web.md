---
layout: single
title: NEMBV 21 운영과 배포 방법
category: nembv
tag: [nembv,pm2]
comments: true
sidebar:
  nav: "nembv"
---

> 이 강좌는 종료되었습니다.  
새로운 강좌로 시작하세요~  
[모던웹(NEMV) 제작 강좌](/nemv/){: .btn .btn--success}  

지금까지 개발환경에서만 테스트를 했다. 실제 서비스를 해야할 곳은 어딘가의 서버인데..

어떻게 운영하고 배포하는 알아보겠다..

{% include toc %}

# 개요: 운영

지금까지 npm run start라는 package.json에 등록되어 있는 스크립트로 동작되게 하였다.

별 문제 없이 동작하지만 에러가 난다면 어떻게 될까?

어떤 api를 잘못 설계했을 때도 있다. 실수로 api 안 코드를 아래처럼 예외처리를 안 넣었다면

**routes/api/test/hello/index.js**  
```javascript
const foo = (a, b) => {
  return a + b + c; // c 없음
}

foo(1,2);
```

당연한 절차이긴 하지만 다른 페이지는 운영에 전혀 문제가 없기 때문에 리셋하면서 시간을 벌고 코드를 수정하면 되지만

바로 서버는 꺼져버리고 에러가 나는 것을 볼 수 있다.

그래서 단순 node run보다는 node를 관리해주는 앱(프로세스 매니져)을 사용하는 것이 좋다.

어떤 것들이 있는지는 의외로 [Process managers for Express apps](http://expressjs.com/en/advanced/pm.html) 에 설명이 있다. *생각보다 친절해서 의외임*

종류는 아래 3가지를 예를 들고 있는데 써본 결과는 

- strongloop: 어려웠음, 무거움
- pm2: 쉽고 간편, 선택함
- forever: 쉬운데 너무 라이트함

각종 리뷰, 난이도, 배포등의 문제로 pm2를 선택하게 되었다.

# pm2 기본

공식사이트([http://pm2.keymetrics.io](http://pm2.keymetrics.io))에서 확인 하면 다 알 수 있지만... 

실제 서버를 운영하면서 어떻게 쓰는지 작성해보겠다.

> node에 최적화 되어 있지만 node 만을 위한 것이 아니다. 다른 데몬을 관리할 때도 쓰일 것이다.

## 설치

타겟서버, 개발서버 모두 설치한다. 개발서버에서 테스트후 배포 시나리오 까지 고려하기 때문.

```bash
$ npm i pm2 -g
```

## 기본 사용

우선 아주 간단한 사용법 부터 작성해본다.

### 실행

해당 js파일을 pm2로 돌려준다. 이제 문제가 생기면 리스타트된다.

```bash
$ pm2 start bin/www
```

### 상태 확인

pm2는 여러개의 실행을 할 수 있는데. 각각 실행 후 상태를 확인할 수 있다.
 
```bash
$ pm2 status
```

eg)  
- 실제 구동할 80 서버
- 개발용 3000 서버 

### 로그 확인

기존 로그(npm start or node bin/www)는 아래 명령어를 입력하면 각 요청 및 사용자 로그가 표시 된다.

```bash
$ pm2 log
```

### 모니터

현재 활성중인 앱들의 cpu, ram등의 상태등을 보여준다.

```bash
$ pm2 monit
```

여기까지만 해도 리셋 안되는 구동 서버가 되었다.

단 시작하자마자 리셋이 걸리는 경우 무한 리셋에 안걸리도록 10회정도 리트라이하고 종료된다.

## 응용 사용

개발용도가 아닌 서비스운영을 하려면 좀더 고민해야할 것들이 있다 아래로 진행해보자. 

### 로그 처리

로그는 기본적으로 ~/.pm2/logs 에 저장된다.

별다른 설정이 없다면 파일 하나에 계속 어펜드되서 1년 후에는 몇백기가의 혐오스러운 로그파일 한덩이를 만나게 된다.

이를 방지하기 위해 용량별 혹은 일자별로 파일을 끊어주는 애드온 기능을 설치해준다.

```bash
$ pm2 install pm2-logrotate 
```

> 기본값으로 구동시켜놨는데 일단위로 파일 끊으면서.. 잘하고 있다. 설치하고 나면 pm2 status 목록에 뜬다.  
나의 경우 꼭 필요한 경우가 아니라면 디폴트의 미학(설정)을 지향하는데 그것은 사이드 이펙트 + 관리요소 증가 때문이다.  
혹시 사이즈별이나 다른 방법은 [http://pm2.keymetrics.io/docs/usage/log-management/](http://pm2.keymetrics.io/docs/usage/log-management/)을 참고하여 바꾸면 된다.  

### 시작프로그램 등록

서버는 늘 켜있어서 리셋할 때가 별로 없지만. 그래도 켜지면 바로 작동 되게 해놓는 것이 유지보수 측면에서 당연 이득이다.

아래 링크에 각 os별 방법이 있다

[http://pm2.keymetrics.io/docs/usage/startup/](http://pm2.keymetrics.io/docs/usage/startup/)

일반적으로 아래 명령어로 자동으로 디텍해서 설정해주지만

```bash
$ pm2 startup
```

만약 자동으로 안되면 위 링크대로 아래 와 같이 해당 os를 입력해서 실행하면 된다.

```bash
$ pm2 startup centos 
```

> centos7의 경우 위와 같이 했다.

그런데 뭘 시작할 것인가를 고민해야한다.

스타트업 설정 후 아니면 전 꼭 해야 되는 일은 마지막 상태 저장이다

```bash
$ pm2 save
```

pm2로 여러가지 데몬을 구동한 상태를 저장해두면 리붓후에도 상태를 유지해준다.

### 설정 파일로 시작

pm2 기능중 watch는 특정 화일들을 지정해서 프로그램이 수정될 경우 리셋을 걸어주는 용도다.

반대로 ignore_watch는 반대의 의미며 무시한다.

이런 기능을 같이 넣으려면 실행시 아래와 같이 넣을 수도 있지만 역시 불편하다.

```bash
$ pm2 start xxx.js --watch "bin/www" --ignore_watch "어쩌구저쩌구, "
```

그래서 아래와 같이 json 파일을 만들어 두고 하는 것이 좋다.

**cfg/pm2.json**  
```json
{
  "name": "nembv",
  "watch": ["server", "client"],
  "ignore_watch" : ["node_modules", "client/img"],
  "watch_options": {
    "followSymlinks": false
  }
}
```

```bash
$ pm2 start cfg/pm2.json
```

한쪽에 pm2 log를 걸어두고 다른 한쪽에서 소스 변경을 해보면 리셋되는 것을 눈으로 볼 수 있다.

하지만 위의 방법은 쓰지 않는다. 그 이유는 이제 설명할 배포와 같이 설명하도록 하겠다. 

# pm2 배포

쉽게 말해 보통 배포는 deploy라고 부르며 그 과정을 에코시스템이라고 한다.

아래의 방밥은 git과 ssh를 이용한 배포이기 때문에 참고용이 아닌 경우 다음 챕터로 넘어가자.

가상 시나리오를 통해 어떻게 배포할지 알아보자.

## 계획

우선 내가 배포하고 싶은 시나리오는 이렇다.

예를들어 로드밸런서는 a server, b server 두군대로 이어져있다.

> 쉽게 말해 로드밸런서의 도메인이 xxx.com 이면 xxx.com 접속시 a or b server로 요청하게 된다.  
결국 a, b는 같은 디비와 같은 내용의 소스가 돌아가면 사용자는 같은 화면을 볼 수 있다. 

현재는 각 서버에 접속해서 소스가 저장된 위치에서 git pull을 하는데 두 서버에 접속해서는 할만했는데 트래픽 증가로 c,d,e, 서버가 증설되면서 힘들어졌다.

**이제 개발 노트북에서 커맨드 하나로 5대의 서버를 최종버전으로 변경 하고 싶다**

## 준비

pm2 deploy([http://pm2.keymetrics.io/docs/usage/deployment/](http://pm2.keymetrics.io/docs/usage/deployment/)) 를 이용해 명령을 전달하면 해결이 되는데..

ssh와 github(그 외 git hosting) 설정이 되어 있어야한다.

### ssh

타겟 서버로 ssh는 암호 입력 없이 들어 갈 수 있어야한다.

```bash
$ ssh-copy-id account@serverurl -p12345
```

한번 해두면 이제부터는 

```bash
$ ssh account@serverurl -p12345
```

패스워드 없이 들어갈 수 있다.

> 대부분 이렇게 할 것이겠지만.. 리눅스 초짜라 난 몇년을 암호를 입력하고 들어갔다.

### github

타겟 서버가 ssh로 깃헙에 연결되어야 한다.(*패스워드 없이 다운로드*)

#### generate and copy key
```bash
$ ssh-keygen -t rsa -b 4096 -C "aaa@bbb.com"

# copy key
$ cat ~/.ssh/id_rsa.pub
```

해당 서버에서 깃헙에 등록하기 위한 키를 생성해야한다.

#### github.com > personal setting

![git setting](/images/server/2018-03-22 11-36-14 git ssh.png)

깃헙에서 personal setting에 들어가서 **new ssh key**를 누른다.


#### github.com > personal setting > new key button

![git new key](/images/server/2018-03-22 11-51-43 git ssh key.png)

복사한 키를 넣어준다.

#### regist

```bash
$ ssh -T git@github.com
The authenticity of host 'github.com (111.222.111.222)' can't be established.
RSA key fingerprint is xx:xx:xx.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,111.222.111.222' (RSA) to the list of known hosts.
Hi aaa! You've successfully authenticated, but GitHub does not provide shell access.
```

해당 서버에서 위 명령어를 넣으면 이제부터 인증 된 서버이기 때문에 ssh로 접속이 된다.

결국 클라이언트 -> 타겟서버 ssh -> git hub 까지의 과정이 전부 인증이 되어있고 패스워드가 필요 없는 상태로 만들어야 된다는 것이다.

## 배포설정

### cfg/cfg.js

```bash
pm2: {
  user: 'root',
  host: 'xxx.com',
  port: '12345',
  path: '/var/www/nembv',
},
``` 

타겟서버에 접속할 계정 정보와 호스트 그리고 저장될 위치를 적어둔다.

### ecosystem.config.js

```javascript
const cfg = require('./cfg/cfg');

module.exports = {
  /**
   * Application configuration section
   * http://pm2.keymetrics.io/docs/usage/application-declaration/
   */
  apps : [

    // First application
    {
      name      : 'nembv',
      script    : 'bin/www',
      // watch: ["bin","routes","views","system"],
      ignore_watch : ["node_modules","cfg"],
      watch_options: {
          "followSymlinks": false
      },
      max_memory_restart : "2G",
      env: {
        COMMON_VARIABLE: 'true'
      },
      env_production : {
        NODE_ENV: 'production'
      }
    }
  ],

  /**
   * Deployment section
   * http://pm2.keymetrics.io/docs/usage/deployment/
   */
  deploy : {
    production : {
      user : cfg.pm2.user,
      host : [
          {
              "host": cfg.pm2.host,
              "port": cfg.pm2.port,
          }
        ],
      ref  : 'origin/master',
      repo : 'git@github.com:fkkmemi/nembv.git',
      path : cfg.pm2.path,
        // 'post-deploy' : 'npm install && pm2 startOrRestart ecosystem.config.js --env production'
      'post-deploy' : 'npm install && cd fe && npm install && npm run build --env production'
    },
    dev : {
      user : cfg.pm2.user,
      host : [
        {
          "host": cfg.pm2.host,
          "port": cfg.pm2.port,
        }
      ],
      ref  : 'origin/master',
      repo : 'git@github.com:fkkmemi/nembv.git',
      path : cfg.pm2.path,
      'post-deploy' : 'npm install && cd fe && npm install && npm run build && cd .. && pm2 startOrRestart ecosystem.config.js --env dev',
      env  : {
        NODE_ENV: 'dev'
      }
    }
  }
};
```

- 위에서 언급했던 pm2.json과 같은데 아래 deploy가 추가 된것이다.
- apps
    - 앱은 여러개를 넣을 수도 있다.
    - nembv라는 네임을 주었기 때문에 pm2 status를 확인해보면 해당 명칭이 사용된다.
    - watch는 꺼두었다. *어짜피 배포후에 다시 시작하기 때문, ignore_watch 또한 의미가 없다.*
    - max_memory_restart: 2기가 이상 메모리를 사용하면 리셋된다. *2기가 이상 사용하면 뭔가 꼬인것이다*
    - 그밖에 env 관련은 사용하진 않지만 대략 설치, 배포 등을 다르게 할 때 쓰인다.
- deploy:
    - production: 이름은 정하기 나름이지만 의도로는 비어있는 타겟서버에 소스를 밀어넣고 의존요소 설치 목적
    - dev: 설치 후 재시작(*명칭은 사실 build가 적당하다*)
    - host: 현재 한대만 넣어두었지만. 어레이 안에 a,b,c,d,e 서버를 넣어주면 된다. production용과 dev용 호스트들을 달리 두면 된다.
    - repo: 깃 저장소 위치 by ssh
    - path: 타겟서버 저장소
    - post-deploy: 깃에서 내려받은 이후 할 행동
    
> aws(amazon web service) ec2등이라면 키를 같이 넣어줘야한다. 위 링크에 그 방법 또한 나오니 참고하면 된다.

### pm2 deploy

이제 위의 파일을 이용해 실제 배포를 해본다.

개발자 데스크톱에서 하는 것이다.

원리는

- pm2 deploy xxx
- 등록되어 있는 서버에 접속
- 정해진 경로에 다운로드 혹은 갱신(git clone or pull)
- 경로는 예시대로라면 /var/www/nembv/source 가 됨
- post-deploy가 실행됨
    - npm install: 백엔드 의존요소 설치
    - cf fe && npm install: 프론트엔드 의존요소 설치
    - npm run build: 프론트엔드 webpack으로 빌드함
    - cd .. && pm2 startOrRestart ecosystem.config.js: 실행되어 있지 않다면 start 실행되어있다면 restart하여 위에 써진 apps 설정대로 실행
    
최근 깃헙으로 푸쉬된 사항이 없으면 동작하지 않는다.
 
#### 설치용 배포

```bash
$ pm2 deploy production
```

최 상단에 ecosystem.config.js로 정의 했기 때문에 파일 이름이 생략이 된것이다.

다른 파일 로 관리하고 싶다면

```bash
$ pm2 deploy xxx.json production
```

과 같이 해야한다.

등록된 각 서버에 소스들이 있는 것을 확인 할 수 있다.

여러대일 경우 동시 진행이 아닌 한대 끝나고 다른 한대라 대수 만큼 시간이 증가한다. 

처음엔 잘 되었었는데 언젠가부터 해보니 에러가 났다.(*원인을 찾는 중*)

대처법은 찾는 중이지만.. 어짜피 처음 한번 설치이므로 해당 서버가서 한번의 수고를 해준다 ...

pm2는 원래 정상적으로 처리되었다면 /www/nembv/source 로 위치하게 된다. 해당 서버에 가서 그렇게 만들어 주면된다..

```bash
# 만약 /www/nembv 로 하고 싶다면..
$ cd /www
$ mkdir nembv
$ cd nembv
$ git clone git@github.com:fkkmemi/nembv.git # 본인 아이디/저장소 다 끝나고나면 /www/nembv/nembv
$ mv nembv source # 정리 /www/nembv/source
```

#### 개발용 or 빌드 배포

진행 과정을 확인하기 위해 해당 서버들에 접속하여 pm2 log를 켜두는 것이 좋겠다.

```bash
$ pm2 deploy dev
```

이렇게만 해주면 이제 알아서 추가된 의존요소도 설치하고 서버도 잘 재시작하는 것을 볼 수 있다.

최대한 개발사이드에서 열심히 디버깅하고 실행하다가 이제 확실하다 싶을 때 날리면 된다.

> 깃 푸쉬 없이 하려면 뒤에 --force를 붙여주면 된다. 

# 실사용기 예

현재 사이트를 운영할때는 각각의 멤버를 따로 만들어 배포하고 있다.

- 수신부 3대: pm2 deploy rcv
- 웹서버 2대: pm2 deploy web
- 계산서버 2대: pm2 deploy calc
- 개발서버들 각: pm2 deploy lab, work, test 등

# 의견

배포를 하는 방법은 다양하다. 전용 배포툴을 이용해도 되고, 과거에는 ftp를 사용해서 직접 수정하기도 했다.

여기 작성한 글도 최선의 방법이 아닐 수 있으니 참고용으로 사용하길 바란다.

# 결론

여기까지 하느라 정말 귀찮았지만. 개발 시간이 훨씬 많이 남게 되었다.

3:3:3:3 보다는 6:3:2:1 로 작업하길 원한다.

그말은 매번 3시간 걸릴일을 초기 투자 시간이 필요하더라도 6시간을 투자해서 시간을 더 세이브하길 희망하는 것이다.

배포의 문제가 그런 것 같다.

은근히 시간 걸리지만은 왠지 개선은 귀찮은..

귀찮은 상태에서 했지만 오랜시간 사용하며 결국 초기 투자 비용을 훨씬 더 벌었다.

당연히 이보다 더 나은 솔루션을 발견하고 적용 가능하다 느낀다면 **개발자라면** 언제든 그 길을 떠날 준비가 되어 있어야 되지 않을까?
