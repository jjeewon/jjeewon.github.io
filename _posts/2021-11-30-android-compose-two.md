---
layout: post
title:  "Jetpack Compose 이해하기(2)"
author: ella
categories: [ Android, Compose ]
image: assets/images/compose_title_2.jpeg
---
🤖  
# 개념 4️⃣-2️⃣ Thinking in Compose  

Jetpack Compose는 안드로이드를 위한 **modern declarative UI Toolkit**이다. Compose는 declarative API를 제공하여 더 코드를 작성하게 쉽게 만들어주고 어플을 유지보수하기 수월하게 해준다. declarative API는 프론트엔드 뷰를 강제적으로 변경하지 않고도 어플의 UI를 렌더링할 수 있도록 도와준다. 

## 선언형 프로그래밍 패러다임
역사적으로, 안드로이드 view hierarchy는 UI 위젯 트리로 나타내왔다. 어플의 state가 사용자의 interaction에 영향을 받아 바뀌기 때문에, UI hierarchy는 현재 데이터를 보여주기 위해서 업데이트를 해야 한다. UI를 업데이트하는 가장 일반적인 방법은 findViewById()를 통해 트리로 건너가서 button.setText(String), container.addChild(View), img.setImageBitmap(Bitmap)과 같은 방법으로 트리의 노드를 변화시키는 것이다. 이 방법은 위젯의 내부 state를 변화시키는 방법이다.  
위와 같이 뷰를 직접 조작해서는 에러가 날 확률이 높다. 만약에 데이터를 코드 여러 군데에서 렌더링해놓으면, 그 데이터를 보여주는 뷰들 중에 몇 개를 까먹고 업데이트하지 않을 수도 있다. 
아니면 두 가지 업데이트 내용 간에 충돌이 일어났을 때, illegal state를 만들어낼 수도 있다(ex. UI에서 막 제거한 노드의 value를 설정하려고 할 때). 일반적으로, 업데이트해야 하는 뷰가 늘어날수록 어플 유지보수하기가 복잡해진다.  
지난 몇 년 동안에, 안드로이드 업계는 '기존의 전통 UI 모델'에서 'UI 빌드 및 업데이트를 간편하게 해주는 declarative UI 모델'로 옮겨가기 시작했다. declarative UI 모델은 필수적으로 업데이트해야 되는 change들만 적용하면서 scratch로부터 전체 화면을 개념적으로 재생성하는 방식으로 작동한다. 이 접근 방식을 이용하면 stateful view hierarchy를 수동적으로 업데이트했던 기존의 방식보다 훨씬 단순하게 업데이트할 수 있다. Compose는 이와 같은 declarative UI 프레임워크이다.  
이와 같이 전체 화면을 재생성하는 방식은 비용(시간, computing power, 배터리 사용 등)이 든다는 단점이 있기는 하다. 이 비용을 커버하기 위해서 Compose는 똘똘하게도 UI의 어떤 부분을 다시 그려야되는지를 찹찹 골라낸다. 

## 간단한 composable 함수를 작성해보자!
Compose를 사용하여 UI를 빌드하려면, '데이터를 파라미터로 가지고, UI element를 내보내는 ```composable funtion```' 들을 정의해야 한다. 간단하게 Greeting 위젯을 만들어보자. 이 위젯에는 String 값을 하나 가지고 greeting 메시지를 보여주는 Text 위젯을 내보낸다. 
<img src="/assets/images/compose_4.png">  
위와 같은 간단한 composable 함수에 포함된 내용에 대해 살펴보자!
* 함수에 ```@Composable``` 어노테이션이 붙어있다. 모든 Composable 함수들은 반드시 이 어노테이션을 갖고 있어야 한다. Composable 어노테이션은 Compose 컴파일러한테 이 함수가 data를 UI로 바뀔 것이라고 알려준다.
* 함수가 data를 파라미터로 가지고 있다. Composable 함수들은 파라미터를 받을 수 있는데, 이 파라미터들을 통해 app logic으로 UI를 구성할 수 있다. 위 함수에서는  ```String``` 을 받아다가 해당 사용자에게 인사하는 문구를 보여주는 위젯을 만들었다.
* 함수가 UI에서 텍스트를 출력한다. 여기서 ```Text()```라는 Composable function을 호출하고 있는데, 이 함수는 실제 text UI element를 생성한다. Composable function들은 다른 composable funtion을 호출함으로써 UI hierarchy를 내보낸다는 것을 알 수 있다. 
* 함수가 어떤 값을 반환하지 않는다. UI를 내보내는 Compose 함수들은 뭘 반환할 필요가 없다. 왜냐하면 Compose 함수는 UI 위젯을 구성하는게 아니라 개발자가 원하는 screen state를 describe하는 함수이기 때문이다. 
* 함수는 빠르고, 부작용이 적다. 
    * 이 함수는 같은 argument로 여러번 호출될 지라도 동일한 방식으로 작동하며 전역 변수를 사용하지도 않고, random() 호출도 하지 않는다.
    * 이 함수는 프로퍼티나 전역 변수를 변경시키는 부작용 없이 UI를 구성할 수 있게 도와준다.


## 선언형 패러다임으로의 이동 
명령형 객체지향 UI toolkit에서는, 위젯들의 트리를 인스턴스화해서 UI를 초기화한다. 여기서는 XML Layout 파일을 inflating해서 이러한 작업을 수행하게 된다. 각각의 위젯은 고유의 내부 state를 유지하고, 앱 로직이 위젯과 상호작용할 수 있도록 get/set 메소드를 expose 해놓는다. 

위의 방식과는 다르게, Compose의 선언형 접근 방식에서는 위젯이 상대적으로 stateless하고 set/get 함수를 expose하지도 않는다. 위젯들은 오브젝트로서 expose 되지 않는다. 개발자는 argument들을 가진 동일한 composable 함수를 호출함으로써, UI를 업데이트한다. 이러한 방식으로 인해 Compose에서는 ViewModel과 같은 아키텍처 패턴에 state를 제공하기 쉽다. 여기서 composable 함수들은 observable data가 변경될 때마다, 현재 어플의 state를 UI로 변환시키는 역할을 한다.    
<img src="/assets/images/compose_5.png">
위의 그림에서, 앱 로직이 데이터를 top-level 의 composable 함수로 제공한다. 그럼 그 함수는 data를 이용해서 hierarchy가 아래인 다른 composable 함수들을 호출해서 UI를 묘사한다. 여기서의 다른 composable 함수에게 적절한 데이터를 전달하게 된다.

사용자가 UI와 상호작용할 때, 그 UI는 ```onClick```과 같은 이벤트를 일으킨다. 이러한 이벤트들은 app logic에게 notify해서 어플의 state가 바뀔 수 있도록 해야 한다. 어플의 state가 바뀌게 되면, composable 함수들은 바뀐 새로운 데이터가 파라미터로 넘어가면서 다시 호출된다. 이러한 과정으로 인해 **UI element들이 redrawn**되는데, 이러한 과정을 **recomposition**이라고 한다.
 <img src="/assets/images/compose_6.png">
 위 그림에서, 사용자가 Story Widget이라는 UI element랑 상호작용을 하면 이벤트가 trigger되는 것을 볼 수 있다. 그럼 app logic이 해당 event에 반응하고, composable 함수들은 필요한 경우에 자동적으로 새로운 파라미터들과 함께 다시 호출된다. 
 
 ## Dynamic content
 composable 함수들은 XML이 아니라, Kotlin으로 작성되기 때문에, 다른 코틀린 코드들처럼 dynamic하게 쓸 수 있다. 예를 들어, 다음과 같이 user list에 인사하는 UI를 빌드한다고 생각해보자.
```
@Composable
fun Greeting(names: List<String>) {
    for (name in names) {
        Text("Hello $name")
    }
}
```

이 함수는 name 리스트를 가져다가 각 user들을 위한 인삿말을 생성한다. composable 함수들은 정교하게 작성될 수 있다. 만약에 특정 UI element를 보여주고 싶으면, if문을 쓸 수 있다. loop도 쓸 수 있다. 이러한 강력함과 유연함이 XML Layout이 아니라 Kotlin을 사용하는 Jetpack Compose의 핵심적인 장점이다. 

## Recomposition
기존의 명령형 UI 모델에서는, 위젯을 바꾸려면 개발자가 setter를 호출해서 위젯의 내부 state를 바꿔야했다. 이와는 다르게 Compose에서는, 개발자가 그냥 새로운 데이터를 가진 composable function을 다시 호출하면 된다. 이러한 방식은 함수들이 recomposed되게 한다. 여기서 recomposed는 필요한 경우에 composable 함수에서 내보내진 위젯이 redrawn 되는 것을 의미한다. Compose 프레임워크는 똘똘해서 변화된 컴포넌트만 recompose한다. 

버튼 하나를 출력하는 다음과 같은 composable function이 있다고 하자.     
```   
@Composable
fun ClickCounter(clicks: Int, onClick: () -> Unit) {
    Button(onClick = onClick) {
        Text("I've been clicked $clicks times")
    }
}
```
버튼이 클릭될 때마다, caller는 ```clicks```의 값을 업데이트 한다. Compose는 새로운 value를 보여주기 위해서 람다식으로 된 Text 함수를 다시 호출한다. 이러한 과정을 recomposition이라고 부른다. 앞에서 말했던 것 처럼 해당 value와 관련없는 다른 함수들은 recompose되지 않는다.  
위에어 언급했던 것 처럼, 전체 UI 트리를 recomposing하는 것은 비용이 든다. 왜냐하면 computing power와 battery life을 이용하기 때문이다. Compose는 ```intelligent recomposition``` 으로 이와 같은 문제를 해결한다.  
Recomposition은 input이 바뀔 때, composable 함수를 다시 호출하는 과정이다. 새로운 입력이 들어와서 Compose가 recompose하게 되면, 바꿔야 되는 함수/람다식만 호출하고 나머지는 다 스킵한다. 파라미터가 바뀌지 않는 함수/람다식들을 스킵함으로써, Compose는 효율적으로 recompose하게 된다.   
주의해야 할 점은 실행하고 있는 composable 함수의 side-effect에 의존하면 안된다는 것이다. 왜냐하면 함수의 recomposition이 스킵되고 넘어갈지도 모르기 때문이다. 그렇게 되면, 사용자는 어플에서 뭔가 이상하고 예측할 수 없는 behavior를 경험하게 될 수 있다. 여기서 말하는 'side-effect'라는 것은 앱의 나머지 부분에서 보이는 어떤 변화를 말한다. 다음과 같은 위험한 side-effect들이 이에 해당한다. 
* shared object의 프로퍼티에 write 하는 것
* ViewModel에서 observable을 업데이트하는 것
* shared preferences를 업데이터하는 것

Composable 함수들은 애니메이션이 렌더링되고 있을 때, 매 프레임마다 재실행될 수 있다. Composable 함수는 애니메이션 중에 버벅거리지 않게 하려면 그만큼 빨라야 한다. shared preference에서 값을 읽어오는 것과 같이 비용이 큰 operation을 수행하려면, 백그라운드 코루틴에서 실행하고 composable 함수의 파라미터로 결과 값을 넘겨줘야 한다.  
예를 들어서, 아래의 코드에서 SharedPreferences에다가 값을 업데이트하는 composable를 만든 것을 볼 수 있다. Composable은 Composable 함수 안에서 shared preference의 값을 읽어오거나 값을 쓰면 안된다. 그래서 보통 Composable 대신에, 백그라운드 코루틴에서 ViewModel에다가 읽고 쓰는 것을 볼 수 있다. 이러한 app logic은 업데이트를 trigger하는 콜백과 함께 현재값을 받아 넘겨주는 방식으로 짜여져있는 것을 볼 수 있다.
```
@Composable
fun SharedPrefsToggle(
    text: String,
    value: Boolean,
    onValueChanged: (Boolean) -> Unit
) {
    Row {
        Text(text)
        Checkbox(checked = value, onCheckedChange = onValueChanged)
    }
}
```
## 🌟Compose를 적용할 때 알아야 하는 꿀팁 5️⃣개
* Composable 함수는 어떤 순서로든 실행될 수 있음
* Composable 함수는 병렬로 실행될 수 있음
* Recomposition은 가능한 많은 composable 함수/람다식들을 스킵함
* Recomposition은 optimistic하고 취소될 수도 있음
* Composable 함수는 애니메이션의 매프레임마다 실행될만큼 자주 실행될 수도 있음

## 1️⃣ Composable 함수는 어떤 순서로든 실행될 수 있음!
composable 함수 속 코드는 적어놓은 순서대로 실행되는 것처럼 보일 수도 있지만 항상 그렇지만은 않다. 만약에 composable 함수에 다른 composable 함수를 호출하는 함수를 가지고 있으면, 여기 포함된 함수들은 어떤 순서대로든 간에 실행될 수 있다. Compose는 어떤 UI element가 다른 element들보다 우선순위가 높은지를 식별해서 높은 것을 먼저 draw할 수 있다.  
예를 들어, 탭 레이아웃에서 세 개의 화면을 draw하는 다음과 같은 코드를 짰다고 해보자.
```
@Composable
fun ButtonRow() {
    MyFancyNavigation {
        StartScreen()
        MiddleScreen()
        EndScreen()
    }
}
```
```StartScreen```, ```MiddleScreen```, ```EndScreen``` 함수는 적어놓은 순새대로 호출되는게 아니라 랜덤으로 호출될 수도 있다. 즉 ```StartScreen()```에서 전역 변수를 세팅하고 ```MiddleScreen```에서 세팅된 전역변수를 사용하는 로직은 써서는 안된다는 뜻이다. 써놓은 순서대로 함수가 호출되기를 바랄 것이 아니라, 각각의 함수에서 자기 값을 가지고 처리해야 한다.

## 2️⃣ Composable 함수는 병렬로 실행될 수 있음!
Compose는 병렬로 composable 함수들을 돌림으로써 recomposition을 최적화시킬 수 있다. 병렬로 돌리는 방법은 Compose가 멀티 코어를 활용하여 우선위가 낮고 화면에는 없는 composable 함수들을 돌릴 수 있다.  
여기에서의 최적화는 백그라운드 스레드 풀 안에서 composable 함수를 실행할 수 있다는 것을 의미한다. 만약에 composable 함수가 Viewmodel에 있는 함수를 호출하면, Compose는 동시에 몇 가지 돌아가고 있는 스레드에서 해당 함수를 호출할 수도 있다.  
어플이 제대로 돌아가게 하려면, 모든 composable 함수들이 side-effect를 못갖고있게 해야 한다. 대신에, 항상 UI 스레드에서 실행되는 onClick과 같은 콜백에서 side-effect가 trigger되게 해야 한다.  
composable 함수가 호출되었을 때, 함수를 호출한 caller와는 다른 스레드에서 해당 함수가 호출될 수가 있다. 따라서 composable 람다식에서 변수를 수정하는 코드를 쓰면 안된다. 왜냐하면 (1) 해당 코드가 thread-safe하지 않으며, (2) composable 람다식에서 허용불가능한 side-effect이기 때문이다. 
밑의 코드는 리스트와 cound를 출력하는 composable의 예를 보여준다. 
```
@Composable
fun ListComposable(myList: List<String>) {
    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Text("Item: $item")
            }
        }
        Text("Count: ${myList.size}")
    }
}  
```
ListComposable 함수는 side-effect이 일어나지 않고, input 리스트를 UI로 변환시킨다. 이러한 방식은 크기가 작은 리스트를 보여주는데 좋은 코드다. 하지만, 만약에 함수가 지역 변수를 쓴다면, 이 코드는 three-safe하지 않거나 정확하지 않을 것이다. 밑의 코드가 그러하다. 
```
@Composable
@Deprecated("Example with bug")
fun ListWithBug(myList: List<String>) {
    var items = 0

    Row(horizontalArrangement = Arrangement.SpaceBetween) {
        Column {
            for (item in myList) {
                Text("Item: $item")
                items++ // Avoid! Side-effect of the column recomposing.
            }
        }
        Text("Count: $items")
    }
}
```
이 예제 코드에서는, ```items```가 recomposition될 때마다 수정된다. recomposition은 애니메이션의 프레임마다 일어나거나, 리스트가 업데이트될 때마다 발생할 수 있다. 두 방법에서 모두, UI는 잘못된 count를 보여줄 것이다. 따라서 위와 같은 방식은 Compose에서 지원되지 않는다. 이러한 방식을 금지함으로써, 프레임워크가 composable 람다식을 실행하는 스레드를 바꿀 수 있다. 

## 3️⃣ Recomposition은 가능한 많이 스킵됨!
만약에 UI의 일정 부분이 invalid하다면, Compose는 그 부분중에서 업데이트되어야 하는 부분을 최선을 다해 recompose 해준다. 즉, UI 트리에서 앞이나 뒤에 있는 composable을 실행하지 않고 버튼 한 개의 composable만 다시 실행하도록 건너뛸 수 있다는 것이다.  
모든 composable 함수와 람다식은 자기들 자체로 recompose할 수도 있다. 아래의 코드를 통해 리스트를 렌더링할 때 recomposition이 어떻게 몇몇 element를 스킵할 수 있는지를 알아보자.
```
/**
 * Display a list of names the user can click with a header
 */
@Composable
fun NamePicker(
    header: String,
    names: List<String>,
    onNameClicked: (String) -> Unit
) {
    Column {
        // this will recompose when [header] changes, but not when [names] changes
        Text(header, style = MaterialTheme.typography.h5)
        Divider()

        // LazyColumn is the Compose version of a RecyclerView.
        // The lambda passed to items() is similar to a RecyclerView.ViewHolder.
        LazyColumn {
            items(names) { name ->
                // When an item's [name] updates, the adapter for that item
                // will recompose. This will not recompose when [header] changes
                NamePickerItem(name, onNameClicked)
            }
        }
    }
}

/**
 * Display a single name the user can click.
 */
@Composable
private fun NamePickerItem(name: String, onClicked: (String) -> Unit) {
    Text(name, Modifier.clickable(onClick = { onClicked(name) }))
}
```
각각의 scope는 recomposition하는 동안에 실행할 것들이다. Compose는 ```header```가 바뀔 때, 부모를 실행하지 않고 ```Column``` 람다식으로 건너뛸 수 있다. ```Column```을 실행할 때, Compose는 ```name```이 바뀌지 않는다면 ```LazyColumnItems```를 건너뛸 수도 있다.  
다시 말해서, 모든 composable 함수들과 람다식들은 side-effect free해야 한다. side-effect가 필요하다면, 콜백에서 trigger해야 한다.

## 4️⃣ Recomposition is optimistic
Recomposition은 Compose가 composable의 파라미터가 바뀔 수 있다고 판단할 때 시작된다. Recomposition은 optimistic한데, 여기서의 **optimisitic**은 Compose가 파라미터가 다시 바뀌기 전에 recomposition을 완료할 것이라고 예상한다는 것이다.  
만약에 recomposition이 끝나기 전에 파라미터가 바뀌면, Compose는 recomposition을 취소하고 새로운 파라미터로 다시 recomposition할 수도 있다.  
recomposition이 취소될 때, Compose는 recomposition에서 UI 트리를 제거한다. 만약 개발자가 디스플레이되고 있는 UI에 의존하는 side-effect를 가지고 있다면, composition이 취소되더라도 해당 side-effect가 적용될 것이다. 이러한 상황은 inconsistent한 어플 state를 초래할 수 있다.  
🌟 따라서, 낙관적인 recomposition을 잘 처리하기 위해서, **모든 composable 함수들과 람다식들이 side-effect free**가 되도록 코드를 작성해야 한다. 

## 5️⃣ Composable 함수는 자주 실행될 수 있음
composable 함수는 어떨 때는 UI 애니메이션의 매 프레임마다 실행될 수 있다. 만약에 함수가 기기의 storage에서 뭔가를 읽어오는 것처럼 비용이 드는 operation을 수행한다면, 해당 함수가 UI를 버벅거리게 할 수 있다.  
에를 들어 위젯이 기기의 setting들을 읽으려고 한다면, 잠재적으로 세팅을 초당 수백번을 읽어서 어플의 퍼포먼스가 안좋아질 것이다.  
composable 함수에서 데이터가 필요하면, 그 데이터를 위한 파라미터를 정의해야 한다. **mutableStateOf 또는 LiveData**를 이용하여 데이터를 Compose의 파라미터로 넘기는 방식을 쓰면, 비용이 많이 드는 작업을 composition 밖의 다른 스레드에서 실행할 수 있다.  

[참고 사이트]  
[Android 공식 문서 - Thinking in Compose](https://developer.android.com/jetpack/compose/mental-model?authuser=1&continue=https%3A%2F%2Fdeveloper.android.com%2Fcourses%2Fpathways%2Fcompose%3Fauthuser%3D1%23article-https%3A%2F%2Fdeveloper.android.com%2Fjetpack%2Fcompose%2Fmental-model)  
