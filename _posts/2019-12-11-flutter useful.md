---
layout: single
title: Flutter Tip - 유용한 vscode extension
category: flutter
tag: [mobile, flutter, Awesome Flutter Snippets]
comments: true
toc: true
toc_label: "목차"
toc_icon: "list"
---

Flutter 코딩시 유용한 툴들을 정리해봅니다.

# Snippet

Snippet은 조각이란 뜻입니다.

잘 정돈되어 있는 코드 조각을 키워드 몇 자 넣으면 정돈된 코드를 작성해줍니다.

저처럼 손 느리고 암기력 딸리는 분들에게 필요한 기능입니다.

> 이런 류의 익스텐션은 해당 언어(.dart) 파일에서만 작동하니 과도한 익스텐션 설치로 난잡해지는 것을 걱정할 필요는 없습니다.

## Awesome Flutter Snippets

[https://marketplace.visualstudio.com/items?itemName=Nash.awesome-flutter-snippets](https://marketplace.visualstudio.com/items?itemName=Nash.awesome-flutter-snippets)

현재 플러터 최고의 스니펫 익스텐션입니다.

플러터 코딩시 제일 짜증나는 부분이 Stateful 클래스 작성하는 부분인데 키워드 s만 넣어도 복잡한 클래스가 자동으로 생성됩니다.

![alt st](/images/flutter/2019-12-11_09.53.52.png)

만들어진 클래스의 이름을 변경하면 관련 내용이 전부 변경되는 부분이 압권입니다.

![alt name](/images/flutter/2019-12-11_09.55.18.png)

그림의 abcde 문자열이 무려 6개나 변경되는 것을 볼 수 있습니다.

> 항상 느끼지만.. 다트는 신생 언어 치고는 너무 올드한 것 같습니다.

### shortcuts

from 19.12.11

| Shortcut   | Expanded                 | Description                                                                                                                                                                             |
| ---------- | ------------------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `statelessW`    | Stateless Widget         | Creates a Stateless widget                                                                                                                                                              |
| `statefulW`    | Stateful Widget          | Creates a Stateful widget                                                                                                                                                               |
| `build`      | Build Method             | Describes the part of the user interface represented by the widget.                                                                                                                     |
| `initS`     | InitState                | Called when this object is inserted into the tree. The framework will call this method exactly once for each State object it creates.                                                   |
| `dis`      | Dispose                  | Called when this object is removed from the tree permanently. The framework calls this method when this State object will never build again.                                            |
| `reassemble`     | Reassemble               | Called whenever the application is reassembled during debugging, for example during hot reload.                                                                                         |
| `didChangeD`      | didChangeDependencies    | Called when a dependency of this State object changes                                                                                                                                   |
| `didUpdateW`      | didUpdateWidget          | Called whenever the widget configuration changes.                                                                                                                                       |
| `customClipper`       | Custom Clipper           | Used for creating custom shapes                                                                                                                                                         |
| `customPainter`       | Custom Painter           | Used for creating custom paint                                                                                                                                                          |
| `listViewB`      | ListView.Builder         | Creates a scrollable, linear array of widgets that are created on demand.Providing a non-null `itemCount` improves the ability of the `ListView` to estimate the maximum scroll extent. |
| `customScrollV`      | Custom ScrollView        | Creates a `ScrollView` that creates custom scroll effects using slivers. If the `primary` argument is true, the `controller` must be null.                                              |
| `streamBldr`      | Stream Builder           | Creates a new `StreamBuilder` that builds itself based on the latest snapshot of interaction with the specified `stream`                                                                |
| `animatedBldr`    | Animated Builder         | Creates an Animated Builder. The widget specified to `child` is passed to the `builder`                                                                                                 |
| `statefulBldr` | Stateful Builder         | Creates a widget that both has state and delegates its build to a callback. Useful for rebuilding specific sections of the widget tree.                                                 |
| `orientationBldr`  | Orientation Builder      | Creates a builder which allows for the orientation of the device to be specified and referenced                                                                                         |
| `layoutBldr`  | Layout Builder           | Similar to the `Builder` widget except that the framework calls the builder function at layout time and provides the parent widget's constraints.                                       |
| `singleChildSV`     | Single Child Scroll View | Creates a scroll view with a single child                                                                                                                                               |
| `futureBldr`   | Future Builder           | Creates a Future Builder. This builds itself based on the latest snapshot of interaction with a Future.                                                                                 |
| `nosm`   | No Such Method           | This method is invoked when a non-existent method or property is accessed. |
| `inheritedW`   | Inherited Widget          | Class used to propagate information down the widget tree. |
| `mounted`   | Mounted  | Whether this State object is currently in a tree. |
| `snk`   | Sink  | A Sink is the input of a stream. |
| `strm`   | Stream  | A source of asynchronous data events. A stream can be of any data type. |
| `subj`   | Subject  | A BehaviorSubject is also a broadcast StreamController which returns an Observable rather than a Stream. |
| `toStr`   | To String  | Returns a string representation of this object. |
| `debugP`   | Debug Print  | Prints a message to the console, which you can access using the flutter tool's `logs` command (flutter logs). |
| `importM`    | Material Package | Import Material package.
| `importC`    | Cupertino Package | Import Cupertino package.
| `mateapp`    | Material App | Create a new Material App.
| `cupeapp`    | Cupertino Package | Create a New Cupertino App.
| `tweenAnimationBuilder`    | Tween Animation Builder | Widget builder that animates a property of a Widget to a target value whenever the target value changes.
| `valueListenableBuilder`    | Value Listenable Builder | Given a ValueListenable<T> and a builder which builds widgets from concrete values of T, this class will automatically register itself as a listener of the ValueListenable and call the builder with updated values when the value changes.

## Flutter Widget Snippets

[https://marketplace.visualstudio.com/items?itemName=alexisvt.flutter-snippets](https://marketplace.visualstudio.com/items?itemName=alexisvt.flutter-snippets)

Awesome Flutter Snippets과 비슷합니다..

주로 위젯 스니펫들이 모여있습니다.

![alt name](/images/flutter/2019-12-11_10.05.35.png)

f 만 입력하면 적당한 코드가 생성됩니다.

### Flutter related snippets

| Snippet     | Description                                                                      |
| ----------- | -------------------------------------------------------------------------------- |
| `fscaff`    | Scaffold widget snippet                                                          |
| `fstfulapp` | StatefulWidget snippet App                                                       |
| `fstless`   | StatelessWidget snippet                                                          |
| `fedgall`   | EdgeInsets widget snippet with named constructor `all`                           |
| `fedgonly`  | EdgeInsets widget snippet with named constructor `only`                          |
| `ftxt`      | Text widget snippet                                                              |
| `finitlf`   | Flutter initState lifecycle method snippet                                       |
| `fic`       | Flutter Icon widget snippet                                                      |
| `fcont`     | Flutter Container widget snippet                                                 |
| `fcent`     | Flutter Center widget snippet                                                    |
| `frow`      | Flutter Row widget snippet                                                       |
| `fcol`      | Flutter Column widget snippet                                                    |
| `fex`       | Expand widget snippet                                                            |
| `fszbw`     | SizedBox widget snippet with just width argument                                 |
| `fszbh`     | SizedBox widget snippet with just height argument                                |
| `fszb`      | SizedBox widget with width and height arguments                                  |
| `fedgsym`   | EdgeInsets widget with named constructor `symmetric`                             |
| `fedgsymv`  | EdgeInsets widget with named constructor `symmetric` with `vertical` parameter   |
| `fedgsymh`  | EdgeInsets widget with named constructor `symmetric` with `horizontal` parameter |
| `fimpmat`   | Add material's package import statement                                          |
| `fstream`   | Display a StreamBuilder widget                                                   |

### Dart related snippets

| Snippet    | Description                                        |
| ---------- | -------------------------------------------------- |
| `dvar`     | Dart variable declaration using `var`              |
| `dfinal`   | Dart variable declaration using `final`            |
| `dconst`   | Dart variable declaration using `const`            |
| `dinvar`   | Dart Public Instance variable snippet              |
| `dprinvar` | Dart Private instance variable snippet             |
| `dmt`      | Dart public method snippet                         |
| `dprmt`    | Dart private method snippet                        |
| `darr`     | Dart public arrow function snippet                 |
| `dprarr`   | Dart private arrow function snippet                |
| `dopnctor` | Dart optional named parameters constructor snippet |
| `dlist`    | Dart `List` collection snippet                     |
| `dmap`     | Dart `Map` collection snippet                      |
| `dset`     | Dart `Set` collection snippet                      |
| `dgetarr`  | Dart arrow function getter snippet                 |
| `dimpas`   | Dart `import as` snippet                           |
| `dimpshow` | Dart `import show` snippet                         |
| `dimplazy` | Dart `import deffered as` snippet                  |
| `dimphide` | Dart `import hide` snippet                         |
| `dexhide`  | Dart `export hide` snippet                         |
| `dexshow`  | Dart `export show` snippet                         |
| `dconvert` | Dart `convert` lib import snippet                  |