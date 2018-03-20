---
layout: single
title: NEMBV 11 Back-end and Front-end api test
category: nembv
tag: [nembv,nodejs,vue,api,bootstrap]
comments: true
---

fe에서 xhr로 최소한의 Debug를 할 수 있으므로 이제 be api를 실제 데이터로 채워 보겠다. 

# api에 실데이터 넣기

## be company 조회, 추가

**routes/api/data/company/ctrls.js**  
```javascript
const Company = require('../../../../models/companies');

exports.list = (req, res) => {
  // res.send({ success: false, msg: 'list 준비중입니다' });
  Company.find()
    .then(rs => res.send({ success: true, ds: rs }))
    .catch(err => res.send({ success: false, msg: err.message }));
};

exports.add = (req, res) => {
  // res.send({ success: false, msg: 'add 준비중입니다' });
  const { name, rmk } = req.body;

  if (!name) return res.send({ success: false, msg: '이름 없음' });
  if (!rmk) return res.send({ success: false, msg: '비고 없음' });
  const cp = new Company({ name: name, rmk: rmk });
  cp.save()
    .then(r => res.send({ success: true, d: r }))
    .catch(err => res.send({ success: false, msg: err.message }));
};

exports.mod = (req, res) => {
  res.send({ success: false, msg: 'mod 준비중입니다' });
};

exports.del = (req, res) => {
  res.send({ success: false, msg: 'del 준비중입니다' });
};
```

- data가 array type 일때는 ds, object type 일 때는 d 로 전송했다.
- add의 경우 추가 기능이기 때문에 post body가 제대로 안채워 있으면 못내려가게 했다.

## fe company 조회, 추가

post 부분에 회사명과 비고를 입력할 수 있는 폼을 2개 넣는다.

**fe/src/components/test.vue**  
```html
<!-- html -->
<b-card header="POST">
    <b-form-textarea v-model="txtPost" class="mb-3"
                     placeholder="이곳에 결과 값이 나옴"
                     :rows="3"
                     :max-rows="6">
    </b-form-textarea>
    <b-form-input v-model="txtPostName" class="mb-3"
                  type="text"
                  placeholder="회사명"></b-form-input>
    <b-form-input v-model="txtPostRmk" class="mb-3"
                  type="text"
                  placeholder="비고"></b-form-input>
    <b-button @click="sendPost"
              variant="primary">전송</b-button>
</b-card>

<!-- script -->
<script>
export default {
  name: 'test',
  data() {
    return {
      txtPost: '',
      txtGet: '',
      txtPut: '',
      txtDelete: '',
      txtPostName: '', // add
      txtPostRmk: '', // add
    };
  },
  methods: {
    sendPost() {
      const body = {
        name: this.txtPostName,
        rmk: this.txtPostRmk,
      };
      this.$axios.post('http://localhost:3000/api/data/company', body)
        .then((res) => {
          this.txtPost = JSON.stringify(res.data);
        })
        .catch((err) => {
          this.txtPost = JSON.stringify(err);
        });
    },
```

- 너무 길어서 post 부분만 html과 script 부분을 발췌했다.
- 결국 바디에 회사명과 비고를 전송하는 부분이고 나머진 그대로다.

## 각 서버 런
```bash
$ npm start # be server on
$ cd fe
$ npm run dev # fe server on
```

**출력된 화면**  
![test fe](/images/nembv/2018-03-20 17-07-43 nembv project.png)

## 시험

### get 전송

**return json**  
```json
{
  "success":true,
  "ds":[
    {
      "pos":{"lat":37.1,"lng":127.1},
      "type":0,
      "rmk":"신규",
      "gr_ids":["5aafac643ab6dc27e71e07af","5aafaedf408fdb27f5d6181c"],
      "ut":"2018-03-19T11:48:56.838Z",
      "_id":"5aafa3a8f8b7a8274f97560e",
      "name":"테스트",
      "__v":0
    }
  ]
}
```

- ds array에 '테스트'라는 회사 한개를 수신 받았다.

### post 전송

**return json**  
```json
{
  "success":true,
  "d":{
    "pos":{"lat":37.1,"lng":127.1},
    "type":0,
    "rmk":"bbb",
    "gr_ids":[],
    "_id":"5ab0c231a26bd3382b30440d",
    "name":"aaa",
    "ut":"2018-03-20T08:11:29.718Z",
    "__v":0
  }
}
```

- d object에 'aaa'라는 회사 한개를 수신 받았다.

### 다시 get 전송

**return json**  
```json
{
  "success":true,
  "ds":[
    {
      "pos":{"lat":37.1,"lng":127.1},
      "type":0,
      "rmk":"신규",
      "gr_ids":["5aafac643ab6dc27e71e07af","5aafaedf408fdb27f5d6181c"],
      "ut":"2018-03-19T11:48:56.838Z",
      "_id":"5aafa3a8f8b7a8274f97560e",
      "name":"테스트",
      "__v":0
    },
    {
      "pos":{"lat":37.1,"lng":127.1},
      "type":0,
      "rmk":"bbb",
      "gr_ids":[],
      "ut":"2018-03-20T08:11:29.718Z",
      "_id":"5ab0c231a26bd3382b30440d",
      "name":"aaa",
      "__v":0
    }
  ]
}
```

- ds array에 '테스트','aaa' 회사 두개를 수신 받았다.

**화면 확인**  
![test fe2](/images/nembv/2018-03-20 17-18-58 nembv project.png)



