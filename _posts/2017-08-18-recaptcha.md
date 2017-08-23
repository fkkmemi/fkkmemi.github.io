---
title: recaptcha
category: javascript
tag: [javascript]
comments: true
---

회원 가입 같은 폼에서 문제는 늘 보안인데.. 사람이 아닌 로봇이 무한 가입을 하기 때문이다.

사실 프로그래머가 마음만 먹으면 get,post로 브라우징 없이 쉽게 무한가입봇은 만들 수 있다.

그래서 사람만이 판단할 수 있는 휘갈겨 쓴 숫자영문을 치는 것이 대부분인데

**일반적인 봇 방어**  
![alt secret](/images/javascript/1.png)

엄청 찌그러진 문자라 판단이 안되는 경우가 많다.

이러한 로봇 식별 처리를 해주는 것이 recaptcha 다.

꽤 많은 곳에 쓰여서 한번쯤은 보았을 것이다.

현재는 구글에서 도메인 등록을 하여 사용하게 되어있다.

해당 페이지 : [https://www.google.com/recaptcha](https://www.google.com/recaptcha) 에 너무 잘나와 있어서 리뷰하기도 시간낭비지만 까먹을까봐 기록해본다.

{% include toc %}

## 사용

구글 계정으로 로그인하여 절차대로 따라하면 붙힐 수 있게 코드조각까지 준다.

### 코드 받기

도메인 생성하고 나면 사이트키와 시크릿키를 주는데 간단히 적용하려면 사이트키만 있으면 된다.

**클라이언트 코드**  
![alt code](/images/javascript/2.png)

### 코드 적용

script를 추가만하면 전역변수 **grecaptcha** 를 사용할 수 있는데 아래와 같이 간단히 작업해도 상관없다.

**recaptcha 판단**  
```javascript
let g = grecaptcha.getResponse();

if(g.length === 0) return alert('로봇이 아닙니다를 체크해주세요');
```
코드대로 체크 안되어 있으면 내용이 없기 때문이다.

### 로컬 테스트

해당 도메인에서 밖에 테스트가 안되는데..

요즘 세상에 ftp로 서버소스를 손봐가며 테스트 할 수 없는 노릇이라

로컬 테스트 완료 후 디플로이하려면 테스트 키 확인해보도록 한다..

공식 테스트 키

- Site key: 6LeIxAcTAAAAAJcZVRqyHh71UMIEGNQ_MXjiZKhI
- Secret key: 6LeIxAcTAAAAAGG-vFI1TnRWxMZNFuojJ4WifJWe

하지만 그림 문제가 팝업이 안되서 제대로 적용하려면 역시 해당 도메인에서 해야한다.

아래 링크에 더 자세한 내용이 있다.

[https://developers.google.com/recaptcha/docs/faq](https://developers.google.com/recaptcha/docs/faq)

**팝업 모습**  
![alt rc](/images/javascript/3.png)

그림을 맞추면 되는 데 이게 어쩔때는 문제팝업을 3회 이상 낼때도 있고 클릭하면 고맙게도 팝업이 안뜰때도 있다..

아마 여러가지로 판단하는 것 같다

## 결론

깔끔하고 좋긴한데 가끔 문제 난이도가 높고 3,4회 이상 재도전하라고 하면 짜증이 난다.

다음엔 invisible recaptcha를 사용해보겠다.
