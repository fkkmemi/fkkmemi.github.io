---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 85 이미지 컴포넌트로 업로드하기(filepond)
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify,filepond]
comments: true
sidebar:
  nav: "nemv6"
---

업로드 콤포넌트를 이용해서 이미지를 업로드해보겠습니다.

{% include toc %}
{% raw %}

# 개요

이미지를 업로드하기 전에 프론트에서 파일을 로드 후, base64로 화면에 표시해 볼 수 있습니다.

다양한 방법이 있지만, 그 중 유명한 업로드 라이브러리를 이용해서 구현해보겠습니다.

다른 라이브러리도 응용하여 설치하고 추가할 수 있게 하기 위한 연습게임으로 보시면 됩니다.

# 프론트

## filepond 설치

참고: [https://github.com/pqina/vue-filepond](https://github.com/pqina/vue-filepond)

```bash
$ cd fe && yarn add filepond vue-filepond filepond-plugin-file-validate-type filepond-plugin-image-preview
```

위 링크에는 플러그인 설치에 대한 내용이 없지만.. 설치를 안하면 당연히 예제가 실행되지 않습니다.(_예제의 임포트 부분.._)

## 컴포넌트 만들기

**fe/src/components/imgUpload.vue**  
```vue
<template>
  <div>
    <file-pond
      name="bin"
      ref="pond"
      allow-multiple="false"
      max-files="1"
      accepted-file-types="image/jpeg, image/png"
      :server="server"
      v-bind:files="myFiles"
      v-on:init="handleFilePondInit"
      v-on:processfile="onload"
      />
  </div>
</template>

<script>
// Import Vue FilePond
import vueFilePond from 'vue-filepond'

// Import FilePond styles
import 'filepond/dist/filepond.min.css'

// Import FilePond plugins
// Please note that you need to install these plugins separately

// Import image preview plugin styles
import 'filepond-plugin-image-preview/dist/filepond-plugin-image-preview.min.css'

// Import image preview and file type validation plugins
import FilePondPluginFileValidateType from 'filepond-plugin-file-validate-type'
import FilePondPluginImagePreview from 'filepond-plugin-image-preview'

// Create component
const FilePond = vueFilePond(FilePondPluginFileValidateType, FilePondPluginImagePreview)

export default {
  name: 'app',
  data () {
    return {
      myFiles: [],
      server: {
        url: `${this.$apiRootPath}user`,
        process: {
          headers: {
            Authorization: localStorage.getItem('token')
          }
        }
      }
    }
  },
  methods: {
    handleFilePondInit () {
      console.log('FilePond has initialized')
      // FilePond instance methods are available on `this.$refs.pond`
    },
    onload (e, r) {
      console.log(r)
    }
  },
  components: {
    FilePond
  }
}
</script>
```

예제파일을 이용해서 그대로 만듭니다.

이 중 서버 연결 부분만 적절하게 변경했습니다.

:server 는 네이티브 ajax요청을 하기 때문에 요청에 토큰 전송부분을 따로 넣어줘야합니다.

나머지 옵션 참고: [https://pqina.nl/filepond/docs/patterns/api/server/#configuration](https://pqina.nl/filepond/docs/patterns/api/server/#configuration)  

## 화면 만들기

**fe/src/views/user.vue**  
```vue
<template>
  <v-container fluid fill-height>
    <v-layout align-center justify-center>
      <v-flex xs12 sm8 md4>
        <v-card class="elevation-12">
          <v-card-title>회원 정보 수정</v-card-title>
          <v-card-text>
            <img-upload />
          </v-card-text>
        </v-card>
      </v-flex>
    </v-layout>
  </v-container>
</template>

<script>
import imgUpload from '@/components/imgUpload'

export default {
  components: { imgUpload },
  data: () => ({
  }),
  mounted () {
  },
  methods: {
  }
}
</script>
```

위에 만든 imgUpload 콤포넌트만 추가한 파일입니다.

# 백엔드

## 모델

**be/models/users.js**  
```javascript
mongoose.set('useFindAndModify', false)
const userSchema = new mongoose.Schema({
  name: { type: String, default: '' },
  age: { type: Number, default: 1 },
  id: { type: String, default: '', unique: true, index: true },
  pwd: { type: String, default: '' },
  lv: { type: Number, default: 2 }, //add
  inCnt: { type: Number, default: 0 }, //add
  retry: { type: Number, default: 0 },
  img: { type: String, default: '' }
})
```

모델은 base64처리된 이미지 넣을 공간만 추가하면 됩니다.

## api

**be/routes/api/users/index.js**  
```javascript
// ..
const sharp = require('sharp')
const imageDataURI = require('image-data-uri')
const fs = require('fs')
const User = require('../../../models/users')

router.post('/', multer({ dest: 'public/' }).single('bin') ,(req, res, next) => {
  
  if (!req.user._id) throw createError(401, 'xxx')
  sharp(req.file.path).resize({
      width: 200,
      height: 200,
      fit: sharp.fit.cover,
      position: sharp.strategy.entropy
    }).toBuffer()
    .then(bf => {
      fs.unlinkSync(req.file.path)
      const img = imageDataURI.encode(bf, 'png')
      return User.findByIdAndUpdate(req.user._id, { $set: { img } }, { new: true }).select('-img')
      // res.send(imageDataURI.encode(bf, 'png'))
    })
    .then(r => {
      res.setHeader('Content-Type', 'text/plain')
      res.send(r._id.toString())
    })
    .catch(e => next(e))
})
router.delete('/', (req, res, next) => {
  res.status(204).send()
})
```

샤프로 사이즈를 줄이고 User.img를 업데이트 합니다.

응답으로는 해당 유저의 _id를 전송합니다.

# 결과

![alt result](/images/nemv/2018-12-24_15.28.31.png)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/8273a52da904c0e5e3dbbd67fc15f6c0b90d873b)

{% endraw %}

# 영상

이번 편은 어떻게(how?)가 중요한 활용편이라 영상을 보시는 것이 도움이 될 것 같습니다.

{% include video id="P1eKJ-SPhWo" provider="youtube" %}

