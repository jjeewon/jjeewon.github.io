---
layout: post
title:  "Coroutine Scope 이해하기"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/coroutine_scope_title.png
---
🤖  
# 개념 5️⃣ Coroutine Scope

CoroutineScope는 launch 또는 async를 이용하여 만든 코루틴을 추적한다. 코루틴은 언제든지 scope.cancel()를 호출하여 취소가능하다. Android의 KTX 라이브러리에서는 특정 lifecycle 클래스에 자체 CoroutineScope를 제공한다. 예를 들어, ViewModel에는 viewModelScope가 있고, Lifecycle에는 lifecycleScope가 있다. viewModelScope는 코루틴을 이용하여 백그라운드 스레딩을 구현할 때 사용한다. 오늘 메인으로 공부해볼 스코프가 바로 viewModelScope이다.  

## viewModelScope
viewModelScope는 ViewModel이 파괴될 때, 뷰모델의 자식 코루틴을 자동으로 취소해주는 extention propety를 ViewModel 클래스에 추가해줌으로써, structured concurrency에 contribute한다. 

## Scope in ViewModels
CoroutineScope는 스코프가 만들어낸 모든 코루틴들을 추적한다. 따라서, 개발자가 만약에 스코프 하나를 cancel하면, 해당 스코프에서 생성한 코루틴들이 모두 취소된다. ViewModel에서 코루틴을 돌린다면, 이와 같은 내용을 잘 알고 있어야 한다. 만약에 ViewModel이 파괴되는 중이라면, 모든 비동기 작업이 stop되어야 한다. 그렇지 않으면, 리소스를 낭비하고 메모리 누수가 일어나게 된다.  
onCleared() 메소드에서 SupervisorJob을 이용해서 새로운 scope를 만들어서 ViewModel에다가 CoroutineScope를 추가해보자. 이 Scope로 생성된 코루틴은 ViewModel이 살아있는 동안에는 지속된다. 

class MyViewModel : ViewModel() {

    /**
     * This is the job for all coroutines started by this ViewModel.
     * Cancelling this job will cancel all coroutines started by this ViewModel.
     */
    private val viewModelJob = SupervisorJob()
    
    /**
     * This is the main scope for all coroutines launched by MainViewModel.
     * Since we pass viewModelJob, you can cancel all coroutines 
     * launched by uiScope by calling viewModelJob.cancel()
     */
    private val uiScope = CoroutineScope(Dispatchers.Main + viewModelJob)
    
    /**
     * Cancel all coroutines when the ViewModel is cleared
     */
    override fun onCleared() {
        super.onCleared()
        viewModelJob.cancel()
    }
    
    /**
     * Heavy operation that cannot be done in the Main Thread
     */
    fun launchDataLoad() {
        uiScope.launch {
            sortList() // happens on the background
            // Modify UI
        }
    }
    
    // Move the execution off the main thread using withContext(Dispatchers.Default)
    suspend fun sortList() = withContext(Dispatchers.Default) {
        // Heavy work
    }
}

```ViewModel```이 destroy되면 백그라운드에서 처리중인 무거운 작업은 취소될 것이다. 왜냐하면, 해당 코루틴은 ```uiScope```라는 특정한 scope에서 시작되기 때문이다. 위의 코드를 단순화해주는 scope가 바로 ```viewModelScope```이다. 

## ViewModelScope는 boilerplate code를 줄여준다.
AndroidX lifecycle v2.1.0부터는 extension property인 viewModelScope를 ViewModel 클래스에 도입했다. viewModelScope는 위의 코드 예제에서 보여줬던 것과 동일한 방식으로 코루틴을 관리해준다.

```
class MyViewModel : ViewModel() {
  
    /**
     * Heavy operation that cannot be done in the Main Thread
     */
    fun launchDataLoad() {
        viewModelScope.launch {
            sortList()
            // Modify UI
        }
    }
  
    suspend fun sortList() = withContext(Dispatchers.Default) {
        // Heavy work
    }
}
```
ViewModelScope를 사용하려면 다음의 dependency를 build.gradle에 추가해줘야 한다. 
```
implementation "androidx.lifecycle.lifecycle-viewmodel-ktx$lifecycle_version"
```

## viewModelScope 파헤쳐보기
viewModelScope는 실제로는 다음과 같이 구현되어 있다. 
```
private const val JOB_KEY = "androidx.lifecycle.ViewModelCoroutineScope.JOB_KEY"

val ViewModel.viewModelScope: CoroutineScope
    get() {
        val scope: CoroutineScope? = this.getTag(JOB_KEY)
        if (scope != null) {
            return scope
        }
        return setTagIfAbsent(JOB_KEY,
            CloseableCoroutineScope(SupervisorJob() + Dispatchers.Main.immediate))
    }
```
ViewModel 클래스는 ConcurrentHashSet라는 attribute를 가지는데, ConcurrentHashSet는 어떤 형태의 object이든 저장할 수 있는. CoroutineScope이 ConcurrentHashSet에 저장된다. getTag(JOB_KEY) 메소드는 ConcurrentHashSet에서 CoroutineScope를 가져온다. 만약에, CoroutineScope가 없다면, 전에 했던 방식대로 새로운 코루틴스코프를 만들고 tag를 bag안에다가 추가한다.  
ViewModel이 clear되는 시점에는, onCleared() 메소드가 호출되기 전에 clear() 메소드를 먼저 수행한다. clear() 메소드에서 ViewModel은 viewModelScope의 Job을 cancel한다. 
```
@MainThread
final void clear() {
    mCleared = true;
    // Since clear() is final, this method is still called on mock 
    // objects and in those cases, mBagOfTags is null. It'll always 
    // be empty though because setTagIfAbsent and getTag are not 
    // final so we can skip clearing it
    if (mBagOfTags != null) {
        for (Object value : mBagOfTags.values()) {
            // see comment for the similar call in setTagIfAbsent
            closeWithRuntimeException(value);
        }
    }
    onCleared();
}  
```

clear() 메소드는 bag 안에 있는 모든 아이템들을 훓어보면서, (Closeable 타입인 object인지를 확인하여 close하는) closeWithRuntimeException를 호출한다. ViewModel이 scope를 close하기 위해서는, Closeable 인터페이스를 구현해야 한다. 따라서 CoroutineScope는 coroutineContext를 오버라이드하고 Closeable 인터페이스를 구현하는 CloseableCoroutineScope 타입이다.

```
internal class CloseableCoroutineScope(
    context: CoroutineContext
) : Closeable, CoroutineScope {
  
    override val coroutineContext: CoroutineContext = context
  
    override fun close() {
        coroutineContext.cancel()
    }
}
```

## Dispatchers.Main as default
```Dispatchers.Main.immediate```은 ```viewModelScope```을 위한 디폴트  CoroutineDispatcher의 집합이다. 
```
val scope = CloseableCoroutineScope(SupervisorJob() + Dispatchers.Main.immediate)
```
ViewModel은 업데이트가 자주 발생하는 UI와 관련된 개념이고, 또다른 dispatcher에서 launching하는 것은 적어도 2가지 이상의 thread switch가 발생하기 때문에,```Dispatchers.Main```이딱 맞다. suspend 함수가 자신의 스레드 confinement를 적절하게 수행하는 것을 고려하면, 다른 Dispatcher들과 함께하는 것은 좋지 않다. 왜냐하면, ViewModel이 하는 일에 대해 개발자가 딱 정해놓기 때문이다. 
 ```immediate```는 적절한 스레드로 work를 re-dispatch할 필요 없이, 즉각적으로 코루틴을 사용할 때 쓰인다. 
 
 ...Unit Testing viewModelScope
....
