---
layout: post
title:  "Cold Observable vs Hot Observable"
author: ella
categories: [ Android ]
image: assets/images/hot_cold_title.png
---
π€  
# κ°λ 6οΈβ£ Cold Observable vs Hot Observable

RxJavaμμλ ```Observable```μ subscribeνλ ```Observer```κ° μ‘΄μ¬νλ€. ```Observer```λ ```Observable```μ΄ μμ°¨μ μΌλ‘ λ°ννλ λ°μ΄ν°μ λν΄μ λ°μνλ€. Observableμ κ΅¬ννλ λ°©μμμ Observableκ³Ό Observer μ¬μ΄μ λμ μ°¨μ΄κ° μλλ°, μ΄λ₯Ό Cold Observableκ³Ό Hot Observableμ΄λΌκ³  νλ€.

### 1οΈβ£ Cold Observable 
```Cold Observable```μ Observableμ κ΅¬λμ μμ²­νλ©΄ μμ΄νμ λ°ννκΈ° μμνλ€. μμ΄νμ **μ²μλΆν° λκΉμ§ λ°ν**λκ³ , μμλ‘ μ’λ£μν€μ§ μλ μ΄μ μ¬λ¬ λ² μμ²­ν΄λ **μ²μλΆν° λκΉμ§ λ°ννλ κ²μ λ³΄μ₯**νλ€. interval μ°μ°μλ₯Ό μ΄μ©νμ¬ 1μ΄λ§λ€ μμ΄νμ λ°ννλ Cold Observable μμ  μ½λλ₯Ό νμΈν΄λ³΄μ.
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
Observableμ κ΅¬λνκ³  3μ΄ λ€μ μλ‘μ΄ κ΅¬λμλ‘ λ€μ κ΅¬λνμ λλ **μ²μλΆν° λ€μ μμ΄νμ λ°ν**νλ κ²μ λ³Ό μ μλ€.  

### 2οΈβ£ Hot Observable
λ°λ©΄μ ```Hot Observable```μ μμ΄ν λ°νμ΄ μμλ μ΄νλ‘ λͺ¨λ  κ΅¬λμμκ² λμμ κ°μ μμ΄νμ λ°ννλ€. λ§μ½μ μ²« λ²μ§Έ κ΅¬λμκ° μμ΄νμ λ°ννλ ```Observable```λ₯Ό κ΅¬λνκ³ , λͺ μ΄ λ€μ λ λ²μ§Έ κ΅¬λμκ° λκ°μ ```Observable```μ κ΅¬νλ€λ©΄, λμ κ°μ μμ΄νμ λμμ μμ νμ§λ§, λ λ²μ§Έ κ΅¬λμλ κ΅¬λνκΈ° μ μ λ°νλ μμ΄νμ λμΉ  μ μλ€. 


## Subject 
```Subject```λ Observableκ³Ό Observerλ₯Ό λͺ¨λ κ΅¬νν μΆμ νμμ΄λ©°, νλμ μμ€μμ μ¬λ¬ κ΅¬λμμκ² λ©ν°μΊμ€νμ ν  μ μλ€. Subjectλ Observerλ₯Ό κ΅¬ννλ―λ‘, onNext(), onError(), onComplete() λ©μλλ₯Ό μλμΌλ‘ νΈμΆνμ¬ μ΄λ²€νΈλ₯Ό κ΅¬λμλ€μκ² μ λ¬ν  μ μλ€. Subjectλ₯Ό κ΅¬νν νμ μ€μμ κ°μ₯ λ§μ΄ μ°μ΄λ (1) ```PublishSubject```μ (2) ```BehaviorSubject```μ λν΄ μμλ³΄μ.  

### 1οΈβ£ PublishSubject
```PublishSubject```λ Subjectλ₯Ό κ΅¬νν κ°μ₯ λ¨μν νμ μ€ νλμ΄λ€. ```PublishSubject``` κ°μ²΄λ₯Ό μμ±νκ³  κ°λ¨ν μ΄λ²€νΈλ₯Ό λΈλ‘λμΊμ€ννλ μμ λ₯Ό νμΈν΄λ³΄μ. 
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
create() λ©μλλ‘ Subject κ°μ²΄λ₯Ό μμ±νλ€. ```Subject```λ Observableμ΄λ©΄μ, Observerμ΄λ―λ‘ λ°νκ³Ό κ΅¬λμ λͺ¨λ Subject κ°μ²΄λ₯Ό ν΅ν΄ νλ κ²μ λ³Ό μ μλ€(onNext, subscribe). Subjectλ₯Ό μ΄μ©ν  λ μ£Όμν  μ μ ```Subject```κ° **Hot Observable**μ΄λΌλ κ²μ΄λ€.  
μλμ μ½λμ κ°μ΄ μμ΄νμ λ°νν λ€ κ΅¬λνλ©΄ μμ΄νμ 1λ μλΉν  μ μλ€. 
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

```Subject```λ Observerμ΄κΈ°λ νλ€. λ°λΌμ λ€λ₯Έ Observableμ κ΅¬λμλ‘μ μ΄λ²€νΈλ₯Ό μ²λ¦¬ν  μλ μλ€. μλΉνλ μμ΄νμ λ€μ Observableλ‘ κ΅¬λνμ¬ λ€λ₯Έ κ΅¬λμμκ² μ λ¬νλ μμ  μ½λλ₯Ό νμΈν΄λ³΄μ. 
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

μμ μ½λλ₯Ό λ³΄λ©΄ PublishSubjectμΈ subjectλ₯Ό ν΅ν΄ κ΅¬λνκ³  μμ΄νμ μ¬λ°ννλ©°, λ Observable μμ€λ₯Ό λ¬Άμ΄μ μ΄λ²€νΈ κ΄λ¦¬ν  μ μμμ μ μ μλ€(System.out::printlnμΌλ‘ src1+src2 μμ΄νμ΄ κ°μ΄ λμ΄!). 

### 2οΈβ£ BehaviorSubject
λ λ€λ₯Έ Subjectμ μλΈ ν΄λμ€λ‘ ```BehaviorSubject```κ° μλ€. ```BehaviorSubject```λ PublishSubjectμ λμΌνκ² λμνμ§λ§, μλ‘μ΄ Observerλ₯Ό ν΅ν΄ κ΅¬λν  λ, **κ°μ₯ λ§μ§λ§ μμ΄νλ§μ λ°ν**νλ€λ μ°¨μ΄μ μ΄ μλ€. BehaviorSubjectλ **κ°μ₯ μ΅κ·Ό μνκ°μ κ°μ Έμ€λ κ²μ΄ μ€μ**ν  λ μ¬μ©ν  μ μλ€. 

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

μμμ νμΈν κ²κ³Ό κ°μ΄ ```BehaviorSubject```λ κ΅¬λμ μμν  λ Subjectκ° **λ§μ§λ§μΌλ‘ λ°νν μμ΄νμ κ°μ Έμ€λ©°**, μ΄νμ λ°νλλ μμ΄νλ€μ λͺ¨λ PublishSubjectμ λμΌνκ² λͺ¨λ μμ ν  μ μλ€.
