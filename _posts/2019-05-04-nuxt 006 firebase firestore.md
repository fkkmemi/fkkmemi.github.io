---
layout: single
title: NUXT로 혼자 웹사이트 만들기 6 firebase로 데이터베이스(firestore) 사용하기 
category: nuxt
tag: [nuxt,node,vue,vuetify,plugin,firebase,firestore]
comments: true
sidebar:
  nav: "nuxt1"
toc: true
toc_label: "목차"
toc_icon: "list"
---

파이어베이스의 noSQL 데이터베이스인 firestore를 파이어베이스 호스팅 환경에서 사용해봅니다.

# 개요

파이어베이스는 realtime, firestore 2가지의 데이터베이스를 사용할 수 있습니다.

그 중 firestore를 사용해서 잘 동작하는 지 확인해봅니다.

지난 강좌에서 만든 테스트용 앱에서 구동해봅니다.

> firestore는 몽고디비처럼 noSQL입니다.

# 파이어스토어 가격

항상 문제는 가격입니다.

무료로 이용할 수 있는 스펙.

|무료 등급|	할당량|
| - | - |
|저장된 데이터	|1GiB|
|문서 읽기	|50,000/일|
|문서 쓰기	|20,000/일|
|문서 삭제	|20,000/일|
|네트워크 송신	|10GiB/월|

> 무료로 하루 2만건이면 엄청나지만..  
실수로 for라도 잘못 돌리면 훅 갈 수 있으니 주의..

# firestore 활성화

## 시작하기

![alt firestore](/images/nuxt/2019-05-07_11.47.07.png)

먼저 프로젝트에서 데이터베이스 만들기를 클릭합니다.

## 보안규칙 설정

![alt secret](/images/nuxt/2019-05-07_11.49.30.png)

테스트모드로 설정.

## 생성 확인

![alt fin.](/images/nuxt/2019-05-07_11.51.07.png)

이제 생성된 것을 확인했습니다.

클라이언트에서 써보고 여기서 확인해도 됩니다~

# 파이어베이스 클라이언트 설치

참고: [https://firebase.google.com/docs/firestore/quickstart?hl=ko](https://firebase.google.com/docs/firestore/quickstart?hl=ko)

**중요: 넉스트에 firebase를 설치해야 하는데 여기서 중요한것은 넉스트가 최신버전 firebase 모듈을 지원하지 않습니다**

```bash
$ cd src
$ yarn add firebase # X 5.11이 설치됨 그러나 동작 안됨
$ yarn add firebase@5.5.0 # O
```

구버전 5.5 정도로 설치해줍니다.

> corejs 오류가 나는데 정말 사경을 해메고 왔습니다. 구글링해도 이슈를 찾을 수가 없었습니다...  
공식홈에는 4.X로 설치되어 있는 걸 봐서는.. 구글도 뭔가 도큐 정리가 필요할듯..

# 파이어스토어에 쓰기 읽기

구글 파이어스토어 퀵스타트 문서에 있는 그대로 적용해봤습니다.

**src/pages/test.vue**  
```vue
<template>
  <v-card>
    <v-card-text>
      <v-textarea v-model="txt" />
    </v-card-text>
    <v-card-actions>
      <v-btn @click="write">write</v-btn>
      <v-btn @click="read">read</v-btn>
    </v-card-actions>
  </v-card>
</template>

<script>
const firebase = require("firebase")
// Required for side-effects
require("firebase/firestore")

// Initialize Cloud Firestore through Firebase

export default {
  data() {
    return {
      txt: '',
      db: null
    }
  },
  mounted() {
    firebase.initializeApp({
      apiKey: '### FIREBASE API KEY ###',
      authDomain: '### FIREBASE AUTH DOMAIN ###',
      projectId: '### CLOUD FIRESTORE PROJECT ID ###'
    });
    this.db = firebase.firestore();
    this.db.settings({ timestampsInSnapshots: true })
  },
  methods: {
    write() {
      this.db.collection("users").add({
        first: "Ada",
        last: "Lovelace",
        born: 1815
      })
        .then((docRef) => {
          console.log("Document written with ID: ", docRef.id);
          this.txt = docRef.id
        })
        .catch((error) => {
          console.error("Error adding document: ", error);
        });
    },
    read() {
      this.db.collection("users").get().then((querySnapshot) => {
        querySnapshot.forEach((doc) => {
          this.txt = JSON.stringify(doc.data())
          console.log(`${doc.id} => ${doc.data()}`);
        });
      });
    }
  }
}
</script>
```

![alt fb key](/images/nuxt/2019-05-07_13.17.51.png)

파이어베이스 호스팅에서 파이어스토어를 사용하려면 3가지를 입력해줘야합니다.

- apiKey: 프로젝트 설정에서 웹 API 키
- projectId: 생성된 프로젝트 아이디
- authDomain: https://<projectId>.firebaseio.com

- 마운트시 초기화하고 this.db라는 객체를 만들어서 사용했습니다.
- this.db.settings를 해주는 이유는 콘솔에 에러가 뜬데로 넣은 것입니다.

## 쓰기 결과 콘솔에서 확인

![alt fb data](/images/nuxt/2019-05-07_13.35.13.png)

콘솔에서 사용된 데이터를 확인할 수 있습니다.

## 읽기 결과 뷰에서 확인

![alt fb data read](/images/nuxt/2019-05-07_13.43.33.png)

# 결론

이것으로 정적프론트에서 백엔드 없이 데이터를 읽고 쓸 수 있다는 것을 알게 되었습니다.

# 영상

{% include video id="bWDq1Hc6SWU" provider="youtube" %}
