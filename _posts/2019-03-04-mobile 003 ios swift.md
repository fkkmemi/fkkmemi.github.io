---
layout: single
title: 모바일앱(iOS Android) 둘 다 만들기 - 3 iOS 스위프트 이정도만 하고 넘어가자
category: mobile
tag: [mobile,ios,talk,idea,swift]
comments: true
sidebar:
  nav: "mobile"
---

기본적인 앱을 개발하기위해 스위프트를 최소한만 알아보겠습니다.

어떤 언어든 똑같이 있어야 하는 것은 조건, 변수, 함수, 반복문등입니다.

모든 언어제작사들의 고민은 "어떻게하면 안전하면서 쉽고 편리하게 사용할 수 있을까?" 입니다.

그래야 자기네 플랫폼 성장할테니까요~

결국 새로운 언어일 수록 기존 언어의 문제를 개선하고 쉬울 가능성이 큽니다~

{% include toc %}

# 놀이터(플레이그라운드)

Xcode에서 제공하는 놀이터에서 아주 편리하게 스위프트를 가지고 놀 수 있습니다.

먼저 Xcode를 실행합니다.

![alt pg](/images/mobile/2019-03-04_16.09.42.png)

Xcode 런쳐에서 놀이터 시작을 누릅니다.

![alt sg](/images/mobile/2019-03-04_17.32.58.png)

비어있는 파일(Blank)로 시작

![alt hw](/images/mobile/2019-03-04_17.37.07.png)

> 애플 역시 개발자의 시작은 "헬로"네요~

print를 이용해 찍어보고 좌측 코드 행에 있는 플레이 버튼을 누르면 출력됩니다.

이제 여기서 맘껏 놀면됩니다~


# 콘솔 찍기

```swift
var s = "글자", i = 12, d = 3.14

print(s, i, d)
print("원주율은 \(d) 인가요?")
```

아무 거나 찍을 수 있습니다.

```\(값)``` 을 이용해 문자열 사이에 값을 끼워 넣을 수도 있습니다.(_자바스크립트의 템플릿 스트링? 같네요_)

디버그나 학습용도로 매우 편리하죠.

> 자스(자바스크립트)의 console.log 와 거의 같습니다.

# 변수 유형

두가지만 기억하면 됩니다.

- var: 선언 후 변경이 가능
- let: 선언 후 변경이 불가능

> 자스의 const가 스위프트의 let 인 셈입니다.  
둘 다 개발하는 저는 매우 헷갈려서 실수가 많습니다.. 

**테스트**  
```swift
var str = "Hello, playground"
let str2 = "헬로"

str = "바꿔"
str2 = "바꿔"

print(str, str2)
```

let으로 선언한 str2는 못바꾼다고 에러가 나옵니다.

# 데이터 유형

대소문자를 구분합니다.

종류는 여러가지가 있지만 4가지만 자주 쓰입니다.

- Bool: true or false, 결국 0 or 1
- String: 글자들(문자) 입니다. 아마 제일 많이 쓰일 것 같습니다. 한글자는 Character를 사용합니다. 
- Int: 64비트 숫자입니다. 양수만 쓰려면 UInt를 사용하면 됩니다.(_U는 아마도 unsigned 이겠죠?_)
- Double: 64비트 소수입니다. 32비트 소수는 Float를 사용하면 됩니다.

> 자스처럼 자유형이였으면 했는데.. 역시 위험할 것 같아서 지정하게 해놓은 것 같습니다..    
기존 언어와 매우 흡사합니다.  
C언어와 특히 비슷한데요..  
너무 뻔한 것이라 개선의 의미가 없어서 그런 것 같습니다.  
String을 아예 형으로 넣어 놓은 것만 틀리네요.  

**test**  
```swift
let b2:Bool = false
var b1 = true // 지정 안해도 됨

b1 = false

print(b1, b2)

let i = 123
print(i)

var d = 1.23
//d = 3 // d:Double이기 때문에 정수인 3이 안됨
print(d)

var s = "abc"
//s = 123 // s:String으로 인식되었기 때문에 에러남
print(s)
var sl = """
big string
줄바꿈도 됨
"""
print(sl)
``` 

궁금하면 놀이터에서 직접 시험해보면 됩니다.

- 데이터 유형을 지정해도 되고 안해도 됩니다. eg) i:Int = 12 or i = 12
- 숫자와 소수는 점(.) 차이 입니다. eg) i = 0.0 and i = 0 둘은 틀림
- 문자열에서 쌍따옴표 3개를 쓰면 줄바꿈이되는 문자를 넣을 수 있음

# 데이터 연산

기본적으로 같은 유형끼리는 다른 언어와 마찬가지로 당연히 됩니다.

```swift
//var si = i + d  // int + double은 다른 유형이므로 안됨
//var si = s + sl
//var si = s + String(i)
var ss = "123"
var si = i + Int(ss)
print(si)
```

그러나 Int + Double 등 다른 유형끼리는 연산이 안됩니다.

기존 언어들 처럼 형변환을 해줘야 연산이 가능합니다.

주로 많이 쓰이는 문자 + 숫자의 경우 숫자를 String(숫자) 식으로 변환해주면 됩니다.

그런데 숫자 + 문자의 경우 문자를 Int(문자)로 변환하면 에러가 납니다.

그 이유는 ss가 "12가나" 일수도 있다는 것입니다.

그래서 대응책이 필요합니다.

```swift
var si = i + Int(ss)!
print(si)
```

요렇게 하면 되는데 이유가 뭘까요?

바로 옵셔널값으로 "확실하게 이값은 숫자다" 라고 지정하는 것이죠.

# 옵셔널

스위프트의 강점인지 모르겠지만.. 다른 언어에서 흔히 하던 예외처리가 강제 적용되었다고 보면 됩니다.

스위프트는 값이 없는 것을 nil 로 표현합니다.

서버와 통신을 하다보면 자주 겪는 문제중 하나가 값이 있기도 하고 없기도 할 때 입니다.

예를 들어 서버에서 a = 3 이라는 값을 보내주는데

다른 언어들은 주로 값이 없는 것(NULL)을 체크해야했죠..

**기존 언어들 eg**  
```javascript
var a
if (a !== undefined) doIt()
if (bb === NULL) throw Error(e)
```

보통 다른 언어에서는 위와 같이 값이 없이도 선언이 가능했지만 스위프트는 용납하지 않습니다.

스위프트는 대신 옵셔널이라는 유형을 사용합니다.

Int String처럼 하나의 유형입니다.

## 물음표

**test**  
```swift
var a: Int?
print(a)
a = 333
print(a)
``` 

유형에 물음표를 주면 숫자가 올 수도 있고 nil이 올 수도 있는 변수가 되는 것입니다.

위의 코드를 실행해보면 단순히 333이 찍히는 것이 아닌 Optional(333) 이라는 것이 찍힙니다.

불러다 쓰려면.. if let 같은 오묘한 조건식을 써야합니다.

```swift
var ss = "123가"

if let gg = Int(ss) {
    print(gg)
} else {
    print("이건 숫자가 아니야")
}
```

위와 같이 "123가" 는 숫자로 변환할 수 없으므로 else로 갑니다.

> 다른 언어에서 만들어 쓰 tryStrToInt 같은 함수가 좀 정돈된 느낌입니다.

## 느낌표

확정적일 때 씁니다.

이건 절대 숫자가 올꺼야 라는 확신이 들 때..

위에 Int(ss)! 처럼 쓰는 것이죠..

결론은 **"스위프트는 항상 값이 없는 상황을 고려해서 코딩해야 컴파일이 된다"** 입니다.

다른 언어의 경우 에러가 나든 안나든 컴파일은 되지만 스위프트는 다음 줄로 넘어가지가 않습니다.

# 조건

조건문은 기존 언어와 다를 것이 별로 없습니다.

## if else

그 유명한 if else 그대로 쓰면 됩니다.

단점은 {} 로 꼭 감싸야하는 것.. 

장점은 () 로 조건을 안 감싸도 되는 것..

```swift
if i < 444 {
    print("작다")
} else {
    print("크다")
}
```

> 간단한 한줄짜리 조건도 써야해서 안타깝습니다.

## switch

전통적인 스위치 문법 그대로입니다.

```swift
switch i {
case 0:
    print("없음")
case 1...222:
    print("범위에 있음")
default:
    print("??")
}
```

다만 ... 같은 범위로 구분이 가능하다는 정도가 차이입니다.

# 배열

기존 언어들과 역시 유사합니다. 

## 생성

같은 데이터 유형으로 마치 자스 같이 생성됩니다.

```swift
var ss = ["ab", "cd"]
// var ss = ["ab", 33] X: 문자와 숫자 같이 안됨~
```

사실 빈 배열을 만들고 싶을 때가 많죠~ 그 때는 좀 다릅니다.

```swift
var ss = [String]()
```

어떤 유형으로 만들지 정하고 ()를 해주면 끝입니다.

다른 방법도 많습니다

예를 들어

```swift
var ss: Array<String> = [String]()
```

이것도 위와 같은 결과입니다.

> 선언 방법은 더 많지만..    
전 코드 미니멀라이즈 주의자라 가장 단순한 방법만 사용합니다..

## 추가/삭제

```swift
var ss = [String]()

ss.append("x")
ss.append("ABC")
ss.remove(at: 0) // 맨 앞 삭제
```

그밖에 removeAll(), contains 등 많은 메쏘드들이 있지만 작명을 보면 너무 뻔해서 하나하나 소개하지는 않겠습니다.

# 딕셔너리

자스의 오브젝트 느낌의 데이터형입니다.

키와 값으로 이루어져있습니다.

스위프트는 딕셔너리라는 명을 부여했는데 적절한 것 같습니다.

```swift
var s = [String: Int]()

s["ab"] = 1
s["bc"] = 2
print(s) // ["bc": 2, "ab": 1]
```

추후 서버에서 받을 JSON 데이터는 결국 이 형태로 변환하는 것이 좋겠죠..

# 반복문

for 와 in 구조입니다. 자스에서 힌트를 얻은 것 같네요..

## 일정 구간 반복

```swift
var ss = [String]()

for i in 0...4 {
    ss.append("www" + String(i))
}
// ["www0", "www1", "www2", "www3", "www4"]
```

## 값 만큼 반복

```swift
let ss = ["www0", "www1", "www2", "www3", "www4"]

for s in ss {
    print(s)
}
```

너무 쉬워서 예제만 봐도 될 것 같습니다~

# 함수

함수 역시 다른 언어와 별반 차이가 없지만 미묘한 차이만 알고 넘어가면 됩니다.

## 반환에 대해(return)

```swift
func abc () -> Int {
    return 3
}

func bce () {
    print("return 없음")
}
print(abc())
```

반환 될 내용을 화살표 -> 로 명시해줘야하고 없을 경우 void를 쓰거나 빼버리면 됩니다.

## 인자(파라메터)

```swift
func cde (name: String, age: Int) {
    print(name, age)
}
cde(name: "하이", age: 31)
``` 

함수 호출 시 인자명도 입력해야합니다..

> 정말 숨막히게 답답한 형식입니다.  
대부분 IDE에서 어떤 타입인지 지원해줘서 굳이 안쓰는 것이 편한데..

# 구조체

소위 스트럭쳐라고 불리우는 놈입니다.

여기서부터는 진지해질 필요가 있습니다.

왜냐하면 우리가 앞으로 다뤄야할 데이터는 대부분 단순 값이 아닌 집합형? 값이죠

```javascript
var school = {
  name: '궁민학교',
  position: { lat: 37.1111, lng: 127.2222},
  students: [
    {
      name: '영희',
      age: 12
    },
    {
      name: '길동',
      age: 44
    }
  ],
  hobby: '탁구'
}
```

결국 위와 같은 JSON 내용을 UI에 써먹어야 하는데.. 안타깝게도 위의 문자열 자체를 자스처럼 쓸 수 있을리가 없죠..

그래서 위의 내용을 스위프트 구조체로 변환해야 써먹을 수 있는 데이터가 되는 것이죠..

## 기본 구조

```swift
struct Cat {
    var name: String = ""
    var age: Int = 0
}

var s = Cat()
s.name = "xxx"
s.age = 12

print(s)
```

너무 간단하죠~ struct 안에 원하는 값들만 채워 넣으면 끝입니다.

이제 위의 구조 처럼 사용하려면.. 응용이 필요합니다.

## 응용 구조

```swift
struct Position {
    var lat: Double? = 37.1
    var lng: Double? = 127.1
}
struct Student {
    var name: String?
    var age: UInt?
}

struct School {
    var name: String? = "sss"
    var position: Position?
    var hobby = ""
    var students = [Student]()
}

var s = School()
s.hobby = "탁구"
s.students.append(Student(name: "영희", age: 12))
s.students.append(Student(name: "길동", age: 44))
```

위의 json 처럼 계층적인 데이터를 만드려면 이렇게 struct's struct 같은 구조가 됩니다.

다른 언어에서도 많이 쓰던 방법들이죠..

# 클래스

struct와 거의 차이가 없습니다.

## 참조

중요한 차이는 스트럭트는 값덩어리고 클래스는 참조라는 것이죠.

이렇게 얘기해서는 대체 뭔 이야기인지 알 수가 없을 것입니다.

```swift
struct stTest {
    var a = 1
}

let st = stTest()
var v1 = st

v1.a = 333

print(st)

class clTest {
    var a = 1
}
let cl = clTest()
var v2 = cl

v2.a = 1234

print(st.a, cl.a)
```

예제를 보면 알 수 있습니다.

스트럭트로 생성한 st.a 는 초기값을 유지하고 있는데 클래스로 생성한 cl.a는 값이 바뀌어 버렸습니다.

st 와 v1은 각각 따로 동작하는 개체이지만 cl 과 v2는 사실 같은 값이라는 것이죠..

## 상속

클래스 끼리 상속도 가능합니다.

```swift
class Root {
    var name = "/"
    func good () {
        print("root!")
    }
}

class Child: Root {
    var str1 =  "/tmp"
    var str2 = "/xxx"
    override func good() {
        print("root 아냐")
    }
}

var r = Root()
var c = Child()

print(c.name)

r.good()
c.good()
```

딱봐도 : 표현으로 Child는 Root를 기반으로 만들어진 것 같죠?

같은 함수이지만 오버라이드해서 다른 활용을 할 수 있습니다.

잘 생각해보면 제일 먼저 만들어본 프로젝트에서 본 것 같은 느낌이 있을 것입니다.
 
첫 화면이 뷰컨트롤러를 상속 받은 클래스이니 아~ 이런느낌이 있구나 정도로만 아시면 됩니다.

그 외에 재활용 금지를 위해 final이 static 이니 있지만 복잡도를 증가시키므로.. 다루지 않겠습니다.

# 결론

스위프트 학습은 여기까지 입니다.

물론 이 정도의 지식만 가지고는 모자람이 있습니다.(_enum이라든지 오류처리라든지.._)

그 모자란 것을 채우려 노력하면 루즈해집니다.

완벽하지는 않지만 구동키는데는 이정도 지식이면 충분합니다.

모자란 것은 만들며 하나둘씩 다른 스위프트 지식을 찾아보면 되는 것이죠.. 


확실히 자바스크립트보다 많이 불편합니다.

문자니 숫자니 지정없이, 걱정없이 연산했으니까요.

노드로 작업하다 스위프트 돌아오면 답답합니다.

그래도 C나 자바보다는 나아보이는 것은 세미콜론이 없고 선언을 짧게 줄일 수 있는 정도겠네요..

사람마다 차이는 있겠지만.. 생산성 측면에서 자스 > 파이썬 > 스위프트 > 기타 같습니다..
  

# 영상

{% include video id="udaevG5SYVA" provider="youtube" %}
