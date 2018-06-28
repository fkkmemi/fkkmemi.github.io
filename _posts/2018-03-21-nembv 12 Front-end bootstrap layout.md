---
layout: single
title: NEMBV 12 Front-end bootstrap layout
category: nembv
tag: [nembv,nodejs,vue,bootstrap]
comments: true
sidebar:
  nav: "nembv"
---

나중을 위해 최소한의 메뉴바등을 추가한 화면을 먼저 만들어 놓고 실제 돌아갈 페이지를 추가해보도록 하겠다.

{% include toc %}

# 파일 추가
편의를 위해 아래와 같이 구조를 변경하였다.  
![src struct](/images/nembv/2018-03-21 10-03-32 nembv.png)

- layout에 top/left등의 고정된 페이지를 넣었다.
- page에 페이지들을 추가
- layout을 사용하는 페이지들은 따로 디렉토리를 만들어 두었다.
- 404에러 페이지등은 레이아웃 없이 표시되는 느낌을 살리기위해 상단에 두었다. 

## 시작 페이지 레이아웃 변경

**fe/src/app.vue**  
```html
<template>
  <div id="app">
    <b-container fluid class="px-0">
      <router-view></router-view>
    </b-container>
  </div>
</template>
<script>
  export default {
    name: 'app',
  };
</script>
<style>
</style>
```

- b-container fluid는 고정폭이아닌 유동 폭을 쓰겠다는 것이다.
- router-view 컨테이너에 index.vue가 들어 갈 것이다.

## 메뉴를 포함한 껍데기 생성

**fe/src/components/index.vue**  
```html
<template>
  <div>
    <top></top>
    <b-container fluid>
      <router-view></router-view>
    </b-container>
  </div>
</template>

<script>
  import top from '../layout/top';
  // import left from '../layout/left';

  export default {
    name: 'index',
    components: {
      top,
      // left,
    },
  };
</script>

<style scoped>
</style>
```

- layout을 포함한 껍데기다. *app.vue 에 들어가도 될 내용이긴 하지만 편의를 위해 하부에 두었다.*

## 상단 메뉴 추가

**fe/src/components/layout/top.vue**  
```html
<template>
  <div>
    <b-navbar toggleable="md" type="dark" variant="secondary">

      <b-navbar-toggle target="nav_collapse"></b-navbar-toggle>

      <b-navbar-brand href="#/">
        nembv prj
        <!--<img src="../../assets/logo.png" alt="logo" width="100"/>-->
      </b-navbar-brand>

      <b-collapse is-nav id="nav_collapse">
        <b-navbar-nav>
          <b-nav-item-dropdown text="환경설정">
            <topItem link="company" icon="sss">
              회사 관리
            </topItem>
            <topItem link="group" icon="sss">
              그룹 관리
            </topItem>
          </b-nav-item-dropdown>
          <b-nav-item-dropdown text="테스트">
            <topItem link="test" icon="sss">
              test
            </topItem>
            <topItem link="hello" icon="sss">
              hello-vue
            </topItem>
          </b-nav-item-dropdown>
          <b-nav-item href="#" disabled>추후예정</b-nav-item>
        </b-navbar-nav>

        <!-- Right aligned nav items -->
        <b-navbar-nav class="ml-auto">
          <b-nav-item-dropdown text="Lang" right>
            <b-dropdown-item href="#">한국어</b-dropdown-item>
            <b-dropdown-item href="#">English</b-dropdown-item>
          </b-nav-item-dropdown>

          <b-nav-item-dropdown right>
            <!-- Using button-content slot -->
            <template slot="button-content">
              <em>User</em>
            </template>
            <b-dropdown-item href="#">Profile</b-dropdown-item>
            <b-dropdown-item href="#">Signout</b-dropdown-item>
          </b-nav-item-dropdown>
        </b-navbar-nav>

      </b-collapse>
    </b-navbar>

    <b-breadcrumb :items="this.$route.meta.breadcrumb"/>
  </div>
</template>

<script>
  import topItem from './topItem';

  export default {
    name: 'top',
    components: { topItem },
  };
</script>

<style scoped>
</style>
```

- b-navbar: 주로 top menu용으로 쓰인다.
    - toggleable="md": md size 이하에서 아디다스로 접는 다는 것이다. *md size는 레퍼런스 사이트를 찾아보자 기억이 안난다*
    - type="dark": light로 하면 하얗다. 
    - variant="secondary": booststrapV4에서 추가된 색상. *다른 색은 너무 강렬해서 .. eg)primary, danger*
- topItem에 link와 icon을 전달한다. *icon은 fontawesome 등을 쓸려고 우선 넣어둔 것*
- b-nav-item-dropdown: 이하는 공식사이트 내용을 그대로 가져왔다.
- b-breadcrumb: 주로 (홈 > 고객관리 > q&a) 이런 거 표시하는 콤포넌트다
    - :items="this.$route.meta.breadcrumb": 라우트할때 메타정보를 기입하여 바인딩 할 것이다.

### 상단 메뉴 아이템 추가

**fe/src/components/layout/topMenu.vue**  
```html
<template>
  <router-link :to="link" tag="b-dropdown-item" exact-active-class="active" exact>
    <slot></slot>
  </router-link>
</template>

<script>
  export default {
    name: 'topItem',
    props: ['link', 'icon'],
    mounted() {
    },
  };
</script>

<style scoped>
</style>
```

- top.vue에서 내려온 item이다
- exact-active-class="active" exact 으로 해당 메뉴 페이지 진입시에 불이 들어오는 효과를 준다.  
> 실제 출력은 <li class="active"> 가 된다.

## router 추가

**fe/src/router/index.js**  
```javascript
import Vue from 'vue';
import Router from 'vue-router';
import Hello from '@/components/page/Hello';
import index from '@/components/page/index';
import intro from '@/components/page/intro';
import e404 from '@/components/page/e404';
import company from '@/components/page/setting/company';
import group from '@/components/page/setting/group';
import test from '@/components/page/dev/test';

Vue.use(Router);

export default new Router({
  routes: [
    {
      path: '/',
      name: 'index',
      component: index,
      children: [
        {
          path: '/',
          name: 'intro',
          component: intro,
          meta: {
            title: 'NEMBV introduce',
            breadcrumb: [{
              text: 'Welcome',
              href: '/',
            }],
          },
        },
        {
          path: '/company',
          name: 'company',
          component: company,
          meta: {
            title: '회사 관리',
            breadcrumb: [{
              text: '환경설정 > 회사 관리',
              href: '/',
            }],
          },
        },
        {
          path: '/group',
          name: 'group',
          component: group,
          meta: {
            title: '그룹 관리',
            breadcrumb: [{
              text: '환경설정 > 그룹 관리',
              href: '/',
            }],
          },
        },
        {
          path: '/test',
          name: 'test',
          component: test,
          meta: {
            title: 'test',
            breadcrumb: [{
              text: '개발자 > test',
              href: '/',
            }],
          },
        },
      ],
    },
    {
      path: '/hello',
      name: 'Hello',
      component: Hello,
    },
    {
      path: '*',
      name: 'e404',
      component: e404,
    },
  ],
});
```

- 만들어 둔 페이지를 라우트에 등록하였다. *여기 없으면 동작하지 않는다*
- /로 들어갈 경우 자식요소중 /인 intro로 들어간다.

## 하위 페이지 추가

**fe/src/components/page/intro.vue**  
```html
<template>
  <div>
    <b-jumbotron header="NEMBV Stack" lead="Node.js Express.js MongoDB BootstrapVue" >
      <p>넴비 프로젝트에 오신 것을 환영합니다.</p>
      <b-btn variant="primary" href="#">더보기</b-btn>
    </b-jumbotron>
  </div>
</template>

<script>
export default {
  name: 'intro',
};
</script>
```

- b-jumbotron을 이용한 인트로 화면이다

**fe/src/components/page/setting/company.vue**  
```html
<template>
  <div>
    <h1>company...</h1>
  </div>
</template>

<script>
export default {
  name: 'company',
  data() {
    return {
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

- 우선 껍데기만 만들어두었다.

**fe/src/components/page/setting/group.vue**  
```html
<template>
  <div>
    <h1>group...</h1>
  </div>
</template>

<script>
export default {
  name: 'group',
  data() {
    return {
    };
  },
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

- 역시 껍데기만 만들어두었다.

**fe/src/components/page/e404.vue**  
```html
<template>
  <div>
    <h1>{ { msg } }</h1>
  </div>
</template>

<script>
export default {
  name: 'e404',
  data() {
    return {
      msg: '그런 페이지 없어요..',
    };
  },
};
</script>
```

- 없는 페이지 들어왔을때 설명 페이지다. *머시태그가 '{ {' 으로 한칸 떨어져 있으니 복사후 붙혀야한다*

# 시험

## localhost:8080 접속시 시작 페이지
![t1](/images/nembv/2018-03-21 10-33-33 nembv project.png)

## b-navbar 동작 상황
![t2](/images/nembv/2018-03-21 10-35-26 nembv project navbar.png)

## 환경설정 > 회사관리 b-breadcrumb 기능과 라우팅
![t3](/images/nembv/2018-03-21 10-37-58 nembv project company.png)

## 기존 test 페이지 이사 확인
![t4](/images/nembv/2018-03-21 10-38-57 nembv project test.png)

## 404페이지 확인
![t5](/images/nembv/2018-03-21 10-48-47 nembv project xxx.png)

# 결론
bootstrap을 쓰기로 마음 먹었다면 가급적 custom style은 넣지 말고 기존 클래스를 재정의 해서 써야한다.

추한 결과라도 우선 만들면서 느낌을 받아야 시작이 된다. 

이 프로젝트의 의미는 node도 bootstrap도 vue도 아니고 nembv stack을 이용해 전반적인 웹플로를 익히기를 희망하는 것이다.

https://github.com/fkkmemi/nembv 에 소스는 있으나 단순 클론 후 수정하기 보다는 한땀한땀 만들면서 느낌을 받기를 추천한다.



