---
layout: post
title:  "Jetpack Compose 이해하기"
author: ella
categories: [ Android, Compose ]
image: assets/images/compose_title.png
---
🤖  
# 개념 4️⃣-1️⃣ What's new in Compose?  

요즘 핫허다는 Jetpack Compose에 대해 공부해보려고 한다. 안드로이드 공식 홈에서 Jetpack Compose 이해를 돕기 위한 튜토리얼<sup>[1](#footnote_1)</sup>을 제공한다. 액티비티가 총 15개 정도가 되는데, 하나하나씩 정리해가면서 Compose가 어떤 toolkit인지에 대해 알아가보자!🧐
 

## Jetpack Compose가 뭔데?
🌟 **기존 XML Layout** 방식보다 더 빠르고 쉽게 빌딩가능한 (1) **Declarative** (2) **UI Toolkit**  

### ⚠️ 기존 XML Layout 방식에서의 문제점  
<img src="/assets/images/compose_1.png">
- XML Layout과 Activity/Fragment 간에 state를 Synchronizing 해줘야 함
- 즉, model & UI 간 state를 동기화해줘야 했는데, 이 과정에서 overhead가 큼
    - 버그 날 가능성 ⬆
    - state가 동기화되도록 적절히 업데이트해주는건 개발자의 책임이었음
- App이 커지고 UI가 복잡해질수록 로직이 꼬일 확률이 높아짐

## (1) Declarative한 Compose
- Compose에서는 given state에 대해 UI가 어떻게 보여져야 하는지를 개발자가 전적으로 **describe** 함
- 그렇게 해놓으면 Compose 프레임워크가 state를 바뀔 때마다, UI를 **update** 해줌 
- Compose는 state를 UI로 transform 시킴(Composable에서 data를 파라미터를 통해 UI에 넘김으로써!)
<img src="/assets/images/compose_2.png">
- UI는 immutable함
    - produce된 후에 업데이트할 수 없음
    - 그래서 앱 state가 바뀌면, 새로운 state로 변환시키기 위해 re-excute함 
    <img src="/assets/images/compose_3.png">
    - 전체 UI를 다시 생성하게 함으로써 Synchronization issue를 막을 수 있음
    - Compose는 더 효율적으로 만들기 위해서, 변경되지 않은 element 처리하는 작업은 알아서 스킵해주지만 개념적으로 새로운 'given state'를 위해서 전체 UI를 재생성함
    - 개발자는 given state에서 UI가 어떤 모습이여야 하는지에 대해 코드를 짜면 됨

### ✏️ Composable function
        ```kotlin
        @Composable
        fun MessageList(messages: List<String>) {
            Column{
                if (messages.size == 0) {
                    Text("No messages")
                } else {
                    messages.forEach{ message ->
                        Text(text = message)
                  }
                }
            }
        }
        ```
* Compose에서는 위와 같이 UI 컴포넌트가 function 형태임
* function 형태는 UI를 잘게 쪼개서 reusable한 element들로 구성할 수 있게 도와줌으로써, UI 컴포넌트를 더 빠르고 쉽게 만들게 해줌
* UI componenet function은 리턴값이 없지만, UI에 보이게 됨!
* Column composable : 수직 정렬
* Text composable : text label 출력  
* 메시지가 없으면 No message 라벨 하나 출력하고, 메시지가 있으면 
* Composable들은 파라미터를 받을 수 있음
* Composable에 파라미터를 받아서 UI 처리를 하는 것이 바로!! data를 UI로 변환하는 것임
* 이렇게 data를 받음으로써 UI는 sync에 꽁꽁 묶여있을 수 있는 것임
* 만약에 개발자가 No message 라벨을 제거하는것을 까먹더라도, function을 다시 실행하면서 새로운 UI가 알아서 생성됨

### ✏️ Viewmodel과 Composable 함수
```
@Composable
fun ConversationScreen() {
    val viewModel: ConversationViewModel = viewModel()
    val messages by viewmodel.messages.observeAsState()
    MessageList(messages)
}
```
* ViewModel이 messages의 LiveData를 가지고 있고 observe하고 있음
* message field를 읽고 있는 composable은 새로운 데이터가 생길때마다 re-composed 됨
* 개발자가 직접 observer를 세팅할 필요가 없음
* Compose 컴파일러가 어떤 composable이 state를 읽는지를 추적해서, state가 바뀔 때마다 자동으로 re-excute함
* Compose는 안바뀐 input들은 놔두고 바뀐 input을 가진 composable들만 re-excute함 
* composable들은 immutable이기 때문에, composable에 reference를 갖고 있다가 나중에 query하거나, reference의 content를 업데이트할 수 없음
* 개발자는 composable의 파라미터를 통해 모든 정보를 관리해야 함
* 그렇다고 composable이 static해서 dynamic이 될 수 없다는 뜻은 아님
```kotlin
@Composable
fun MessageList(messages: List<String>) {
    Column{
        if (messages.size == 0) {
            Text("No messages")
        } else {
            messages.forEach{ message ->
            Text(text = message)
            }
        }
    }
}
```
* View에서와는 달리, Compose에서는 체크박스를 클릭한다고 해서, 체크박스가 토글된 상태로 바로 보여지지 않음
* Compose에서는 state 상수를 넘김. 즉, onCeckChange 콜백에서 local state인 selectAll을 업데이트 시켰음
* 이렇게 함으로써, 콜백함수가 state를 읽을 때마다, 체크박스가 readmitted됨
* 🌟 콜백에서 state를 바꾸지 않으면, 체크박스는 우리가 봤을 때 업데이트가 안됨 
    - 먼저, 탭될 때마다 체크박스를 만들기 위해서 코드를 작성한다는게 직관적이지 않다고 보일지도 모르지만, 이게 declarative UI의 핵심 컨셉임 !!
    - Element들은 개발자가 파라미터로 넘기는 값들에 의해서만 전적으로 컨트롤됨
    - 즉, 싱크를 맞추기 위해서 keep해야 하는 다른 state가 없는 것임
    - 체크박스를 누르는 사용자에게 앱이 어떻게 반응하게 할지는 개발자가 전적으로 결정하는 것임
* 입력 데이터가 바뀔 때 composable funtion이 re-excute되지만, re-excution(recomposition)하더라도  keep해야되는 변수가 있다면?
    - Composable function은 ```remember funtion```을 이용하면, 바로 전 excution에서 value를 기억해놓을 수 있음
        ```
        @Composable
        fun MessageList(
            messages: List<String>
        ){
        var selectAll by remember { mutableStateOf(false) }
        CheckBox(
            checked = selectAll,
            onCheckChange = { checked ->
                selectAll = checked
            }
        )
        ...
        }
        ```
    - remember는 value를 재사용할 수 있게 해줘서 value를 재할당하지 못하게 하거나, state에 value를 hold할 수 있음
    - 위의 코드에서는 event handling(=onCheckChange)을 inline으로 해줬지만, 다음과 같이 파라미터에 state를 넘겨주고 lambda로 업데이트할 수 있게 해줄수도 있음
        ```
        @Composable
        fun MessageList(
            messages: List<String>,
            selectAll: Boolean,
            onSelectAll: (Boolean) -> Unit
        ){
        var selectAll by remember { mutableStateOf(false) }
        CheckBox(
            checked = selectAll,
            onCheckChange = onSelectAll
        )
        ...
        }
        ```

## ✏️ Compose with Single stream
- Compose는 unidirectional data flow와 찰떡궁합임
- ex) 뷰모델이 screen state의 single stream을 가지고 있을 때
- 이 LiveData는 Compose UI에서 observe될 수 있음
- 그리고 파라미터로 각각의 컴포넌트들로 타고타고 전달될 수 있음
- 각각의 컴포넌트들은 자기가 필요한 데이터만 받으므로, 자기가 받는 데이터가 바뀌었을 때만 업데이트 됨
- ViewState 오브젝트의 single stream을 Producing 하는 것( = LiveData<ScreenState> )
    - state가 변화하는 코드에서 centralizing할 수 있게 해줌
    - 화면의 전체의 state를 판단하여 error를 줄이기 쉽게 해줌
    - composable을 test하기 쉽게 만들어줌(composable이 입력값에 의해서만 컨트롤되니까)
     

## (2) UI Toolkit으로서의 Compose

### ✏️ Compose는 Material Design 컴포넌트들과 theming system을 implement 하고 있음

- Material Design 컴포넌트들과 theming system은 개발자가 앱을 만들 때 필요한 컴포넌트들과 building 블록들을 제공해줌
    - Button, Card, FloatingActionButton, TopAppBar..
    - 해당 컴포넌트들은 material styling을 따름
- Material Theming을 implement 함
```
    MaterialTheme(
        colors = JetnewsColors,
        typograph = JetnewsTypography,
        shapes = JetnewsShapes) {
        ...
    }
```
    - 개발자가 따로 정의한 color, shape, typography 스타일을 이용하여 컴포넌트를 커스터마이징할 수 있게 도와줌

### ✏️ Row, Column, Box
```
    Row {
        // Horizontal LinearLayout
    }
    Column {
        // Vertical LinearLayout
    }
    Box {
     // FrameLayout
    }
```
- Row는 XML Layout의 Horizontal Linear Layout이랑 비슷한 느낌
- Column은 Vertical Linear Layout과 유사한 느낌
- Box 는 FrameLayout과 비슷한 느낌
- Compose layout model이 XML Layout과 다른 점은, 여러 measure pass들을 막아준다는 것임

### ✏️ ConstraintLayout

- Compose 한정 DSL을 통해 더 복잡한 레이아웃을 구성할 수 있게 도와줌 
- Custom layout을 만들기 더 쉬움
- 개발자가 자기가 갖고있는 수치를 가지고 배치하기가 function 한 개를 implementing하는 것 만큼 쉬움

### ✏️ Animation System

- 엄청나게 사용하기 쉬움 
- UI에 모션을 쓰기에 쉽고 강력한 방법을 제공함

### ✏️ Testing and accesablity
- Compose의 first-class citizen들임
- 둘 다 UI에서 parallel tree를 생성하는 sementics 시스템에서 만들어짐
- accessiblility service에 대한 정보를 더 많이 제공함
- UI element에 매칭할 수 있게 도와줌 
- testablity를 극대화하기 위해 만든 tesing artifact를 제공함
- 다른 애들과 똑 떨어트려서 composable를 테스트할 수 있는 API들을 제공함
- test rule은 clock을 노출시켜서, UI가 업데이트되게 하고, clock을 컨롤하는 API들을 제공함 
- 애니메이션 코드를 테스트하더라도 개발자가 완전히 컨트롤해서 테스트할 수 있게 도와줌

### ✏️ Compose는 Kotlin으로만 되어있음
- 코루틴을 통해서 더 간단하게 비동기 API들을 작성할 수 있음
    - 제스처, 애니메이션, 스크롤링 describing
    - 비동기 이벤트를 결합하는 코드를 작성하기 더 쉬워짐(ex. 애니메이션에서 손을 떼는 제스처)
    - 구조화된 concurrency를 통해 cancellation과 clean up이 가능함
   


    

[참고 서적]  
<a name="footnote_1">1</a>: [Jetpack Compose Tutorial](https://developer.android.com/courses/pathways/compose)  
