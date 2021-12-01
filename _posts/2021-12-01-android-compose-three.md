---
layout: post
title:  "Jetpack Compose 이해하기(3)"
author: ella
categories: [ Android, Compose ]
image: assets/images/compose_title_2.jpeg
---
🤖  
# 개념 4️⃣-3️⃣ Jetpack Compose basics

이제 선언형 UI와 state, layout, theming들을 이용하여 개발하는 방법을 배워보자! 이번 포스트에서 알 수 있는 것은 (1) composable과 modifier이 뭔지, (2) Row와 Column과 같은 기본 UI element들이 어떻게 동작하는지, 그리고 (3) 어플에다가 state를 부여하는 방법 이다. 

## Compose 배경지식
Jetpack Compose는 UI 개발을 단순하게 하기 위해 설계된 modern toolkit이다. Compose는 reactive 프로그래밍 모델과 conciseness 그리고 사용하기 쉬운 코틀린언어의 장점이 결합되어 있다. 또한 Composes는 **데이터를 UI hierarchy로 변환시키는 함수**들을 호출하여 개발자가 UI를 묘사하게한다는 점에서, 전적으로 **declarative**하다. 데이터가 변하면, 프레임워크는 UI hierarchy를 업데이트하면서 해당 함수들을 자동으로 재실행한다.  
Compose 앱은 composable 함수들로 구성된다. composable 함수는 @Composable 어노테이션이 붙은 함수이며, 이 함수에서는 다른 composable 함수를 호출할 수 있다. composable 함수만 있으면 개발자는 새로운 UI 컴포넌트를 생성할 수 있다. @Composable 어노테이션은 Compose가 어노테이션이 붙은 함수를 서포트해서 UI를 업데이트할 수 있게 해준다. Compose는 또한 개발자가 코드를 작은 덩어리들로 구조화할 수 있게 도와준다. 그리고 composable 함수들은 짧게 "composables"라고 부르니까 알아두자.  
재사용할 수 있는 작은 composables를 만들면, 앱에서 UI element의 라이브러리를 빌드하기도 쉬워진다. 각각의 element들은 화면의 한 부분을 맡으며, 개발자가 각각  독립적으로 수정할 수 있다.

## Composables
하나의 composable 함수는 @Composable 어노테이션을 붙인 함수이다. 이 어노테이션이 붙어있으면 해당 함수에서 다른 @Composable 함수를 호출할 수 있다. 밑의 함수에서 Greeting 함수가 @Composable 어노테이션이 붙어있는걸 볼 수 있다. 이 함수는 String을 입력받아서 UI hierarchy의 한 조각을 생성한다. 함수 안에 있는 ```Text```는 라이브러리에서 제공되는 composable 함수이다.  
```
@Composable
private fun Greeting(name: String) {
   Text(text = "Hello $name!")
}
```

## Compose in Android app
매니페스트파일이 명시되어있는 것처럼, 사용자가 어플을 열면 MainActivity가 시작된다. 개발자는 여기서 setContent를 이용하여 레이아웃을 정의한다. 만약 Compose를 사용한다면 전통적인 View 시스템에서 사용하던 XML 파일을 쓰는 대신에, Composable 함수들을 호출하게 된다.
```
class MainActivity : AppCompatActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            BasicsCodelabTheme {
                // A surface container using the 'background' color from the theme
                Surface(color = MaterialTheme.colors.background) {
                    Greeting("Android")
                }
            }
        }
    }
}
```
### **BasicsCodelabTheme**
* Composable 함수들을 호출하는 방법 중 하나
* 화면에서 텍스트가 어떻게 보여지는지 확인하고 싶다면, 에듈레이터나 실제 안드로이드 폰 또는 ```Android Studio preview``` 이용할 수 있음

```
@Preview(showBackground = true, name = "Text preview")
@Composable
fun DefaultPreview() {
    BasicsCodelabTheme {
        Greeting(name = "Android")
    }
}
```  

<img src="/assets/images/compose_7.png">  

### **Android Studio preview**
* preview를 사용하려면 @Preview 어노테이션을 붙인 (파라미터가 없는 Composable function)이나 (default 파라미터를 가진 함수)를 만들어서 빌드해야 함
* 같은 파일에서 여러 개의 프리뷰를 가질 수 있고 각각에 이름을 부여할 수 있음

## Tweaking the UI


이제 Greeting을 위한 백그라운드 컬러를 세팅해보자! 백그라운드 컬러를 바꾸려면 ```Surface```로 Text composable을 감싸줘야 한다. ```Surface```는 color를 가질 수 있다. 밑의 예에서는 MaterialTheme.colors.primary 를 color로 사용했다.
```
@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text (text = "Hello $name!")
    }
}
```  

Surface 블록 안에 있는 컴포넌트들은 해당 백그라운드 컬러위에서 그려지게 된다. 

<img src="/assets/images/compose_8.png">  

✏️ Surface와 MaterialTheme은 Material Design과 관련된 개념임. Material Design은 구글이 개발자들이 UI를 생성하는 것을 도와주려고 만든 디자인 시스템!

위의 사진에서 보면 텍스트가 흰색인 것을 알 수 있다. androidx.compose.material.Surface와 같은 Material 컴포넌트들은 텍스트를 위해 적절한 컬러를 골라주는 것과 같이 개발자가 원할 것 같은 부분들을 알아서 케어해준다. Material은 독단적이라고도 하는데, 왜냐하면 대부분의 어플에 흔히 쓰이는 좋은 디폴트값들과 패턴들을 알아서 제공하기 때문이다. Compose에서 Material 컴포넌트들은 다른 foundational 컴포넌트들의 위에 구축되어있는데, 유연하게 처리해야할 경우에는 앱 컴포넌트가 해당 foundational 컴포넌트에 접근할 수 있다.  
위의 상황에서 ```Surface```는 백그라운드 색이 ```primary``` 컬러로 세팅되면, 그 위에 오는 텍스트는 theme에 정의된 대로 ```onPrimary``` 컬러를 써야한다고 자동으로 인식한다. 

## Modifiers  
```Surface```와 ```Text``` 같은 대부분의 Compose UI element들은 옵션으로 ```modifier```라는 파라미터를 가진다. Modifier는 UI에게 부모 레이아웃에서 어떻게 lay out하고, 디스플레이해야 하는지를 알려준다.  
예를 들어서, padding modifier를 통해 element가 장식하는 공간 주변의 amount를 정할 수 있다. padding modifier는 ```Modifier.padding()```으로 정의한다. 이제 화면의 Text에 padding을 넣어보자!
```
import androidx.compose.foundation.layout.padding
import androidx.compose.ui.Modifier
import androidx.compose.ui.unit.dp
...

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}
```
<img src="/assets/images/compose_9.png">  

## Reusing composables

UI에다가 컴포넌트를 많이 넣으면 넣을수록, nesting 레벨이 높아진다. 함수가 엄청 커지게 되면, 가독성이 떨어지게 된다. 따라서 재사용할 수 있는 작은 componenet들을 만들어서 UI element들의 라이브러리를 빌드하는 것이 좋다. MyApp이라는 Composable 함수를 만들어보자.
```
import android.os.Bundle
import androidx.activity.ComponentActivity
import androidx.activity.compose.setContent
import androidx.compose.foundation.layout.padding
import androidx.compose.material.MaterialTheme
import androidx.compose.material.Surface
import androidx.compose.material.Text
import androidx.compose.runtime.Composable
import androidx.compose.ui.Modifier
import androidx.compose.ui.tooling.preview.Preview
import androidx.compose.ui.unit.dp
import com.codelab.basicstep1.ui.theme.BasicsCodelabTheme

class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            BasicsCodelabTheme {
                MyApp()
            }
        }
    }
}

@Composable
private fun MyApp() {
    Surface(color = MaterialTheme.colors.background) {
        Greeting("Android")
    }
}

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Text(text = "Hello $name!", modifier = Modifier.padding(24.dp))
    }
}

@Preview(showBackground = true)
@Composable
private fun DefaultPreview() {
    BasicsCodelabTheme {
        MyApp()
    }
}
```
MyApp이라는 composable 함수가 onCreate 콜백이랑 preview에서 재사용되어 코드 중복 없이 깔끔하게 정리되는 것을 볼 수 있다. 

## column & row 만들어보기
Compose에는 Column, Row, Box라는 세 가지 기본 레이아웃 element가 있다.  
<img src="/assets/images/compose_10.png">  
Column, Row, Box는 Composable content를 가지는 Composable 함수라서 안에다가 아이템들을 넣을 수 있다. 예를 들어서, Column 안에 있는 자식들은 '수직'으로 배치된다.
```  
Column {
    Text("First row")
    Text("Second row")
}
```

이러한 Column을 이용하여 Greeting을 바꿔보면 다음과 같이 된다. 
```
import androidx.compose.foundation.layout.Column
...

@Composable
private fun Greeting(name: String) {
    Surface(color = MaterialTheme.colors.primary) {
        Column(modifier = Modifier.padding(24.dp)) {
            Text(text = "Hello,")
            Text(text = name)
        }
    }
}
```

## Compose와 코틀린
Composable 함수는 코틀린에서의 다른 함수들과 같이 사용될 수 있다. 따라서 개발자는 코틀린으로 UI가 보여지는데에 영향을 주는 statement들을 추가할 수 있기 때문에, UI를 더 강력하게 빌딩할 수 있다. 예를 들어, Column 안에서 for문을 이용하여 element들을 추가할 수 있다. 
```
@Composable
fun MyApp(names: List<String> = listOf("World", "Compose")) {
    Column {
        for (name in names) {
            Greeting(name = name)
        }
    }
}
```
<img src="/assets/images/compose_11.png">  
아직 composable에 size constraint를 주지않았기 때문에, 각각의 row가 최소한의 공간을 차지하여서 위에 있는 row랑 아래에 있는 row랑 가로가 맞지 않다. Preview에서 폰의 보통 width인 320dp로 파라미터를 추가해보자.
```
@Preview(showBackground = true, widthDp = 320)
@Composable
fun DefaultPreview() {
    BasicsCodelabTheme {
        MyApp()
    }
}
```
Modifier는 Compose에서 전반적으로 사용되므로 modifier를 이용한 연습을 더 해보자. 다음과 같이 ```fillMaxWidth```와 ```padding``` modifier를 이용하여 코드를 작성한다.
```
@Composable
fun MyApp(names: List<String> = listOf("World", "Compose")) {
    Column(modifier = Modifier.padding(vertical = 4.dp)) {
        for (name in names) {
            Greeting(name = name)
        }
    }
}

@Composable
private fun Greeting(name: String) {
    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Column(modifier = Modifier.fillMaxWidth().padding(24.dp)) {
            Text(text = "Hello, ")
            Text(text = name)
        }
    }
}
```

* Modifier는 오버로드될 수 있으므로, 패딩을 만드는데 다를 방식을 쓸 수 있다.
* modifier를 여러개 추가하고 싶다면, 그냥 chain하면 된다.(연달아 쓰면 됨!)

## 버튼 추가해보기
이제 Greeting을 확장해서 클릭할 수 있는 element인 button을 추가해보자.  
<img src="/assets/images/compose_11.png">
Button은 마지막 argument로 composable을 취하는 composable이다. trailing lambda는 괄호 밖으로 이동할 수 있기 때문에, button에 자식으로 어떤 내용이라도 추가할 수 있다. 밑의 예에서는 자식으로 Text를 추가했다.
```
Button(
    onClick = { } // You'll learn about this callback later
) {
    Text("Show less")
}
```
위의 그림과 같이 row의 맨 끝에 버튼이 오게 해야 하는데, composable에는 alignEnd modifier가 없다. 그 대신에 처음부분에 있는 composable에다가 ```weight```을 줄 수 있다. ```weight``` modifier는 weight가 없는 inflexible한 element를 옆으로 밀어버리고선 element가 차지할 수 있는 공간을 채우게 해준다. 
```
import androidx.compose.material.Button
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
...

@Composable
private fun Greeting(name: String) {

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { /* TODO */ }
            ) {
                Text("Show more")
            }
        }
    }
}
```

🧐 Compose에는 Material Design Buttons spec에 따라 Button이 여러 종류가 있다. ```Button```, ```OutlinedButton```, ```TextButton```이 해당한다. 위의 코드에서는 Button의 content로 Text를 감싸는 ```OutlinedButton```을 사용했다.

## State in Compose
이번에는 화면에 interaction을 넣어보자. 지금까지는 static한 레이아웃을 만들었지만, 이번에는 아래와 같이 유저의 행동에 반응하게 해보자.
![compose_13](https://user-images.githubusercontent.com/18653295/144225394-0970c73f-7cb9-4e99-b345-f0611d934aae.gif)  
클릭할 수 있는 버튼을 만드는 방법과 어떻게 item 크기를 재조정하는지에 대해 알아보기 전에, 각각의 아이템이 펼쳐졌는지 아닌지를 알려주는 '아이템의 ```state```'를 어딘가에다가 저장해놔야 한다. greeting마다 이 ```state``` 값들을 하나씩 가지고 있어야 하므로, Greeting composable 함수에 ```state``` 값들이 있어야 한다. 밑의 예제에서 boolean인 ```expanded```가 어떻게 쓰이고 있는지를 확인해보자.(잘못된 예이지만) 
```
@Composable
private fun Greeting(name: String) {
    var expanded = false // Don't do this!

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded = !expanded }
            ) {
                Text(if (expanded) "Show less" else "Show more")
            }
        }
    }
}
```
위의 코드에서 Button에 onClick 액션과 동적인 button text가 추가된 것을 볼 수 있다. 하지만, 위의 코드는 제대로 작동하지 않을 것이다. 왜냐하면 ```expanded``` 변수에 다른 값을 설정한다고 해서 Compose가 state change로 받아들이지 않아서, 아무 변화가 일어나지 않을 것이기 때문이다.  
즉, expanded 변수를 변형시켜도 ```recomposition```이 되지 않는 이유는 Compose가 추적하고 있지 않아서이다. 또한 Greeting이 호출될 때마다, expanded 변수는 false로 리셋될 것이다.  
따라서 원하는대로 작동하게 하기 위해서 composable에 **internal state**를 추가하려면, ```mutableStateOf``` 함수를 써야 한다. mutableStateof 함수는 Compose가 ```State```를 읽는 함수를 recompose하게 해준다. 

✏️ State와 MutableState : 어떠한 value를 가지고 있다가, 그 value에 변화가 일어났을 때 UI 업데이트를 trigger(=recompositions)해주는 인터페이스임!
```
import androidx.compose.runtime.mutableStateOf
import androidx.compose.runtime.remember
...

@Composable
fun Greeting() {
    val expanded = remember { mutableStateOf(false) }
    ...
}
```
여기서 ```remember```는 **mutable state를 기억**하고 있게 해주는 역할을 한다. 만약에 remember 없이 그냥 ```val expanded = mutableStateOf(false)```라고 해버리면 recomposition이 일어날 때마다 false로 새로운 mutable state를 리셋하므로 꼭 remember를 써줘야한다.  
즉, ```remember```는 **recomposition으로부터 guard**하는 역할을 하여 해당 state가 reset되지 않게 해준다. 

⚠️ 클래스에서 internal state는 private variable 같은 느낌이다. 따라서 같은 composable 함수라도 화면에서 다른 부분에서 쓰이면, UI element도 달라질꺼고 그 안에서의 state도 각각 다른 state라는 것에 주의하자.  
composable 함수는 자동적으로 state에 subscribe된다. 만약에 state가 변화하면, 이 필드를 읽는 composable 함수도 바뀐 내용을 업데이트하기 위해 recompose된다. 

## Mutating state and reacting to state changes
**state를 변화**시키기 위해 Button은 **onClick**이라는 파라미터를 가진다. onClick은 value가 아니라 **funtion**을 취한다. on click 액션을 정의하기 위해서 **람다 표현식**을 사용할 수 있다. 예를 들어, 토글되는 expanded state에 따라 다른 값을 텍스트로 보여주는 코드가 있다고 해보자. 
```
OutlinedButton(
                onClick = { expanded.value = !expanded.value },
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
 ```
 이 코드를 실행하면, 버튼이 클릭되었을 때 button 안에 있는 text가 recomposition되면서 expanded가 토글되는 것을 볼 수 있다. 각각의 Greeting은 서로 다른 UI element에 속하기 때문에, 자기 자신만의 expanded state를 가진다.
 ```
 @Composable
private fun Greeting(name: String) {
    var expanded = remember { mutableStateOf(false) } 

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier.weight(1f)) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }
    }
}
```
 
 ![compose_14](https://user-images.githubusercontent.com/18653295/144246424-8d265ef4-7009-498b-ba92-57053b0296f6.gif)  
   
 
## Expanding the item  
 이제 필요할 때 아이템을 확장시키는 코드를 작성해보자. state에 따라 달라지는 변수(extraPadding)를 추가한다. 
```
 @Composable
private fun Greeting(name: String) {

    val expanded = remember { mutableStateOf(false) }

    val extraPadding = if (expanded.value) 48.dp else 0.dp
...
```
위의 코드에서 extraPadding은 expanded와는 다르게 remember를 쓰지 않고 있다. 왜냐하면 extraPadding은 state에 의존하고 단순 계산만 하는 변수이기 때문이다.  
이제 Column에 새로운 padding modifier를 적용하여 코드를 작성해보자.
```
@Composable
private fun Greeting(name: String) {

    val expanded = remember { mutableStateOf(false) }

    val extraPadding = if (expanded.value) 48.dp else 0.dp

    Surface(
        color = MaterialTheme.colors.primary,
        modifier = Modifier.padding(vertical = 4.dp, horizontal = 8.dp)
    ) {
        Row(modifier = Modifier.padding(24.dp)) {
            Column(modifier = Modifier
                .weight(1f)
                .padding(bottom = extraPadding)
            ) {
                Text(text = "Hello, ")
                Text(text = name)
            }
            OutlinedButton(
                onClick = { expanded.value = !expanded.value }
            ) {
                Text(if (expanded.value) "Show less" else "Show more")
            }
        }
    }
}
```
![compose_15](https://user-images.githubusercontent.com/18653295/144247368-585d943a-5554-4e13-a073-67d25ff8fa2c.gif)  
위의 코드를 에뮬레이터에서 실행해보면, 다음과 같이 아이템을 각각 확장되는 것을 확인해볼 수 있다.

[참고 사이트]  
[Android 공식 문서 - Jetpack Compose basics](https://developer.android.com/codelabs/jetpack-compose-basics?continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fcompose%23codelab-https%3A%2F%2Fdeveloper.android.com%2Fcodelabs%2Fjetpack-compose-basics#7)  

