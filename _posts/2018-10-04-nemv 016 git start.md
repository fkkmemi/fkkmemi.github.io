---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 16 깃 사용하기
category: nemv
tag: [nemv,vue,vuetify,git,express,node]
comments: true
sidebar:
  nav: "nemv2"
---

지금까지 프로젝트를 깃을 사용해 다시 만들어 보겠습니다.

{% include toc %}

# 깃허브 가입

깃헙 가입하신 분이나 다른 서비스(깃랩, 혹은 자체서버) 쓰시는 분은 패스

# 깃허브 저장소 생성

저장소는 리포지토리라고 합니다.

![alt github](/images/nemv/2018-10-05 14-58-35 github.png)

리포 이름과 설명을 씁니다.

public은 공개한다는 것이며 무료입니다.

private는 공개하지 않는 것이며 유료입니다.

readme 파일은 무조건 만드는 것이 좋습니다. 마크다운 문법으로 설명을 작성 할 수 있습니다.

Add .gitignore 는 파일을 추가한 다는 것인데 깃허브에 올리지 말아야될 노드를 지정하면 node_modules 같은 것은 올라가지 않습니다.

설정파일 같은 것이나 빌드된 디렉토리등이 올라가면 안됩니다.

![alt git repo](/images/nemv/2018-10-05 15-15-26 git repo.png)

# 깃허브에서 소스 내려받기

```bash
$ git clone https://github.com/fkkmemi/nemv3.git
$ cd nemv3
```

nemv3라는 디렉토리에 README.md와 숨겨진 파일들(.gitignore등)이 보입니다.

# 계획

- nemv3
    - be // 백엔드 설치
    - fe // 프론트엔드 설치

# 백엔드 설치하기

```bash
$ express --view=pub be
$ cd be
$ yarn
```

# 프론트엔드 설치하기

```bash
$ cd ..
$ vue create fe
# router and vuex 설치 나머지는 다 엔터로 기본
$ cd fe
$ vue add vuetify
$ yarn serve
```

localhost:8080에서 확인해보기

# 백엔드 퍼블리 디렉토리 프론트엔드에 연결하기

## 프론트엔드 빌드

```bash
$ yarn build
```

fe/dist 라는 곳에 빌드된 파일들이 쌓여있습니다.

## 백엔드 설정

**be/app.js**  
```javascript
// app.use(express.static(path.join(__dirname, 'public')));
app.use(express.static(path.join(__dirname, '../', 'fe', 'dist')));
```
기존 public을 위와 같이 변경합니다.

## 백엔드 실행

```bash
$ cd ..
$ cd be
$ npm start
```

http://localhost:3000 에 접속하여 확인해보기

# 깃 코드 올리기

```bash
$ cd .. 
$ git add ./
$ git commit -m "start prj"
$ git push
```

# 소스

깃헙 소스는 아래 링크에 있습니다.

[https://github.com/fkkmemi/nemv3](https://github.com/fkkmemi/nemv3)


# 영상

{% include video id="S2pi67fqHnI" provider="youtube" %}  



