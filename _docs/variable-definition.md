---
title: "Variable definition"
permalink: /docs/variable-definition/
excerpt: "변수 정의 룰"
last_modified_at: 2017-08-26T16:40:00+09:00
redirect_from:
  - /theme-setup/
---

프로그래밍 변수 작성법 by memi

{% include toc %}

# 일반

- eslint standard 규격에 일반적으로 맞춘다.
- 전역변수,함수 외에는 대문자(Uppercase)를 사용하지 않는다  
- 전역변수는 충분히 검토 후, 꼭 필요한 곳에만 사용한다(가급적 사용하지 않는다)
- 영어, 숫자, 언더바로 구성
- 최대한 별도의 문서를 사용하고 주석은 사용하지 않는다.   
    eg) 사용 금지 예
    ```javascript
    /******
      author : XXX
      date : 1999.11.11
    ******/
    ```
- 용도에 맞는 형을 사용한다.  
    eg) const, let, long, double, etc..
- Array type 변수 뒤에는 복수를 의미하는 's'를 넣는다.

# 전역변수 : global


- ~~단어의 첫글자만 대문자를 사용한다. 이니셜 대문자도 예외가 아님~~  
    ~~eg) TCPhandle X, TcpHandle O~~
- 단어의 첫글자는 소문자를 사용한다.
- 두단어 이상일 경우 단어마다 대문자로 구분  
    eg) memberCountReset
- 비슷한 단어가 두개 이상일 경우 객체 생성 후 소문자(Lowercase) 멤버로 넣는다  
    eg) rawCount, rawSize -> raw.count, raw.size

    
# 지역변수 : local

- ~~두단어 이상일 경우 언더바로 구분~~  
    eg) ~~member_cnt_rst~~
- 가능하다면 단어 길이를 알아 볼 수 있을 만큼 줄임  
    eg) count -> cnt, length -> len
- 비슷한 단어가 두개 이상일 경우 객체 생성 후 멤버로 넣는다  
    eg) rawCount, rawSize -> raw.count, raw.size
- 컴파일러가 지원된다면 휘발성 변수는 행위 바로 위에 놓는다  
    eg)
    ```c
      // X
      double abc,bbc,dde=3.0;
      short ss,cc=4;
      String kk="",gg="";
      char cc[4] = {0,};
      int i,j;
    
      for(i=0;i<3;i++) kk += "a";
      //...
    ```  
    ```javascript
      // O 
      let vals = [], add = '';
      for (let f = 0; f < 10; f++) {
        let b = GetData(f);
        vals.push(b);
        add += b;
      }
      let j = 0, k = 3;
      let x = j+k;
      let val = foo(x);
      //...
    ```

# 함수 : function

- 함수선언 다음의 괄호는 공백이 앞뒤로 한칸씩 추가된다.
- 파라메터는 소문자를 사용한다.
- 파라메터가 여러개일 경우 콤마 뒤에 한칸씩 공백이 추가된다.
- parameter는 소문자를 사용한다  
    eg)
    ```javascript
    function foo (a, b, c) {} //javascript
    ```
    ```c
    int foo(int a, short b, unsigned char* c) {} //c
    ```
- parameter의 입력, 출력은 달라야한다.  
eg)    
**함수**  
```javascript
const foo = (a, b) => {
    return a + b;
}
```

**잘못된 예: 입력 변수명과 함수 인자 변수명이 같다**  
```javascript
const a = 3, b = 4;

foo(a, b);
```

**수정: 다르게 변경**  
```javascript
const ai = 3, bi = 4;

foo(ai, bi);
```

# 주석 : description

- 코드 스니펫(조각)의 경우 시작과 끝을 명시한다.  
    eg) 
    ```javascript
    // start copy doc
    let d = {a:3,b:4}
    b.copyof(d)
    // end copy doc
    ```
- 나중에 참고 해야할 내용의 경우 'todo:'를 사용한다  
    eg)
    ```javascript    
    if(c > 0.0) // todo : 다음 버전에서 삭제, 다음 버전에서는 c는 항상 0보다 크다
        a = b/c; 
    ```
    
# 데이터베이스 : database

- ~~데이터베이스에서 가져온 값은 시작 문자를 언더바로 구분한다.~~  
    ~~eg) _member._id, _params[3]._set._max_payment~~
    
## NoSQL

- _id 값을 참고 할 경우 언더바를 포함한 값을 가진다.

```javascript
company = {
  _id: 1,
  name: 'n',
  _dv: device._id
}
device = {
  _id: 54321,
  name: '장치1',
  _cp: company._id
}
```

- gps 좌표값은 구글 객체와 같게 한다.(그래야 변환이 필요 없다.)

```javascript
pos = {
  lat: 37.1,
  lng: 127.1
}
```