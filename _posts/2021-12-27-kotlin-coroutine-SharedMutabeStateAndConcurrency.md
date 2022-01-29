---
layout: post
title:  "Shared mutable state & concurreny"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/shared_mutable_state_title.jpeg
---
🤖  
# 개념 8️⃣ Shared mutable state and concurreny  
코루틴은 Dispatchers.Default와 같은 멀티스레드 Dispatcher를 사용하여 **병렬**로 수행된다. 이러한 코루틴에서 병렬로 무언가를 수행할 때 주의해야할 문제가 있는데, 바로 **shared mutable state**에 접근할 때의 **synchronization** 문제이다. 
## Problem 👿
수천번 같은 행동을 반복하는 백개의 코루틴을 ```launch``` 한다고 해보자. 
```
suspend fun massiveRun(action: suspend () -> Unit) {
    val n = 100  // number of coroutines to launch
    val k = 1000 // times an action is repeated by each coroutine
    val time = measureTimeMillis {
        coroutineScope { // scope for coroutines
            repeat(n) {
                launch {
                    repeat(k) { action() }
                }
            }
        }
    }
    println("Completed ${n * k} actions in $time ms")
}
```
이러한 suspend 함수에 counter라는 shared mutable variable를 증가시키는 간단한 action을 파라미터로 넣어서 돌려보자. 여기서는 Dispatcher로 Dispatcher.Default를 사용했다. 
```
var counter = 0

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            counter++
        }
    }
    println("Counter = $counter")
}
```

위의 코드를 돌렸을 때 어떤 값이 출력될까??😯 단순하게 생각하면 아래와 같은 값들이 출력될 것 같다.
```
// 단순히 생각했을 때의 출력값
Completed 100000 actions in 23 ms
Counter = 100000
```

하지만, 실제로는 Counter의 값이 위와는 전혀 다른 값이 나올 것이다!! 왜냐하면 100개의 코루틴이 ```counter```라는 변수를 **synchronization** 하지 않고 멀티스레드로 동시에 증가시켰기 때문이다.  
따라서 실제 출력값은 아래와 같이 나오게 된다.
```
Completed 100000 actions in 23 ms
Counter = 85519
```

## ➡️ Volatiles로는 해결이 안될까..?🤔
그렇다면 혹시 ```volatile``` 변수가 concurrency 문제를 해결해주지 않을까..? 
위의 코드의 counter 변수에 ```Volatile``` 어노테이션을 붙여서 다시 코드를 돌려보자!!
```
@Volatile // in Kotlin `volatile` is an annotation 
var counter = 0

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            counter++
        }
    }
    println("Counter = $counter")
}
```
코드가 앞전보다 더 느리게 돌아가기는 하지만, 여전히 Counter가 100000으로 찍히지는 않는다. 왜냐하면 volatile 변수가 해당 변수에 linearizable(=atomic)한 read & write를 보장해주긴 하지만, 더 큰 action들에게 atomocity를 제공하지 않기 때문이다. 

## 🌟 Thread-safe한 자료구조들
thread-safe한 자료구조를 사용한다면 (1)스레드와 (2)코루틴의 해결책이 될 수 있다. thread-safe한 자료구조는 **shared state**를 이용하는 연산을 위한 synchronization 기능을 제공해주며, synchronized, linearizable, atomic이 이에 해당한다. 
## 1️⃣ AtomicInteger
간단한 counter일 경우에는 ```AtomicInteger```를 쓸 수 있으며, ```AtomicInteger```는 ```incrementAndGet()``` 메소드를 가진다. 
```  
val counter = AtomicInteger()

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            counter.incrementAndGet()
        }
    }
    println("Counter = $counter")
}
```
```  
Completed 100000 actions in 26 ms
Counter = 100000
```  
AmomicInteger를 쓰면 위에서 Volatile 변수를 썼을 때와는 달리 counter가 100000가 정확히 찍히는 것을 볼 수 있댜. AtomicInteger는 보통의 counter와 collection, queue 그리고 다른 표준 자료구조나 기본 연산에서 동기화 문제를 해결해준다.  
그러나, thread-safe를 위한 implementation이 되지않은 복잡한 state나 복잡한 연산을 계산하는 것은 어려운 일이다.

## 2️⃣ Thread confinement fine-grained
**Thread confinement**, 즉 스레드를 제한하는 방법을 쓰면 shared mutable state의 동기화 문제에 또다른 해결책이 될 수 있다. 이 방식은 특정 shared state로 접근하는 모든 것들이 **단일 스레드**를 이용하게 하는 것이다. 주로 UI 어플리캐이션에서 사용되는데, 여기서 모든 UI state는 **single event-dispatch/application 스레드로 제한**된다. 코루틴에서 **single-thread context** 사용함으로써 적용할 수 있다. 
```  
val counterContext = newSingleThreadContext("CounterContext")
var counter = 0

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            // confine each increment to a single-threaded context
            withContext(counterContext) {
                counter++
            }
        }
    }
    println("Counter = $counter")
}
```  
```  
Completed 100000 actions in 2190 ms
Counter = 100000
```  
위의 코드는 각각의 increment를 single-thread로 제한하는 **fine**-grained thread-confinement를 수행하기 때문에 매우 느리게 동작한다. 각각의 ++은 withContext 블록을 이용하여 multi-threaded **Dispatchers.Default** 컨텍스트에서 **single-threaded** 컨텍스트로 전환시킨다.

## 3️⃣ Thread confinement coarse-grained
thread-confinement는 예를 들어서, state를 업데이트 시키는 비즈니스 로직을 싱글 스레드로 제한시키는 경우와 같이 큰 덩어리에서 사용된다. 아래의 예제에서는 single thread 컨텍스트에서 각 코루틴을 실행하면서 시작한다. 
``` 
val counterContext = newSingleThreadContext("CounterContext")
var counter = 0

fun main() = runBlocking {
    // confine everything to a single-threaded context
    withContext(counterContext) {
        massiveRun {
            counter++
        }
    }
    println("Counter = $counter")
}
``` 
위의 코드는 앞에서의 increment마다 single-thread로 제한하는 fine한 경우와는 달리, **coarse**하게 massiveRun 뭉텅이를 single-thread로 제한함으로써 더 빠르게 동작하고 정확한 결과를 내는 것을 볼 수 있다.

## 4️⃣ Mutual exclusion
상호 배제는 동시에 접근해서는 절대로 안되는 임계영역에서 shared state를 수정할 때 쓸 수 있는 방법이다. blocking을 할 때 개발자들은 보통 ```synchronized```나  ```ReentrantLock```를 이용한다. 코루틴의 다른 해결책으로는 **Mutex**가 있다. **Mutex**는 임계영역의 상태를 구분하기 위해서 lock 메소드와 unlock 메소드를 가지고 있다. Mutex.lock()메소드는 suspending 함수이며 thread를 block하지 않는다는 특징이 있다.  
withLock이라는 확장함수를 사용하면 ```mutex.lock(); try { ... } finally { mutex.unlock() }``` 패턴을 간단히 적용할 수 있다. 
``` 
val mutex = Mutex()
var counter = 0

fun main() = runBlocking {
    withContext(Dispatchers.Default) {
        massiveRun {
            // protect each increment with lock
            mutex.withLock {
                counter++
            }
        }
    }
    println("Counter = $counter")
}
``` 
위의 예제는 fine-grained한 locking 예제라서 비용이 많이 든다. 그러나, 주기적으로 shared state를 변경해야 하지만 해당 state를 confine시킬 스레드가 기존의 스레드 풀에 마땅히 없는 경우에는 좋은 해결책이 될 수 있다. 

## 5️⃣ Actors
actor는 state(코루틴에 confine되고 캡슐화되는)와 channel(다른 코루틴들과 커뮤니케이션하는)로 이루어진 코루틴 entity이다. 간단한 actor는 그냥 함수로 쓸 수 있지만, 복잡한 state를 가진 actor는 클래스로 작성하는 것이 더 적합하다.  
actor의 코루틴 빌더는 액터에 대한 단일 참조 핸들로 사용할 수 있게 하기 위해서 (1) 메시지를 수신하기 위해서 actor의 mailbox channel을 scope에 결합한다. 그리고 (2) 전송 채널을 결과 job object에 결합한다. 
actor를 쓰기 위해 첫 번째로 할 일은 actor가 처리할 메시지 클래스를 정의하는 것이다. 코틀린의 **sealed class**가 이러한 메시지 클래스를 정의할 때 적합하다. 밑의 예제에서는 CounterMsg sealed class를 이용하여 IncCounter 메시지와 GetCounter 메시지를 정의하였다. IncCountr 메시지는 counter를 증가시킨다. 그리고 GetCounter는 값을 get해준다. GetCounter는 response를 보내야 하므로 파라미터로 달려있다. ```CompletedDeffered```(single value를 나타냄)가 response로 사용되게 된다. 
``` 
// Message types for counterActor
sealed class CounterMsg
object IncCounter : CounterMsg() // one-way message to increment counter
class GetCounter(val response: CompletableDeferred<Int>) : CounterMsg() // a request with reply
``` 
그러고 나서, actor 코루틴 빌더를 사용하여 actor를 launch하는 함수를 정의한다. 
``` 
// This function launches a new counter actor
fun CoroutineScope.counterActor() = actor<CounterMsg> {
    var counter = 0 // actor state
    for (msg in channel) { // iterate over incoming messages
        when (msg) {
            is IncCounter -> counter++
            is GetCounter -> msg.response.complete(counter)
        }
    }
}
``` 
이를 이용하여 메인 함수를 작성해보자. 
``` 
fun main() = runBlocking<Unit> {
    val counter = counterActor() // create the actor
    withContext(Dispatchers.Default) {
        massiveRun {
            counter.send(IncCounter)
        }
    }
    // send a message to get a counter value from an actor
    val response = CompletableDeferred<Int>()
    counter.send(GetCounter(response))
    println("Counter = ${response.await()}")
    counter.close() // shutdown the actor
}
``` 
```
Completed 100000 actions in 1016 ms
Counter = 100000
```
어떤 context에서 acter가 수행되느냐는 중요하지 않다. actor 자체가 코루틴이고, 코루틴은 순차적으로 실행되어서 특정 코루틴으로 state를 한정하는 것은 shared mutable state의 문제점에 대한 해결책이 될 수 있다. actor는 private state를 변경시킬 지도 모르지만, lock할 필요 없이 message를 통해서 서로 영향을 끼칠 뿐이다.  
위의 상황에서는 Actor가 항상 작업할 것이 있고 다른 context로 전환시킬 필요가 없기 때문에, Actor가 load 하에 locking을 수행하는 것보다 더 효율적인 방법이다.  

[참고 사이트]  
[Kotlin 공식 문서 - Shared mutable state and concurrency](https://kotlinlang.org/docs/shared-mutable-state-and-concurrency.html)  
