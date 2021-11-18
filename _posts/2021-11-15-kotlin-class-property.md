---
layout: post
title:  "코틀린의 Class와 Property"
author: jiwon
categories: [ Kotlin ]
image: assets/images/kotlin_class_property.jpg
---
🥑    
# 개념 1️⃣ Kotlin Class & Property  

코틀린에서 클래스를 선언하려면 자바와 같이 ```class``` 키워드를 써야 한다.
 
```
class Animal{
    var name: String = ""
}
```

클래스 내부에 선언한 name이라는 변수를 ```Property```라고 부르며, 자바에서 ```Member Variable```라고 부르는 것과는 다르다. ```Property```는 ```field```(실제 값을 가짐)와 getter/setter 으로 이루어져 있다. getter/setter는 ```Property```를 선언할 때 기본적으로 제공되고, 원하는 경우에는 custom getter/setter를 선언할 수 있다. 

```
class Multiplier{
    var a: Int = 0
    var b: Int = 0
    
    val sum: Int
        get(){
            return a + b
        }
}
```

위의 Mutliplier 클래스에서 sum은 읽기 전용 ```Property```의 custom getter를 선언하는 것을 보여준다. 
접근자를 호출하는 코드는 함수를 호출하는 것이 아니라(.get() X, .set() X) ```Property```명을 그대로 사용하면 된다. 

```
val multiplier = Multiplier() // new 키워드 필요 x
multiplier.a = 2 // a의 set 접근자 호출
multiplier.b = 3 // b의 set 접근자 호출 
println(multiplier.sum) // sum의 get 접근자 호출 
```

만약에 클래스에 함수가 필요없고, 값만 필요하다면 중괄호를 생략할 수 있는데 이러한 클래스를 ```value object```라고 부른다.
코틀린에서는 ```value object```를 위한 특별한 한정 키워드인 ```data```가 존재한다. 

```
data class Animal(val name: String)
```

```data class```를 선언하면 코틀린 컴파일러는 세 가지의 일을 부가적으로 해준다. 
* equals() / hashCode() 생성
* toString()의 결과를 "Animal(name="gomdol,...)" 형태로 변형
* Property의 순서를 번호로 하는 componentN() 함수 생성

