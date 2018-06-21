---
layout: single
title: Electron 0 데스크탑 앱 만들기
category: electron
tag: [electron,talk,idea]
comments: true
---

javascript는 정말 편리한 언어입니다.

하지만 브라우저 안의 개구리입니다.

브라우저 안에서는 보안 측면등의 이유로 많은 시스템자원 억세스가 힘듭니다.

특히 파일, 위치정보를 브라우저가 가져가면 곤란하기 때문입니다.

node.js의 V8엔진은 javascript가 브라우저 밖으로 나가게 도와주었습니다.

javascript 주제에 파일은 기본이고 웹서버를 구동할 수 있게했습니다. 

그러나 대부분 서버용도로 많이들 사용했습니다.

이제 V8의 자유도로 브라우저와 서버를 넘어 데스크탑 앱을 만들어 보겠습니다.

{% include toc %}

# 개요

 