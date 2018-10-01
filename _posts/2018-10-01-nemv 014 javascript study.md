---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 14 자바스크립트 기본
category: nemv
tag: [nemv,vue,vuetify,javascript]
comments: true
sidebar:
  nav: "nemv"
---

이제부터 기본편 시작합니다.

기초편보다 어려운 것이 아니고 기초편은 개발환경 구축이라면 기본편은 응용의 시간인 거죠~

언어란 무엇인지 초심을 다듬으며 떠올려봅니다.

컴퓨터가 이해하는 언어는 사실 맞냐 틀리냐 밖에 없습니다.

참 거짓, 즉 true or false 이며 결국 101010의 반복이죠..

사람이 컴퓨터에게 1010이라고 이진수로 얘기하기 힘들기 때문에 1010을 편하게 만들 수 있는 언어를 개발한 것이죠..

각종 언어는 컴퓨터와 사람이 소통하기 위한 것입니다.

자바니 파이썬이니등의 언어나 기술은 소통을 좀 더 편하기 위해 만든 것입니다.

새로운 언어일 수록 기존의 문제점을 개선하고 더 쉽게 만들 수 밖에 없습니다.

우리가 해야할 일은 컴퓨터가 알아 들을 뭔가를 지시해야하고 좀 더 편리하게 쓰고 반복을 줄이는 일을 하면 되는 것입니다. 

최소한의 기본만 다뤄보고 더 알고 싶으면 어떤 방법으로 배워야 할 지 알아 보도록 하겠습니다.

자스를 잘 다루시던 분들은 이번 강좌는 패스하시면 됩니다.

_편의를 위해 자바스크립트는 자스로 줄여 부르겠습니다._

자바스크립트를 처음 다뤘을 때를 떠올리며 작성해봅니다.

{% include toc %}

# 개요

자스의 기본은 매우 쉽습니다.

자스가 어려운 이유는 응용 할 때 힘든 것이죠.

자스로 리액트나 뷰를 만든다고 생각해보세요 얼마나 정교하게 모듈화를 해야하고 많은 것을 생각했을까요?

리액트 뷰 모두 도구 일 뿐입니다. 우리가 도구를 만들 필요는 없죠..

그 도구를 사용해서 생산적인 것을 만들어야겠죠..

제가 처음 언어를 배울 때 제 사수가 했던 것은 네가지만 기억하라는 것이었습니다.

조건(if & else), 반복(for & while), 데이터의 형(type), 생각(think)

딱 네가지면 된다는 것이죠..(_실제 그 네가지로 펌웨어는 완성이 되었습니다._)

정말 기본적인 것을 갖추고 추가적으로 알아야 할부분은 그때 그때 배우면 된다는 것이죠..

어떤 언어든 기술이든 배움의 끝이란 것은 없습니다.

계속 배우며 발전 시키고 다른 기술을 모색하는 것이죠.

# 조건

어느 프로그램도 이것 만큼은 다 같은 것 같습니다.

if 와 else 죠

if (조건) { 실행 } 이죠

여기서 조건이 중요한데 조건이라 함은 true or false(0 or 1, 참 거짓) 밖에 없습니다.

그래서 if else 보다는 조건 이라는 것을 명확하게 이해하는 것이 좋습니다.

어떻게 이해하냐면 알고 싶은 조건들을 직접 구현해보면 됩니다.

지난번 help.vue를 연습장으로 쓰기로 했었죠?

이렇게 테스트해보면 됩니다.

![alt test환경](/images/nemv/2018-10-01 10-07-46 test.png)

1. 프론트엔드(화면) 실행
2. 코드 수정
3. 브라우저에서 버튼 클릭하고 콘솔로 출력내용 확인하기

**be/fe/src/views/help.vue**  
```vue
<template>
  <div>
    <v-btn @click="test">test</v-btn>
  </div>
</template>

<script>
export default {
  name: 'help',
  data () {
    return {
      text: ''
    }
  },
  methods: {
    test () {
      let a = 3, b = 4
      console.log(a < b)
      if (a < b) console.log('a가 작구나')
      a = {}
      if (a) console.log('빈 오브젝트도 참이구나')
      a = []
      if (a) console.log('빈 어레이도 참이구나')
      a = 0
      if (a) console.log('0 참')
      else console.log('0 거짓')
      a = '문자'
      if (a) console.log('문자는 참')
      else console.log('문자는 거짓')
      a = ''
      if (a) console.log('빈문자는 참')
      else console.log('빈문자는 거짓')
      a = null
      if (a) console.log('null은 참')
      else console.log('null은 거짓')
      a = new Date()
      if (a) console.log('시간은 참')
      else console.log('시간은 거짓')
      if (a) {
        console.log('두줄은 괄호가 필요하다')
        console.log('두줄은 괄호가 필요하다')
      }
    }
  }
}
</script>
```

b > a 라는 조건을 찍어보면 참 거짓을 출력 한다는 것을 알 수 있습니다.

그 밖에 다른 조건도 헷갈리면 한번 찍어보면 알 수 있습니다.

# 변수

사실 자스는 변수가 더 중요합니다.

전통언어 개발자가 보기에 변수와 함수 경계가 변태스러운 것들도 있습니다.

예를 들어보면

```vue
<template>
  <div>
    <v-btn @click="test">test</v-btn>
    <v-btn @click="testVar">testVar</v-btn>
  </div>
</template>

<script>
export default {
  name: 'help',
  methods: {    
    testVar () {
      const v = {
        a: 1,
        b: (x) => {
          return x + 3
        },
        c: ['1', 33]
      }
      console.log(v.b(4))
    }
  }
}
</script>
```
위와 같은 변수 const v는 함수도 들어 있고 어레이도 들어 있는 변태입니다.

## 선언: const, let, var

심도 있게 얘기하면 길어질 이야기지만 간단하게 말하 그냥 **const** 쓰시면 됩니다. 

var는 옛 것이라 쓰면 안됩니다.

쓰시다 안되면 let 쓰시면 됩니다.

언제 안되는 지 잠깐 보고 정리하겠습니다.

```javascript
const a = 3
a = 4 // 안됨
```

const는 변형이 불가능 하다는 것입니다.

그래서 이때 let을 쓰면 된다는 것입니다.

그런데 항상 불가능할까요?

```javascript
const a = {
  b: 3,
  c: 4
}
a.b = 333 // 됨
const b = {
  aa: 33,
  aaa: 44
}
a = b // 안됨
```

오브젝트나 어레이 변수 일 경우 변형이 가능합니다.

const a라는 메모리 어딘가의 번지가 상수라는 것이죠 그 안에서 이루어지는 값 변경은 가능합니다.

그래서 a오브젝트를 b로 변경 할 수 없는 것입니다.

만약 a나 b가 let일 경우 a는 b가 되어 버립니다. b의 메모리 어딘가를 같이 공유해버리죠.. 아주 조심해야할 부분입니다.

조심할 필요 없이 object = object 이런 짓을 안하면 됩니다.

제대로 카피하고 싶으면 object.a = object.a 같이 멤버들을 일일히 대입해야합니다.

이제부터 거의 오브젝트나 어레이를 다룰 것이기 때문에 **const**가 도배 될 것이란 겁니다.

## 숫자, 문자등

숫자는 숫자일 뿐 문자는 문자일 뿐 따로 형을 지정하지 않습니다.

```javascript
let a = 3 //숫자
a = -3.3 //소수점숫자
a = '문자'
a = '33' //문자 33인데
a = Number(a) // 이제 숫자 33입니다.
console.log( typeof a ) // number: 숫자임을 알 수 있습니다.
a = a.toString() // 다시 문자 변경도 되죠.
console.log( typeof a ) // string: 문자임을 알 수 있습니다.
let b = 1 // 숫자와 문자가 더해질 수도 있습니다.
let c = 1 + b + a // 1133이라는 문자가 됩니다 문자가 하나라도 껴있으니.
c = 1 + b + Number(a) // 숫자 35가 되죠
a = { c: 1, b: 2 } // 갑자기 오브젝트가 될 수도 있고..
a = [3, 4] //어레이가 될 수도 있죠
```
정말 자유롭죠?

문자에 대한 것이 편리한 것이 있는데

보통 문자열 더하기는 이렇게 하죠

한 글짜씩 더하려면 

```javascript
let a = 'a'
a = a + 'b' // ab
a += 'c' // abc 위의 것의 코드를 줄인 방법입니다.
a += "d" // abcd 쌍따옴표도 됩니다.
```

한꺼번에 더하려면

```javascript
let a = 'a' + 'b' + 'c' // abc죠
```

근데 이것보다 편리한 것이 있습니다.

디비 쪽 연결 문자열 만드신 분들 이런 경험 있죠?

```javascript
const sel = 'id, name'
const id = '철수'
let qry = 'select ' + sel + ' from users where id = ' + id
```
이게 쿼리가 복잡해지면 엄청 보수가 어려워 지는 코드가 됩니다.

그래서 이런 방법이 고안된 것 같습니다.

```javascript
const sel = ['id', 'name']
const id = '철수'
let qry = `select ${sel.join(',')} from users where id = ${id}`
```

위와 같은 방법이지만 + 연산자가 없어서 훨씬 유지보수가 좋고 가독성이 나아졌죠.

이런 문자열을 탬플릿스트링이라고 한답니다.

eslint라는 문법 검사에서도 변수와 문자열 조합은 위와 같은 방법을 강요하니 써주는 게 좋겠죠?

## 오브젝트

만능 데이터입니다. 정말 매력적이죠.

쉽게 말해 키와 값으로 이루어져 있습니다.

예를 들면 이런 식입니다.

```javascript
const a = {
  a: 111,
  "한글": 33,
  arr: [
    'ab',
    obj: {
      b: 3,
      cc: '문자'
    }
  ],
  func: (d) => {
    console.log(d)
  }
}
```

이렇게 변태 같은 조합의 변수가 만들어 집니다.

이걸 통째로 문자열로 바꾸면 그 유명한 제이슨(JSON)이 되는거죠

자스는 대충 대충 써도 다 됩니다.

```javascript
const a = {}
a.b = 3
a.name = 'aa'
a.arr = ['bb', 33]
a.c.b = 4 // a.c가 아직 정의되지 않아서 오류
```

이런 식으로 비워 놓고 추가해서 써도 됩니다.

> 그래서 자유로운 오브젝트가 데이터인 몽고디비는 컬럼이 자유로운 것입니다.  
실무에서도 설계미스로 부족한 필드를 그냥 추가해서 씁니다. 참 자유로운 데이터베이스죠

## 배열(어레이)

배열은 쉽게 말해 복수의 개체라고 생각 하면 됩니다.

일반적으로 배열은 아래와 같이 쓰이죠 

### 배열 꺼내기

```javascript
const as = ['a', 'b', 'c'] // 저는 배열 변수 뒤에는 복수를 의미하는 s를 가급적 붙힙니다.

console.log(a[0], a[1], a[2]) // a,b,c 이렇게 꺼내서 쓰죠
```

### 배열 추가

```javascript
as.push('d') // 이제 as는 ['a', 'b', 'c', 'd'] 가 되었습니다.
console.log(as.length) // 4 개죠~ 개수도 파악 됩니다
```

### 배열 수정

```javascript
as[2] = 333 // 난데 없는 숫자도 문제 없습니다. 이제는 ['a', 'b', 333, 'd']가 되었습니다.
```

### 배열 삭제

맨 앞 지우기
```javascript
const v = as.shift() // v는 a가 되고 as는 ['b', 333, 'd']가 되었습니다.
```

맨 뒤 지우기
```javascript
as.pop() // as는 ['b', 333]이 되었습니다.
```

생각해보면 CRUD의 만들고 읽고 수정하고 지우고 소트등이 가능해서 아주 간단한 디비워크가 됩니다.

배열 또한 자유로워서 요상하게 만들 수 있습니다.

```javascript
const bs = [
  '333',
  { //이렇게 오브젝트가 와도 자유롭답니다.
    c: {
      d: {
        ee: '끝 없는 오브젝트의 향연'
      },
      ss: [ 'ㅁ', 'ㅇㅇ', 'fwef'] //어레이 안에 어레이도 전혀 지장이 없고요
    }
  },
  123
]
console.log(bs[0]) // 333
console.log(bs[1].c.ss[2]) // fwef 
```
 
물론 자유분방한 배열이라고 저런식으로 배열을 사용하면 안되겠죠~

# 반복문

반복은 조건문과 마찬가지로 기존 언어들과 비슷합니다.

기존 언어를 쓰시던 분은 친숙한 for, while, do while등 그대로 사용하시면 됩니다.

```javascript
const as = ['a', 'b', 'c']

for (let i = 0; i < as.length; i++) console.log(as[i])
```

근데 기존 언어 스타일은 뭔가 좀 부적절한 느낌이 들죠 쓸데 없는 i 같은 걸 선언도 해야하고요..

그래서 자스에서 주로 배열을 다룰 때는 forEach라는 것을 씁니다.

```javascript
forEach(v => console.log(v)) // 위와 같지만 불필요한 i가 없어서 깔끔하죠?
forEach((v, i) => console.log(i)) // 가끔 인덱스 i가 필요할 경우 요렇게 하나만 추가하면 됩니다.
forEach((v, i) => {
  console.log(v)
  console.log(i) // 반복문 역시 2개 이상 실행해야할때 괄호를 넣어주면 됩니다.
})
```

배열만 반복문을 사용할 수 있는 것은 아닙니다.

바로 오브젝트도 사용이 가능한데요.

```javascript
const obj = {
  a: 1, b: 2, c: '3', xxx: 'gggg'
}
for (let k in obj) {
  console.log(k, obj[k])
  // a, 1
  // b, 2
  // c, 3
  // xxx, gggg
}
console.log(obj.c) // 3
console.log(obj['c']) // 3
console.log(obj['gggg']) // gggg
```

바로 키와 값을 조회 할 수 있습니다.

그래서 놀라운 것이 오묘한 방법으로 데이터를 꺼낼 수 있습니다.

위의 코드에서 3번째 값을 얻으려면 배열 꺼내듯이 가져올 수 있습니다~

obj.c와 obj['c']는 같은 값인 거죠~

필수 알아야 할 정보는 여기 까지 입니다.

한땀한땀 재미 들려서 다른 것도 해보고 싶으신 분은 좀 촌스러운 사이트지만 아래 사이트들을 추천합니다.

https://www.w3schools.com/js/default.asp

# 뷰티파이와 연동 해보기

이제 공부는 지겨우니 화면으로 구현한 결과들을 확인해볼께요..

코드가 조금 긴 이유는 모양 내려고 vuetifyjs.com 중 card 예제 소스를 그냥 긁어왔기 때문입니다.

**be/fe/src/views/help.vue**  
```vue
<template>
  <div>
    <v-btn @click="test">test</v-btn>
    <v-container grid-list-md text-xs-center>
      <v-layout row wrap>
        <v-flex xs6 sm4 v-for="user in cards" :key="user.name">
          <v-card>
            <v-img
              :src="user.src"
              height="200px"
            >
              <v-container
                fill-height
                fluid
                pa-2
              >
                <v-layout fill-height>
                  <v-flex xs12 align-end flexbox>
                    <span class="headline white--text" v-text="user.name"></span>
                  </v-flex>
                </v-layout>
              </v-container>
            </v-img>
            <!-- <v-card-title>이름: {{user.name}}</v-card-title> -->
            <v-card-text>나이: {{user.age}}</v-card-text>
            <v-card-actions>
              <v-spacer></v-spacer>
              <v-btn icon>
                <v-icon>favorite</v-icon>
              </v-btn>
              <v-btn icon>
                <v-icon>bookmark</v-icon>
              </v-btn>
              <v-btn icon>
                <v-icon>share</v-icon>
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-flex>
      </v-layout>
    </v-container>
  </div>
</template>

<script>
export default {
  name: 'help',
  data () {
    return {
      cards: []
    }
  },
  methods: {
    test () {
      const user = {
        name: `memi ${Math.random()}`,
        src: 'https://cdn.vuetifyjs.com/images/cards/house.jpg'
      }
      user.age = 11
      this.cards.push(user)
    }
  }
}
</script>
```

![alt vuetify test](/images/nemv/2018-10-01 15-24-27.png)

버튼을 누르면 카드가 동적으로 추가되는 것으로 만들어 봤습니다.

위의 코드에서 스크립트 부분 부터 살펴보겠습니다.

우선 스크립트를 보면

동적으로 카드를 만들기위한 배열 cards = [] 초기화 시켜놨습니다.

버튼을 누를 때 마다 카드에 user라는 오브젝트를 추가합니다.

user.name을 랜덤숫자를 추가해서 유니크하게 줬습니다. 

두개를 추가하면 이런 데이터가 되는 것이죠..

```javascript
this.cards = [
  {
    name: '어쩌고1',
    age: 11,
    src: '이미지 경로1'    
  },
  {
    name: '어쩌고2',
    age: 11,
    src: '이미지 경로2'    
  }
]
```

이제 화면쪽을 보면 ```<v-flex xs6 sm4 v-for="user in cards" :key="user.name">```  같은 경우 동적으로 카드의 개수만큼 짜여진 html로 덮는 것이죠~

찬찬히 보시면 위에 공부했던 내용들을 꼭 알아야 진행이 된다는 느낌이 들죠?

# 마치며

이번 강좌로 자스 기본 강좌는 마침니다.

앞으로 해야할 뷰만 해도 일주일은 해야 될까말까인데 하루만에 되냐고요?

충분합니다. 더 많은 훌륭한 방법으로 차차 공부하고 바꾸면 되니까요~

사실 제일 중요한 것은 문제를 해결 하는 과정과 방법이 더 중요합니다.

바로 디버그라고 할 수 있는 것이죠.

이 강좌에서 추구하는 방향은 고수의 길이 아닌 구현의 길입니다~

# 영상

준비중