---
layout: single
title: 모던웹(NEMV) 혼자 제작 하기 3기 - 89 활용편 정리
category: nemv
tag: [nemv,node,express,mongo,vue,vuetify,toast,editor,WYSIWYG]
comments: true
sidebar:
  nav: "nemv6"
---

모던웹(NEMV) 제작 하기 활용편 정리합니다.

{% include toc %}

# 보안연결(ssl) 적용하기

http로 되어 있는 사이트에서 form 내용을 전송하는 것은 매우 위험한 것입니다.

폼에 적혀있는 id/password 같은 정보가 전부 노출되기 때문입니다.

문제는 입력폼도, 쿠키도 전혀 사용하지 않는 학술페이지나 홍보용 페이지도 "https를 사용하지 않았으니 주의하세요" 문구가 뜬다는 것입니다.

node express로 https가 적용된 사이트를 만드는 방법은 간단합니다.

인증서파일(cert)과 기업정보파일(ca) 2가지만 있으면 몇줄이면 끝입니다.

문제는 돈이 든다는 것입니다.(godaddy 기준 연간 8만원정도)

무료 ssl 구현하는 방법도 있으나 여러가지 제약이 따릅니다.

[바로가기](/nemv/nemv-079-ssl/)

# 구글 애널리틱스로 사이트 현황 파악하기

사이트에 얼마나 접속하고 어느 페이지가 점유율이 높은지 구글에서 무료로 통계를 내줍니다.

마케팅 측면에서 중요한 요소가 될 수 있습니다.

구현은 구글 애널리틱스에 도메인 등록하고 프론트페이지에 몇 줄 넣으면 끝입니다.

[바로가기](/nemv/nemv-080-google-analytics/)

# 대시보드 게시판 다르게 표현하기

기존 게시판 데이터를 활용하여 일반 게시판(테이블 표) 형태가 아닌 다른 형태로도 표현할 수 있다는 것을 보여드립니다. 

[바로가기](/nemv/nemv-081-dashboard-tab/)

# 대시보드 꾸며보기

일반적으로 2~30불 정도에 팔리는 대시보드, 어드민패널 류를 굳이 구매하지 않고 직접 만들어 보는 것을 권합니다.

구매해봐야 남이 짠 소스에 차트와 패널만 덕지덕지 붙혀진 것이기 때문입니다.(_물론 css와 배치를 연구해보는 것으로 한 두개 사보는 것도 좋습니다._)

최근에 뷰티파이가 1.5 릴리즈되면서 스파크라인등이 추가되어서 더 아름답게 대시보드를 만들어 볼 수 있습니다.(_추후에 다시 만들어보겠습니다._)

[바로가기](/nemv/nemv-082-dashboard-layout/)

# 파일 업로드 하기(multer)

RESTful API로 주로 텍스트만 주고 받고 했습니다만, 이미지 같은 바이너리덩어리 파일을 어떤 식으로 처리하는 지 멀터(multer) 모듈을 통해 쉽게 구현해봅니다.

[바로가기](/nemv/nemv-083-file-upload/)

# 이미지 변환하기(base64, sharp)

최근 핸드폰으로 찍은 사진은 3,4메가가 훌쩍 넘습니다.

바이트 덩어리인 이미지를 잘라내고 압축한 썸네일은 문자화(base64) 처리해서 디비에 두고 실제 파일은 저장후 경로만 디비에 저장하게 됩니다.
  
그 과정을 간단하게 구현해봅니다.

[바로가기](/nemv/nemv-084-img-convert/)

# 이미지 컴포넌트로 업로드하기(filepond)

이미지를 업로드하는 것은 쉽지만 미리보기 하고 나서 업로드하는 것을 구현하려면 좀 더 손이 많이 갑니다.

파일폰드(filepond)라는 무료 콤포넌트로 쉽고 아름답게 구현해봅니다.

목적은 떠도는 모듈들을 어떻게 설치하고 사용하는지 학습하기 위함입니다.

[바로가기](/nemv/nemv-085-img-upload/)

# 사용자 정보 표시하기

뷰저장소(vuex)를 이용해서 메인프레임의 사용자 정보를 변이 시켜봅니다.(_단순 복습_) 

[바로가기](/nemv/nemv-086-get-user/)

# 위지웍 에디터 사용하기(toast ui editor)

nhn에서 만든 위지웍에디터를 사용해서 기존의 딱딱한 게시물 쓰기와 읽기를 개선해봅니다.

[바로가기](/nemv/nemv-087-editor-toast/)

# 게시물에 댓글 추가하기

간단한 댓글 기능을 추가합니다.(_지금까지 학습한 내용 최종 복습 및 실습_)

[바로가기](/nemv/nemv-088-comment/)

# 마치며

하다보니 어느덧 89화나 되었네요~

여기까지 다 구현하며 따라 오셨다면 진득하게 뭘 못하는 저보다 실력이 좋은 분이 되셨을 겁니다.

늘 말씀드렸지만.. 아는 것이 많고, 고수여서 강좌를 진행하는 것이 아니고 오히려 하수도 가능하다는 것을 증명하고 같이 공유하고자 하는 마음에서 진행해왔습니다.

_가끔 일 의뢰를 거절하는 이유는.. 정말로 제가 기술력 없는 개발자임을 고합니다. 죄송합니다.._

특히 제 성향이 어느정도 파고 나면 다른 걸 해야하는 요상한 개발자라 넓고 얕은 지식을 갖추고 있습니다.

그래서 어떤 플랫폼으로 개발하던 "버튼을 만들고 눌러서 뭘 표시한다!" 로 시작해서 적당히 만들며 기능 추가해서 현업에 조금 끼워 넣고 또 다른 플랫폼으로 갈아타는 개발철새입니다.


유튜브와 같이 강좌를 운영해봤는데 편집, 준비도 없이 쌩으로 코딩쑈를 하는 것이 쉬운 일이 아니란 것을 깨달았습니다.

역시 유튜버는 스튜디오에서 전문인력이 만들어야될 것 같습니다~

하지만 글로 표현하기 힘들었던 부분과 진짜 된다는 검증 차원에서는 유튜브는 좋은 표현 방법이었던것 같습니다~

참고로 구독, 좋아요 권장하는 행위 혐오합니다.. 

구독하고 알림받고 강좌를 볼만한 내용도 아니고 수익 같은 것 때문에 운영하는 것이 절대 아닙니다.

강좌 어느정도 챕터 끝나면 그때 한꺼번에 학습하세요~ 

제가 기대하는 것은 아직 재능을 발휘하지 못하고 방황하는 인재가 눈을 뜨게 하기를 바랍니다.

실제로 프로그램 전공도 아니고 해본적도 없지만 제 강좌를 계기로 입사한 사원이 지금은 정말 고수가되어 버려서 지금은 제가 배우고 있답니다...

그동안 즐거웠습니다~

# 예고

물론 아직 끝난 것은 아닙니다.

하반기 예정인 모던웹 강좌 활용편2는 아직 사용하지 않았던 믹스인이나 단위테스팅, 기타등등을 포스팅해보려합니다.

당분간은 모던웹 강좌는 쉬고 모바일앱 개발강좌를 진행하려 합니다.

네~ 역시 무식하게 버튼 만들어서 시작하는 쉬운 컨셉으로 가야죠..


힘들지만 웹만해서는 고객 만족을 높이기가 쉽지 않을 것입니다.

앱푸시 같은 기능 때문인데..(_소통이 중요한 시대라_)

분명 PWA의 서비스워커로 웹푸시를 구현할 수 있지만.. 대부분 모바일 브라우징을 싫어합니다.

클라이언트들은 결국 앱 개발 얘기를 꺼낼 것입니다..

결국 웹은 관리자로 가는 추세라 앱도 하셔야합니다.(_특히 프리랜서 분들.._)

# 소스

[소스 확인](https://github.com/fkkmemi/nemv3)

# 시험해보기

[https://fkkmemi.com](https://fkkmemi.com)

# 영상

{% include video id="gDV6Vc5j0kc" provider="youtube" %}