---
layout: single
title: Electron 3 일렉트론뷰로 우아한 데스크탑 앱 만들기 ToDo
category: electron
tag: [electron,talk,idea,nodejs,vue,vuetify,todo]
comments: true
sidebar:
  nav: "electron"
---

과정이 지겨우니 뭔가 만들면서 재미를 찾는 것이 훨씬 개발에 득이 된다고 생각합니다..

그래서 일렉트론뷰의 마지막 강좌인 투두앱(일명 GTD 할일관리)을 재미로 만들어보려합니다.

단순 재미입니다. 그저 지난 강좌에서 쓰인 db&ui가 어떻게 쓰일까 정도의 예시 정도입니다.(_당연히 구글이나 애플에서 만들어 둔거 쓰시는 게 좋죠._)

> GTD는 매우 중요하다고 생각합니다..(나이가 들 수록 더..)  
시간 되시는 분은 [GTD에 대하여](/talk/gtd/) 를 한번 읽어보시기 바랍니다.
 
연습앱들을 만들며 숙달된 피지컬, 개발력, 기능을 이용해 좋은 아이디어로 좋은 작품 만드시길 기원합니다..  

{% include toc %}

{% raw %}

# 설치: 또 시작!

저는 유난히 새 프로젝트 만들 때 희열을 느끼는데요..

지난날의 과오도 엎고.. 새로운 문법, 언어등으로 기존과의 비교도 할 수 있어서 매우 흥분됩니다.

린트(lint: 코드 검사툴) 같은 경우도 새프로젝트를 진행해봐야 airbnb가 나은지 standard가 나은지 차이를 알 수 있겠죠..

예전에는 기존 제작한 것을 챗바퀴 돌 듯 유지보수 한 적도 많았었는데요.. 차라리 새로 만들고 기존 코드를 새 형식으로 고치는 게 여러모로 편하더라고요.. 

## 깃 리포 생성 및 다운로드

깃 없이 하실 분은 패스하세요

우선 깃 저장소를 만들어 봅니다.

![alt git](/images/electron/2018-07-09 13-19-36 Create a New Repository.png)

```bash
$ git clone git@github.com:fkkmemi/todo.git
```

todo라는 디렉토리가 생성되며 README.md 파일 달랑 하나 있습니다.

## 일렉트론뷰 생성

챕터1 에서 다루었던 내용 그대로 설치 진행합니다. 

```bash
$ vue init vuetifyjs/electron todo

? Target directory exists. Continue? Yes
? Application Name todo
? Project description An electron-vue project
? Select which Vue plugins to install axios, vue-electron, vue-router, vuex
? Use linting with ESLint? Yes
? Which eslint config would you like to use? Standard
? Setup unit testing with Karma + Mocha? Yes
? Setup end-to-end testing with Spectron + Mocha? Yes
? What build tool would you like to use? builder
? author fkkmemi <fkkmemi@gmail.com>

   vue-cli · Generated "todo".

---

All set. Welcome to your new electron-vue project!

Make sure to check out the documentation for this boilerplate at
https://simulatedgreg.gitbooks.io/electron-vue/content/.

Next Steps:

  $ cd todo
  $ yarn (or `npm install`)
  $ yarn run dev (or `npm run dev`)
```

## 깃 추가

깃 없이 하실 분은 패스하세요

yarn등으로 의존요소 설치나 빌드 전에 깃 추가해 놓는 게 편합니다.

node_modules or build등 디렉토리 깃 예외 걸기 귀찮기 때문이죠.

```bash
$ git add ./
```

## 의존 요소 설치

뷰티파이 + 일렉트론등 package.json에 지정된 요소들이 깔리죠..

조금 의아한것은 "vuetify": "1.0.0" 앞에 ^가 없어서 1.0 고정인데요..

이걸 "vuetify": "^1.0.0" 바꾸고 yarn을 해서 1.1.1이 되었지만.. 호환성 문제가 있는 것 같았습니다.(_종종 콘솔에 에러가 뜸_)

^가 앞에 있다는 것은 x.y.z 라는 버전중 y z는 올라가도 상관 없다는 것입니다.

별수 없이 구닥다리 1.0으로 작업했습니다..

```bash
$ yarn
```

# 레이아웃

불필요한 기본 요소를 빼고.. 상큼한 색상으로 툴바를 바꿔봤습니다.

[https://vuetifyjs.com/ko/style/colors](https://vuetifyjs.com/ko/style/colors)

```#AEEA00``` 이런식으로 쓰지 말고 lime accent-4 정해진 네이밍을 사용하세요

색상이나 테마는 위에 색상중 골라야 잘 어울립니다..

**App.vue**  
```vue
<template>
  <div id="app">
    <v-app>
      <v-navigation-drawer
        fixed
        v-model="drawer"
        app
      >
        <v-list>
          <v-list-tile 
            router
            :to="item.to"
            :key="i"
            v-for="(item, i) in items"
            exact
          >
            <v-list-tile-action>
              <v-icon v-html="item.icon"></v-icon>
            </v-list-tile-action>
            <v-list-tile-content>
              <v-list-tile-title v-text="item.title"></v-list-tile-title>
            </v-list-tile-content>
          </v-list-tile>
        </v-list>
      </v-navigation-drawer>
      <v-toolbar fixed app dark color="pink lighten-2">
        <v-toolbar-side-icon @click.native.stop="drawer = !drawer"></v-toolbar-side-icon>
        <v-toolbar-title v-text="title"></v-toolbar-title>
        <v-spacer></v-spacer>
        <v-btn icon>
          <v-icon>search</v-icon>
        </v-btn>
        <v-btn
          icon
        >
          <v-icon>more_vert</v-icon>
        </v-btn>
      </v-toolbar>
      <v-content>
        <v-container fluid fill-height>
          <v-slide-y-transition mode="out-in">
            <router-view></router-view>
          </v-slide-y-transition>
        </v-container>
      </v-content>
    </v-app>
  </div>
</template>

<script>
  export default {
    name: 'todo',
    data: () => ({
      drawer: false,
      items: [
        { icon: 'apps', title: 'Welcome', to: '/' },
        { icon: 'bubble_chart', title: 'Inspire', to: '/inspire' }
      ],
      title: 'ToDo'
    })
  }
</script>

<style>
  @import url('https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons');
  /* Global CSS */
</style>
```

![alt toolbar](/images/electron/2018-07-10 13-46-44 todo.png)

툴바가 블링블링 하네요

컬러 속성을 color="pink lighten-2"로 줬기 때문이죠..


## 배치

WelcomeView.vue에 v-list 콤포넌트를 꽉차게 넣어보겠습니다.

[https://vuetifyjs.com/ko/components/lists](https://vuetifyjs.com/ko/components/lists)

위에 예제중 아무거나 넣은 것기 때문에 따로 코드는 작성하지 않습니다.

![alt layout](/images/electron/2018-07-10 14-06-35 todo.png) 

배치를 확인하기 위해 dark list를 넣어봤는데요..

정말 맘에 안드네요 꽉 차지도 않는데다가 리스트 동서남북에 공간이 있네요..

![alt css](/images/electron/2018-07-10 14-30-26 Image.png)

우측에 개발자 모드는 요렇게 쓰는 것입니다. container div 까지 마우스로 콕콕 찍어서 오면

패딩이 16이나 있다는 것을 알 수 있습니다.

저도 html css 전문가는 아니지만 어쩔 수 없이 알아야하는 것 2가지는 바로 마진, 패딩 귀찮은 2요소입니다..(_가끔 보더도 신경써야됨_)

어떤 html 요소든 저런 식입니다.

마우스를 가져다가 여기저기 찔러보시면 2요소가 대충 뭔지 아실 겁니다.

어떤 개체의 안쪽이 패딩이고 바깥쪽이 마진인 것 같습니다..

우선 패딩을 빼고 싶네요..

vuetify는 위의 색상 정의 처럼 마진 패딩등을 축약된 단어로 이미 만들어 놨답니다..

[https://vuetifyjs.com/ko/layout/spacing](https://vuetifyjs.com/ko/layout/spacing)

위의 링크에 정말 자세히도 나와있고.. 테스트도 가능하죠~

```html
<v-container fluid class="pa-0">
```

![alt pa-0](/images/electron/2018-07-10 14-39-15 todo.png)

그중 꽉차게 하기 위해 v-container를 fill-height를 제거하고 패딩 없는 클래스를 입히면 딱 들어 꽉 차게 됩니다.

# 데이터 설계

간단하게 한번 작성해봤습니다.

- rt: new Date(), // regist time: 등록 시간
- t: new Date(), // time: 목표 시간
- type: '직장', // 
- title: '빨래 널기', // 제목
- comment: '그만하고 싶다..', // remark: 비고
- done: false // 

샘플로 이런 구조를 가지게 되는거죠..

# UI 설계

UI를 대충 구성하고 데이터 하나만 넣고 함 돌려봅니다..

vuetify에서 참조한 콤포넌트는 list, dialog, picker 정도 입니다.

예제에서 가져온 것을 조금 손본 정도 입니다.

**WelcomeView.vue**  
```vue
<template>
  <v-layout>
    <v-flex xs12>
      <v-list two-line>
        <template v-for="(td, i) in todos">
          <v-list-tile
              :key="td.title"
              avatar
              @click=""
          >
            <v-list-tile-avatar>
              <v-icon :color="td.done ? 'success' : 'secondary'">{{td.done ? 'check' : 'hourglass_empty'}}</v-icon>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title v-html="td.title"></v-list-tile-title>
              <v-list-tile-sub-title v-html="td.content"></v-list-tile-sub-title>
            </v-list-tile-content>
            <v-list-tile-action>
              <v-btn icon ripple>
                <v-icon color="grey lighten-1">info</v-icon>
              </v-btn>
            </v-list-tile-action>
          </v-list-tile>

        </template>
      </v-list>
    </v-flex>

    <v-dialog
        v-model="dialog"
        width="500"
    >
      <v-btn
          color="primary"
          dark
          fab
          fixed
          bottom
          right
          slot="activator"
      >
        <v-icon>edit</v-icon>
      </v-btn>
      <v-card>
        <v-card-title>
          <span class="headline">할일 작성</span>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid" lazy-validation>
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field label="제목" required counter="40" prepend-icon="title"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field label="내용" hint="굳이 넣고 싶다면.." counter="40" prepend-icon="note"></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuDate"
                      :close-on-content-click="false"
                      v-model="menuDate"
                      :nudge-right="40"
                      :return-value.sync="date"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="date"
                        label="목표 날짜"
                        prepend-icon="event"
                        readonly
                    ></v-text-field>
                    <v-date-picker v-model="date" @input="$refs.menuDate.save(date)"></v-date-picker>

                  </v-menu>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuTime"
                      :close-on-content-click="false"
                      v-model="menuTime"
                      :nudge-right="40"
                      :return-value.sync="time"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      max-width="290px"
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="time"
                        label="목표 시간"
                        prepend-icon="access_time"
                        readonly
                    ></v-text-field>
                    <v-time-picker
                        v-if="menuTime"
                        v-model="time"
                        @change="$refs.menuTime.save(time)"
                    ></v-time-picker>
                  </v-menu>
                </v-flex>

                <v-flex xs12 sm6>
                  <v-select
                      :items="['집', '직장', '밖']"
                      label="종류"
                      required
                  ></v-select>
                </v-flex>

              </v-layout>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="dialog = false">Close</v-btn>
          <v-btn color="blue darken-1" flat @click.native="save()">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
  </v-layout>
</template>

<script>
  export default {
    name: 'welcome',
    data () {
      return {
        todos: [
          {
            rt: new Date(),
            t: new Date(),
            type: 0,
            title: '빨래 널기',
            content: '그만하고 싶다...',
            done: false
          }
        ],
        menuDate: false,
        menuTime: false,
        date: null,
        time: null,
        valid: false,
        dialog: false
      }
    },
    methods: {
      save () {
        this.dialog = false
      }
    }
  }
</script>

<style scoped>
</style>

```

- v-layout으로 기본 골격을 잡고

- v-flex x12로 리스트를 꽉 차게 구현했습니다.

- v-list도 어찌보면 v-card와 비슷한 느낌으로 접근 하시면 됩니다.  
v-list-tile 하나가 v-card 하나 같은 것입니다.  
v-list-tile = v-card  
v-list-tile-avatar = v-card-title  
v-list-tile-content = v-card-text  
v-list-tile-action = v-cart-action  

- v-list 안에서 totos 만큼 반복해서 데이터를 찍게 되는데요  
template으로 돌리고 있는데 사실 v-list-tile 에서 v-for 반복 시켜도 됩니다.  
template은 실제 표시되는 html코드가 아닙니다.  
v-list-tile을 돌릴 때 조건에 따라 다르게 표시하거나.. i, j, k로 2중 3중 배열 데이터를 표시할 때..    
template같은 유령 태그를 넣어서 작업하면 됩니다. 

- v-list-tile-avatar는 사진이나 아이콘이 들어가기 적합합니다.

- 컬러와 아이콘을 done의 약소한 조건식과 바인딩해서 표시됩니다.  
뭐가 done이 되었을지 어울리는지 아래 아이콘 링크를 보며 직접 화면에 뿌려보면서 확인해보는 것이 좋습니다.  
[https://material.io/tools/icons/?icon=hourglass_empty&style=outline](https://material.io/tools/icons/?icon=hourglass_empty&style=outline)  
eg)  

```html 
<v-icon color="primary">schedule</v-icon>
<v-icon color="red">query_builder</v-icon>
```

- dialog는 v-model="dialog" 의 변수 하나로 켜지고 꺼집니다.  
메쏘드에서는 this.dialog = true 만 해주면 켜집니다.  
특이한 부분은 dialog 아래에 있는 v-btn 속성중 slot="activator" 부분인데요. 이것은 dialog의 슬롯에 활성버튼이 되겠다는 것입니다.(_dialog=true_)  

- dialog 아래에 v-btn은 둥그렇고 항상 하단아래에 둥둥 떠있게 되는데 이런걸 머터리얼 디자인의 핵심인 플로팅 액션 버튼(fab: floating action button)이라고 합니다.  
그저 옵션 몇개만 주면 단순 버튼이 저렇게 쉽게 fab가 되죠..  

- dialog 안의 내용은 폼으로 덮었습니다.  
모양새를 위해 v-card로 만들었고요..  
역시 v-card-title에 제목을 넣었고..  
v-card-text에 실제 폼을 넣었습니다.  
v-card-action에 저장이나 취소를 넣어 v-card 정석대로 작업했습니다.

- v-form에 ref라는 태그가 있는데요 v-menu에도 있고요..  
이것은 말 그대로 참조며 메쏘드 등에서 ref명을 참고해서 어떤 행위를 할 수 있습니다.  
(_jquery를 해보신 분은 셀렉터 역활을 한다고 생각하심 됩니다. eg: $('#anyid').show()_)  
v-form 유효성 검사등을 할 때 필요할 것 같네요

- 날짜, 시간 선택기  
[https://vuetifyjs.com/ko/components/date-pickers](https://vuetifyjs.com/ko/components/date-pickers) 를 그대로 가져왔습니다.  

- 구현한 결과

![alt git](/images/electron/2018-07-11 08-50-29 none.png)

![alt git](/images/electron/2018-07-11 08-51-12 todo.png)

- 약간의 버그 픽스

플로팅 버튼 안의 아이콘이 가운데 딱 안떨어지고 상단에 붙는 버그가 있네요 1.1.1은 문제 없는데..

원래는 node_modules - vuetify - dist - 어딘가.. - ??.css 에 height/width가 inherit(상속) 으로 되어있는데..

어쩔 수 없이 css 오버라이딩(재정의)을 했습니다.

**App.vue**  
```vue
<style>
  @import url('https://fonts.googleapis.com/css?family=Roboto:300,400,500,700|Material+Icons');
  /* Global CSS */
  .btn--floating .icon {
    height: 32px;
    width: 32px;
  }
</style>
```

나중에 일렉트론뷰가 업뎃될때 지워주면 되겠죠..

아래 링크에서 참고했습니다.. (요럴 때 구글링을 사용해서 간편하게 임시방편 때웁니다~)

[https://github.com/vuetifyjs/vuetify/issues/3433](https://github.com/vuetifyjs/vuetify/issues/3433)

검색 키워드: v-btn fab bug

> 구글링도 중요한데 당연히 검색을 뭘로 하느냐입니다. 디비를 해보면 알겠지만 자연어는 검색이 어렵습니다. 에러코드와 단어 조합이 좋습니다.  
eg)  
X: i have problem with.. 어쩌고  
O: electron build error 14021

# 데이터 추가

## 날짜 라이브러리 설치

우선 날짜관련 라이브러리 모먼트를 설치 해줍니다.

[https://momentjs.com](https://momentjs.com)

```bash
$ yarn add moment
```

**main.js**  
```javascript
import moment from 'moment'
moment.locale('ko')
Vue.prototype.$moment = moment
```

프로토타입 설정으로 이제 아무 페이지에서나 moment를 사용할 수 있습니다.

moment는 날짜관련 연산, 포멧팅을 편하게 해줍니다.(_eg:2월27에서 3월 2일까지 남은 날짜, 시간, 분, 초등으로 구할 수 있습니다. 윤달도 있어서 2월은 참 곤란하죠_)

> 예전에 펌웨어를 만들때는 gps로 부터 받은 utc 시간에 9시간 더하는 것을 c코드로 일일이 다 구현했던 기억이 납니다.  
2018 2월 28일 18시 더하기 9시간은? if ((day%4) == 0) day++; 이런식으로 말이죠..  
time.h 같은 거 적용했으면 쉽게 끝날 것을 ... 좋은 경험도 했었죠.. 자스 moment(time).add('hours', 9) 면 끝이네요..

## 화면에 데이터 추가 적용

이제 껍데기 뿐인 폼에 실제 바인딩될 데이터를 넣고 화면에 뿌려지게 하겠습니다.

**WelcomeView.vue**  
```vue
<template>
  <v-layout>
    <v-flex xs12>
      <v-list two-line>
        <template v-for="(td, i) in todos">
          <v-list-tile
              :key="td.title"
              avatar
              @click=""
          >
            <v-list-tile-avatar>
              <v-icon :color="td.done ? 'success' : 'secondary'">{{td.done ? 'check' : 'hourglass_empty'}}</v-icon>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>
                <v-icon>
                  {{types[td.type].icon}}
                </v-icon>
                <span>
                  {{td.title}}
                </span>
                <small class="text--primary">
                  기한: {{fromNow(td.t)}}
                </small>
              </v-list-tile-title>
              <v-list-tile-sub-title>
                <span>
                  {{td.content}}
                </span>
                <small class="text--primary">
                  작성: {{fromNow(td.rt)}}
                </small>
              </v-list-tile-sub-title>
            </v-list-tile-content>
            <v-list-tile-action>
              <v-btn icon ripple>
                <v-icon color="grey lighten-1">info</v-icon>
              </v-btn>
            </v-list-tile-action>
          </v-list-tile>

        </template>
      </v-list>
    </v-flex>
    <v-btn
        color="primary"
        dark
        fab
        fixed
        bottom
        right
        @click="openDialog"
    >
      <v-icon>edit</v-icon>
    </v-btn>

    <v-dialog
        v-model="dialog"
        width="500"
    >

      <v-card>
        <v-card-title>
          <span class="headline">할일 작성</span>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid">
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field label="제목" required counter="40" prepend-icon="title" v-model="form.title"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field label="내용" hint="굳이 넣고 싶다면.." counter="40" prepend-icon="note" v-model="form.content"></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuDate"
                      :close-on-content-click="false"
                      v-model="menuDate"
                      :nudge-right="40"
                      :return-value.sync="date"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="date"
                        label="목표 날짜"
                        prepend-icon="event"
                        readonly
                    ></v-text-field>
                    <v-date-picker
                        v-model="date"
                        locale="ko"
                        @input="$refs.menuDate.save(date)"
                    ></v-date-picker>
                  </v-menu>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuTime"
                      :close-on-content-click="false"
                      v-model="menuTime"
                      :nudge-right="40"
                      :return-value.sync="time"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      max-width="290px"
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="time"
                        label="목표 시간"
                        prepend-icon="access_time"
                        readonly
                    ></v-text-field>
                    <v-time-picker
                        v-if="menuTime"
                        v-model="time"
                        locale="ko"
                        @change="$refs.menuTime.save(time)"
                    ></v-time-picker>
                  </v-menu>
                </v-flex>

                <v-flex xs12 sm6>
                  <v-select
                      :items="types"
                      label="종류"
                      v-model="form.type"
                      item-text="text"
                      item-value="value"
                      prepend-icon="map"
                      required
                  ></v-select>
                </v-flex>

              </v-layout>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="dialog = false">Close</v-btn>
          <v-btn color="blue darken-1" flat @click.native="save()">Save</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
        :timeout="snackbar.timeOut"
        v-model="snackbar.act"
        :color="snackbar.color"
    >
      {{ snackbar.text }}
      <v-spacer></v-spacer>
      <v-btn flat color="white" @click.native="snackbar.act = false">닫기</v-btn>
    </v-snackbar>
  </v-layout>
</template>

<script>
  export default {
    name: 'welcome',
    data () {
      return {
        todos: [],
        form: {
          rt: new Date(),
          t: new Date(),
          type: 0,
          title: '',
          content: '',
          done: false
        },
        menuDate: false,
        menuTime: false,
        date: null,
        time: null,
        valid: false,
        dialog: false,
        types: [
          { value: 0, text: '집', icon: 'home' },
          { value: 1, text: '회사', icon: 'store' },
          { value: 2, text: '밖', icon: 'timeline' }
        ],
        snackbar: {
          act: false,
          text: '',
          color: 'success',
          timeOut: 5000
        }
      }
    },
    computed: {
      dt2date () {
        if (!this.date || !this.time) return null
        return this.$moment(this.date + ' ' + this.time).toDate()
      }
    },
    methods: {
      pop (msg, cl, t) {
        this.snackbar.act = true
        this.snackbar.text = msg
        this.snackbar.color = cl
        this.snackbar.timeOut = t
      },
      save () {
        this.dialog = false
        const td = {
          rt: new Date(),
          t: this.dt2date,
          type: this.form.type,
          title: this.form.title,
          content: this.form.content,
          done: false
        }
        this.todos.push(td)
        this.pop('잘 등록 되었습니다.', 'success', 5000)
      },
      fromNow (rt) {
        return this.$moment(rt).fromNow()
      },
      openDialog () {
        this.$refs.form.reset()
        this.dialog = true
      }
    }
  }
</script>

<style scoped>
</style>

```

{% endraw %}

## 시연 영상

미완성이지만 대충 어떤 느낌인지 영상으로 보는 게 나을 것 같아서 영상을 넣었습니다.(_대충 보세요_)

{% include video id="Xbuijv2lHk0" provider="youtube" %}    

## 설명

- 데이터를 보여주기 위해 리스트에 지저분하게 많이 넣은 것입니다.  
리스트는 심플 이즈 베스트이기 때문에 데이터 확인되면 딱 필요한 데이터만 넣는 게 좋습니다.

- v-model로 각 폼요소에 데이터들을 바인딩 시켰습니다. form.x는 이제 화면과 같이 놉니다.

- computed 라는 것이 있는데요 위 처럼 따로 변수를 쓰고 싶지 않으면서 뭔가 조합을 해줄 때 사용하면 됩니다.  
[https://kr.vuejs.org/v2/guide/computed.html](https://kr.vuejs.org/v2/guide/computed.html)

- ref 활용  
위에서 언급한 참조를 dialog를 열때 폼을 초기화 해주는데 사용했습니다.  
form.title = '' 이런식으로 초기화 할 수도 있지만 유효성 판단 넣어주면 에러난 것 처럼 보여지기 때문입니다.
초기화를 위해 fab 버튼을 dialog slot에서 바깥으로 뺐습니다.
  
```javascript
this.$refs.form.reset()
```

# 디비 연동

이제 CRUD(Create Read Update Delete) 를 해서 어플이 다시 켜져도 데이터가 유지되도록 해보겠습니다.

## database 설치

지난 강좌 참고

## CRUD

약간의 UI 변형과 이해가 쉽도록 메쏘드를 작명해봤습니다.

{% raw %}

```vue
<template>
  <v-layout>
    <v-flex xs12>
      <v-list two-line>
        <template v-for="(td, i) in todos">
          <v-list-tile
              :key="td.title"
              avatar
              @click=""
          >
            <v-list-tile-avatar>
              <v-icon color="info">{{types[td.type].icon}}</v-icon>
            </v-list-tile-avatar>

            <v-list-tile-content>
              <v-list-tile-title>
                <span>
                  {{td.title}}
                </span>
                <small class="text--primary">
                  기한: {{fromNow(td.t)}}
                </small>
              </v-list-tile-title>
              <v-list-tile-sub-title>
                <span>
                  {{td.content}}
                </span>
                <small class="text--primary">
                  작성: {{fromNow(td.rt)}}
                </small>
              </v-list-tile-sub-title>
            </v-list-tile-content>
            <v-list-tile-action>
              <v-btn icon ripple @click="dbUpdate(td)">
                <v-icon :color="td.done ? 'success' : 'secondary'">check</v-icon>
              </v-btn>
              <v-btn icon ripple @click="dbDelete(td._id)">
                <v-icon color="error">delete</v-icon>
              </v-btn>
            </v-list-tile-action>
          </v-list-tile>

        </template>
      </v-list>
    </v-flex>
    <v-btn
        color="pink"
        dark
        fab
        fixed
        bottom
        right
        @click="openDialog"
    >
      <v-icon>edit</v-icon>
    </v-btn>

    <v-dialog
        v-model="dialog"
        width="500"
    >

      <v-card>
        <v-card-title>
          <span class="headline">할일 작성</span>
        </v-card-title>
        <v-card-text>
          <v-form ref="form" v-model="valid">
            <v-container grid-list-md>
              <v-layout wrap>
                <v-flex xs12>
                  <v-text-field label="제목" required counter="40" prepend-icon="title" v-model="form.title"></v-text-field>
                </v-flex>
                <v-flex xs12>
                  <v-text-field label="내용" hint="굳이 넣고 싶다면.." counter="40" prepend-icon="note" v-model="form.content"></v-text-field>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuDate"
                      :close-on-content-click="false"
                      v-model="menuDate"
                      :nudge-right="40"
                      :return-value.sync="date"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="date"
                        label="목표 날짜"
                        prepend-icon="event"
                        readonly
                    ></v-text-field>
                    <v-date-picker
                        v-model="date"
                        locale="ko"
                        @input="$refs.menuDate.save(date)"
                    ></v-date-picker>
                  </v-menu>
                </v-flex>
                <v-flex xs12 sm6>
                  <v-menu
                      ref="menuTime"
                      :close-on-content-click="false"
                      v-model="menuTime"
                      :nudge-right="40"
                      :return-value.sync="time"
                      lazy
                      transition="scale-transition"
                      offset-y
                      full-width
                      max-width="290px"
                      min-width="290px"
                  >
                    <v-text-field
                        slot="activator"
                        v-model="time"
                        label="목표 시간"
                        prepend-icon="access_time"
                        readonly
                    ></v-text-field>
                    <v-time-picker
                        v-if="menuTime"
                        v-model="time"
                        locale="ko"
                        @change="$refs.menuTime.save(time)"
                    ></v-time-picker>
                  </v-menu>
                </v-flex>

                <v-flex xs12 sm6>
                  <v-select
                      :items="types"
                      label="종류"
                      v-model="form.type"
                      item-text="text"
                      item-value="value"
                      prepend-icon="map"
                      required
                  ></v-select>
                </v-flex>

              </v-layout>
            </v-container>
          </v-form>
        </v-card-text>
        <v-card-actions>
          <v-spacer></v-spacer>
          <v-btn color="blue darken-1" flat @click.native="dialog = false">취소</v-btn>
          <v-btn color="blue darken-1" flat @click.native="dbCreate()">저장</v-btn>
        </v-card-actions>
      </v-card>
    </v-dialog>
    <v-snackbar
        :timeout="snackbar.timeOut"
        v-model="snackbar.act"
        :color="snackbar.color"
    >
      {{ snackbar.text }}
      <v-spacer></v-spacer>
      <v-btn flat color="white" @click.native="snackbar.act = false">닫기</v-btn>
    </v-snackbar>
  </v-layout>
</template>

<script>
  export default {
    name: 'welcome',
    data () {
      return {
        todos: [],
        form: {
          rt: new Date(),
          t: new Date(),
          type: 0,
          title: '',
          content: '',
          done: false
        },
        menuDate: false,
        menuTime: false,
        date: null,
        time: null,
        valid: false,
        dialog: false,
        types: [
          { value: 0, text: '집', icon: 'home' },
          { value: 1, text: '회사', icon: 'store' },
          { value: 2, text: '밖', icon: 'timeline' }
        ],
        snackbar: {
          act: false,
          text: '',
          color: 'success',
          timeOut: 5000
        }
      }
    },
    mounted () {
      this.dbRead()
    },
    computed: {
      dt2date () {
        if (!this.date || !this.time) return null
        return this.$moment(this.date + ' ' + this.time).toDate()
      }
    },
    methods: {
      pop (msg, cl, t) {
        this.snackbar.act = true
        this.snackbar.text = msg
        this.snackbar.color = cl
        this.snackbar.timeOut = t
      },
      fromNow (rt) {
        return this.$moment(rt).fromNow()
      },
      openDialog () {
        this.$refs.form.reset()
        this.dialog = true
      },
      dbCreate () {
        this.dialog = false
        const td = {
          rt: new Date(),
          t: this.dt2date,
          type: this.form.type,
          title: this.form.title,
          content: this.form.content,
          done: false
        }
        if (!this.$refs.form.validate()) return this.pop('값이 유효하지 않습니다', 'error', 60000)
        this.$db.insert(td, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.dbRead()
          this.pop('저장 성공', 'success', 5000)
        })
      },
      dbRead () {
        this.$db.find({}, (err, rs) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.todos = rs
        })
      },
      dbUpdate (td) {
        this.$db.update({ _id: td._id }, { $set: { done: !td.done } }, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.dbRead()
        })
      },
      dbDelete (_id) {
        this.$db.remove({ _id }, (err) => {
          if (err) return this.pop(err.message, 'error', 60000)
          this.dbRead()
          this.pop('삭제 성공', 'success', 5000)
        })
      }
    }
  }
</script>

<style scoped>
</style>

```

{% endraw %}
## 시연 영상

CRUD가 잘 되는 지 영상으로 확인해보세요.(_대충 보세요_)

{% include video id="Ad_PMssKw44" provider="youtube" %}   

## 설명

CRUD를 보여주기 위해 직접 디비 핸들링을하고 읽고 있지만.. 사실 권장사항은 내부적으로 한번 받고 변경사항만 패치하는 것이 좋습니다.

1000개의 할일이 있다면 매번 1000개의 할일을 읽어오는 구조입니다.

- dbCreate(): 기존 save() 를 dbCreate()로 변경했습니다.  
쓰고 읽어봅니다. 

- dbRead(): 조건이 비었기 때문에 다 읽어옵니다.

- dbUpdate(): item 하나를 인자로 넘겨서 done 만 토글시킵니다.

- dbDelete(): { _id } 는 { _id: _id }와 같습니다. 해당 _id만 찾아서 지우는 것이죠..

CRUD 작업은 허무하지만 이게 다네요

title 이나 content 수정은 직접 dialog 개조해서 테스트 해보시기 바랍니다.

# 빌드

꼭 appId를 유니크하게 변경해야합니다.

저번에 변경 안했더니 기존 설치 앱이 지워져버리고 만든게 깔리더라고요..  

**package.json**  
```javascript
"build": {
    "productName": "todo",
    "appId": "com.memi.todo",
```

전 대충 이렇게 변경했습니다.

```bash
$ yarn build
```

# 원본 소스

[https://github.com/fkkmemi/todo](https://github.com/fkkmemi/todo)

# 결론

이것으로 일렉트론뷰 강좌를 마치겠습니다..

정말 미칠듯한 4시간의 작성이었네요...

좌모니터는 글작성

중모니터는 코딩

우모니터는 결과물 확인

뭐 하다보니 재밌어서 더 진행하고 싶긴한데.. 2부로 넘어갈까 하다가.. 적당한 숙제로 남겨놓고 떠납니다.(_다음에는 모바일이 출격 준비중입니다._)

우아하게 만들기로 제목 지어놓고 우아하지 못한 비쥬얼 죄송합니다...

하지만 머터리얼디자인([https://material.io/design/](https://material.io/design/))으로 구현되어 있기 때문에..

잘만 배치하면 절대 구닥다리 소리는 듣지 않을 겁니다..

어떤 분들이 가끔 초고수라고 말씀해주시는데.. 

그건 이쪽 생태계 종사하시는 분들을 약간은 모욕하는 평가가 될 수 있습니다..

진정한 고수들은 이미 현업에서 더 복잡하게 구현해서 개발중입니다..

고수가 작성한 강좌도 아니고.. 사실 고수가 되고 싶지도 않고요..

오히려 초보개발자가 '처음 접하는 사람도 쉽게 만들 수 있다~' 를 검증하고 나누고 싶을 뿐입니다..

그 중.. 직접 제가 쓰면서 찾아보면서 개발 하기 때문에...  

어느 타이밍에 구글링을 하는 지 레퍼런스 사이트를 얼마나 잘 이용하는지를 알려드리는 게 주 목적입니다. 

강좌 보신분들은 느끼겠지만.. 거의 대부분의 참고는 공식사이트 3개입니다. 

vuetify, vue, electron 죄다 한글이라 정말 좋습니다.

특히 요새 공식사이트의 예제는 절대 무시하면 안됩니다.(_예전에는 진짜 참고만 했었죠 쓸만한 코드가 없어서_)

나름 엄격한 룰을 적용한 샘플코드들입니다. 기이한 습관 지저분한 배치 같은 게 없는 아주 퓨어하면서도 권장 소스니 잘 배껴쓰시기 바랍니다.
