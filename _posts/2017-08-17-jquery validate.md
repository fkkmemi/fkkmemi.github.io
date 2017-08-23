---
title: jquery validate
category: javascript
tag: [javascript, jquery, form]
comments: true
---

유효성 판단을 하면서 페이지 갱신이 없는 입력폼을 정리해보겠다.

아쉽지만 늘 하고싶은 것만 할 수 없다. 
 
현업도 해야하기 때문에 어쩔 수 없이 jquery를 간만에 만져보며 입력폼 작성을 기록해본다.

입력폼은 늘 고려해야하는 것이 입력값의 유효성 처리인데

사실 front-back end 둘다 고민해야 되는 것이다.

> eg) 생년월일 8자리를 받아야한다고 가정했을 때  
웹사이트 : '20170801' 입력  
스크립트 : 8자가 맞는지, 숫자로 구성되어 있는지 확인  
수신서버 : 또 확인한다(프론트만 믿다가 인젝션이라도 들어오면 문제가 크다. 버그도 감안해야하고...)  
데이터베이스 : 여기서 또 확인(rdbms는 테이블이 이미 제한 적이라 오류를 뿜는다.)

그 중 front-end에서 코드 수를 최대한 줄이며 일반 적인 방법으로 유효성 판단을 하는 코드를 작성해보겠다.

{% include toc %}

## 준비

다행히 jquery validate라는 플러그인이 존재하고 있다..

제대로 처리하려면 역시 [https://jqueryvalidation.org](https://jqueryvalidation.org) 에서 확인해보면 된다.

필요한 것은 **jquery.validate.js** 만 있으면 된다.

**공식 리포 예제**  
```html
<form>
	<input required>
</form>
<script src="jquery.js"></script>
<script src="jquery.validate.js"></script>
<script>
$("form").validate();
</script>
```

## 코드

회원가입 폼을 오늘 간단히 만들어 보았는데 첨부해본다..

node-express에서 퍼그로 작성해보았다.

### html

**pug : regist-form**  
```jade
form#smart-form-register.smart-form.client-form(action='/api/reg',method='POST')
    header
        | 회원 가입
    fieldset
        section
            label.input
                i.icon-append.fa.fa-user
                input(type='text', name='username', placeholder='아이디')
                b.tooltip.tooltip-bottom-right 사이트 이용 시 필요합니다
        section
            label.input
                i.icon-append.fa.fa-envelope
                input(type='email', name='email', placeholder='이메일 주소')
                b.tooltip.tooltip-bottom-right 아이디 검증용 이메일 계정이 필요합니다
        section
            label.input
                i.icon-append.fa.fa-lock
                input#password(type='password', name='password', placeholder='패스워드')
                b.tooltip.tooltip-bottom-right 비밀번호를 절대 잊지 마세요
        section
            label.input
                i.icon-append.fa.fa-lock
                input(type='password', name='passwordConfirm', placeholder='패스워드 확인')
                b.tooltip.tooltip-bottom-right 비밀번호가 제대로 입력되어 있는지 확인하세요
    fieldset
        .row
            section.col.col-6
                label.input
                    input(type='text', name='lastname', placeholder='성')
            section.col.col-6
                label.input
                    input(type='text', name='firstname', placeholder='이름')
        .row
            section.col.col-6
                label.select
                    select(name='gender')
                        option(value='0', selected='', disabled='') 성별
                        option(value='1') 남성
                        option(value='2') 여성
                        option(value='3') 중성
        section
            label.checkbox
                input#subscription(type='checkbox', name='subscription')
                i
                | 관련 소식을 받습니다.
            label.checkbox
                input#terms(type='checkbox', name='terms')
                i
                | 약관 동의
                a(href='#', data-toggle='modal', data-target='#modalAgree')  약관
    footer
        button.btn.btn-primary(type='submit')
            | 등록
    .message
        i.fa.fa-check
        p
            | 등록해주셔서 감사합니다
```

퍼그가 편리하지만 기억이 안날때를 대비하여 html로 변환해본다..

> vue를 쓰보니 퍼그 렌더링 할일도 없어서 안쓰다보면 곧 까먹을 것 같아서 적어놓는다.
 
변환은 [http://html2jade.org](http://html2jade.org)에서 쉽게 할 수 있다.

**html : regist-form**  
```html
<form id="smart-form-register" action="/api/reg" method="POST" class="smart-form client-form">
  <header>회원 가입</header>
  <fieldset>
    <section>
      <label class="input"><i class="icon-append fa fa-user"></i>
        <input type="text" name="username" placeholder="아이디"/><b class="tooltip tooltip-bottom-right">사이트 이용 시 필요합니다</b>
      </label>
    </section>
    <section>
      <label class="input"><i class="icon-append fa fa-envelope"></i>
        <input type="email" name="email" placeholder="이메일 주소"/><b class="tooltip tooltip-bottom-right">아이디 검증용 이메일 계정이 필요합니다</b>
      </label>
    </section>
    <section>
      <label class="input"><i class="icon-append fa fa-lock"></i>
        <input id="password" type="password" name="password" placeholder="패스워드"/><b class="tooltip tooltip-bottom-right">비밀번호를 절대 잊지 마세요</b>
      </label>
    </section>
    <section>
      <label class="input"><i class="icon-append fa fa-lock"></i>
        <input type="password" name="passwordConfirm" placeholder="패스워드 확인"/><b class="tooltip tooltip-bottom-right">비밀번호가 제대로 입력되어 있는지 확인하세요</b>
      </label>
    </section>
  </fieldset>
  <fieldset>
    <div class="row">
      <section class="col col-6">
        <label class="input">
          <input type="text" name="lastname" placeholder="성"/>
        </label>
      </section>
      <section class="col col-6">
        <label class="input">
          <input type="text" name="firstname" placeholder="이름"/>
        </label>
      </section>
    </div>
    <div class="row">
      <section class="col col-6">
        <label class="select">
          <select name="gender">
            <option value="0" selected="" disabled="">성별</option>
            <option value="1">남성</option>
            <option value="2">여성</option>
            <option value="3">중성</option>
          </select>
        </label>
      </section>
    </div>
    <section>
      <label class="checkbox">
        <input id="subscription" type="checkbox" name="subscription"/><i></i>관련 소식을 받습니다.
      </label>
      <label class="checkbox">
        <input id="terms" type="checkbox" name="terms"/><i></i>약관 동의<a href="#" data-toggle="modal" data-target="#modalAgree"> 약관</a>
      </label>
    </section>
  </fieldset>
  <footer>
    <button type="submit" class="btn btn-primary">등록</button>
  </footer>
  <div class="message"><i class="fa fa-check"></i>
    <p>등록해주셔서 감사합니다</p>
  </div>
</form>
```  

### html 해석

bootstrap과 font-awesome 등 다른 클래스는 무시하고 보면..

input form 몇 개와 서브밋 버튼 하나 있다고 보면 된다.

저렇게 작성해놓고 등록 버튼을 누르면 페이지 이동이 되어 버린다.

모던 웹에서 페이지 이동은 뭔가 답답한 느낌이 나기 때문에 당연히 ajax로 결과를 받고 페이지 이동을 해야한다...

**중요한 것은 유효성 판단이 필요한 인풋에 꼭 name 속성을 필요로한다 id속성은 의외로 필요없다.**  

### script

스크립트 부분을 보자

**script**  
```javascript
$(function() {
    // Validation
    var errorClass = 'invalid';
    var errorElement = 'em';
    $("#smart-form-register").validate({
        // Rules for form validation
        errorClass: errorClass,
        errorElement: errorElement,
        highlight: function (element) {
            $(element).parent().removeClass('state-success').addClass("state-error");
            $(element).removeClass('valid');
        },
        unhighlight: function (element) {
            $(element).parent().removeClass("state-error").addClass('state-success');
            $(element).addClass('valid');
        },
        rules : {
            username : {
                required : true,
                minlength: 2,
                maxlength: 20
            },
            email : {
                required : true,
                email : true
            },
            password : {
                required : true,
                minlength : 4,
                maxlength : 20
            },
            passwordConfirm : {
                required : true,
                minlength : 4,
                maxlength : 20,
                equalTo : '#password'
            },
            firstname : {
                required : true
            },
            lastname : {
                //required : true
            },
            gender : {
                //required : true
            },
            terms : {
                required : true
            }
        },
        // Messages for form validation
        messages : {
            login : {
                required : '아이디를 입력하세요'
            },
            email : {
                required : '이메일 주소를 입력하세요',
                email : '유효한 이메일 주소를 입력하세요'
            },
            password : {
                required : '비밀번호를 입력하세요'
            },
            passwordConfirm : {
                required : '비밀번호를 한번 더 입력하세요',
                equalTo : '위에 입력한 비밀번호와 같지 않습니다'
            },
            firstname : {
                required : '이름을 입력하세요'
            },
            lastname : {
                required : '성을 입력하세요'
            },
            gender : {
                required : '성별을 선택하세요'
            },
            terms : {
                required : '약관에 동의해 주세요'
            }
        },
        // Ajax form submition
        submitHandler : function(form) {
            $.ajax({
                url: form.action,
                type: form.method,
                dataType: 'json',
                data: $(form).serialize(),
                success: function (res) {
                    if(res.status === 'OK') {
                        $("#smart-form-register").addClass('submited');
                        location.href = '/';
                        //location.reload();
                    }
                    else {
                        return pop('Error', res.msg, false, 4000);
                    }
                }
            });

        },
        // Do not change code below
        errorPlacement : function(error, element) {
            error.insertAfter(element.parent());
        }
    });
    $.extend($.validator.messages, {
        required: "필수 항목입니다.",
        remote: "항목을 수정하세요.",
        email: "유효하지 않은 E-Mail주소입니다.",
        url: "유효하지 않은 URL입니다.",
        date: "올바른 날짜를 입력하세요.",
        dateISO: "올바른 날짜(ISO)를 입력하세요.",
        number: "유효한 숫자가 아닙니다.",
        digits: "숫자만 입력 가능합니다.",
        creditcard: "신용카드 번호가 바르지 않습니다.",
        equalTo: "같은 값을 다시 입력하세요.",
        extension: "올바른 확장자가 아닙니다.",
        maxlength: $.validator.format("{0}자를 넘을 수 없습니다. "),
        minlength: $.validator.format("{0}자 이상 입력하세요."),
        rangelength: $.validator.format("문자 길이가 {0} 에서 {1} 사이의 값을 입력하세요."),
        range: $.validator.format("{0} 에서 {1} 사이의 값을 입력하세요."),
        max: $.validator.format("{0} 이하의 값을 입력하세요."),
        min: $.validator.format("{0} 이상의 값을 입력하세요.")
    });
});
``` 

### script 해석

1. 페이지 로드 완료(document.ready 후)시 validate() 를 초기화 한다.
2. 초기화시 룰, 이벤트 핸들러등을 작성한다.
3. 예쁘게 보이기 위해 클래스를 지정한다.
4. 한글화를 위해 기본 메세지를 수정한다.

## 결과물

**기본**  
![alt default](/images/jquery/1.png)

**틀릴때**  
![alt error](/images/jquery/2.png)

## 결론

jquery를 쓴다면 유효성 판단이 필요 없다고 해도 입력폼은 위와 같이 하는 것이 좋다.

ajax 처리도 매끄럽고 html5에 위배되지도 않는 문법 스타일이기 때문이다.
