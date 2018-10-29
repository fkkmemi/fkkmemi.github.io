---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 37 몽고 클라우드(ATLAS) 가입 및 테스트
category: nemv
tag: [nemv,mongodb,atlas,cloud,node]
comments: true
sidebar:
  nav: "nemv3"
---

몽고디비 클라우드 서비스인 아틀라스를 가입 후 테스트 까지 해봅니다.

{% include toc %}

# 개요 

몽고 아틀라스는 몽고에서 운영하는 클라우드 서비스입니다.

아틀라스의 실제 서버는 AWS를 이용합니다.

몽고디비 서버를 직접구성하는 것도 좋지만([몽고디비 샤드 구성](/mongodb/mongo-sharding-1/)), 나중에 점점 힘들어지므로 혼자 풀스택으로 진행하려면 클라우드 서비스를 이용하는 것이 좋습니다.

참고: [데이터베이스 사용 및 팁](/server/atlas-use/)

# 아틀라스 가입

## 회원 가입

![alt atlas signup](/images/nemv/스크린샷 2018-10-29 오후 2.55.09.png)

[https://cloud.mongodb.com](https://cloud.mongodb.com) 에 들어가서 signup을 눌러서 회원 가입을 합니다.

![alt atlas regist](/images/nemv/스크린샷 2018-10-29 오후 3.01.54.png)

가입 정보를 기입후 Continue를 누릅니다.

## 서버 생성

![alt atlas server](/images/nemv/스크린샷 2018-10-29 오후 3.04.58.png)

사용할 서버 위치를 지정합니다. 

![alt atlas m0](/images/nemv/스크린샷 2018-10-29 오후 3.06.21.png)

M0를 선택하면 무료로 사용가능합니다.

![alt atlas timezone](/images/nemv/스크린샷 2018-10-29 오후 3.08.02.png)

3분정도 기다리면서 시간대를 설정하고 나면 서버가 준비됩니다.

# 서버 접속

## 서버 접속할 IP 허용

아틀라스 서버가 생성되었습니다.

![alt atlas created](/images/nemv/스크린샷 2018-10-29 오후 3.09.20.png)

CONNECT를 눌러서 연결설정을 합니다.

![alt atlas add ip](/images/nemv/스크린샷 2018-10-29 오후 3.11.02.png)
![alt atlas add ip2](/images/nemv/스크린샷 2018-10-29 오후 3.12.14.png)

Add Your Current IP Address 를 눌러서 현재 접속된 아이피를 허용시킵니다.

## 서버 접속할 계정 생성

![alt atlas db user](/images/nemv/스크린샷 2018-10-29 오후 3.13.05.png)

데이터베이스들을 관리할 계정과 비밀번호를 생성합니다.

## 서버 접속 문자열 얻어오기

![alt atlas connect app](/images/nemv/스크린샷 2018-10-29 오후 3.14.37.png)

Connect Your Application 을 눌러서 접속 문자열을 확인합니다.

![alt atlas connect string](/images/nemv/스크린샷 2018-10-29 오후 3.15.54.png)

3.6+ driver를 클릭하고 접속 문자열을 복사합니다.

> 3.6 부터 + 접속으로 간단하게 접속이 됩니다.  
그 이전에는 샤드3개에 접속하는 복잡한 접속 문자열이였습니다.

## 실제 모던웹 소스에서 접속하기

**be/app.js**  
```javascript
// mongoose.connect('mongodb://localhost:27017/nemv', { useNewUrlParser: true }, (err) => {
mongoose.connect('mongodb+srv://nemv:password@cluster0-4ugnm.mongodb.net/nemv', { useNewUrlParser: true }, (err) => {
```

기존 소스에서 접속 주소를 위와 같이 변경하고 password부분을 설정했던 비밀번호로 변경합니다.

```bash
$ yarn dev
```

지난 번 만들어 둔 yarn dev를 이용해서 접속이 잘 되는지 확인해봅니다.

콘솔에 mongoose connected! 가 뜨면 성공입니다.

## 브라우저에서 임의 값 입력해보기

![alt nemv front input](/images/nemv/스크린샷 2018-10-29 오후 3.24.42.png)

http://localhost:8080/user에 들어가서 아무 값이나 입력해봅니다.

## 아틀라스에서 확인해보기

![alt atlas confirm](/images/nemv/스크린샷 2018-10-29 오후 3.26.35.png)

아틀라스에도 값이 잘 저장되어 있음을 확인할 수 있습니다.

# 마치며

무료 티어인 M0의 성능은 최악입니다.

하지만 최악이라서 비동기식 프로그램을 테스트하기에 매우 적절합니다.

그 얘기인 즉슨 매우 빠른 디비일 경우 쓰고 읽는 것이 순식간에 일어나서 쓴 후 콜백을 안받고 읽어도 바로 값을 읽을 수도 있지만..

M0는 매우 느리기 때문에 쓴 후(1초이상 걸릴 수 있음...) 콜백을 받지 않고 읽어버리면 당연히 오류가 납니다.

읽을 시에도 1,2초 걸리는 딜레이가 늘 있기 때문에 그 딜레이 동안 너무 심심해서 회전 로딩바를 넣고 싶은 생각이 생기게 됩니다.   

# 영상

{% include video id="4RZrCiHo7_I" provider="youtube" %}   




