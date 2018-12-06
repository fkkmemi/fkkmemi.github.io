---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 83 파일 업로드 하기(multer)
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify,multer]
comments: true
sidebar:
  nav: "nemv6"
---

파일 업로드 미들웨어인 멀터(multer)를 이용해 서버에 파일을 전송해보겠습니다.

{% include toc %}
{% raw %}

# 개요

대부분의 REST 데이터 송수신은 json 만 왔다갔다 했습니다.

사실 텍스트 덩어리들이죠..
 
파일은 어떻게 서버에 전송할까요?

프론트에서 json에 데이터를 추가해서 올릴 수도 있습니다..

보통 게시물 안에 글과 이미지가 섞어있다면 이미지를 데이터화해서 보내는 것이죠.(_eg: { file: "base64 data", etc: 12 }_)

하지만 첨부로 넣을 경우에는 정석적인 파일업로드 기능을 사용합니다.

노드 파일 업로드 모듈은 다양하지만 그중 가장 인기있는 멀터(multer)를 다뤄보기로 합니다.

당장 활용하기 위해 사용자 정보페이지의 사진 업로드 기능으로 구현해보겠습니다. 

# 멀터(multer) 설치

```bash
$ cd be
$ yarn add multer
```

참고: [https://www.npmjs.com/package/multer](https://www.npmjs.com/package/multer)

# api 만들기

**be/routes/api/user/index.js**  
```javascript
const router = require('express').Router()
const createError = require('http-errors')
const multer = require('multer')

router.post('/', multer({ dest: 'public/' }).single('bin') ,(req, res, next) => {
  console.log(req.body)
  console.log(req.file)
  res.status(204).send()
})

module.exports = router
```

경로와 콜백 사이에 들어가는 것이 사실 미들웨어 입니다.

예제중 파일 하나만 받는 것으로 만들어봤습니다.

현재 사용하지 않는 경로 public을 파일 저장소로 사용했습니다.

이제 프론트에서 파일 요청을 받을 경우 콘솔로 파일 정보를 확인 할 수 있습니다.


**be/routes/api/index.js**
```javascript
// ..
router.use('/manage', require('./manage'))
router.use('/user', require('./user'))
// ..
```  

인증 처리가 된 맨 아래에 user 라우터를 추가합니다.

# 프론트 파일 만들기

**fe/src/views/user.vue**  
```vue
<template>
  <v-container fluid fill-height>
    <v-layout align-center justify-center>
      <v-flex xs12 sm8 md4>
        <v-card class="elevation-12">
          <v-card-title>sss</v-card-title>
          <v-card-text>
            <input id="bin" type="file">
          </v-card-text>
          <v-card-actions>
            <v-btn @click="upload">전송</v-btn>
          </v-card-actions>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
export default {
  data: () => ({
    form: {
      name: 'sss'
    }
  }),
  mounted () {
  },
  methods: {
    upload () {
      const fd = new FormData()

      fd.append('name', this.form.name)
      fd.append('bin', document.getElementById('bin').files[0])
      this.$axios.post('/user', fd)
        .then(() => {
          this.$store.commit('pop', { msg: '파일 업로드 완료', color: 'success' })
        })
        .catch(e => {
          if (!e.response) this.$store.commit('pop', { msg: e.message, color: 'warning' })
        })
    }
  }
}
</script>
```

파일 업로드를 위해 input을 사용했습니다.(_곧 뷰티파이의 v-upload 출시 예정_)

파일을 가져와야하기 때문에 id="bin"을 사용했습니다.

파일이기 때문에 FormData라는 객체를 만들어서 추가 합니다.

자바스크립트의 기본 문법인 getElementById를 이용하여 파일을 추가합니다.

결국 name: 'sss'과 같은 일반 등록용 데이터와 파일이 함께 전송되는 것이죠.


# 프론트 사진 등록

![alt pic](/images/nemv/2018-12-06_16.25.49.png)

아무 사진이나 다운받아서 전송을 눌러봅니다.

# 백엔드 확인

```javascript
{ name: 'sss' }
{ fieldname: 'bin',
  originalname: '20171207013514_2.jpg',
  encoding: '7bit',
  mimetype: 'image/jpeg',
  destination: 'public/',
  filename: 'ccd69a992f22dca899f0611442861f80',
  path: 'public/ccd69a992f22dca899f0611442861f80',
  size: 59233 }
```

콘솔에 찍힌 파일 내용을 보면 내용(req.body)와 파일정보(req.file)가 잘 나오는 것을 확인 할 수 있습니다.

멀터가 파일명은 임시로 지어줍니다.

실제 저장된 파일은 'public/ccd69a992f22dca899f0611442861f80' 으로 되어 있습니다.

# 마치며
 
사실 백엔드의 파일 업로드 기능인 멀터 사용은 간단합니다.

오히려 프론트의 formData 같은 것이 생소하죠..
 
파일 업로드 자체보다 다음 플랜이 더 고민이 많습니다.

사진자체를 디비에 저장할지 파일 경로를 디비에 저장할지..
 

# 사용해보기

[https://fkkmemi.com](https://fkkmemi.com)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/1a362cfcb54cb48a35f128b0fcdd49010afb9991)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}

