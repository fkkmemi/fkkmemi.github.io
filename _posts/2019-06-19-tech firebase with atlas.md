---
layout: single
title: Firebase functions에서 mongodb atlas에 접속하기
category: tech
tag: [talk,idea,firebase,functions,mongodb,atlas]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

Firebase functions에서 mongodb atlas에 접속하는 방법을 다룹니다.

# 개요

Mongodb atlas에서 whitelist에 0.0.0.0/0 추가하면 아무나 접속할 수 있으므로 쉽게 끝날 수 있습니다.

공용디비가 되서 아무나 접속하기 때문에 아이디 암호만 알아내면 다 털릴 수 있습니다.

**다 털린 공용 데이터베이스** 
![alt bitcoin](/images/mongo_sharding/5.png)

그래서 whitelist에 인증된 서버들 ip주소만 등록해서 사용해야합니다.

문제는 firebase functions는 ip주소가 동적이라 계속 바뀌는 문제가 있기 때문입니다.

# firebase functions ip 확인

아이피를 알아 보기위해 간단한 코드를 작성해봅니다.

**functions/index.js**  
```javascript
const cors = require('cors')
const axios = require('axios')

app.use(cors({ origin: true }))
app.get('/', (req, res) => {
  axios.get('http://httpbin.org/ip')
    .then(r => {
      res.send(r.data)
    })
    .catch(e => res.send(e.message))
})
exports.ip = functions.https.onRequest(app)
```

ip를 알아낼 수 있는 public api를 제공하는 곳은 http://httpbin.org/ip이 안되면 아무곳이나 찾으면됩니다.

```bash
$ firebase deploy --only functions
```

배포하고 나서 포스트맨으로 결과를 받아봅니다.

![alt ip result](/images/tech/2019-06-19_10.56.54.png)

의문의 ip를 확인할 수 있습니다.

코드를 조금 수정하고 다시 배포하면 다른 ip로 변경되는 것을 확인할 수 있습니다.

# gcp ip 얻기

firebase는 결국 gcp(Google Cloud Platform)에서 동작하는 것입니다.

gcp에서 운영중인 아이피 대역을 whitelist에 추가하면 되는 것입니다.

참고:  
- [공식문서](https://cloud.google.com/compute/docs/faq#find_ip_range)
- [배치파일 스니펫](https://gist.github.com/n0531m/f3714f6ad6ef738a3b0a)

**gcpip.sh**  
```bash
#!/bin/bash 

# https://cloud.google.com/compute/docs/faq#find_ip_range
# nslookup -q=TXT _cloud-netblocks.googleusercontent.com  8.8.8.8

myarray=()
for LINE in `dig txt _cloud-netblocks.googleusercontent.com +short | tr " " "\n" | grep include | cut -f 2 -d :`
do
	myarray+=($LINE)
	for LINE2 in `dig txt $LINE +short | tr " " "\n" | grep include | cut -f 2 -d :`
	do
		myarray+=($LINE2)
	done
done 

for LINE in ${myarray[@]}
do
	dig txt $LINE +short | tr " " "\n" 
done | grep ip4 | sort -n | cut -f 2 -d :

# changing target to _spf.google.com, you can get a simliar range now for Google Apps mail servers.
# https://support.google.com/a/answer/60764

# changing it to _netblocks.google.com will help get all the ip ranges google uses for its services.
```

bash 파일을 참고에 따라서 만들고 실행해봅니다.

```bash
$ sh gcpip.sh
```

**result**  
```text
104.154.0.0/15
104.196.0.0/14
107.167.160.0/19
107.178.192.0/18
# ... 매우 많이 나옴
```

> 참고로 104.154.0.0/24 의 경우 104.154.0.0~255 까지 사용이 가능한겁니다.  
> 24비트만 fix된 것이죠. 104.154.0.N (8bit * 3 = 24) 나머지는 자유..  
> 그런데 15비트가 fix되고 나머지는 자유니 엄청난 범위의 아이피를 허용하는 것이죠.

# mongodb atlas REST API

저 많은 걸 한땀 한땀 다 입력하는 것은 힘들 것 같습니다.

mongodb atlas에서 제공하는 REST API를 이용하면 다양한 설정을 할 수 있습니다.

설정들 중 whitelist를 등록해봅니다.

## 키 생성 및 접근 허용

참고: [https://docs.atlas.mongodb.com/configure-api-access/](https://docs.atlas.mongodb.com/configure-api-access/)

mongodb atlas REST API를 아무데서나 접근한 다는 것은 정말 위험한 것입니다.

모든 설정을 다 할 수 있기 때문입니다.

그래서 공용키와 아이피 제한을 두고 접속하게 되어 있습니다.

![alt atlas org](/images/tech/2019-06-19_11.17.00.png)

1. 좌측 상단에 계정 클릭 후 Organiztions 클릭
2. Public access api 클릭
3. API key generate
4. public key copy
5. whitelist Add: 현재 REST API를 사용할 머신 IP

## group_id 확인하기

요청 parameter 중요한 요소는 group_id 입니다. 

결국 project id 인데 해당 프로젝스 설정에서 확인할 수 있습니다.

![alt project id](/images/tech/2019-06-19_11.42.18.png)

## 등록된 IP 리스트 가져오기

mongodb atlas REST API는 특이한 인증을 사용합니다.

Digest auth 라는 방법입니다.

참고: [https://docs.atlas.mongodb.com/reference/api/whitelist/](https://docs.atlas.mongodb.com/reference/api/whitelist/)

포스트맨으로 읽어봅니다.

![alt get ip](/images/tech/2019-06-19_11.46.02.png)

REST API가 잘 동작하는 것을 확인했습니다.

# 코드로 추가하기

이제 물리적인 통로가 확인되었으니 앱을 만들어볼 차례입니다.

추가는 post로 하는데 간단한 앱을 만들어서 일괄로 처리해보도록 하겠습니다.

## 프로젝트 생성하기

```bash
$ mkdir atlas-admin && cd $_
$ yarn init # all enter
$ yarn add request request-promise
```

> request를 굳이 쓰는 이유는 digest 인증이 axios에서 지원하지 않기 때문입니다..

**package.json**  
```json
{
  "scripts": {
    "start": "node ."
  }, 
}
```

패키지 파일을 수정해서 yarn start 가 가능하게 만듭니다.

## request로 등록된 IP 리스트 가져오기

**index.js**  
```javascript
const auth = {
  user: 'id',
  pass: 'public key',
  sendImmediately: false
}
const group_id = 'project id'

const getIPs = () => {
  const options = {
    uri: 'https://cloud.mongodb.com/api/atlas/v1.0/groups/' + group_id + '/whitelist' + '?pretty=true',
    auth
  }
  return rp(options)
}

getIPs().then(r => console.log(r)).catch(e => console.error(e.message))
```

```bash
$ yarn start
```

실행해보면 request 모듈이 포스트맨의 역할을 잘 해주고 있는 것을 판단할 수 있습니다.

## gcp ip 대역 가져와서 데이터화 하기

bash 명령어는 노드의 기본 모듈인 child_process로 결과를 가져올 수 있습니다.

참고: [https://nodejs.org/dist/latest-v12.x/docs/api/child_process.html#child_process_child_process_exec_command_options_callback](https://nodejs.org/dist/latest-v12.x/docs/api/child_process.html#child_process_child_process_exec_command_options_callback)

**index.js**  
```javascript
const { exec } = require('child_process')
exec('sh gcpip.sh', (error, stdout, stderr) => {
  if (error) return console.error(error)
  const cidrs = stdout.split('\n')
  cidrs.pop() // last empty string
  cidrs.forEach(v => console.log(`cidr: ${v}`))
  console.log(cidrs)
})
```

위에서 만든 bash file을 프로젝트에 넣어둡니다.

맨 마지막 데이터는 공백이라 지워줍니다.

데이터가 잘 나오는 것을 확인할 수 있습니다.

## 완성하기

이제 모든 재료가 다 모였으니 추가/조립하고 완성합니다.

**index.js**  
```javascript
const rp = require('request-promise')
const { exec } = require('child_process')

const auth = {
  user: 'id',
  pass: 'public key',
  sendImmediately: false
}
const group_id = 'project id'

const getIPs = () => {
  const options = {
    uri: 'https://cloud.mongodb.com/api/atlas/v1.0/groups/' + group_id + '/whitelist' + '?pretty=true',
    auth
  }
  return rp(options)
}

const getIP = (ip) => {
  const options = {
    uri: 'https://cloud.mongodb.com/api/atlas/v1.0/groups/' + group_id + '/whitelist/' + ip + '?pretty=true',
    auth
  }
  return rp(options)
}

const addIP = (body) => {
  const options = {
    uri: 'https://cloud.mongodb.com/api/atlas/v1.0/groups/' + group_id + '/whitelist/?pretty=true',
    auth,
    body,
    json: true,
    method: 'POST'
  }
  return rp(options)
}

const getGcpIPs = () => {
  return new Promise((resolve, reject) => {
    exec('sh gcpip.sh', (error, stdout, stderr) => {
      if (error) return reject(error)
      const cidrs = stdout.split('\n')
      cidrs.pop() // last empty string
      cidrs.forEach(v => console.log(`cidr: ${v}`))
      resolve(cidrs)
    })
  })
}

const gcpIPs2AtlasPush = async () => {
  const cidrs = await getGcpIPs()
  const inputlists = []
  cidrs.forEach(v => {
    inputlists.push({
      cidrBlock: v,
      comment: 'gcpIP'
    })
  })
  const whitelists = await addIP(inputlists)
  return whitelists
}

// getIPs().then(r => console.log(r)).catch(e => console.error(e.message))
// getIP('111.222.112.0%2f24').then(r => console.log(r)).catch(e => console.error(e.message))
// addIP([{ cidrBlock: '111.222.112.0%2f24', comment: 'test' }]).then(r => console.log(r)).catch(e => console.error(e.message))
gcpIPs2AtlasPush()
  .then(r => console.log(r))
  .catch(e => console.error(e.message))
```

편의를 위해 getGcpIPs() 함수를 promise형태로 만들고 addIP() 함수를 포함한 gcpIPs2AtlasPush() 함수를 호출하면 gcp IP가 모두 등록됩니다.

![alt atlas list](/images/tech/2019-06-19_13.21.16.png)

# firebase functions에서 접근 확인

## 코드에 mongodb 접속 코드 추가

**functions/index.js**  
```javascript
const mongoose = require('mongoose')

const dbUrl = 'mongodb+srv://id:pass@cluster.mongodb.net/dbname'
mongoose.connect(dbUrl, {
  useNewUrlParser: true,
  useCreateIndex: true
}, (err) => {
  if (!err) {
    console.log('MongoDB Connection Succeeded.')
  } else {
    console.log('Error in DB connection: ' + err)
  }
})
```

## firebase functions 배포

```bash
$ firebase deploy --only functions
```

## firebase console 확인

![alt ff](/images/tech/2019-06-19_13.27.22.png)

클라우드 콘솔에서 접속이 잘 되고 있는 것을 확인할 수 있습니다.

# 마치며

사실 firebase functions에서 mongodb atlas 접속이 잘 안되길래 무작정 오전에 짬내서 간단하게 만든 것인데..

해놓고 ip를 세어보니 50개 정도 밖에 안된다는 것을 알았습니다.(_한 200개는 되는 줄 알았음.._)

2시간 걸려서 쌩쑈하느니 atlas console에서 그냥 한땀한땀 넣으면 15분이면 끝났을 것입니다..

물론 한번 물리적인 통로(REST API)를 열어 봤으니.. 다른 용도로 사용할 수도 있고..

혹시나 gcp가 ip range 대역을 자주 증설할 경우 동적으로 추가해줄 수도 있겠죠..(_몽고디비 접속이 안될 때 해당 IP 추가_)

포스팅하는 이유는 두가지입니다..

- 비슷한 상황의 다른 개발자분들께 도움이 되고자 하는 것

- 문제가 발생했을 때 어떻게 풀어가야하는 지..
