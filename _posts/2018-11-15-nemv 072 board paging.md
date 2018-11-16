---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 72 게시판 페이징과 정렬 처리하기
category: nemv
tag: [nemv,board,node,express,mongoose,vue,vuetify]
comments: true
sidebar:
  nav: "nemv5"
---

게시판 페이징과 정렬 처리를 해보겠습니다.

게시판 마지막 챕터라 내용이 조금 많지만 꼭 이해하셔야 됩니다..

{% include toc %}

{% raw %}

# 개요

게시판이 웹학습의 꽃인 이유가 바로 페이징 처리를 배울 수 있기 때문입니다.

페이징 처리의 이유는 많은 데이터를 처리하기 위함입니다.

그래서 페이징 처리는 꼭 게시판에서만 활용 되는 것이 아니고 많은 데이터가 예상되는 어떤 곳에서도 쓰이기 때문에 중요합니다.

# 페이징과 정렬을 위한 필수 요소

아래 요소들만 이해하고 있으면 끝입니다.

- 요청시
    - 스킵(skip): 건너 뛰기
    - 리미트(limit): 개수
    - 오더(order): 오름차(asc) 내림차(desc)
    - 정렬(sort): 정렬을 무엇으로 할지
- 응답시
    - 데이터들(datas[]): 실제 받는 데이터
    - 총데이터 개수(total): 전체 데이터 개수
    
# 예제로 필수 요소 알아보기

## 데이터 예시

**전체 데이터**

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 가나다 | abcd | xx |
| 나다라 | efg | xx |
| 다라마 | hijk | xx |
| 바사아 | lmn | xx |
| 자차아 | opqr | xx | 

데이터가 위와 같이 있을 경우 3개 단위로 잘라서 2페이지로 만든다면

**1페이지**

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 가나다 | abcd | xx |
| 나다라 | efg | xx |
| 다라마 | hijk | xx |

**2페이지**

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 바사아 | lmn | xx |
| 자차아 | opqr | xx | 

이렇게 구성됩니다.

데이터를 굳이 5개를 한꺼번에 안받고 3개씩 효율적으로 받은 것이죠.

여기서 필수요소가 어떻게 되는 지 확인해보겠습니다.

## 페이지별 필수 요소 상황

**1페이지 상황**

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 가나다 | abcd | xx |
| 나다라 | efg | xx |
| 다라마 | hijk | xx |

- 요청시
    - 스킵(skip): 0
    - 리미트(limit): 3
    - 오더(order): 오름차
    - 정렬(sort): 제목
- 응답시
    - 데이터들(datas[]): 3
    - 총데이터 개수(total): 5
    
**2페이지 상황**

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 바사아 | lmn | xx |
| 자차아 | opqr | xx | 

- 요청시
    - 스킵(skip): 3
    - 리미트(limit): 3
    - 오더(order): 오름차
    - 정렬(sort): 제목
- 응답시
    - 데이터들(datas[]): 2
    - 총데이터 개수(total): 5

## 4개 단위로 보기

| 제목 | 내용 | 기타 |
| --- | --- | --- |
| 자차아 | opqr | xx | 
| 바사아 | lmn | xx |
| 다라마 | hijk | xx |
| 나다라 | efg | xx |

- 요청시
    - 스킵(skip): 0
    - 리미트(limit): 4
    - 오더(order): 내림차
    - 정렬(sort): 제목
- 응답시
    - 데이터들(datas[]): 4
    - 총데이터 개수(total): 5
    
# 목록 불러오기 API

위의 데이터가 있다고 생각하고 실제 코드를 구현해보겠습니다.

**be/routes/api/article/index.js**  
```javascript
router.get('/list/:_board', (req, res, next) => {
  const _board = req.params._board
  let { search, sort, order, skip, limit } = req.query
  if (!(sort && order && skip && limit)) return res.send({ success: false, msg: '잘못된 요청입니다' })
  if (!search) search = ''
  order = parseInt(order)
  limit = parseInt(limit)
  skip = parseInt(skip)
  const s = {}
  s[sort] = order

  const f = {}
  if (_board) f._board = _board
  let total = 0

  Article.countDocuments(f)
    .where('title').regex(search)
    .then(r => {
      total = r
      return Article.find(f)
        .where('title').regex(search)
        .sort(s)
        .skip(skip)
        .limit(limit)
        .select('-content')
        .populate('_user', '-pwd')
    })
    .then(rs => {
      res.send({ success: true, t: total, ds: rs, token: req.token })
    })
    .catch(e => {
      res.send({ success: false, msg: e.message })
    })
})
```

- 프론트에서는 이제 5가지 정보를 (req.query)를 이용해 전달해야 됩니다.(_물음표 후 키=값 그리고(&) 조합)  
실제 요청: ```/api/article/list/5bea5d91264274045f8de41f?search=&skip=0&sort=_id&order=1&limit=5```
- 조건(!(sort && order && skip && limit))을 걸어서 유효성 판단을 합니다.(_undefined or null or 0 이면_)
- 문자열로 들어온 값들(order, skip, limit)을 숫자로 변경해줍니다.
- 몽고디비에서 정렬은 sort({ name: -1 or 1}) 로 처리됩니다.
- 몽고디비 소트의 1은 오름차 -1은 내림차입니다.(_eg: s[sort] = order 는 s._id = 1 이 되는 겁니다._)
- countDocuments는 데이터 개수를 구할 때 사용하는 메쏘드입니다.
- .where('title').regex(search) 을 이용해 패턴매치가 된것의 개수만 구합니다.(_eg: search='나' 일 경우 포함된 '가나다', '나다라' 2개가 매치됨_)
- 전체 개수(let total)를 받아놓습니다.
- 정렬(.sort(s)), 건너뛰기(.skip(skip)), 가져올 개수(limit(limit))를 검색쿼리에 추가합니다.
- 찾은 데이터들과 전체 데이터 개수를 전송합니다.   
실제 전송: { ds: [5개], t: 5 }

# 뷰 감시기능 필요한 만큼만 알아보기

## computed란?

참고: [https://kr.vuejs.org/v2/guide/computed.html](https://kr.vuejs.org/v2/guide/computed.html)

뷰 공식홈에 참고가 너무 잘되어 있지만 간단히 요약하면..

자동으로 계산해서 변수를 줄이는 방법입니다.

```javascript
<script>
export default {
  data () {
    return {
      a: 1,
      b: 2
    }
  },
  watch: {
    a () {
      console.log('change')
    }
  },
  methods: {
    aPlusBm () {
      return this.a + this.b
    },
    anyAction () {
      this.a = 5
    }
  },
  computed: {
    aPlusBc () {
      return this.a + this.b
    }
  },
  mounted () {
    console.log(this.a + this.b)
    console.log(this.aPlusBm())
    console.log(this.aPlusBc)
  }
}
</script>
```

3개 다 같은 결과입니다. 아직까진 큰차이가 없죠.

anyAction()으로 this.a 가 4가 된다면 aPlusBm()를 호출해줘야 하는 반면 aPlusBc는 그냥 바뀌어 집니다.

자동으로 감지해서 만들어 주기 때문에 편리하게 변수 처럼 쓸 수 있습니다.

## 변수감시하기 (watch)

```javascript
  data () {
    return {
      a: 1
    }
  },
  watch: {
    a () {
      console.log('change')
    }
  },
  methods: {
    anyAction () {
      this.a = 5
    }
  }
```

변수 값이 변경될 때를 감지할 수 있습니다.

검색어 변경시에 넣으면 되겠죠?

# 뷰티파이 데이터테이블의 페이지네이션(pagination) 변수에 대해

참고: [https://vuetifyjs.com/ko/components/data-tables](https://vuetifyjs.com/ko/components/data-tables)

공식홈 예제 대부분 pagination이라는 변수를 선언하고 :pagination.sync 에 바인드 시켜놓은 것을 확인 할 수 있습니다.

페이지네이션 구조는 공식홈에 이렇게 나와 있습니다.

```javascript
{
  descending: boolean
  page: number
  rowsPerPage: number // -1 for All
  sortBy: string
  totalItems: number
}
```

간단히 설명하면 위에서 배웠던 개념과 비슷합니다.

- descending: 내림차를 할 것인지 정합니다. (_백엔드 order_)
- page: 1~N 페이지 번호(_백엔드 skip(page-1)_)
- rowPerPage: 불러올 데이터 개수 입니다.(_백엔드 limit_)
- sortBy: 정렬 대상(_백엔드 sort_)
- totalItem: 전체 데이터 개수(_백엔드 countDocuments()_)

# 프론트 구현하기

**fe/src/views/board/anyone.vue**  
```vue
<template>
  <v-flex xs12 sm4 offset-sm8>
    <v-text-field
      label="검색"
      append-icon="search"
      v-model="params.search"
      clearable
    ></v-text-field>
  </v-flex>
  <v-flex xs12>
    <v-data-table
      :headers="headers"
      :items="articles"
      :total-items="pagination.totalItems"
      :pagination.sync="pagination"
      rows-per-page-text=""
      :loading="loading"
      class="text-no-wrap"
      disable-initial-sort>
      <template slot="items" slot-scope="props">
        <td :class="headers[0].class">{{ id2date(props.item._id)}}</td>
        <td :class="headers[1].class"><a @click="read(props.item)"> {{ props.item.title }}</a></td>
        <td :class="headers[2].class">{{ props.item._user ? props.item._user.id : '손님' }}</td>
        <td :class="headers[3].class">{{ props.item.cnt.view }}</td>
        <td :class="headers[4].class">{{ props.item.cnt.like }}</td>
      </template>
    </v-data-table>
    <div class="text-xs-center pt-2">
      <v-pagination v-model="pagination.page" :length="pages"></v-pagination>
    </div>
  </v-flex>
</template>
<script>

export default {
  data () {
    return {
      // ..
      pagination: {},
      dlMode: 0, // 0: read, 1: write, 2: modify
      selArticle: {},
      ca: false,
      params: {
        draw: 0,
        search: '',
        skip: 0,
        sort: '_id',
        order: 0,
        limit: 1
      },
      timeout: null
    }
  },
  watch: {
    pagination: {
      handler() {
        this.list()
      },
      deep: true
    },
    'params.search': {
      handler() {
        this.delay()
        // this.list()
      }
    }
  },
  computed: {
    setSkip () {
      if (this.pagination.page <= 0) return 0
      return (this.pagination.page - 1) * this.pagination.rowsPerPage
    },
    setSort () {
      let sort = this.pagination.sortBy
      if (!this.pagination.sortBy) sort = '_id'
      return sort
    },
    setOrder () {
      return this.pagination.descending ? -1 : 1
    },
    pages () {
      if (this.pagination.rowsPerPage == null ||
        this.pagination.totalItems == null
      ) return 0
      return Math.ceil(this.pagination.totalItems / this.pagination.rowsPerPage)
    },
  },
  methods: {
    // ..
    list () {
      if (this.loading) return
      if (!this.board._id) return
      this.loading = true
      this.params.draw ++
      this.params.skip = this.setSkip
      this.params.limit = this.pagination.rowsPerPage
      this.params.sort = this.setSort
      this.params.order = this.setOrder

      this.$axios.get(`article/list/${this.board._id}`, { params: this.params })
        .then(({ data }) => {
          if (!data.success) throw new Error(data.msg)
          this.pagination.totalItems = data.t
          this.articles = data.ds
          this.loading = false
        })
        .catch((e) => {
          this.pop(e.message, 'error')
          this.loading = false
        })
    },
    // ..
    delay () {
      clearTimeout(this.timeout)
      this.timeout = setTimeout(() => {
        this.list()
      }, 1000)
    }
  }
}
</script>
```

- pagination = {} 로 비워져있지만 알아서 채워집니다.  
초기값을 준다면 그것으로 시작하겠죠?(_eg: pagination.sortBy: 'title')
- computed된 값들
    - setSkip: pagination.page가 항상 1부터 시작하기 때문에 -1을 넣어서 skip은 0부터 시작합니다.
    - setSort: pagination.sortBy가 없을 때 예외처리만 해준 것입니다.
    - setOder: pagination.descending가 true와 false인데 몽고디비는 1과 -1이라서 바꿔준것입니다.
    - pages: 페이지 개수는 전체 개수 나누기 보고자 하는 개수입니다.(_Math.ceil은 소수 이하를 올려주는 자스 기본 기능입니다._)
- 요청하기
    - 이제 겟 요청시 query를 넣을 것인데 액시오스에서는 params라는 값에 넣어주면 됩니다.  
    아니면 직접 써줘도 똑같이 동작합니다.  
    ```javascript
    get('test/x?a=1&b=3') === get('test/x' { params: { a: 1, b: 3 } })
    ```
    - params 값은 computed에 의해 자동 계산되서 날아가게 됩니다.
    - this.params.draw를 백엔드에서 받지도 않는데 의미 없이 증가하는 이유는 브라우저중 같은 요청을 할때 막아버리는 경우가 있기 때문입니다.
    - 응답을 받고 전체 개수(this.pagination.totalItems)를 받아야 모든 페이징 계산이 되는 것입니다.
- 페이지 감시
    - pagination 변수가 바뀌기만 하면 목록이 갱신됩니다.(this.list())
    - deep: true는 깊숙히 다 감시한다는 것입니다. (eg:p.a.b.d)
- 검색하기
    - params.search의 값이 변경 될때 마다 watch에 들어오게 됩니다.
    - 검색어를 11111이라고 치면 5번 요청하는 것을 방지하기 위해 this.delay 함수로 지연 처리를 했습니다.
    
# 결과

**a 검색 화면**  
![alt search](/images/nemv/스크린샷 2018-11-16 오후 2.59.35.png)

**2페이지 화면**  
![alt page2](/images/nemv/스크린샷 2018-11-16 오후 2.59.46.png)
    
# 마치며

여기까지 잘 이해하고 오셨다면 이제 생산성만 필요하다고 생각합니다.

뭘 더 공부할 생각 보다는 쌓아온 지식을 바탕으로 무엇인가 먼저 새로 만들고나면 개선(코드 축약, 반복행위 줄이기등)하면서 자연스럽게 공부하게 됩니다..

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3/commit/75488ffcfd15f7520df6c946511cbe53f0e8cccf)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}
