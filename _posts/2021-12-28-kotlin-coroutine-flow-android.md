---
layout: post
title:  "Flow in Android"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/flow_in_android_title.png
---
🤖  
# 개념 🔟 Flow in Android  

Flow는 순차적으로 여러 값을 emit하는 데이터스트림이며, 단 하나의 값만 반환하는 suspending 함수와는 다르다. flow를 쓰면 DB에서 실시간 없데이트하는 값들을 수신할 수 있다.  
flow는 코루틴을 기반으로 빌드되며, 비동기식으로 계산할 수 있는 데이터 스트림이다. flow에서 emit하는 값들은 동일한 타입이여야 한다. 예를 들어서 ```Flow<Int>```는 Int 값만을 내보내는 flow가 된다.  
값 시퀀스를 생성하는 ```Iterator```와 유사하지만, flow는 suspending 함수를 이용하여 값을 비동기로 만들어서 쓴다. flow를 쓰면 기본 스레드를 block하지 않고도 네트워크 요청을 해서 다음 값을 만들어낼 수 있다.  

## Data Stream의 세 가지 요소 : 생산자 + 중개자(option) + 소비자
<p align="center"><img src="/assets/images/flow_1.png"></p>
* 생산자(Producer) : 스트림에 추가되는 데이터를 생산함. flow에서 비동기적으로 데이터를 생산함
* 중개자(Intermediaries, option) : 스트림에 내보내는 각각의 값을 수정하거나, 스트림 자체를 수정함
* 소비자(Consumer) : 스트림의 값을 사용함

안드로이드에서 일반적으로 repository는 UI 데이터의 Producer다. 그리고 UI는 최종적으로 데이터를 표시하는 Consumer다. UI 레이어는 User Input Event의 Producer이며, 계층 구조의 다른 레이어가 input event를 사용하기도 한다. Producer와 Consumer 사이에 있는 레이어는 일반적으로 중개자의 역할을 하는데, 다음 레이어의 요구사항에 맞춰주기 위해서 데이터 스트림을 수정해준다.  
## 🌟 Flow 만들어보기
Flow를 만들려면 앞서 포스팅했던 내용에서 본 바와 같이 Flow Builder API를 써야한다. flow 빌더 함수는 ```emit``` 함수를 이용하여 새로운 값을 데이터 스트림에 내보내는 **새로운 flow**를 만들어준다.  
밑의 코드에서 ```DataSource```는 고정된 간격으로 최신 뉴스를 자동으로 가져온다. suspending 함수는 단일 값을 반환할 뿐, 여러 값을 반환할 수 없으므로 DataSource가 이러한 요구사항을 충족하는 flow를 만들어서 반환한다. 이와 같은 경우에서 DataSource는 **Producer** 역할을 한다.  
``` 
class NewsRemoteDataSource(
    private val newsApi: NewsApi,
    private val refreshIntervalMs: Long = 5000
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        while(true) {
            val latestNews = newsApi.fetchLatestNews()
            emit(latestNews) // flow로 request 결과를 내보냄
            delay(refreshIntervalMs) // refreshIntervalMs만큼 코루틴을 suspend함
        }
    }
}

// suspend 함수로 네트워크 요청을 만들어놓은 인터페이스
interface NewsApi {
    suspend fun fetchLatestNews(): List<ArticleHeadline>
}
```
```flow``` 빌더는 코루틴 내에서 실행되며 아래와 같은 제한사항이 적용된다.
* flow는 순차적임. Producer는 코루틴에 있으므로, suspending 함수를 호출하면 producer는 정지 함수가 반환될 때까지 suspend 상태로 유지됨. 위의 코드에서 Producer는 ```fetchLatestNews``` 네트워크 요청이 완료될 때까지 정지함. 완료된 후에야 결과를 스트림에 내보낸다.
* ```flow``` 빌더에서 생산자가 다른 ```CoroutineContext```의 값을 ```emit```할 수 없음. 따라서 새 코루틴을 따로 만들거나, ```withContext```로 다른 ```CoroutineContext```에서 emit해봤자 소용 없음. 이러한 경우에는 다른 ```callbackFlow```와 같은 flow 빌더를 써야함  

## ✍️ Stream 수정해보기
중개자(Intermediaries)는 중간 연산자(```intermediate operator```)를 사용함으로써, 값을 소비하지 않고도 데이터 스트림을 수정할 수 있다. intermediate 연산자는 데이터 스트림에 적용되는 경우, 값이 나중에 사용되기 전까지는 실행되지 않는 work chain을 설정해주는 함수이다.  
아래의 코드에서 Repository 레이어는 중간 연산자 ```map``` 연산자를 사용하여 데이터가 ```View```에 표시되도록 변환하고 있다.
   
``` 
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData
) {
    /**
     * Returns the favorite latest news applying transformations on the flow.
     * These operations are lazy and don't trigger the flow. They just transform
     * the current value emitted by the flow at that point in time.
     */
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            // Intermediate operation to filter the list of favorite topics
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            // Intermediate operation to save the latest news in the cache
            .onEach { news -> saveInCache(news) }
}
```
intermediate 연산자는 시간 간격을 두고 순차적으로 적용할 수 있어서, item들을 flow에 내보낼 때 느리게 실행되는 work chain을 구성해준다. 스트림에 intermediate 연산자를 적용하는 것만으로는 flow가 collect 되지 않는다.

## 🥍 flow에서 collect해보기
터미널 연산자(```terminal operator```)를 사용하면, '값들을 수신하기 위해 listening'을 시작하는 flow를 트리거해준다. ```collect```을 쓰면 emit할 때 스트림의 모든 값들을 가져올 수 있다.  
```collect```는 suspending 함수이므로, 코루틴 내에서 실행되어야 한다. 새로운 값에서 호출되는 매개변수에는 람다를 쓴다. collect는 suspending 함수라서 collect를 호출하는 코루틴은 flow가 종료될 때까지 정지될 수 있다.  
위의 코드에서 repository의 데이터를 사용하는 ```ViewModel```을 간단히 구현해보면 다음과 같다. 
```
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            // collect를 통해 flow를 트리거하여 element들을 소비한다.
            newsRepository.favoriteLatestNews.collect { favoriteNews ->
                // 여기서 최신 favorite news에 대한 View를 업데이트하면 됨
            }
        }
    }
}
```
flow를 collect하면 일정한 시간 간격으로 최신 뉴스를 새로고침하여 네트워크 요청의 결과를 emit하는 producer가 트리거된다. producer는 while(true) 루프로 항상 활성 상태가 유지되므로 Viewmodel이 삭제되어 ```viewModelScope```가 취소되면 데이터 스트림이 종료된다.  
## ⚠️ flow의 collect가 중지되는 경우?
* collect된 코루틴이 취소된 경우, flow의 collect가 중지되고 기본 producer도 중지됨
* producer가 항목 emit을 모두 완료한 경우, 데이터 스트림이 종료되고 collect를 호출한 코루틴이 다시 실행하기 시작함  
다른 intermediate 연산자가 지정되지 않으면, flow는 ```cold```하고 ```lazy```하다. 즉, flow에서 terminal 연산자가 호출될 때마다, producer 코드가 실행된다. 여러 consumer가 동시에 collect하는 경우에 flow를 최적화시키고 공유하게 하려면 ```shareIn```연산자를 사용하면 된다. 

##  🤚 Unexpected exception 캐치하기
타사 라이브러리에서 producer를 구현하는 경우에 unexpected exception이 발생할 수 있다. 이러한 exception을 처리하려면 intermediate 연산자인 ```catch```를 써야 한다. 
```
class LatestNewsViewModel(
    private val newsRepository: NewsRepository
) : ViewModel() {

    init {
        viewModelScope.launch {
            newsRepository.favoriteLatestNews
                // 중간 연산자인 catch operator. 만약에 exception이 throw되면 
                // catch하고 UI를 업데이트 함
                .catch { exception -> notifyError(exception) }
                .collect { favoriteNews ->
                    // 여기서 최신 favorite news에 대한 View를 업데이트하면 됨
                }
        }
    }
}
```
catch를 쓰지 않았던 이전의 코드에서는 exception이 발생했을 때, 새 element가 수신되지 않으므로 ```collect``` 람다가 호출되지 않는다. ```catch```를 통해 flow에 항목을 emit할 수 있다. 밑의 코드에서 repository 레이어는 캐시된 값을 emit하고 있다.

```
class NewsRepository(...) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> news.filter { userData.isFavoriteTopic(it) } }
            .onEach { news -> saveInCache(news) }
            // If an error happens, emit the last cached values
            .catch { exception -> emit(lastCachedNews()) }
}
```
위의 코드에서 exception이 발생하면 ```collect``` 람다가 호출되어서 exception으로 인한 새 항목이 스트림에 전송된다.

## 다른 CoroutineContext에서 실행하기
기본적으로 ```flow``` 빌더의 producer는 collect하는 코루틴의 **CoroutineContext**에서 실행된다. 다른 **CoroutineContext**에서 값을 emit할 수가 없으며, 그럴 경우 동작이 이상하게 돌아갈 수도 있다. 예를 들어서, 위의 예제에서 repository 레이어는 viewModelScope가 사용하는 Dispatchers.Main에서 작업을 실행하면 안된다.  
flow의 **CoroutineContext**를 변경하려면 intermediate 연산자인 ```flowOn```를 써야한다. ```flowOn```은 upstream flow의 **CoroutineContext**를 변경해준다. 즉, ```flowOn``` 전에 있는 producer와 intermediate 연산자에 적용된다. downstream flow(flowOn 이후의 intermediate 연산자와 producer)는 ```flowOn```에 영향을 받지 않으며, flow에서 collect하는데 사용하는 **CoroutineContext**에서 실행된다. ```flowOn``` 연산자가 여러개가 있다면, 각 연산자는 현재 위치에서 upstream을 변경한다.  
```
class NewsRepository(
    private val newsRemoteDataSource: NewsRemoteDataSource,
    private val userData: UserData,
    private val defaultDispatcher: CoroutineDispatcher
) {
    val favoriteLatestNews: Flow<List<ArticleHeadline>> =
        newsRemoteDataSource.latestNews
            .map { news -> // defaultDispatcher에서 실행됨
                news.filter { userData.isFavoriteTopic(it) }
            }
            .onEach { news -> // defaultDispatcher에서 실행됨
                saveInCache(news)
            }
            // flowOn이 upstream flow ↑ 에 영향을 미침
            .flowOn(defaultDispatcher)
            // downstream flow ↓ 는 영향 x
            .catch { exception -> // consumer의 context에서 실행됨
                emit(lastCachedNews())
            }
}  
```
위의 코드에서 ```onEach```와 ```map``` 연산자는 defaultDispatcher를 사용하는데 반해, ```catch``` 연산자와 소비자는 viewModelScope에 사용되는 Dispatchers.Main에서 실행된다.  
DataSource 레이어는 I/O 작업을 수행하므로, I/O 작업에 최적화된 디스패처인 IO Dispatcher를 사용해야 한다. 
```
class NewsRemoteDataSource(
    ...,
    private val ioDispatcher: CoroutineDispatcher
) {
    val latestNews: Flow<List<ArticleHeadline>> = flow {
        // Executes on the IO dispatcher
        ...
    }
        .flowOn(ioDispatcher)
}
```

## Jetpack 라이브러리의 flow
Flow는 ```Jetpack``` 라이브러리에 통합되며 **실시간으로 데이트를 업데이트**해야 하거나, **무제한 데이터 스트림**이 필요할 때 매우 적합하다.  
```Flow with Room```을 사용하면 DB 변경 알림을 받을 수 있다. DAO를 사용하는 경우에 실시간 업데이트를 받으려면, Flow 타입을 반환하면 된다.
```
@Dao
abstract class ExampleDao {
    @Query("SELECT * FROM Example")
    abstract fun getExamples(): Flow<List<Example>>
}
```
이렇게 정의해놓으면 ```Example``` 테이블이 변경될 때마다 DB의 새항목이 포함된 새 목록이 emit된다. 

## 콜백 기반 API를 flow로 변환하기
```callbackFlow```는 콜백 기반 API를 flow로 변환할 수 있는 flow 빌더이다. 예를 들어서 Firebase Firestore Android API는 콜백을 사용하는데, 이 API를 flow로 변환하고, Firebase DB 업데이트를 수신 대기하려면 아래와 같은 코드를 이용하면 된다. 
```
class FirestoreUserEventsDataSource(
    private val firestore: FirebaseFirestore
) {
    // Method to get user events from the Firestore database
    fun getUserEvents(): Flow<UserEvents> = callbackFlow {

        // Reference to use in Firestore
        var eventsCollection: CollectionReference? = null
        try {
            eventsCollection = FirebaseFirestore.getInstance()
                .collection("collection")
                .document("app")
        } catch (e: Throwable) {
            // If Firebase cannot be initialized, close the stream of data
            // flow consumers will stop collecting and the coroutine will resume
            close(e)
        }

        // Registers callback to firestore, which will be called on new events
        val subscription = eventsCollection?.addSnapshotListener { snapshot, _ ->
            if (snapshot == null) { return@addSnapshotListener }
            // Sends events to the flow! Consumers will get the new events
            try {
                offer(snapshot.getEvents())
            } catch (e: Throwable) {
                // Event couldn't be sent to the flow
            }
        }

        // The callback inside awaitClose will be executed when the flow is
        // either closed or cancelled.
        // In this case, remove the callback from Firestore
        awaitClose { subscription?.remove() }
    }
}
```
```flow``` 빌더와는 달리 ```callbackFlow```에서는 ```send``` 함수를 사용하면 다른 ```CoroutineContext```에서 값을 emit 할 수 있다. 그리고 ```offer``` 함수를 사용하면 값을 코루틴의 외부로 emit할 수 있다.  
```callbackFlow```는 개념상 blocking ```queue```와 매우 유사한 channel을 사용한다. channel은 capacity(버퍼링이 가능한 element의 최대 개수)로 구성된다. ```callbackFlow```에서 생성된 채널의 기본 capacity는 element 64개이다. 전체 채널에 새로운 element를 추가하는 경우 (1) ```send```는 새로운 element를 위한 공간이 생길 때까지 producer를 정지한다. 반면에 (2) ```offer```는 채널에 element를 추가하지 않고 즉시 ```false```를 반환해버린다. 
  


[참고 사이트]  
[안드로이드 공식 문서 - Flow](https://developer.android.com/kotlin/flow)  
