---
layout: single
title: NEMBV 18 부드러운 게시판
category: nembv
tag: [nembv,api,mongoose,vue,bootstrap,bootstrap-vue]
comments: true
sidebar:
  nav: "nembv"
---

> 이 강좌는 종료되었습니다.  
새로운 강좌로 시작하세요~  
[모던웹(NEMV) 제작 강좌](/nemv/){: .btn .btn--success}  

글을 작성하거나 수정하면 테이블을 갱신하게 되어있다. 댓글을 작성후 갱신되어버려서 영 보기가 좋지 않다. 그리고 vue스럽지도 않고..

table provider로 관리되는 row라는 객체를 이용해서 필요할 때만 갱신이 되게 변경해보겠다.

{% include toc %}

# 각 행위들 정리

| methods	| 기능 | table 갱신 |
| --- | --- | ---  |
| add	| 글 작성 | O |
| read	 | 글 읽기 | X |     
| mod	 | 글 수정 | X |     
| del	 | 글 삭제 | O |     
| addCmt | 댓글 추가 | X |     
| modCmt | 댓글 수정 | X |     
| delCmt | 댓글 삭제 | X |     

글 작성과 글 삭제는 테이블의 행 개수가 바뀌기때문에 갱신이 자연스러우나 나머지는 그렇지 않다.

# 테이블 row data에 필요한 변수 선언

**fe/src/components/page/board/talk.vue**  
```javascript
data() {
  return {
    row: {},
    rowCmt: {},
  };
},
```

modal을 이용해 명령이 submit되기 때문에 중간 포인터 변수가 필요하다.

> 포인터는 c/c++ 에서의 int* ptr; 같은 것인데 자스에서는 어떤 명칭인지 모르겠다. 

# 글 수정

글 수정은 이미 _id를 알고 있고 본인이 작성한 데이터를 본인만 보기 때문에 데이터 치환만 하면된다.

## modal open

```html
<b-btn variant="outline-warning" @click="mdModOpen(row.item)"><icon name="pencil"></icon></b-btn>
```

## data 치환

```javascript
mdModOpen(v) {
    this.form._id = v._id;
    this.form.id = v.id;
    this.form.title = v.title;
    this.form.content = v.content;
    this.row = v;
    this.$refs.mdMod.show();
},
```

this.row의 주소를 row.item으로 변경한다.

> 값이 복제되는 것이 아닌 주소 변경이다 즉 참조. object = object 이기 때문

## data 변경

```javascript
mod() {
    then(() => {
        this.$refs.mdMod.hide();
        this.row.id = this.form.id;
        this.row.title = this.form.title;
        this.row.content = this.form.content;
    })
}
```

form에 입력된 값들(본인이 작성한 값)을 해당 row의 값으로 대입한다. 

> 서버로부터 수정되었다고 응답 받았으므로 굳이 서버의 갱신된 값을 대입할 필요는 없다. 

# 댓글 수정

## modal open

```html
<b-btn variant="outline-warning" @click="mdModCmtOpen(row.item, cmt)"><icon name="pencil"></icon></b-btn>
```

소속된 row와 현재 댓글을 넘겨서 모달을 띄운다.

## data 치환

```javascript
mdModCmtOpen(r, v) {
    this.formCmt.bd_id = r._id;
    this.formCmt._id = v._id;
    this.formCmt.id = v.id;
    this.formCmt.content = v.content;
    this.row = r;
    this.rowCmt = v;
    this.$refs.mdModCmt.show();
},
```

this.row(게시물), this.rowCmt(댓글) 의 주소를 각각 변경

## data 변경

```javascript
modCmt() {
    then(() => {
        this.$refs.mdModCmt.hide();
        this.rowCmt.id = this.formCmt.id;
        this.rowCmt.content = this.formCmt.content;
        this.rowCmt.ut = new Date();
    })
}
```

- formCmt에 입력된 값들(본인이 작성한 값)을 해당 댓글의 값으로 대입한다.  
- rowCmt.ut는 갱신시간인데 서버와 몇미리초 차이가 있을수 있으나 큰 의미가 없기 때문에 현재 시간으로 대체한다.

# 댓글 삭제

## modal open

```html
<b-list-group-item v-for="(cmt, ci) in row.item.cmt_ids" :key="cmt._id">
  <b-btn variant="outline-danger" @click="delCmt(row.item, cmt, ci)"><icon name="trash"></icon></b-btn>
</b-list-group-item>
``` 

굳이 모달을 띄울 필요 없는 행위기 때문에 바로 실행하는데 해당 로에서 삭제할 댓글 인덱스가 필요하다

## data 변경

```javascript
delCmt(r, cmt, i) {
    then(() => {
        r.cmt_ids.splice(i, 1);
    })
}
```

해당 로우의 댓글중 지울 인덱스 값만 배열에서 제거한다.

# 댓글 추가

댓글 추가는 _id를 얻어야 하기 때문에 백엔드에서 추가후 전체 내용을 전송해야한다.

## api server

**routes/api/data/board/talk/ctrls.js**  
```javascript
let cr;
  cmt.save()
    .then((r) => {
      cr = r; // add
      const f = { _id: r.bd_id };
      const s = { $addToSet: { cmt_ids: r._id }};
      return Talk.updateOne(f, s);
    })
    .then((r) => {
      if (!r.nModified) return res.send({ success: false, msg : 'already Talk' });
      res.send({ success: true, d: cr }); // add d
    })
    .catch((err) => {
      res.send({ success: false, msg : err.message });
    });
```

기존 success: true에 d를 추가했다.

## modal open

```html
<b-btn variant="outline-success" @click="mdAddCmtOpen(row.item)"><icon name="plus"></icon></b-btn>
```

## data 치환

```javascript
mdAddCmtOpen(r) {
    this.formCmt.bd_id = r._id;
    this.formCmt._id = '';
    this.formCmt.id = '';
    this.formCmt.content = '';
    this.row = r;
    this.$refs.mdAddCmt.show();
},
```

this.row의 주소를 row.item으로 변경한다.

## data 추가

```javascript
addCmt(evt) {
    evt.preventDefault();
    this.$axios.post(`${this.$cfg.path.api}${this.path}/comment`, this.formCmt)
      .then((res) => {
        if (!res.data.success) throw new Error(res.data.msg);
        this.row.cmt_ids.push(res.data.d);
        return this.swalSuccess('댓글 추가 완료');
      })
      .then(() => {
        this.$refs.mdAddCmt.hide();
      })
      .catch((err) => {
        this.swalError(err.message);
      });
},
```

- 수정, 삭제와는 다르게 서버로 부터 받은 값을 추가 해야되는 이유는 _id 때문이다.
- _id 없이 row.cmt_ids에 추가되었다면 댓글 추가후 수정/삭제가 안되기 때문이다.

# 그밖에 바꾼 것들

```javascript
name: 'qna',
data() {
      return {
        path: 'data/board/qna',
        
// use eg)
this.$axios.delete(`${this.$cfg.path.api}${this.path}/comment`, {
```

패스를 등록해서 이제 새 게시판 복제는 name과 path만 변경하면 된다. *추후 템플릿으로 만들 것*

# 테스트

아래 경로로 가서 직접 테스트해보면 됨

[http://fkkmemi.com:3000](http://fkkmemi.com:3000)