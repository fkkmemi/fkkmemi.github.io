---
layout: single
title: NEMBV 14 Front-end finished
category: nembv
tag: [nembv,vue,bootstrap,bootstrap-vue,googlemap]
comments: true
---

이제 본격적으로 쓸만한 라이브러리들을 추가하여 화면을 구성해보도록 하겠다.

전처럼 하나 하고 리뷰하고 하나 하고 리뷰하기엔 좀 방대헤서 일단 전부 만들고 리뷰한다.

{% include toc %}

# 라이브러리 설치

## 목록

- moment: 시간관련
- sweetalert: alert 모양변경
- vue-awesome: font-awesome
- vue2-google-maps: 구글맵 사용
- fontawesome-markers: 구글맵 마커용

## 설치

### moment
```bash
$ npm i moment --save
```

### sweetalert
```bash
$ npm i sweetalert --save
```

### vue-awesome
```bash
$ npm i vue-awesome --save
```

### vue2-google-maps
```bash
$ npm i vue2-google-maps --save
```

### fontawesome-markers
```bash
$ npm i fontawesome-markers --save
```

## eslint 예외처리

몇가지 나랑 안맞는 부분들을 룰에서 무시하도록 설정하였다.

### eslint rule add
```javascript
    'no-debugger': process.env.NODE_ENV === 'production' ? 2 : 0, // 밑에 추가
    'no-underscore-dangle': ['error', { 'allow': ['_id'] }],
    'consistent-return': 0, // ['error', { 'treatUndefinedAsUnspecified': false }],
    'no-param-reassign': 0,
    // 'no-undef': 0,
    'no-undef': 0,
    'no-unused-vars': 0,
    'object-shorthand': 0,
```

## front-end cfg file 제작

**fe/static/cfg.js**
```javascript
module.exports = {
  path: {
    api : '/api/', // npm run build 용
    // api: 'http://localhost:3000/api/', // npm run dev 용
  },
};
```

> 빌드 별로 경로를 바꿔주면 사실 이건 안해도 되는데 아직 ~~webpack 사용이 미숙해서 일단 이런 파일이 필요하다..~~ - 빌드용 process 변수로 구분할 수 있다. 



# 설치된 라이브러리 추가

**fe/src/main.js**  
```javascript
// The Vue build version to load with the `import` command
// (runtime-only or standalone) has been set in webpack.base.conf with an alias.
import Vue from 'vue';
import BootstrapVue from 'bootstrap-vue';
import axios from 'axios';
import moment from 'moment';
import swal from 'sweetalert';
import Icon from 'vue-awesome/components/Icon';
import * as VueGoogleMaps from 'vue2-google-maps';
import App from './App';
import router from './router';

import '../node_modules/bootstrap/dist/css/bootstrap.min.css';
import '../node_modules/bootstrap-vue/dist/bootstrap-vue.css';
import '../node_modules/vue-awesome/icons';
import fam from '../node_modules/fontawesome-markers/fontawesome-markers.json';
import cfg from '../static/cfg';

moment.locale('ko');

if (process.env.NODE_ENV === 'development') cfg.path.api = 'http://localhost:3000/api/';

Vue.prototype.$axios = axios;
Vue.prototype.$cfg = cfg;
Vue.prototype.$moment = moment;
Vue.prototype.$swal = swal;
Vue.prototype.$fam = fam;

Vue.component('icon', Icon);

Vue.use(BootstrapVue);
Vue.use(VueGoogleMaps, {
  load: {
    key: 'AIzaSyBzlLYISGjL_ovJwAehh6ydhB56fCCpPQw',
    // OR: libraries: 'places,drawing'
    // OR: libraries: 'places,drawing,visualization'
    // (as you require)
  },
  // installComponents: true,
});

Vue.config.productionTip = true;

/* eslint-disable no-new */
new Vue({
  el: '#app',
  router,
  template: '<App/>',
  components: { App },
});
```  

- process.env.NODE_ENV 가 'production' 면 npm run build 'development' 면 npm run dev
- moment 표현 한글로 변경
- 전역으로 쓰기 위한 prototype 구성
- 하위 템플릿에서 icon 태그 쓰기 위해 등록
- 회사 위치를 표현하기 위한 googlemap init *해당 라이브러리 예제에 있는 키인데 테스트 되길래 쓰고 있다. 가지고 있는 키가 있다면 변경해서 쓰면된다*

# company 화면 구성

**fe/src/components/page/setting/company.vue**  
```html
<template>
  <div>
    <b-row class="mb-4">
      <b-col cols="4">
        <input type="text" v-model="search" class="form-control" id="input-text" placeholder="회사 검색">
      </b-col>
      <b-col>
      </b-col>
    </b-row>
    <b-alert variant="warning" show v-if="!d.ds.length">
      <h4><icon name="info"></icon> 새로 시작하셨나 봐요 회사가 하나도 없네요</h4>
      <p>회사추가를 눌러서 시작하세요!</p>
    </b-alert>
    <b-card-group deck class="mb-3" v-else>
      <b-card no-body
              v-for="(v, i) in d.ds"
              :sub-title="v.rmk"
              :key="v._id">
        <b-card-header>
            <h4>{ { v.name } }</h4>
        </b-card-header>
        <b-card-body>
          <p class="card-text">{ {v.rmk} }</p>
          <gmap-map
            :id="'map' + i.toString()"
            :center="v.pos"
            :zoom="16"
            style="height: 200px">
            <gmap-marker :position="v.pos" :icon="markerIcon"></gmap-marker>
            <gmap-circle
              :center="v.pos"
              :radius="100"
              :options="circleOptions"

            ></gmap-circle>

            <!--<gmap-marker v-for="(w, j) in v.sgr_ids" :key="w._id" v-for="(x, k) in w.sdv_ids" :key="x._id" :position="x.r.pos" :icon="markerIcon"></gmap-marker>-->
          </gmap-map>
        </b-card-body>
        <!--<b-list-group flush>-->
          <!--<b-list-group-item v-for="(w, j) in v.gr_ids" :key="w._id" class="d-flex justify-content-between align-items-center">-->
            <!--<span> { { w.name } } </span>-->
          <!--</b-list-group-item>-->
        <!--</b-list-group>-->

        <b-list-group flush>
          <b-list-group-item v-for="(gr, j) in v.gr_ids" :key="gr._id">
            <span> { { gr.name } } </span>
            <b-button-group class="float-right" size="sm">
              <b-btn variant="outline-warning" @click="modGr(gr)"><icon name="pencil"></icon></b-btn>
              <b-btn variant="outline-danger" @click="delGr(gr)"><icon name="trash"></icon></b-btn>
            </b-button-group>
          </b-list-group-item>
          <b-list-group-item>
            <span> 새그룹 추가 </span>
            <b-button-group class="float-right" size="sm">
              <b-btn variant="outline-success" @click="addGr(v)"><icon name="plus"></icon></b-btn>
            </b-button-group>
          </b-list-group-item>

        </b-list-group>

        <b-card-footer>
          <p>
            <small class="text-muted">{ { ago(v.ut) } }</small>
            <b-button-group class="float-right">
              <b-btn variant="outline-warning" @click="modalOpen(v)"><icon name="pencil"></icon></b-btn>
              <b-btn variant="outline-danger" @click="del(v)"><icon name="trash"></icon></b-btn>
            </b-button-group>
          </p>
        </b-card-footer>
      </b-card>
    </b-card-group>

    <b-row>
      <b-col>
        <b-btn variant="info" @click="list">새로고침</b-btn>
        <b-btn variant="success" @click="add" >회사 추가</b-btn>
      </b-col>
      <b-col>
        <b-pagination align="right" size="md" @input="list" :total-rows="d.cnt" v-model="page" :per-page="s.limit">
        </b-pagination>
      </b-col>
    </b-row>


    <b-modal ref="mdRef" :title="md.set.name">
      <b-row class="mb-2">
        <b-col>
          <b-input-group prepend="회사 이름">
            <b-form-input type="text" v-model="md.set.name"></b-form-input>
          </b-input-group>
        </b-col>
        <b-col>
          <b-input-group prepend="설명">
            <b-form-input type="text" v-model="md.set.rmk"></b-form-input>
          </b-input-group>
        </b-col>
      </b-row>
      <gmap-map
        :center="md.set.pos"
        :zoom="12"
        style="height: 200px"
        @click="mdSetPos">
        <gmap-marker :position="md.set.pos"></gmap-marker>
      </gmap-map>
      <div slot="modal-footer">
        <b-btn class="float-right" variant="primary" @click="mod(md.set)">
          저장
        </b-btn>
      </div>
    </b-modal>
  </div>
</template>

<script>

  export default {
    name: 'company',
    data() {
      return {
        page: 1,
        search: '',
        s: {
          draw: 0,
          skip: 0,
          limit: 3,
          order: 'name',
          sort: 1,
        },
        d: {
          draw: 0,
          cnt: 0,
          ds: [],
        },
        md: {
          set: {
            _id: '',
            name: '',
            rmk: '',
            pos: {
              lat: 37,
              lng: 127,
            },
          },
          // name: 'def',
        },
        markerIcon: {
          path: this.$fam.BUILDING_O,
          scale: 0.4,
          strokeWeight: 0.0,
          strokeColor: 'black',
          strokeOpacity: 1,
          fillColor: '#3276B1',
          fillOpacity: 0.9,
        },
        circleOptions: {
          strokeColor: '#1cc3ff',
          strokeOpacity: 0.8,
          strokeWeight: 2,
          fillColor: '#99fdff',
          fillOpacity: 0.35,
        },
      };
    },
    mounted() {
      this.list();
    },
    computed: {
      setSkip() {
        if (this.page <= 0) return 0;
        return (this.page - 1) * this.s.limit;
      },
    },
    methods: {
      swalSuccess(msg) {
        return this.$swal({
          icon: 'success',
          // button: false,
          title: '성공',
          text: msg,
          timer: 2000,
        });
      },
      swalWarning(msg) {
        return this.$swal({
          icon: 'warning',
          // button: false,
          title: '실패',
          text: msg,
          timer: 2000,
        });
      },
      swalError(msg) {
        return this.$swal({
          icon: 'error',
          // button: false,
          title: '에러',
          text: msg,
          timer: 2000,
        });
      },
      modalOpen(v) {
        this.md.set = v;
        this.$refs.mdRef.show();
      },
      ago(t) {
        return this.$moment(t).fromNow();
      },
      mdSetPos(m) {
        this.md.set.pos = m.latLng;
      },
      list() {
        this.$axios.get(`${this.$cfg.path.api}data/company`, {
          params: {
            draw: (this.s.draw += 1),
            search: this.search,
            skip: this.setSkip,
            limit: this.s.limit,
            order: this.s.order,
            sort: this.s.sort,
          },
        })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            this.d = res.data.d;
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('리스트를 불러올 수 없습니다.');
          });
      },
      add() {
        this.$swal({
          title: '회사 추가',
          content: 'input',
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '추가',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error('');
            return this.$axios.post(`${this.$cfg.path.api}data/company`, {
              name: res,
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('회사 추가 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('회사 이름을 입력하세요');
          });
      },
      del(v) {
        this.$swal({
          title: '회사 삭제',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '삭제',
            },
          },
        })
          .then((sv) => {
            if (!sv) throw new Error('');
            return this.$axios.delete(`${this.$cfg.path.api}data/company`, {
              params: { id: v._id },
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('회사 삭제 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('회사 삭제 취소');
          });
      },
      mod(v) {
        this.$swal({
          title: '회사 정보 변경',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '변경',
            },
          },
        })
          .then((sv) => {
            if (!sv) throw new Error('');
            return this.$axios.put(`${this.$cfg.path.api}data/company`, {
              _id: v._id,
              name: v.name,
              rmk: v.rmk,
              pos: v.pos,
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            this.$refs.mdRef.hide();
            return this.swalSuccess('회사 정보 변경 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('회사 정보 변경 취소');
          });
      },
      addGr(cp) {
        this.$swal({
          title: '그룹 추가',
          content: {
            element: 'input',
            attributes: {
              placeholder: '선릉',
            },
          },
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '추가',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error(''); // return; // swal('취소됨');
            return this.$axios.post(`${this.$cfg.path.api}data/group`, {
              name: res,
              cp_id: cp._id,
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('그룹 추가 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('그룹 이름을 입력하세요');
          });
      },
      modGr(gr) {
        this.$swal({
          title: '그룹 이름 변경',
          // dangerMode: true,
          content: {
            element: 'input',
            attributes: {
              placeholder: '소속1',
              value: gr.name,
            },
          },
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              className: 'red-bg',
              text: '변경',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error('');
            return this.$axios.put(`${this.$cfg.path.api}data/group`, {
              _id: gr._id,
              name: res,
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('그룹 변경 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) this.swalError(err.message);
            else this.swalWarning('그룹 변경 취소');
          });
      },
      delGr(gr) {
        this.$swal({
          title: '그룹 삭제',
          dangerMode: true,
          buttons: {
            cancel: {
              text: '취소',
              visible: true,
            },
            confirm: {
              text: '삭제',
            },
          },
        })
          .then((res) => {
            if (!res) throw new Error('');
            return this.$axios.delete(`${this.$cfg.path.api}data/group`, {
              params: { _id: gr._id },
            });
          })
          .then((res) => {
            if (!res.data.success) throw new Error(res.data.msg);
            return this.swalSuccess('그룹 삭제 완료');
          })
          .then(() => {
            this.list();
          })
          .catch((err) => {
            if (err.message) return this.swalError(err.message);
            this.swalWarning('그룹 삭제 취소');
          });
      },
    },
    watch: {
      search() {
        // this.inputSync();
        this.list();
      },
    },
    destroyed() {
    },
  };
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
```

> 우선 현재 이 블로그의 liquid 문법과 vue Mustache( { { } }) 가 겹쳐서 띄어 쓰여져있으니 카피했다면 붙혀야한다 ..

## html

- b-row, b-col: grid system의 기초인데 cols="4" 선언으로 4/12 영역에 검색창을 만들었다.
- b-alert: 주로 버튼등 액션있을 때 표시용, 데이터가 없을때 표시했다.
- b-card: nobody로 주면 패딩없이 꽉꽉한 내용이 담기는데 아래 b-card-body는 nobody로 부터 자유롭다., 회사 리스트만큼 루프를 돌려서 맵과 이름을 찍었다.
- gmap-map: google map canvas다 
- gmap-marker, gmap-circle: gmap-map 하위에서 동작한다 좌표만 넣으면 찍힌다.
- b-list-group: flush 되어 있으면 테두리 없이 카드에 붙는다. 회사에 속한 그룹들을 루프를 돌려서 이름을 취했다.
- b-card-footer: 하단에 회색 공간이 생긴다 회사를 수정할 수 있는 모달과 삭제할수 있는 버튼을 만들었다.
- b-modal: modal 레퍼런스를 만들어 두고 모달을 위한 변수로 핸들링된다. *원래 따로놀아야 하는 변수지만 번지만 넘겨서 시각적인 즐거움을 줘봤다*
- b-pagination: paging을 위한 바인드 부분을 채워주면 알아서 작동한다. 

## script

- data
    - page: b-pagination과 한쌍
    - search: 검색창
    - s: 서버에 페이징으로 전달할 값들
    - d: 수신된 데이터
    - md: modal에서 사용할 데이터
    - markerIcon: 기본 아이콘이 못생겨서 바꿈
    - circleOption: 옵션 안주면 검정색 원임
- mounted: 회사 리스트를 불러오는 부분
- computed
    - setSkip: page는 1부터 시작하지만 디비 입장에선 skip은 0부터 줘야하기 때문에 간단한 계산식 
- methods
    - swal 시리즈: 모양 좋은 알러트, 간단한 선택 및 입력창인데 매번 쓰기 지저분해서 만들어놓음
    - ago: 몇분전
    - mdSetPos: 모달 지도에서 온클릭 이벤트로 넘어온 좌표
    - list, add, mod, del: company api 에 대응하는 CRUD func.
    - addGr, modGr, delGr: group api 에 대응하는 CRUD func.

# 구현된 화면

직접 눈으로 확인하는게 좋을 것 같아서 영상으로 만들어봤다.

{% include video id="A92u4s2nCZ8" provider="youtube" %}    

# 전체 소스

[https://github.com/fkkmemi/nembv](https://github.com/fkkmemi/nembv)

# 결론

이로서 NEMBV 1편을 마친다. 

처음에 이곳에 기록하기 시작한 것은 나 자신이 까먹는 걸 방지하려 기록한 것인데..

항상 라이브러리들을 고맙게 쓰기만 했지 소스를 오픈한 적은 없는 것 같다. 

약 3일동안 업무 중간중간 만들었는데 다 만들고 나니 조금 후련해졌다.

이 소스가 도움이 될 만한 사람들이 많아졌으면 좋겠다.

다음에는 한국형 게시판과 jwt 인증등을 2편에서 이어 진행해보려한다.     