---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 60 뷰 컴포넌트 사용하기
category: nemv
tag: [nemv,vue,component]
comments: true
sidebar:
  nav: "nemv5"
---

뷰의 특장점중 하나인 콤포넌트에 대해 알아보겠습니다.

{% include toc %}

{% raw %}

# 개요

뷰 컴포넌트에 대한 설명은 공식 홈에 가장 잘 나와 있습니다.

참고: [https://kr.vuejs.org/v2/guide/components.html](https://kr.vuejs.org/v2/guide/components.html)

하지만 뭔가 이해가 어려운 느낌은.. 

그리고 뷰컴(vue-cli3)으로 만들어진 파일로 하는 것이 아닌 코드 조각만 가지고 설명하기 때문에 헷갈리죠..

그래서 실제 사용해보면서 이해를 해보고자 합니다.

# 컴포넌트 왜 사용할까?

코드를 재사용하기 위함입니다!

모듈로 만들어 두고 여러 페이지에서 불러다 쓸 수 있습니다.

뷰가 추구하는 방향이기도 합니다.  

# 직접 기본 콤포넌트 만들어보기

설명하고 예제 파일을 만드는 것 보다는 아무래도 실제 쓰고 있는 사용자관리를 통해 이해하는 것이 더 이해가 쉬울 것 같습니다.

기존에 있던 user.vue 파일을 개선해 보겠습니다.

컴포넌트는 부모와 자식 관계로 이루어 집니다.

## 자식 컴포넌트 만들기

**fe/src/components/user.vue**  
```vue
<template>
  <v-card>
    <v-card-title primary-title>
      <h3 class="headline mb-0">유저!</h3>
    </v-card-title>
  </v-card>
</template>
```

components 디렉토리에 사용자 조각파일을 만듭니다.

일반적인 html 코드만 넣었습니다.

## 부모 컴포넌트 만들기

**fe/src/views/users.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6>
        <user></user>
      </v-flex>
      <v-flex xs12 sm6>
        <user></user>
      </v-flex>
      <v-flex xs12 sm6>
        <user></user>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import user from '@/components/user'

export default {
  components: { user }
}
</script>
```
## 적용 화면

![alt component](/images/nemv/스크린샷 2018-11-07 오후 4.13.04.png)

부모(users.vue) 안에 위에 만들어두었던 자식(user.vue)들을 3개 표시해봤습니다.

- 임포트 할때 @ 는 src 디렉토리를 가르킵니다.
- 임포트 하고나서 사용하려면 components에 등록을 해주면 됩니다.
- 이제사용자 조각은 html ```<user></user>```  로 사용 가능합니다.

직접 작성해서 눈으로 보면 이해가 쉽습니다.

# 부모가 자식에게 값 전달하기

현재까지는 자식 컴포넌트에게 뭔가를 전달하지 않으면 쓸모 없는 자식일 뿐이죠..

사용자 정보를 받을 수 있도록 먼저 전달하는 방법을 알아봅니다.

## 자식: 값 받을 준비하기

**fe/src/components/user.vue**  
```vue
<template>
  <v-card>
    <v-card-title primary-title>
      <h3 class="headline mb-0">{{str}}</h3>
    </v-card-title>
    
    <v-card-title primary-title>
      <h3 class="headline mb-0">{{obj.a}}</h3>
    </v-card-title>
  </v-card>
</template>
<script>
export default {
  props: [ 'str', 'obj' ]
}
</script>
```

자식 콤포넌트에 props를 넣어서 받을 수 있습니다.

단순 문자 str과 오브젝트형 obj를 넣어봤습니다.

## 부모: 값 전달하기

**fe/src/views/users.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6>
        <user str="abc" obj="{a:1}"></user>
      </v-flex>
      <v-flex xs12 sm6>
        <user str="def" :obj="{a:1}"></user>
      </v-flex>
      <v-flex xs12 sm6>
        <user :str="str" :obj="obj"></user>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import user from '@/components/user'

export default {
  components: { user },
  data () {
    return {
      obj: { a: 1 }
    }
  }
}
</script>
```

## 적용 화면

![alt props](/images/nemv/스크린샷 2018-11-07 오후 5.15.22.png)

str obj(바인드 되어 있지 않음) 와 :str :obj(바인드 되어 있음)가 뭐가 다른지 한 눈에 들어옵니다.

이제 뷰티파이의 v-btn v-card등이 어떻게 만들어 진 것인지 아시겠죠?

바로 자식 컴포넌트를 잘만드는 개발자들이죠. 

# 실제 데이터 넣어보기

## 자식: 값 받을 준비하기
**fe/src/components/user.vue**  
```vue
<template>
  <v-card>
    <v-card-title primary-title>
      <h3 class="headline mb-0">{{user.id}}</h3>
    </v-card-title>
    <v-divider light></v-divider>
    <v-card-text>
      <div>이름: {{user.name}}</div>
      <div>권한: {{user.lv}}</div>
      <div>나이: {{user.age}}</div>
      <div>로그인 횟수: {{user.inCnt}}</div>
    </v-card-text>
  </v-card>
</template>
<script>
export default {
  props: [ 'user' ]
}
</script>
```

사용자 오브젝트를 받아서 표시하게 만들었습니다.

## 부모: 값 전달하기

**fe/src/views/users.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6 v-for="user in users" :key="user._id">
        <user :user="user"></user>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import user from '@/components/user'

export default {
  components: { user },
  data () {
    return {
      users: []
    }
  },
  mounted () {
    this.getUsers()
  },
  methods: {
    getUsers () {
      this.$axios.get('manage/user')
        .then((r) => {
          this.users = r.data.users
        })
        .catch((e) => {
          console.error(e.message)
        })
    }
  }
}
</script>
```

기존 액시오스 요청으로 데이터를 가져왔습니다.

:user에 받은 데이터를 바인드 시켰습니다.

## 적용 화면
![alt user](/images/nemv/스크린샷 2018-11-07 오후 5.45.24.png)

잘 동작되고 있습니다.

# 혼란스러운 작명 변경하기

```vue
<user :user="user"></user>
```

이 구문을 보면 정말 혼란 스럽죠.

그래서 파일명은 user.vue로 해도 되지만 코드 내부에서는 조금 다르게 쓰는 것이 좋습니다.

그러려면 번거롭지만 카멜과 케밥을 어쩔 수 없이 알아야합니다.

html이 대소문자를 구분하지 못하기 때문입니다.

## 카멜 케이스

스크립트 내부에서는 대소문자 구분해서 vCard 로 사용합니다.

## 케밥 케이스

html에서는 v-card로 사용합니다.

대소문자가 갈릴 때 - 를 넣어주는 것이죠

## 변경해보기

**html**  
```vue
<user-card :user="user"></user-card>
```

**script**  
```javascript
import userCard from '@/components/user'

export default {
  components: { userCard },
  // ..
```

이제 좀 볼만해 졌습니다~

# 자식과 부모의 할일은?

이제 자식이 부모에게 값을 전달해야 할 차례인데..

먼저 자식이 부모에게 무언가를 전달할 상황은 언제인지 생각해봅시다.

## 부모의 할 일

부모는 공용으로 쓰는 자원을 가지고 있습니다.

예를들어 다이얼로그나 스낵바(하단에 메세지 띄우는 기능)를 띄울 때 자식들이 다 가지고 있을 필요가 없습니다.

액시오스로 사용자 명단을 가져오는 것과 공용 자원들을 사용하는 것이 어울립니다. 

## 자식의 할 일

자식은 부모로부터 단일 데이터를 받아서 혼자서 할 수 있는 액시오스로 수정/삭제를 하는 것이 어울립니다.

# 자식이 부모에게 값 전달하기

전달하는 방법은 **emit** 입니다.

조금 복잡하니 코드를 잘 봐야합니다. 

## 자식

**fe/src/components/user.vue**  
```vue
<template>
  <v-card>
    <v-card-title primary-title>
      <h3 class="headline mb-0">{{user.id}}</h3>
    </v-card-title>
    <v-divider light></v-divider>
    <v-card-text>
      <div>이름: {{user.name}}</div>
      <div>권한: {{user.lv}}</div>
      <div>나이: {{user.age}}</div>
      <div>로그인 횟수: {{user.inCnt}}</div>
    </v-card-text>
    <v-card-actions>
      <v-btn color="primary" @click="children">자식용</v-btn>
      <v-btn color="warning" @click="parent">부모용</v-btn>
    </v-card-actions>
    <v-card-text v-if="va">
      <v-alert v-model="va" dismissible>자식 혼자 떠들기</v-alert>
    </v-card-text>
  </v-card>
</template>
<script>
export default {
  props: [ 'user' ],
  data () {
    return {
      va: false,
    }
  },
  methods: {
    children () {
      this.user.name = 'xxx'
      this.va = true
    },
    parent () {
      this.$emit('sbOpen', '부모에게 알림')
    }
  }
}
</script>
```

- this.user.name 으로 쉽게 접근이 가능하니 추후에 수정/삭제는 자식 혼자 해도 됩니다.
- children 이벤트는 혼자 할 수 있는 것을 표현해보려고 만든 것입니다.
- parent 이벤트를 주목해야합니다. $emit으로 부모에게 알릴 때 sbOpen 이라는 함수명과 내용을 전달합니다.

## 부모

**fe/src/views/user.vue**  
```vue
<template>
  <v-container grid-list-md>
    <v-layout row wrap>
      <v-flex xs12 sm6 v-for="user in users" :key="user._id">
        <user-card :user="user" @sbOpen="pop"></user-card>
      </v-flex>
    </v-layout>
    <v-snackbar v-model="sb.act">{{sb.msg}}</v-snackbar>
  </v-container>
</template>
<script>
import userCard from '@/components/user'

export default {
  components: { userCard },
  data () {
    return {
      users: [],
      sb: {
        act: false,
        msg: 'xxxx'
      }
    }
  },
  mounted () {
    this.getUsers()
  },
  methods: {
    pop (msg) {
      this.sb.act = true
      this.sb.msg = msg
    },
    getUsers () {
      this.$axios.get('manage/user')
        .then((r) => {
          this.users = r.data.users
        })
        .catch((e) => {
          console.error(e.message)
        })
    }
  }
}
</script>
```

- 부모의 역할 샘플로 스낵바를 하나 만들었습니다.
- @sbOpen="pop" 이부분이 핵심입니다. 자식과의 통로입니다.
- sbOpen으로 전달 받아서 pop 함수를 실행 하는 것입니다. 

## 적용 화면

![alt cp](/images/nemv/스크린샷 2018-11-07 오후 7.30.18.png)

잘 동작되는 화면입니다.

# 마치며

자식 컴포넌트를 솔직히 처음에는 안쓰는 것이 좋다고 생각했습니다.

강좌 내용처럼 괜한 복잡함이 증가하기 때문이죠..

하지만 뷰가 추구하는 방향이기도 하고 코드가 길어지면 충분히 쓰일만합니다.

현재 부모-자식-자식-자식 까지도 넣어서 사용하고 있습니다.

만약 팀으로 일하면 분업하기도 좋고 코드 유지보수 부분이 상당히 좋아집니다.

연습 삼아 한번 해보시고 복잡하면 패스하시고 꼭 필요해질 날이 올때 분리작업 하셔도 괜찮습니다~

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/eb121d2f2635081c636e18e470108148d03ba7e8)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}
