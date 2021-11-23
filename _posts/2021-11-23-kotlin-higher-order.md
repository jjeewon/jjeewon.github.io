---
layout: post
title:  "코틀린의 고차함수"
author: jiwon
categories: [ Kotlin ]
image: assets/images/higher_title.png
---
🥑    
# 개념 4️⃣ Higher-Order Function  

      
코틀린의 함수는 **일급 객체(first-class)**로서, 함수를 **일반 객체**처럼 취급할 수 있다. 즉 함수를 변수에 할당할 수도 있고, 자료구조에 사용할 수도 있으며 다른 함수의 매개변수로 전달하거나 반환값으로 사용할 수도 있다.  
```고차함수(Higher-Order function)```는 매개변수로 함수를 받거나 다른 함수를 반환하는 함수를 의미한다. 코틀린에서는 이러한 함수 매개변수나 반환되는 함수를 표현하기 위해 **함수 타입**을 지원한다.

```
fun runHigherOrderFunction(f: (Int, String) -> String): String{
    return f(3, "은 3이지요")
}
```
위의 코드에서, 매개변수로 선언된 f는 (Int, String) -> String 이라는 **함수 타입**으로 정의되어 있다. 이 타입은 Int과 String를 매개변수로 받고 String을 반환하는 함수를 나타낸다. 이 **함수 타입**의 인스턴스를 얻기 위해서는 다음과 같은 몇가지 방법을 사용할 수 있다.
### (1) **람다**
```
println(runHigherOrderFunction{ num: Int, str: String ->  num.toString() + str  })
```
```
>> 3은 3이지요 
```

### (2) **익명함수**
```
println(runHigherOrderFunction(fun(num: Int,str: String): String{
        return num.toString() + str
    }))
```
```
>> 3은 3이지요 
```

### (3) **함수 타입 인터페이스**를 구현한 클래스의 인스턴스
```
class SomeFunction: (Int, String) -> String{
    override operator fun invoke(num: Int, str: String): String = num.toString() + str
}


val someFunction: (Int, String) -> String = SomeFunction()
println(runHigherOrderFunction(someFunction))
```
```
>> 3은 3이지요 
```

## 🌟 람다(lambda) 팁
(1) 코틀린에서는 함수의 마지막 매개변수가 함수일 경우에 괄호 바깥에 람다를 넘겨줄 수 있다.
```
fun higherOrderFunction(stringParam: String, funParam: () -> String){
    println(stringParam + funParam())
}
fun main(){
    higherOrderFunction("Gomroid ") {
        "Studio"
    }
}
```

(2) 함수에 매개변수가 함수 하나일 경우는 괄호없이 람다를 넘겨줄 수 있다.
```
fun higherOrderFunction(funParam: () -> String){
    println(funParam())
}
fun main(){
    higherOrderFunction{
        "Gomroid Studio"
    }
}
```
