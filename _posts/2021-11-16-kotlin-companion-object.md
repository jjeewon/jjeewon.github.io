---
layout: post
title:  "코틀린의 companion object & Singleton"
author: ella
categories: [ Kotlin ]
image: assets/images/kotlin_companionobject_object.jpg
---
🥑    
# 개념 3️⃣ companion object & Singleton  

## companion object  
코틀린에는 자바와는 다르게 ```static``` 키워드가 없다. 대신에 다음과 같이 ```companion object```{...} 블록 안에 정적 멤버들을 추가할 수 있다.
```
class WithStaticmember {
    companion object{
        val hello = "world"
    }
}

println(WithStaticMember.hello)

>> world
```
companion object 안에 선언된 멤버들은 코틀린 코드에서 자바의 static 변수를 사용하듯이 쓸 수 있다.

## Singleton
싱글톤 객체를 만드는 전통적인 방법으로는, ```private``` 생성자를 만들고 ```companion object```에 객체를 반환하는 정적 함수를 만들어서 사용하는 방법이 있다. 하지만 코틀린에는 ```object``` 키워드를 이용해서 보다 간단하게 싱글톤 객체를 만들 수 있다.
```
object SingletonClass{
    // ...
}
```
```object```로 선언한 클래스는 선언과 함께 객체가 생성된다. ```object```로 선언된 클래스는 ```val singleton = SinglethonClass()```와 같이 다시 객체화할 수 없다.

