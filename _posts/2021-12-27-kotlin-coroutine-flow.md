---
layout: post
title:  "Flow란?"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/flow_title.jpeg
---
🤖  
# 개념 9️⃣ Coroutines Flow  

Flow란 순차적으로 값을 emit하고, (정상적으로 또는 exception을 내면서) complete되는 비동기 데이터 스트림이다.
``` 
interface Flow<out T>
```
flow에서 ```Intermediate operators```라고 불리는 (map, filter, take, zip)과 같은 것들은 **upstream** flow에 적용되는 함수이며, 다음 operator들이 적용될 수 있는 **downstream** flow를 반환하는 함수이다. Intermediate operation은 flow에서 특정 코드를 실행하지도 않고, 해당 operation 자체가 suspending 함수인 것도 아니다. Intermediate operation은 미래의 execution을 위한 연쇄 operation을 셋업해주고 재빨리 리턴해주기만 한다. 이러한 특성이 바로 ```cold flow``` 프로퍼티의 특성이며, Intermediate operator는 cold flow 프로퍼티에 해당한다.  
flow에는 ```Terminal operators```라고 불리는 suspending 함수들(collect, single, reduce, toList)과 launchIn 연산자(주어진 scope에서 collection flow를 시작해줌)가 있다. Terminal operator는 **upstream** flow에 적용되고, 모든 operation의 execution을 트리거해준다. flow를 execution해주는 것을 다른 말로 flow를 **collect**한다고도 하는데, 이 행위는 실제로 blocking 없이 suspending하는 방식으로 수행된다. Terminal operator는 정상적으로 complete되거나, exeception 나면서 complete되는데, *upstream**에서 모든 flow 연산들이 succesful하면 정상적으로 complete되는 거고 failed하면 exception나면서 complete된다. 앞에서 말한 terminal operator들 중에 가장 기본적인 operator는 ```collect```이다.   
``` 
try{
    flow.collect{ value ->
        println("Received $value")
    }
} catch (e: Exception) {
    println("the flow has thrown an exceptoin: %e")
}
```
디폴트로 flow들은 순차적이고, 같은 코루틴 안에서는 모든 flow 연산들은 순차적으로 실행된다. 하지만 buffer와 flatMapMerge와 같이 flow에 concurrency를 도입하도록 설계된 operation들은 순차적으로 실행되지 않는다.  
```Flow``` 인터페이스는 cold stream인지 hot stream인지에 대해서는 어떠한 정보도 가지고 있지 않다.  
🌟 Cold stream이란?  
➡️ collect될 때마다 매번 반복적으로 collected되고 동일한 코드를 실행하는 방식  
🌟 Hot stream이란?  
➡️ collect될 때마다 같은 running source로부터 다른 값들을 emit하는 방식  
보통 flow는 **cold stream**이지만, hot stream인 subtype(ex. SharedFlow)도 있다. flow는 stateIn과 shareIn 연산자를 통해 hot stream으로 전환될 수 있으며, produceIn 연산자를 통해 hot channel로 전환될 수도 있다.
## Flow builder
flow를 만드려면 다음과 같은 방식들을 이용할 수 있다.
* flowOf(...) 함수 : 정해진 크기의 value의 set으로 flow를 만들 때 사용할 수 있는 함수
* asFlow() 확장함수 : 다양한 타입들을 flow로 전환해주는 함수
* flow {...} 빌더 함수 : 순차적인 호출되던 것이 emit 함수가 되도록 임의의 flow를 만들어주는 함수 
* channelFlow {...} 빌더 함수 : 잠재적으로 동시 호출되던 것이 send 함수가 되도록 임의의 flow를 만들어주는 함수
* MutableStateFlow & MutableSharedFlow : 다이렉트로 업데이트되는 **hot flow**의 생성자 함수들을 정의함

## Flow constraints
Flow 인터페이스를 implement할 때는 아래에 명시된 두 가지의 중요한 특성들을 고려해야 한다.
* Context preservation
* Exception transparency  
위의 두 특성들은 flow와 관련된 코드에 대한 local reasoning을 할 수 있게해주고, downstream flow collector들로부터 분리되어 upstream flow emitter가 develop되는 방식으로 코드가 모듈화되도록 해준다.

## (1) Context preservation
flow는 **context preservation**한 특성을 가진다. flow는 flow 고유의 execution 컨텍스트를 캡슐화하며, downstream으로 전파시키거나 누출시키지 않으므로 (특정 transformation이나 terminal operation)의 execution 컨텍스트에 대해 추론하기가 더 간단하다.  
flow의 컨텍스트를 변경하는 방법은 오로지 하나다. ➡️ ```flowOn``` 연산자를 쓰면 된다. flowOn 연산자는 upstream context(flowOn 연산자 앞에 있는 모든 것들)를 변경시킨다.  
```
val flowA = flowOf(1, 2, 3)
    .map { it + 1 } // ctxA에서 실행됨 
    .flowOn(ctxA) // upstream 컨텍스트를 바꿈: flowOf and map

// 이제 컨텍스트를 보존하는 flow인 flowA를 만들었음 : flowA의 정보는 이제 flowA 자체에 캡슐화되어 있음

val filtered = flowA // ctxA는 flowA에 캡술화되어 있음
   .filter { it == 3 } // filter는 아직은 context가 없이 퓨어한 연산자임

withContext(Dispatchers.Main) {
    // 모든 캡슐화되지 않은 연산자들은 Main에서 실행될 거임 : filter and single
    val result = filtered.single()
    myUi.text = result
}  
```  
flow를 구현할 때는 같은 코루틴에서만 emit을 해야 한다. 이러한 제약사항은 디폴트 flow 빌더에 의해 적용된다. flow 빌더는 flow implementation에서 **어떠한 코루틴도 시작시키지 않을 때** 쓸 수 있다. flow의 implemetation은 대부분의 개발할 때 개발자들이 저지르는 실수들을 예방해준다. 
``` 
val myFlow = flow {
   // GlobalScope.launch { // is prohibited
   // launch(Dispatchers.IO) { // is prohibited
   // withContext(CoroutineName("myFlow")) // is prohibited
   emit(1) // OK
   coroutineScope {
       emit(2) // OK -- still the same coroutine
   }
}
``` 
만약에 flow의 collection이랑 emission이 여러 코루틴으로 분리되어야 한다면, ```channelFlow```를 쓰면 된다. **channelFlow**는 모든 컨텍스트 보존 작업들을 캡슐화해준다. 그리고 channelFlow을 구체적으로 어떻게 구현할지에 대해 생각하는게 아니라, 도메인에 한정된 문제에 개발자가 집중할 수 있도록 도와준다. 이러한 channelFlow 안에서는 coroutine builder들을 조합해서 쓸 수 있다.  

## ⁉️ 성능 중요 + (동시 emit & 컨텍스트 jump)이 없다면?
flow 빌더 대신에 **coroutineScope** 또는 **supervisorScope**를 쓸 수 있다. 
* 범위가 지정된 primitive는 CoroutineScope를 제공하기 위해 사용되어야 함
* withContext(ctx)이든, launch(ctx)와 같이 빌더의 인자든 간에 emission의 컨텍스트를 바꾸는건 안됨
* 별개의 컨텍스트에서 다른 flow를 collect할 수는 있지만, flowOn 연산자를 적용하는 것과 똑같은 결과를 내지만, flowOn이 더 효율적임   

## (2) Exception transparency
```emit```이나 ```emitAll```이 예외를 던질 때, Flow implementation에서는 새로운 값을 emit하는 것을 멈추고 exception으로 finish해줘야 한다. downstream이 실패한 후에 값들을 emit해야 한다면, ```catch``` 연산자를 쓰면 된다. ```catch``` 연산자는 upstream exception만 잡아주고, 모든 downstream exception을 그냥 통과시켜버린다. 이와 유사하게 collect 같은 terminal 연산자들은 코드나 upstream flow에서 나타난 **unhandled exception**을 throw한다. 
```
flow { emitData() }
    .map { computeOne(it) }
    .catch { ... } // catches exceptions in emitData and computeOne
    .map { computeTwo(it) }
    .collect { process(it) } // throws exceptions from process and computeTwo
```
같은 추론이 ```finally``` 블록을 대체해주는 onCompletion 연산자에도 적용될 수 있다. 모든 exception을 처리하는 Flow의 연산자들은 **exception suppression** 원칙을 따른다. 만약에 downstream exception이 throw되었을 때 upstream flow가 completion 중에 exception을 throw한다면, downstream exception은 upstream exception에 의해 대체되고 suppress되며 finally 블록에서 throw하는 것과 의미상 동일하게 된다. 그러나 downstream exception을 root cause로 여기고 uptream은 아무것도 throw 안한 것처럼 작동하기 때문에, exception을 핸들링하는 연산자들의 operation에 영향을 끼치지는 않는다.  
제대로 exception transparency를 지키지 않으면, 코드에 대한 local reasoning을 제대로 하지 못하고 collect {...} 에서의 exception이 upstream flow에 의해 "caught"될 수 있기 때문에 코드를 추론하기 어려워져서 이상하게 작동하게 될 것이다.  
Flow는 런타임에 exception transparency를 검사해서 만약에 exception이 이전 시도에서 throw되었다면, 값을 emit하려고 시도할 때마다 IllegalStateException을 throw한다. 

## Reactive streams
Flow는 Reactive streams와 호환된다. Flow.asPublisher과 Publisher.asFlow를 써서 reactive stream과 상호운용할 수 있다. 

## Not stable for inheritance
Flow 인터페이스는 다른 오픈소스 라이브러리에서 상속해서 사용하기에 적합하지는 않다. 왜냐하면 새로운 메소드들이 추후에 Flow 인터페이스에 추가될 수 있기 때문이다.  
구현하려면 flow {...} 빌더 함수를 쓰거나, AbstractFlow를 extend해야 한다. 두 방법은 모두 컨텍스트 보존 특성을 위반하지 않으며 개발자가 concurrency와 관련된 실수를 하거나, 일관성없게 flow dispatcher나 cancellation을 쓰는 것을 예방해줄 것이다. 


[참고 사이트]  
[Kotlin 공식 문서 - Flow](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/)  
