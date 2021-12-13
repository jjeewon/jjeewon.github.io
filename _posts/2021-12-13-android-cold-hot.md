---
layout: post
title:  "Cold Observable vs Hot Observable"
author: ella
categories: [ Android ]
image: assets/images/hot_cold_title.png
---
🤖  
# 개념 6️⃣ Cold Observable vs Hot Observable

RxJava에서는 ```Observable```을 subscribe하는 ```Observer```가 존재한다. ```Observer```는 ```Observable```이 순차적으로 발행하는 데이터에 대해서 반응한다. Observable을 구현하는 방식에서 Observable과 Observer 사이에 동작 차이가 있는데, 이를 Cold Observable과 Hot Observable이라고 한다.

### 1️⃣ Cold Observable 
```Cold Observable```은 Observable에 구독을 요청하면 아이템을 발행하기 시작한다. 아이템은 **처음부터 끝까지 발행**되고, 임의로 종료시키지 않는 이상 여러 번 요청해도 **처음부터 끝까지 발행하는 것을 보장**한다. interval 연산자를 이용하여 1초마다 아이템을 발행하는 Cold Observable 예제 코드를 확인해보자.
```
 val src = Observable.interval(1, TimeUnit.SECONDS)
        src.subscribe{
                value -> println("#1: $value")
        }
        Thread.sleep(3000)
        src.subscribe {
                value -> println("#2: $value")
        }
        Thread.sleep(3000)
```        
```  
#1: 0
#1: 1
#1: 2
#1: 3
#2: 0
#1: 4
#2: 1
#1: 5
#2: 2
```  
Observable을 구독하고 3초 뒤에 새로운 구독자로 다시 구독했을 때도 **처음부터 다시 아이템을 발행**하는 것을 볼 수 있다.  

### 2️⃣ Hot Observable
반면에 ```Hot Observable```은 아이템 발행이 시작된 이후로 모든 구독자에게 동시에 같은 아이템을 발행한다. 만약에 첫 번째 구독자가 아이템을 발행하는 ```Observable```를 구독하고, 몇 초 뒤에 두 번째 구독자가 똑같은 ```Observable```을 구한다면, 둘은 같은 아이템을 동시에 수신하지만, 두 번째 구독자는 구독하기 전에 발행된 아이템을 놓칠 수 있다. 


## Subject 
```Subject```는 Observable과 Observer를 모두 구현한 추상 타입이며, 하나의 소스에서 여러 구독자에게 멀티캐스팅을 할 수 있다. Subject는 Observer를 구현하므로, onNext(), onError(), onComplete() 메소드를 수동으로 호출하여 이벤트를 구독자들에게 전달할 수 있다. Subject를 구현한 타입 중에서 가장 많이 쓰이는 (1) ```PublishSubject```와 (2) ```BehaviorSubject```에 대해 알아보자.  

### 1️⃣ PublishSubject
```PublishSubject```는 Subject를 구현한 가장 단순한 타입 중 하나이다. ```PublishSubject``` 객체를 생성하고 간단히 이벤트를 브로드캐스팅하는 예제를 확인해보자. 
```
val src: Subject<String> = PublishSubject.create()
        src.subscribe({item -> println("A: $item")},
            {t -> Timber.e(t)},
            { -> println("A: onComplete")})
        src.subscribe({item -> println("B: $item")},
            {t -> Timber.e(t)},
            { -> println("B: onComplete")})
        src.onNext("Hello")
        src.onNext("World")
        src.onNext("!!!")
        src.onComplete()
```
```
A: Hello
B: Hello
A: World
B: World
A: !!!
B: !!!
A: onComplete
B: onComplete
```
create() 메소드로 Subject 객체를 생성했다. ```Subject```는 Observable이면서, Observer이므로 발행과 구독을 모두 Subject 객체를 통해 하는 것을 볼 수 있다(onNext, subscribe). Subject를 이용할 때 주의할 점은 ```Subject```가 **Hot Observable**이라는 것이다.  
아래의 코드와 같이 아이템을 발행한 뒤 구독하면 아이템을 1도 소비할 수 없다. 
```
 val src: Subject<String> = PublishSubject.create()
        src.onNext("Hello")
        src.onNext("World")
        src.onNext("!!!")
        src.onComplete()
        src.subscribe({item -> println("A: $item")},
            {t -> Timber.e(t)},
            { -> println("A: onComplete")})
        src.subscribe({item -> println("B: $item")},
            {t -> Timber.e(t)},
            { -> println("B: onComplete")})
```
```
A: onComplete
B: onComplete
```

```Subject```는 Observer이기도 하다. 따라서 다른 Observable의 구독자로서 이벤트를 처리할 수도 있다. 소비하는 아이템은 다시 Observable로 구독하여 다른 구독자에게 전달하는 예제 코드를 확인해보자. 
```
val src1 = Observable.interval(1, TimeUnit.SECONDS)
        val src2 = Observable.interval(500, TimeUnit.MILLISECONDS)
        val subject: Subject<String> = PublishSubject.create()

        src1.map{item -> "A: $item"}.subscribe(subject)
        src2.map{item -> "B: $item"}.subscribe(subject)
        subject.subscribe(System.out::println)
        Thread.sleep(5000)
```
```
B: 0
B: 1
A: 0
B: 2
B: 3
A: 1
B: 4
B: 5
A: 2
B: 6
A: 3
B: 7
B: 8
B: 9
A: 4
```

위의 코드를 보면 PublishSubject인 subject를 통해 구독하고 아이템을 재발행하며, 두 Observable 소스를 묶어서 이벤트 관리할 수 있음을 알 수 있다(System.out::println으로 src1+src2 아이템이 같이 나옴!). 

### 2️⃣ BehaviorSubject
또 다른 Subject의 서브 클래스로 ```BehaviorSubject```가 있다. ```BehaviorSubject```는 PublishSubject와 동일하게 동작하지만, 새로운 Observer를 통해 구독할 때, **가장 마지막 아이템만을 발행**한다는 차이점이 있다. BehaviorSubject는 **가장 최근 상태값을 가져오는 것이 중요**할 때 사용할 수 있다. 

 ```
 val subject: Subject<Int> = BehaviorSubject.create()
        subject.subscribe{item->println("A: $item")}
        subject.onNext(1)
        subject.onNext(2)
        subject.subscribe{item->println("B: $item")}
        subject.onNext(3)
        subject.subscribe{item->println("C: $item")}
```

```
A: 1
A: 2
B: 2
A: 3
B: 3
C: 3
```

위에서 확인한 것과 같이 ```BehaviorSubject```는 구독을 시작할 때 Subject가 **마지막으로 발행한 아이템을 가져오며**, 이후에 발행되는 아이템들은 모두 PublishSubject와 동일하게 모두 수신할 수 있다.
