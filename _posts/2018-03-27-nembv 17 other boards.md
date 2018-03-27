---
layout: single
title: NEMBV 17 다른 게시판 운영
category: nembv
tag: [nembv,api,mongoose,vue,bootstrap,bootstrap-vue]
comments: true
---

board와 comment라는 모델로 게시판을 구현해봤다.  이번에는 qna라는 게시판을 만들고 싶은데 또 복사해서 만들어야 할까?

당연히 대부분의 프로그래머라면 모듈 재사용을 염두하고 있을 것이다.

talk 와 qna 게시판을 둘로 나눠보도록 하겠다.

# api 변경

board에서 한계층 더 만들어서 나누었다. /api/data/board/talk 이런식이 된다.

- api
    - data
        - board
            - talk
            - qna
            
# model 재사용

**route/api/data/board/qna/ctrls.js**  
```javascript
const mongoose = require('mongoose');
const Board = require('../../../../../models/boards');
const Comment = require('../../../../../models/comments');

const boardSchema = Board.schema.obj;
const commentSchema = Comment.schema.obj;

boardSchema.cmt_ids = [{ type: mongoose.Schema.Types.ObjectId, ref: 'QnAComment' }];
commentSchema.bd_id = { type: mongoose.Schema.Types.ObjectId, ref: 'QnA', index: true, required: true };

const QnA = mongoose.model('QnA', boardSchema);
const QnAComment = mongoose.model('QnAComment', commentSchema);
```

**route/api/data/board/talk/ctrls.js**  
```javascript
const mongoose = require('mongoose');
const Board = require('../../../../../models/boards');
const Comment = require('../../../../../models/comments');

const boardSchema = Board.schema.obj;
const commentSchema = Comment.schema.obj;

boardSchema.cmt_ids = [{ type: mongoose.Schema.Types.ObjectId, ref: 'TalkComment' }];
commentSchema.bd_id = { type: mongoose.Schema.Types.ObjectId, ref: 'Talk', index: true, required: true };

const Talk = mongoose.model('Talk', boardSchema);
const TalkComment = mongoose.model('TalkComment', commentSchema);
```

- 참조될 필드만 재정의해주고 나머지는 재사용 해서 다양한 collection을 만들어 낼수 있다. 

- comment관련 api는 관리 차원에서 각 talks.ctrls, qna.ctrls 에 병합해버렸다. 

# html

**fe/src/components/page/board/talk.vue**  
```javascript
this.$axios.get(`${this.$cfg.path.api}data/board/talk`); // board schema
this.$axios.post(`${this.$cfg.path.api}data/board/talk/comment`, this.formCmt) // comment schema
```

- api path는 위와 같이 변경되었다.

# 테스트

동영상 만들기 귀찮아서 테스트 서버를 만들었다.

아래 경로로 가서 직접 눌러보면된다.

[http://fkkmemi.com:3000](http://fkkmemi.com:3000)

