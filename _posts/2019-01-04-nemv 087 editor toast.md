---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 87 위지웍 에디터 사용하기(toast ui editor)
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify,toast,editor,WYSIWYG]
comments: true
sidebar:
  nav: "nemv6"
---

위지웍 에디터를 사용해서 글을 작성해보겠습니다.

{% include toc %}
{% raw %}

대부분 커뮤니티 게시판에서 글을 쓸때 나오는 화면은 위지웍(WYSIWYG) 에디터입니다.

위지웍이란 WYSIWYG: "What You See Is What You Get"(보는 대로 얻는다) 라는 뜻이라네요.

단순 글만 쓰면 에디터는 필요 없지만(댓글등) MS워드 처럼 폰트 색상들을 조절해서 작성하려면(본문) html 태그를 사용해야하는데 매우 불편하죠..

eg) `<h4>제목</h4><p>내용 어쩌구..</p>`

# 에디터 선정

구글링을 해보면 다양한 에디터들(Ckeditor, Redactor, Summernote)이 있어서 이것 저것 써본 결과 대부분 한글이 문제가 되는 경우가 많았습니다.

최근에 nhn에서 무료로 npm에 공개한 것들이 꽤 있는데요(차트, 에디터등)

차트는 아직 반응형이 아니라 사용하기 싫었지만 에디터는 상당히 만족스럽네요.

nhn 개발진에서 만든 tui editor(toast ui editor)를 사용해보겠습니다.

# 에디터 정보

tui 에디터는 아래 공식 리포에서 설치할 수 있습니다.

[https://github.com/nhnent/tui.editor](https://github.com/nhnent/tui.editor)

위의 링크로 뷰에 적용하려면 콤포넌트화 시켜야하는데요.

최근 만든 모듈 답게 자스 프레임웤 삼대장(react,angular,vue) 포팅이 다되어있습니다.(채택의 가장 큰 이유)

[https://github.com/nhnent/toast-ui.vue-editor](https://github.com/nhnent/toast-ui.vue-editor)

미리 만들어진 콤포넌트만 끼워넣으면 끝입니다.

다른 장점은 최근 대 유행인 마크다운이 지원되서 매우 편리한 글작성을 할 수 있습니다.

마크다운 지난 포스트 참고: [마크다운이란](/github/markdown/)

깃헙 리포에 들어가면 맨 처음 뜨는 README.md 가 마크다운이죠..

# 에디터와 뷰어 동작 원리

일반적인 에디터에서 글을 작성할 때 볼드(글씨 굵게) 처리를 했다면 `<b>abc</b>` 로 저장되기 때문에 뷰에서는 v-html로 표현하면 됩니다.

그러나 tui 에디터는 마크다운으로 저장합니다.

마크다운으로 `**abc**` **가 양 쪽에 있으면 볼드입니다.

그래서 뷰어가 마크다운에서 html로 변경해서 표시해줘야하는 것입니다.

- 작성시: html을 마크다운 변환
- 열람시: 마크다운을 html로 변환

그래서 뷰어와 같이 사용해야합니다.

# 설치

```bash
$ cd fe && yarn add @toast-ui/vue-editor
```

# 화면에 넣어보기

**fe/src/views/test/lv3.vue**  
```vue
<template>
  <v-container fluid :grid-list-md="!$vuetify.breakpoint.xs">
    <v-layout wrap row>
      <v-flex xs12 sm6>
        <editor v-model="editorText"/>
      </v-flex>
      <v-flex xs12 sm6>
        <viewer :value="editorText" />
      </v-flex>
      <v-flex xs12>
        <span>{{editorText}}</span>
      </v-flex>
    </v-layout>
  </v-container>
</template>
<script>
import 'tui-editor/dist/tui-editor.css'
import 'tui-editor/dist/tui-editor-contents.css'
import 'codemirror/lib/codemirror.css'
import { Editor, Viewer } from '@toast-ui/vue-editor'

export default {
  components: {
    'editor': Editor,
    'viewer': Viewer
  },
  data () {
    return {
      editorText: ''
    }
  }
}
</script>
```

아무페이지나 선택하여 공식홈에 있는 소스대로 넣어봤습니다.

에디터와 뷰어를 나란히 두고 어떻게 동작하는 지 확인해봅니다.

아래에 디비에 저장될 값들도 확인해봅니다(span).

## 결과

![alt result](/images/nemv/2019-01-04_14.27.23.png)

아래 탭을 보면 마크다운 혹은 위지웍 둘다 작성이 가능합니다.

결국 디비에 저장되어야할 내용은 하단 표시된 내용입니다.

# 게시판 적용하기

## 에디터 공용으로 등록

먼저 어디서나 쓸 수 있게 메인에 등록합니다.

**fe/src/main.js**  
```javascript
import 'tui-editor/dist/tui-editor.css'
import 'tui-editor/dist/tui-editor-contents.css'
import 'codemirror/lib/codemirror.css'
import { Editor, Viewer } from '@toast-ui/vue-editor'

// ...
Vue.component('editor', Editor)
Vue.component('viewer', Viewer)
```

## 게시판 에디터와 뷰어 각각 변경

**fe/src/views/board.vue**  
```vue
<v-card light>
<!-- 뷰어 부분 -->
    <v-card-text>
      <p>내용</p>
      <!-- {{selArticle.content}} -->
      <viewer :value="selArticle.content" />
    </v-card-text>
    
<!-- 에디터 부분 -->
    <!-- <v-textarea
      label="내용"
      persistent-hint
      required
      v-model="form.content"
    ></v-textarea> -->
  <editor v-model="form.content"/>
</v-card>
```

v-card light를 주지 않으면 뷰티파이의 다크모드에서 잘 안보입니다. 

나머지는 입출력 부분만 간단히 변경했습니다.

## 결과 

**에디터**  
![alt editor](/images/nemv/2019-01-04_15.24.11.png)

**뷰어**  
![alt viewer](/images/nemv/2019-01-04_15.24.25.png)

**이미지 추가**  
![alt image add](/images/nemv/2019-01-04_15.27.46.png)

**이미지 추가 화면**  
![alt image mod](/images/nemv/2019-01-04_15.28.47.png)

글 안에 이미지를 삽입할 수도 있는데요

첨부가 아닐경우 역시나 그림을 베이스64로 만들어 버립니다.

# 마치며

무료인데도 매우 쓸만한 에디터입니다.

다만 조금 아쉬운 것은 뷰티파이의 다크모드와는 어울리지가 않아서 다크 테마에서는 클래스 재정의(오버라이드)를 하거나 화이트 카드 안에서 제대로 표시가 가능합니.

# 소스

[소스 확인: 에디터 테스트](https://github.com/fkkmemi/nemv3/commit/3ba31ae84971a8cf5cf730760e1e50c94a879f40)

[소스 확인: 에디터 게시판 적용](https://github.com/fkkmemi/nemv3/commit/647d4c5dd96390c47a42e11821e8ac412935050d)

{% endraw %}

# 영상

준비중

{% include video id="" provider="youtube" %}

