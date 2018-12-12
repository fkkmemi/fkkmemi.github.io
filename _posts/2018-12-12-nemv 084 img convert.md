---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 84 이미지 변환하기(base64, sharp)
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify,sharp,base64]
comments: true
sidebar:
  nav: "nemv6"
---

이미지 업로드 후 디비 저장까지 하려면 무엇이 필요한지 알아봅니다.

{% include toc %}
{% raw %}

# 개요

파일 전송의 주된 목적 중 하나는 역시 이미지 업로드입니다.

지난번 파일 업로더인 멀터로 public에 따로 저장할 수도 있습니다.

저장된 파일을 html에서는 img src="/img/file.png" 식으로 사용할 수 있습니다.

하지만 https://x.com/img/file.png 로 누구나 열람할 수 있습니다.

프로필 이미지로 사용할 것인데 누구나 링크로 오픈 되면 안되겠죠?

그러려면 로그인된 사용자를 인식하고 디비에 저장된 이미지를 불러오는 것이 좋겠죠.

하지만 디비에 몇 메가씩이나 되는 것을 저장하는 것은 문제가 있습니다.

그래서 이번에 정리해 볼 내용은 2가지 입니다.

- 이미지를 문자로 변경: base64
- 이미지 용량 축소: sharp
 
# 파일이란?

컴퓨터 세상은 모두 비트덩어리로 이루어져있습니다.

그중 데이터라 하는 것들은 사실은 바이트(8비트) 덩어리일 뿐이죠..

자주 보는 파일들인 .png .txt .xls 등 모두 결국 바이트 덩어리입니다.

> 고수분들이 보실 필요 없는 챕터일 수도 있지만.. 추억을 떠올리며 읽어보세요~

## 가볍게 되짚어 보는 16진수

현업에 종사하지 않으면 진수 같은 것은 개념 자체를 잃어 버립니다.

어린시절부터 대학과정까지 수학을 학습 했어도 말이죠..

하지만 세어보면 기억이 날 것입니다.

- 10진수: 00 01 02 03 04 05 06 07 08 09 10 11 12 13 14 15 16 17 ... 255

- 16진수: 00 01 02 03 04 05 06 07 08 09 0A 0B 0C 0D 0E 0F 10 11 ... FF

한바이트는 0~255 혹은 00~FF 로 이루어 져있습니다.

바이트를 11이라고 표현하면 너무 헷갈리죠? 10진수의 17인지 16진수의 11인지..

그래서 16진수는 0x11로 표현합니다.

console.log(0x11) 라고 해보면 17이 콘솔에 찍히는 것을 알 수 있습니다.

## 텍스트 

![alt text](/images/nemv/2018-12-12_13.34.25.png)

알파벳과 숫자는 아스키코드라는 것으로 7F 이하를 사용합니다.

'abcd'는 '61 62 63 64' 라는 4바이트로 이루어 져있고..

한칸 내린 것이 '0A'입니다.

'123'은 '31 32 33' 3바이트죠.

알파벳과 숫자와는 다르게 '가나다'인 한글은 1바이트로 표현이 안되죠. 

'EA B0 80 EB 82 98 EB 8B A4' 무려 9바이트나 써서 표현합니다.

잘 보면 7F 이상인 것들을 조합시켜서 표현합니다.

비영어와 기호등을 만들 수 있죠. 

utf8, unicode, euckr 같은 것들 들어보셨을 텐데 저런 바이트를 만들고 해독하는 방법인 것입니다.

영어가 기본인 컴퓨터 세상이 영문화권 사람들이 참 부럽습니다.

## 이미지

![alt text](/images/nemv/2018-12-12_13.35.54.png)

이미지를 확인해보면 알 수 없는 바이트들로 이루어져있습니다.

## 확장자

파일만 가지고는 어떻게 해석(디코드)해야할 지 모르기 때문에 확장자를 가지게 한 것입니다.

바보 같은 파일 규격은 저도 만들 수 있습니다.

파일도 어찌보면 결국 약속(프로토콜) 이기 때문에.. 

예를들어 

.memi 라는 파일은

헤더: 0~3 까지 1f 2f 3f 4f 를 채우고
내용: 4~N 까지 내용을 채우고
푸터: N~N+2 까지 5f 6f를 채우게 해서 파일을 만든 다면..

조건 절로 헤더 푸터 부분이 맞는 지 체크하고 안에 내용을 취하면 나만의 파일이죠..

> .egg 같은 게 사실 그렇게 만들어진 암덩어리 같은 것이죠..

## 노드의 버퍼 시스템이란

**be/app.js**  
```javascript
const fs = require('fs')

fs.readFile('config/test.txt', (e, r) => {
  if (e) return console.error(e.message)
  console.log(r)
  // <Buffer 61 62 63 64 0a 31 32 33 0a ea b0 80 eb 82 98 eb 8b a4 0a>
})
```

위에 만들어 두었던 파일은 백엔드에서 fs모듈로 쉽게 열어볼 수 있습니다.

콘솔을 찍어본 데이터는 버퍼라고 알려줍니다.

이미지도 저런 버퍼형 데이터일 텐데..

프론트에서 파일 없이 읽을 수 있을까요?

# 이미지를 문자로

## 백엔드 처리

바로 img src에 문자(base64 인코딩)를 넣어서 가능합니다.

그래서 이미지를 문자로 만드는 것입니다.

base64 참고: [http://kyejusung.com/2015/06/it-base64란/](http://kyejusung.com/2015/06/it-base64란/)

당연히 npm base64 img를 구글링 하면 원하는 모듈이 나옵니다.

**base64-img 설치**  
```bash
$ cd be && yarn add base64-img
```

참고: [https://www.npmjs.com/package/base64-img](https://www.npmjs.com/package/base64-img)

이미지를 문자열 버퍼로 만들어주는 모듈입니다.

**be/routes/api/user/index.js**  
```javascript
const imgConvert = require('base64-img')
const fs = require('fs')

router.post('/', multer({ dest: 'public/' }).single('bin') ,(req, res, next) => {
  console.log(req.body)
  console.log(req.file)

  imgConvert.base64(req.file.path, (err, fd) => {
    if (err) return next(err)
    // console.log(fd)
    fs.writeFileSync('public/xxx.txt', fd)
  })
  res.status(204).send()
})
```

이미지 파일을 경로를 넣으면 버퍼로 만들어 줍니다.

여기서 중요한 것은 fd(file data)는 버퍼라는 것입니다. 문자열로 변경하려면 fd.toString()을 사용해야죠..

콘솔에 찍어보니 너무 많아서.. fs를 이용해 변환된 버퍼를 써봤습니다.

변환된 파일을 읽어보면..

```text
data:image/png;base64,/9j/4AAQSkZJRgABAgAAZABkAAD/7AARRHVja3kAA...
```

이런식으로 되어 있습니다.

파일을 비교해보면 

- 원래 파일: 59kb
- 변환된 파일(xxx.txt): 79kb

문자열 변환이 당연히 늘어날 수 밖에 없죠..

> 예를 들어 123이라는 정수는 바이트 하나지만.. 문자로 표현하려면 0x31 0x32 0x33 3바이트나 필요하기 때문입니다.

## 프론트엔드 표시

이제 이 텍스트들을 주욱 긁어다가 표시해보겠습니다.

**fe/src/views/user.vue**  
```vue
<!-- -->
<v-card-text>
    <input id="bin" type="file">
    <img :src="img"/>
</v-card-text>
<!-- -->
<script>
export default {
  data: () => ({
    // ..
    img: 'data:image/png;base64,/9j/4AAQSkZJRgABAgAAZABkAAD/7AA...'
    // ..
</script>
```

**변환 확인**  
![alt convert](/images/nemv/2018-12-12_16.01.59.png)

> 참고로 구글에서 사진 검색해서 아무거나 골라서 테스트 해본 것이니 오해 마시기를...

## 정리

**be/routes/api/user/index.js**  
```javascript
  imgConvert.base64(req.file.path, (err, fd) => {
    if (err) return next(err)
    fs.unlinkSync(req.file.path)
    res.send(fd.toString())
  })
```

백엔드에서는 지저분한 임시파일은 변환 후 지워주고 버퍼를 문자열로 보냅니다.
 
**fe/src/views/user.vue**  
```javascript
export default {
  data: () => ({
    // ..
    img: 'http://medfordgospelmission.org/wp-content/uploads/2018/06/default-user-image-female.png'
    // ..    
  methods: {
    upload () {
      const fd = new FormData()

      fd.append('name', this.form.name)
      fd.append('bin', document.getElementById('bin').files[0])
      this.$axios.post('/user', fd)
        .then(({ data }) => {
          this.img = data
          this.$store.commit('pop', { msg: '파일 업로드 완료', color: 'success' })
        })
    // ..
```

아무이미지나 초기값으로 주고 업로드 후 변경이 되는 지 확인하면 됩니다.

# 이미지 축소하기

79kb나 되는 파일을 디비에 저장하기는 부담스럽습니다.

용량을 줄이는 방법은 여러가지가 있지만..

그 중 유명한 sharp라는 모듈로 조절해보겠습니다.

참고: [https://www.npmjs.com/package/sharp](https://www.npmjs.com/package/sharp)

**설치하기**  
```bash
$ cd be && yarn add sharp
```

```javascript
// ..
const sharp = require('sharp')
const fs = require('fs')

router.post('/', multer({ dest: 'public/' }).single('bin') ,(req, res, next) => {
  console.log(req.body)
  console.log(req.file)

  const fn = req.file.path + 'sharp'
  sharp(req.file.path).resize(200,200).crop(sharp.strategy.entropy).toFile(fn, (err, info) => {
    if (err) return next(err)
    imgConvert.base64(fn, (err, fd) => {
      if (err) return next(err)
      fs.unlinkSync(req.file.path)
      fs.unlinkSync(fn)
      console.log(fd.length)
      res.send(fd.toString())
    })
  })
})
```

기본 예제를 조금 변경해서 사용해봤습니다.

샤프로 변환된 파일을 fn이라는 명으로 저장후 쓰래기 파일 두개만 삭제 하면 끝입니다.

javascript 배열과 같이 fd 버퍼는 length로 사이즈를 알 수 있습니다.

59233 -> 11026으로 비약적으로 줄였습니다.

샤프의 옵션을 좀 더 살펴보면 다양한 시도들을 할 수 있겠죠?

# 결과

![alt sharp](/images/nemv/2018-12-12_16.51.15.png)

200x200 사이즈로 잘 잘려 있습니다~

# 보너스: 프라미스형 모듈 사용하기

왠지 콜백형은 코드가 보기가 싫죠..

다행히 버퍼로 처리하는 모듈이 있었네요..

참고: [https://www.npmjs.com/package/image-data-uri](https://www.npmjs.com/package/image-data-uri)

**설치**  
```bash
$ cd be && yarn add image-data-uri
```

**be/routes/api/user/index.js**  
```javascript
const imageDataURI = require('image-data-uri')
// ..
  sharp(req.file.path).resize(200,200).crop(sharp.strategy.entropy).toBuffer()
    .then(bf => {      
      fs.unlinkSync(req.file.path)
      res.send(imageDataURI.encode(bf, 'png'))
    })
    .catch(e => next(e))
```

샤프도 버퍼를 프라미스로 리턴하게 해서 쓸 데 없는 파일이 안생기게 해서 코드가 깨끗해졌습니다.

npm에는 좋은 모듈들이 많으니 잘 찾아보면 보물을 득템할 수 있습니다.

# 마치며

노드의 버퍼와 문자는 다르기 때문에 바이트에 대해 조금 길게 설명했습니다.

파일 처리를 할 때도 노드의 버퍼로 인아웃을 하기 때문에 중요합니다.

제대로 하려면 이미지는 사실 양방향(프론트와 백엔드)에서 조율해야합니다.

프론트에서 로드해서 base64로 변경한 후 서버로 전송도 사실 가능합니다.

그래서 프론트에 이미지 에디터로 사전 처리가 어느정도 된 후에 전송한 후에 백엔드에서는 용량 체크해서 안전하게 보관하는 용도가 좋겠죠?

다음에는 쓸만한 이미지 에디터를 소개해보겠습니다.

# 소스

[소스 확인 1](https://github.com/fkkmemi/nemv3/commit/994f4c04f9b453c37e52c451ac05ae25ed088351)

[소스 확인 2](https://github.com/fkkmemi/nemv3/commit/b0d3d3b0fb5da5715fe2221140c3ac7f9c1bc14c)

[소스 확인 3](https://github.com/fkkmemi/nemv3/commit/ef0e9692d1bea098da5b08fe2854c3dc1067ea06)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}

