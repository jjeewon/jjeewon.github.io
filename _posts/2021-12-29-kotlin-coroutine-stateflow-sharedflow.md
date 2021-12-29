---
layout: post
title:  "StateFlow & SharedFlow"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/stateflow-sharedflow_title.png
---
🤖  
# 개념 1️⃣1️⃣ StateFlow & SharedFlow  

```StateFlow```와 ```SharedFlow```는 flow에서 **state update를 emit**하고 **여러 consumer에게 값을 emit**할 수 있는 Flow API이다.  
## 1️⃣ StateFlow
```StateFlow```는 **observable한 state holder** flow로서, 현재의 state와 새로운 state 업데이트를 collector에 emit한다. StateFlow의 value 프로퍼티를 통해서 현재 state 값을 읽을 수도 있다. state를 업데이트하여 flow에 전송하려면, MutableStateFlow 클래스의 value에 새로운 값을 할당하면 된다.  
Android에서 StateFlow는 observable한 mutable state를 가지고 있어야하는 클래스에 매우 적합하다. 아래의 예에서는 View가 UI state의 update를 수신 대기하고 configuration 변경에도 기본적으로 UI의 state가 지속되도록 LatestNewsViewModel에서 StateFlow를 노출시키고 있다.
```
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    // 다른 클래스가 state를 업데이트하지 못하게 하기 위한 백업 프로퍼티
    private val _uiState = MutableStateFlow(LatestNewsUiState.Success(emptyList()))
    // 실제 UI에서는 state를 업데이트하기 위해서 uiState에서 collect함
    val uiState: StateFlow<LatestNewsUiState> = _uiState

    init {
        viewModelScope.launch {
            newsRepository.favoriteLatestNews
                // latest favorite news로 뷰를 업데이트해줌 
                // MutableStateFlow인 _uiState의 value에 값을 설정함
                // flow에 새로운 element를 추가해주고,collector들에게 업데이트! 
                .collect { favoriteNews ->
                    _uiState.value = LatestNewsUiState.Success(favoriteNews)
                }
        }
    }
}

// LatestNews 화면의 state를 나타냄 
sealed class LatestNewsUiState {
    data class Success(news: List<ArticleHeadline>): LatestNewsUiState()
    data class Error(exception: Throwable): LatestNewsUiState()
}
```
Producer는 ```MutableStateFlow``` 업데이트를 담당하는 클래스이고, consumer는 StateFlow에서 collect되는 클래스이다. ```flow``` 빌더를 사용하여 빌드되는 cold stream과 달리, ```StateFlow```는 **hot stream**이다. 즉 flow에서 collect해도 producer 코드가 trigger되지 않는다. ```StateFlow```는 항상 active 상태이고 메모리 내에 있다. 그리고 가비지 컬렉션의 루트에서 reference가 없는 경우에만 가비지 컬렉션에서 가져갈 수 있다.  
새로운 consumer가 flow에서 collect하기 시작하면, 스트림의 마지막 state와 다음 state가 수신된다. 아래의 코드에서 ```View```는 다른 flow들과 마찬가지로 ```StateFlow```를 수신대기(listen) 한다.  
```
lass LatestNewsActivity : AppCompatActivity() {
    private val latestNewsViewModel = // getViewModel()

    override fun onCreate(savedInstanceState: Bundle?) {
        ...
        // lifecycle scope에서 코루틴을 launch함
        lifecycleScope.launch {
            // lifecycle이 STARTED state 또는 그 앞의 state들이거나, STOPPED이라서 cancel할 때
            // repeatOnLifecycle 이 블록을 launch함
            repeatOnLifecycle(Lifecycle.State.STARTED) {
                // flow를 트리거하여 value들을 listen하기 시작함
                // lifecycle이 STARTED일때 트리거되고, lifecycle이 STOPPED일 때 collect를 멈춤
                latestNewsViewModel.uiState.collect { uiState ->
                    // 새로운 값이 수신되었을 때!!
                    when (uiState) {
                        is LatestNewsUiState.Success -> showFavoriteNews(uiState.news)
                        is LatestNewsUiState.Error -> showError(uiState.exception)
                    }
                }
            }
        }
    }
}
```

## ⚠️ 주의해야 할 점 
UI를 업데이트해야할 때, UI에서의 launch나 launchIn에서 repeatOnLifecycle 없이 **다이렉트로 flow를 collect하면 안된다**. ```launch```와 ```launchIn```는 **뷰가 표시되지 않는 경우에도 이벤트를 처리**하기 때문이다. 이로인해 앱이 다운될 수도 있으므로 **repeatOnLifecycle API를 사용**해서 앱이 다운되는 것을 방지해줘야 한다.!!  

## 🌟 flow를 StateFlow로 변환하려면?
stateIn이라는 intermediate 연산자를 사용하면 된다. 

## 🤼‍♂️ StateFlow vs LiveData
❤️ 공통점
* observable 데이터 홀더 클래스임
* 앱 아키텍처에서 비슷한 패턴으로 쓰임

💔 차이점
- **StateFlow**
    * 초기 상태를 생성자에 전달해야 함
    * 뷰가 STOPPED 상태가 되었을 때 자동으로 collect를 중지하지 않음. 자동으로 중지되게 하려면 Lifecycle.repeatOnLifecycle 블록에서 flow를 collect해야 함
- **LiveData**
    * 초기 상태를 생성자에 전달하지 않음
    * 뷰가 STOPPED 상태가 되었을 때 cosumer를 자동으로 unregister함 
    
## cold stream ➡️ hot stream 변환? 🌟shareIn🌟
```StateFlow```는 hot stream으로 flow가 collect되는 동안, 그리고 가비지 컬렉션 루트에서 다른 레퍼런스가 있는 경우에 메모리에 남아있다. shareIn 연산자를 하용하여 cold stream을 hot stream으로 전환시킬 수 있다.  
각 collector에서 새로운 flow를 만들 필요 없이 flow에서 생성한 ```callbackFlow```를 쓰면 Firestore에서 가져온 데이터를 ```shareIn```을 통해 collector들끼리 공유할 수 있다. 이를 위해서는 다음과 같은 내용을 전달해줘야 한다.
* flow를 공유하는데 사용되는 ```CoroutineScope```. 공유 Flow를 필요한 만큼 유지하기 위해서, 이 scope는 consumer보다 오래 지속되어야 함
* 각각의 새로운 collector로 replay할 item의 개수
* start behavior 정책
```
class NewsRemoteDataSource(...,
    private val externalScope: CoroutineScope,
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        ...
    }.shareIn(
        externalScope,
        replay = 1,
        started = SharingStarted.WhileSubscribed()
    )
}  
```
위의 코드에서 lastNews flow는 마지막으로 emit한 item을 새로운 collector로 replay하고, ```externalScope```가 active 상태이고 active한 collector가 있는 한, active 상태로 유지된다. ```SharingStarted.WhileSubscribed()``` 정책은 active한 subscriber가 있는 동안에는 upstream producer를 active 상태로 유지한다. 여기에 다른 시작 정채도 사용할 수 있다. 예를 들어서 ```SharingStarted.Eagerly```를 사용하여 producer를 즉시 시작할 수도 있다. 아니면 ```SharingStarted.Lazily```를 사용해서 첫 번째 subscriber가 표시된 후에야 공유를 시작하고, Flow를 영구적으로 active 상태로 유지할 수 있다. 

## 2️⃣ SharedFlow
```shareIn``` 함수는 ```SharedFlow```를 반환한다. ```SharedFlow```는 flow를 collect하는 모든 consumer에게 값을 emit하는 **hot stream**이다. ```SharedFlow```는 StateFlow가 highly-configurable하게 일반화된 Flow이다. 
  ```shareIn```을 쓰지 않고도 ```SharedFlow```를 만들 수도 있다. 예를 들어 모든 콘텐츠가 주기적+동시에 새로고침되도록 앱에 틱을 전송하는 ```SharedFlow```를 사용할 수 있다. 아래의 코드에서 TickHandler는 ```SharedFlow```를 expose해서 다른 클래스가 새로고침하는 타이밍을 알 수 있게 해준다. ```StateFlow```처럼 클래스에서 ```MutableSharedFlow``` 타입을 사용하여 item을 flow로 emit한다. 
```  
// 앱의 콘텐츠가 리프레쉬되어야할 때 centeralize시켜주는 TickHandler 클래스
class TickHandler(
    private val externalScope: CoroutineScope,
    private val tickIntervalMs: Long = 5000
) {
    // Backing property to avoid flow emissions from other classes
    private val _tickFlow = MutableSharedFlow<Unit>(replay = 0)
    val tickFlow: SharedFlow<Event<String>> = _tickFlow

    init {
        externalScope.launch {
            while(true) {
                _tickFlow.emit(Unit)
                delay(tickIntervalMs)
            }
        }
    }
}

class NewsRepository(
    ...,
    private val tickHandler: TickHandler,
    private val externalScope: CoroutineScope
) {
    init {
        externalScope.launch {
            // tick update를 listen 중...
            tickHandler.tickFlow.collect {
                refreshLatestNews()
            }
        }
    }

    suspend fun refreshLatestNews() { ... }
    ...
}
```   

## 🌟 SharedFlow 동작을 커스텀하는 두 가지 방법
* ```replay``` : 이전에 emit한 여러 값들을 새로운 subscriber에게 다시 보낼 수 있음
* ```onBufferOverflow``` : 버퍼에 전송할 item으로 가득 찼을 때 어떻게 할건지에 대해 정책을 지정할 수 있음. 기본값은 호출자를 정지시키는 ```BufferOverflow.SUSPEND```임. 다른 옵션으로는 ```DROP_LATEST```, ```DROP_OLDEST```가 있음
그리고 ```MutableSharedFlow```의 프로퍼티 중에 ```subscriptionCount```가 있는데, ```subscriptionCount```를 통해 active한 colletor의 개수를 알 수 있기 때문에 business logic을 최적화시킬 수 있다. ```MutableSharedFlow```에는 또한 ```resetReplayCache```라는 함수도 있는데, ```resetReplayCache```는 flow에 전송된 최신 정보를 replay하지 않고 싶을 때 쓸 수 있다.


[참고 사이트]  
[안드로이드 공식 문서 - StateFlow and SharedFlow](https://developer.android.com/kotlin/flow/stateflow-and-sharedflow)  
