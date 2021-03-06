---
layout: post
title:  "Shared mutable state & concurreny"
author: ella
categories: [ Android, Coroutine ]
image: assets/images/shared_mutable_state_title.jpeg
---
๐ค  
# ๊ฐ๋ 8๏ธโฃ Shared mutable state and concurreny  
์ฝ๋ฃจํด์ Dispatchers.Default์ ๊ฐ์ ๋ฉํฐ์ค๋ ๋ Dispatcher๋ฅผ ์ฌ์ฉํ์ฌ **๋ณ๋ ฌ**๋ก ์ํ๋๋ค. ์ด๋ฌํ ์ฝ๋ฃจํด์์ ๋ณ๋ ฌ๋ก ๋ฌด์ธ๊ฐ๋ฅผ ์ํํ  ๋ ์ฃผ์ํด์ผํ  ๋ฌธ์ ๊ฐ ์๋๋ฐ, ๋ฐ๋ก **shared mutable state**์ ์ ๊ทผํ  ๋์ **synchronization** ๋ฌธ์ ์ด๋ค. 
## Problem ๐ฟ
์์ฒ๋ฒ ๊ฐ์ ํ๋์ ๋ฐ๋ณตํ๋ ๋ฐฑ๊ฐ์ ์ฝ๋ฃจํด์ ```launch``` ํ๋ค๊ณ  ํด๋ณด์. 
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
์ด๋ฌํ suspend ํจ์์ counter๋ผ๋ shared mutable variable๋ฅผ ์ฆ๊ฐ์ํค๋ ๊ฐ๋จํ action์ ํ๋ผ๋ฏธํฐ๋ก ๋ฃ์ด์ ๋๋ ค๋ณด์. ์ฌ๊ธฐ์๋ Dispatcher๋ก Dispatcher.Default๋ฅผ ์ฌ์ฉํ๋ค. 
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

์์ ์ฝ๋๋ฅผ ๋๋ ธ์ ๋ ์ด๋ค ๊ฐ์ด ์ถ๋ ฅ๋ ๊น??๐ฏ ๋จ์ํ๊ฒ ์๊ฐํ๋ฉด ์๋์ ๊ฐ์ ๊ฐ๋ค์ด ์ถ๋ ฅ๋  ๊ฒ ๊ฐ๋ค.
```
// ๋จ์ํ ์๊ฐํ์ ๋์ ์ถ๋ ฅ๊ฐ
Completed 100000 actions in 23 ms
Counter = 100000
```

ํ์ง๋ง, ์ค์ ๋ก๋ Counter์ ๊ฐ์ด ์์๋ ์ ํ ๋ค๋ฅธ ๊ฐ์ด ๋์ฌ ๊ฒ์ด๋ค!! ์๋ํ๋ฉด 100๊ฐ์ ์ฝ๋ฃจํด์ด ```counter```๋ผ๋ ๋ณ์๋ฅผ **synchronization** ํ์ง ์๊ณ  ๋ฉํฐ์ค๋ ๋๋ก ๋์์ ์ฆ๊ฐ์์ผฐ๊ธฐ ๋๋ฌธ์ด๋ค.  
๋ฐ๋ผ์ ์ค์  ์ถ๋ ฅ๊ฐ์ ์๋์ ๊ฐ์ด ๋์ค๊ฒ ๋๋ค.
```
Completed 100000 actions in 23 ms
Counter = 85519
```

## โก๏ธ Volatiles๋ก๋ ํด๊ฒฐ์ด ์๋ ๊น..?๐ค
๊ทธ๋ ๋ค๋ฉด ํน์ ```volatile``` ๋ณ์๊ฐ concurrency ๋ฌธ์ ๋ฅผ ํด๊ฒฐํด์ฃผ์ง ์์๊น..? 
์์ ์ฝ๋์ counter ๋ณ์์ ```Volatile``` ์ด๋ธํ์ด์์ ๋ถ์ฌ์ ๋ค์ ์ฝ๋๋ฅผ ๋๋ ค๋ณด์!!
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
์ฝ๋๊ฐ ์์ ๋ณด๋ค ๋ ๋๋ฆฌ๊ฒ ๋์๊ฐ๊ธฐ๋ ํ์ง๋ง, ์ฌ์ ํ Counter๊ฐ 100000์ผ๋ก ์ฐํ์ง๋ ์๋๋ค. ์๋ํ๋ฉด volatile ๋ณ์๊ฐ ํด๋น ๋ณ์์ linearizable(=atomic)ํ read & write๋ฅผ ๋ณด์ฅํด์ฃผ๊ธด ํ์ง๋ง, ๋ ํฐ action๋ค์๊ฒ atomocity๋ฅผ ์ ๊ณตํ์ง ์๊ธฐ ๋๋ฌธ์ด๋ค. 

## ๐ Thread-safeํ ์๋ฃ๊ตฌ์กฐ๋ค
thread-safeํ ์๋ฃ๊ตฌ์กฐ๋ฅผ ์ฌ์ฉํ๋ค๋ฉด (1)์ค๋ ๋์ (2)์ฝ๋ฃจํด์ ํด๊ฒฐ์ฑ์ด ๋  ์ ์๋ค. thread-safeํ ์๋ฃ๊ตฌ์กฐ๋ **shared state**๋ฅผ ์ด์ฉํ๋ ์ฐ์ฐ์ ์ํ synchronization ๊ธฐ๋ฅ์ ์ ๊ณตํด์ฃผ๋ฉฐ, synchronized, linearizable, atomic์ด ์ด์ ํด๋นํ๋ค. 
## 1๏ธโฃ AtomicInteger
๊ฐ๋จํ counter์ผ ๊ฒฝ์ฐ์๋ ```AtomicInteger```๋ฅผ ์ธ ์ ์์ผ๋ฉฐ, ```AtomicInteger```๋ ```incrementAndGet()``` ๋ฉ์๋๋ฅผ ๊ฐ์ง๋ค. 
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
AmomicInteger๋ฅผ ์ฐ๋ฉด ์์์ Volatile ๋ณ์๋ฅผ ์ผ์ ๋์๋ ๋ฌ๋ฆฌ counter๊ฐ 100000๊ฐ ์ ํํ ์ฐํ๋ ๊ฒ์ ๋ณผ ์ ์๋. AtomicInteger๋ ๋ณดํต์ counter์ collection, queue ๊ทธ๋ฆฌ๊ณ  ๋ค๋ฅธ ํ์ค ์๋ฃ๊ตฌ์กฐ๋ ๊ธฐ๋ณธ ์ฐ์ฐ์์ ๋๊ธฐํ ๋ฌธ์ ๋ฅผ ํด๊ฒฐํด์ค๋ค.  
๊ทธ๋ฌ๋, thread-safe๋ฅผ ์ํ implementation์ด ๋์ง์์ ๋ณต์กํ state๋ ๋ณต์กํ ์ฐ์ฐ์ ๊ณ์ฐํ๋ ๊ฒ์ ์ด๋ ค์ด ์ผ์ด๋ค.

## 2๏ธโฃ Thread confinement fine-grained
**Thread confinement**, ์ฆ ์ค๋ ๋๋ฅผ ์ ํํ๋ ๋ฐฉ๋ฒ์ ์ฐ๋ฉด shared mutable state์ ๋๊ธฐํ ๋ฌธ์ ์ ๋๋ค๋ฅธ ํด๊ฒฐ์ฑ์ด ๋  ์ ์๋ค. ์ด ๋ฐฉ์์ ํน์  shared state๋ก ์ ๊ทผํ๋ ๋ชจ๋  ๊ฒ๋ค์ด **๋จ์ผ ์ค๋ ๋**๋ฅผ ์ด์ฉํ๊ฒ ํ๋ ๊ฒ์ด๋ค. ์ฃผ๋ก UI ์ดํ๋ฆฌ์บ์ด์์์ ์ฌ์ฉ๋๋๋ฐ, ์ฌ๊ธฐ์ ๋ชจ๋  UI state๋ **single event-dispatch/application ์ค๋ ๋๋ก ์ ํ**๋๋ค. ์ฝ๋ฃจํด์์ **single-thread context** ์ฌ์ฉํจ์ผ๋ก์จ ์ ์ฉํ  ์ ์๋ค. 
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
์์ ์ฝ๋๋ ๊ฐ๊ฐ์ increment๋ฅผ single-thread๋ก ์ ํํ๋ **fine**-grained thread-confinement๋ฅผ ์ํํ๊ธฐ ๋๋ฌธ์ ๋งค์ฐ ๋๋ฆฌ๊ฒ ๋์ํ๋ค. ๊ฐ๊ฐ์ ++์ withContext ๋ธ๋ก์ ์ด์ฉํ์ฌ multi-threaded **Dispatchers.Default** ์ปจํ์คํธ์์ **single-threaded** ์ปจํ์คํธ๋ก ์ ํ์ํจ๋ค.

## 3๏ธโฃ Thread confinement coarse-grained
thread-confinement๋ ์๋ฅผ ๋ค์ด์, state๋ฅผ ์๋ฐ์ดํธ ์ํค๋ ๋น์ฆ๋์ค ๋ก์ง์ ์ฑ๊ธ ์ค๋ ๋๋ก ์ ํ์ํค๋ ๊ฒฝ์ฐ์ ๊ฐ์ด ํฐ ๋ฉ์ด๋ฆฌ์์ ์ฌ์ฉ๋๋ค. ์๋์ ์์ ์์๋ single thread ์ปจํ์คํธ์์ ๊ฐ ์ฝ๋ฃจํด์ ์คํํ๋ฉด์ ์์ํ๋ค. 
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
์์ ์ฝ๋๋ ์์์์ increment๋ง๋ค single-thread๋ก ์ ํํ๋ fineํ ๊ฒฝ์ฐ์๋ ๋ฌ๋ฆฌ, **coarse**ํ๊ฒ massiveRun ๋ญํ์ด๋ฅผ single-thread๋ก ์ ํํจ์ผ๋ก์จ ๋ ๋น ๋ฅด๊ฒ ๋์ํ๊ณ  ์ ํํ ๊ฒฐ๊ณผ๋ฅผ ๋ด๋ ๊ฒ์ ๋ณผ ์ ์๋ค.

## 4๏ธโฃ Mutual exclusion
์ํธ ๋ฐฐ์ ๋ ๋์์ ์ ๊ทผํด์๋ ์ ๋๋ก ์๋๋ ์๊ณ์์ญ์์ shared state๋ฅผ ์์ ํ  ๋ ์ธ ์ ์๋ ๋ฐฉ๋ฒ์ด๋ค. blocking์ ํ  ๋ ๊ฐ๋ฐ์๋ค์ ๋ณดํต ```synchronized```๋  ```ReentrantLock```๋ฅผ ์ด์ฉํ๋ค. ์ฝ๋ฃจํด์ ๋ค๋ฅธ ํด๊ฒฐ์ฑ์ผ๋ก๋ **Mutex**๊ฐ ์๋ค. **Mutex**๋ ์๊ณ์์ญ์ ์ํ๋ฅผ ๊ตฌ๋ถํ๊ธฐ ์ํด์ lock ๋ฉ์๋์ unlock ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์๋ค. Mutex.lock()๋ฉ์๋๋ suspending ํจ์์ด๋ฉฐ thread๋ฅผ blockํ์ง ์๋๋ค๋ ํน์ง์ด ์๋ค.  
withLock์ด๋ผ๋ ํ์ฅํจ์๋ฅผ ์ฌ์ฉํ๋ฉด ```mutex.lock(); try { ... } finally { mutex.unlock() }``` ํจํด์ ๊ฐ๋จํ ์ ์ฉํ  ์ ์๋ค. 
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
์์ ์์ ๋ fine-grainedํ locking ์์ ๋ผ์ ๋น์ฉ์ด ๋ง์ด ๋ ๋ค. ๊ทธ๋ฌ๋, ์ฃผ๊ธฐ์ ์ผ๋ก shared state๋ฅผ ๋ณ๊ฒฝํด์ผ ํ์ง๋ง ํด๋น state๋ฅผ confine์ํฌ ์ค๋ ๋๊ฐ ๊ธฐ์กด์ ์ค๋ ๋ ํ์ ๋ง๋ํ ์๋ ๊ฒฝ์ฐ์๋ ์ข์ ํด๊ฒฐ์ฑ์ด ๋  ์ ์๋ค. 

## 5๏ธโฃ Actors
actor๋ state(์ฝ๋ฃจํด์ confine๋๊ณ  ์บก์ํ๋๋)์ channel(๋ค๋ฅธ ์ฝ๋ฃจํด๋ค๊ณผ ์ปค๋ฎค๋์ผ์ด์ํ๋)๋ก ์ด๋ฃจ์ด์ง ์ฝ๋ฃจํด entity์ด๋ค. ๊ฐ๋จํ actor๋ ๊ทธ๋ฅ ํจ์๋ก ์ธ ์ ์์ง๋ง, ๋ณต์กํ state๋ฅผ ๊ฐ์ง actor๋ ํด๋์ค๋ก ์์ฑํ๋ ๊ฒ์ด ๋ ์ ํฉํ๋ค.  
actor์ ์ฝ๋ฃจํด ๋น๋๋ ์กํฐ์ ๋ํ ๋จ์ผ ์ฐธ์กฐ ํธ๋ค๋ก ์ฌ์ฉํ  ์ ์๊ฒ ํ๊ธฐ ์ํด์ (1) ๋ฉ์์ง๋ฅผ ์์ ํ๊ธฐ ์ํด์ actor์ mailbox channel์ scope์ ๊ฒฐํฉํ๋ค. ๊ทธ๋ฆฌ๊ณ  (2) ์ ์ก ์ฑ๋์ ๊ฒฐ๊ณผ job object์ ๊ฒฐํฉํ๋ค. 
actor๋ฅผ ์ฐ๊ธฐ ์ํด ์ฒซ ๋ฒ์งธ๋ก ํ  ์ผ์ actor๊ฐ ์ฒ๋ฆฌํ  ๋ฉ์์ง ํด๋์ค๋ฅผ ์ ์ํ๋ ๊ฒ์ด๋ค. ์ฝํ๋ฆฐ์ **sealed class**๊ฐ ์ด๋ฌํ ๋ฉ์์ง ํด๋์ค๋ฅผ ์ ์ํ  ๋ ์ ํฉํ๋ค. ๋ฐ์ ์์ ์์๋ CounterMsg sealed class๋ฅผ ์ด์ฉํ์ฌ IncCounter ๋ฉ์์ง์ GetCounter ๋ฉ์์ง๋ฅผ ์ ์ํ์๋ค. IncCountr ๋ฉ์์ง๋ counter๋ฅผ ์ฆ๊ฐ์ํจ๋ค. ๊ทธ๋ฆฌ๊ณ  GetCounter๋ ๊ฐ์ getํด์ค๋ค. GetCounter๋ response๋ฅผ ๋ณด๋ด์ผ ํ๋ฏ๋ก ํ๋ผ๋ฏธํฐ๋ก ๋ฌ๋ ค์๋ค. ```CompletedDeffered```(single value๋ฅผ ๋ํ๋)๊ฐ response๋ก ์ฌ์ฉ๋๊ฒ ๋๋ค. 
``` 
// Message types for counterActor
sealed class CounterMsg
object IncCounter : CounterMsg() // one-way message to increment counter
class GetCounter(val response: CompletableDeferred<Int>) : CounterMsg() // a request with reply
``` 
๊ทธ๋ฌ๊ณ  ๋์, actor ์ฝ๋ฃจํด ๋น๋๋ฅผ ์ฌ์ฉํ์ฌ actor๋ฅผ launchํ๋ ํจ์๋ฅผ ์ ์ํ๋ค. 
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
์ด๋ฅผ ์ด์ฉํ์ฌ ๋ฉ์ธ ํจ์๋ฅผ ์์ฑํด๋ณด์. 
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
์ด๋ค context์์ acter๊ฐ ์ํ๋๋๋๋ ์ค์ํ์ง ์๋ค. actor ์์ฒด๊ฐ ์ฝ๋ฃจํด์ด๊ณ , ์ฝ๋ฃจํด์ ์์ฐจ์ ์ผ๋ก ์คํ๋์ด์ ํน์  ์ฝ๋ฃจํด์ผ๋ก state๋ฅผ ํ์ ํ๋ ๊ฒ์ shared mutable state์ ๋ฌธ์ ์ ์ ๋ํ ํด๊ฒฐ์ฑ์ด ๋  ์ ์๋ค. actor๋ private state๋ฅผ ๋ณ๊ฒฝ์ํฌ ์ง๋ ๋ชจ๋ฅด์ง๋ง, lockํ  ํ์ ์์ด message๋ฅผ ํตํด์ ์๋ก ์ํฅ์ ๋ผ์น  ๋ฟ์ด๋ค.  
์์ ์ํฉ์์๋ Actor๊ฐ ํญ์ ์์ํ  ๊ฒ์ด ์๊ณ  ๋ค๋ฅธ context๋ก ์ ํ์ํฌ ํ์๊ฐ ์๊ธฐ ๋๋ฌธ์, Actor๊ฐ load ํ์ locking์ ์ํํ๋ ๊ฒ๋ณด๋ค ๋ ํจ์จ์ ์ธ ๋ฐฉ๋ฒ์ด๋ค.  

[์ฐธ๊ณ  ์ฌ์ดํธ]  
[Kotlin ๊ณต์ ๋ฌธ์ - Shared mutable state and concurrency](https://kotlinlang.org/docs/shared-mutable-state-and-concurrency.html)  
