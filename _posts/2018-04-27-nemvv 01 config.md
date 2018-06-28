---
layout: single
title: NEMVV 1 설정파일
category: nemvv
tag: [nemvv,config]
comments: true
sidebar:
  nav: "nemvv"
---

설정파일에 대해 알아봅니다.

소스는 [https://github.com/fkkmemi/nemvv.git](https://github.com/fkkmemi/nemvv.git)에서 확인 할 수 있습니다.

{% include toc %}

# 설정파일 작성

깃허브에는 소스코드만 들어갑니다.

하지만 비밀스러운 부분을 공개할 수는 없죠..

설정파일이 필요한 이유는

- 민감정보(디비 패스워드, 이메일 송신 계정등)
- 같은 소스로 다른위치에서 사용할 경우(다른 디비, 다른 계정)

그래서 깃헙 관리대상은 아니면서 구동시 필요한 파일을 만들어야합니다.

## ./cfg/cfg.js

```javascript
module.exports = {
  db: {
    url : "mongodb://id:password@xxx.com:27017/nemvv"
  },
  web: {
    host: 'localhost:3000',
    cors: true,
    secret_key : 'VeryUltraKey',
    http : {
      use : true,
      port : 3000,
      redirect : false
    },
    https : {
      use : true,
      port : 3001,
      key : './cfg/key/xxx.com.key',
      cert : './cfg/key/7d205546d3cd579e.crt',
      ca : './cfg/key/gd_bundle-g2-g1.crt'
    },
    jwt: {
      expiresIn: '7d',
      reTokenTime: 60*60*1,// 60*10,
    },
  },
  pm2: {
    deploy: {
      production: {
        user: 'root',
        host : [
          {
            host: 'xxx.com',
            port: '20002',
          }
        ],
        ref  : 'origin/master',
        repo : 'git@github.com:fkkmemi/nemvv.git',
        path : '/var/www/nemvv',
        'post-deploy' : 'yarn && cd fe && yarn && npm run build',
      },
      dev : {
        user: 'root',
        host : [
          {
            host: 'xxx.com',
            port: '20002',
          }
        ],
        ref  : 'origin/master',
        repo : 'git@github.com:fkkmemi/nemvv.git',
        path : '/var/www/nemvv',
        'post-deploy' : 'yarn && cd fe && yarn && npm run build && cd .. && pm2 startOrRestart ecosystem.config.js',
      }
    },
  },
  mail: {
    host: 'smtp.xxx.com',
    port: 25,
    secure: false,
    auth: {
      user: 'admin@xxx.com',
      pass: 'emailpwd',
    },
  },
  dev : {
    log : true,
  },
};

```

- db: mongoDB로 readWrite 할수 있는 url을 넣으시면 됩니다.
- web
    - host: 토큰 발행시 쓰입니다. 추후 멀티서버(scale-out)를 운용할때 구분할때도 쓰입니다.
    - cors: 로컬 테스트를 위해 필요합니다. 실제서버에서는 false 로컬서버에서는 true 입니다.
    - secret_key: 토큰 발행시와 사용자 암호등을 암호화할때 쓰입니다..
    - http
        - use: false일 경우 https만 사용하는 것입니다.
        - port: 3000은 로컬용이고 실제서버에서는 80으로 서비스합니다.
        - redirect: http로 들어올경우 https로 전환해주기 위함입니다.
    - https
        - use
        - port: 3001은 로컬용이고 실제서버에서는 433입니다.
        - key, cert, ca: ssl사용을 위한 인증서 위치입니다. 자세한 것은 [ssl setting](/server/ssl-server-setting/) 에서 확인해보면됩니다.
    - jwt
        - expiresIn: '7d' 7일 유효기간의 토큰 발행
        - reTokenTime: 유효가 임박했을 때 토큰 재 발행해주는 시간입니다.
- pm2: 배포 관련 설정입니다. 자세한 것은 [pm2 deploy](/nembv/nembv-21-deploy-web/) 에서 확인해보면됩니다.
- mail: 메일 전송기능 입니다. 회원 인증에서 쓰입니다.

# 결론

설정파일에는 최대한 필요한 것만 있어야합니다.

이것저것 넣을 수록 유지보수가 어려워지는 플랫폼이 됩니다.