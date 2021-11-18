---
layout: post
title:  "코틀린의 Scope Functions"
author: jiwon
categories: [ Kotlin ]
image: assets/images/kotlin_scope_functions.jpg
---
🥑    
# 개념 2️⃣ Kotlin Scope Funtions  

코틀린에는 특정 코드 블록을 **한 객체의 스코프** 안에서 실행하고 싶을 때 사용할 수 있는 다섯 가지의 범위 함수(Scope functions)가 있다. 범위 함수는 기술적으로 특별한 점이 있진 않지만 코드를 좀 더 깔끔하고 가독성있게 만들어준다. 5개의 범위 함수는 두 가지의 관점에 있어서 차이점이 있다.
* 블록 안에서 컨텍스트 개체를 지칭하는 방법 
* 반환값  
위의 두 가지 관점에서 5가지의 범위 함수를 설명하면 다음과 같다.   

## (1) let
* 컨텍스트 객체가 묵시적으로 **it**이 되며, it 대신 **명시적인 변수명**을 사용할 수 있음
* **마지막 표현식**의 결과를 반환함 

## (2) apply
* 컨텍스트 객체는 **this**가 됨
* **컨텍스트 객체 자신**을 반환함

## (3) run
* 컨텍스트 객체는 **this**가 됨
* **마지막 표현식**의 결과를 반환함 

## (4) also
* 컨텍스트 객체가 묵시적으로 **it**이 되며, it 대신 **명시적인 변수명**을 사용할 수 있음 
* **컨텍스트 객체 자신**을 반환함

## (5) with
* 컨텍스트 객체는 **this**가 됨
* **마지막 표현식의 결과**를 반환함. 
* **함수의 인자로 객체**가 필요하다는 점에서 run과 다름
 
공식 문서<sup>[1](#footnote_1)</sup>에 5가지의 범위 함수 중 어떤 것을 선택해야 할 지에 대해 가이드가 나와있는데, 그 내용은 다음과 같다.
* 널이 아닌 객체에 대해 코드 블록을 실행할 때 ➡️ let
* 로컬 범위에서 변수로의 표현식을 실행할 때 ➡️ let
* 객체를 초기화할 때 ➡️ apply
* 객체를 초기화하면서 결과값을 계산할 때 ➡️ run
* 표현식이 필요한 실행문일 때 ➡️ run 
* 부수적인 효과 ➡️ also
* 객체의 함수 호출을 그룹핑할 때 ➡️ with 

## 널이 아닌 객체에 대해 코드 블록을 실행할 때 ➡️ let
```
val str: String? = "Hello"   
//processNonNullString(str)       // compilation error: str can be null
val length = str?.let { 
    println("let() called on $it")        
    processNonNullString(it)      // OK: 'it' is not null inside '?.let { }'
    it.length
}

>> let() called on Hello
```
## 로컬 범위에서 변수로의 표현식을 실행할 때 ➡️ let
```
val numbers = listOf("one", "two", "three", "four")
val modifiedFirstItem = numbers.first().let { firstItem ->
    println("The first item of the list is '$firstItem'")
    if (firstItem.length >= 5) firstItem else "!" + firstItem + "!"
}.uppercase()
println("First item after modifications: '$modifiedFirstItem'")

>> The first item of the list is 'one'
>> First item after modifications: '!ONE!'
```
## 객체를 초기화할 때 ➡️ apply
apply 의 의미 : “apply the following assignments to the object.”
```
val adam = Person("Adam").apply {
    age = 32
    city = "London"        
}
println(adam)

>> Person(name=Adam, age=32, city=London)
```
## 객체를 초기화하면서 결과값을 계산할 때 ➡️ run
```
val numbers = mutableListOf("one", "two", "three")
val countEndsWithE = numbers.run { 
    add("four")
    add("five")
    count { it.endsWith("e") }
}
println("There are $countEndsWithE elements that end with e.")

>> There are 3 elements that end with e.
```
## 부수적인 효과 ➡️ also
also 의 의미 : “ and also do the following with the object.”  
```
val numbers = mutableListOf("one", "two", "three")
    numbers
        .also { println("The list elements before adding new one: $it") }
        .add("four")
        
>> The list elements before adding new one: [one, two, three]
```
## 객체의 함수 호출을 그룹핑할 때 ➡️ with 
with 의 의미 : “ with this object, do the following.”   
value를 return하는 것은 그냥 무시하고, 그루핑해서 쓸 때 사용하기도 한다.
```
val numbers = mutableListOf("one", "two", "three")
with(numbers) {
    println("'with' is called with argument $this")
    println("It contains $size elements")
}

>> 'with' is called with argument [one, two, three]
>> It contains 3 elements
```
## 객체의 함수나 프로퍼티 이용해서 계산할 때 ➡️ with 
```
val numbers = mutableListOf("one", "two", "three")
val firstAndLast = with(numbers) {
    "The first element is ${first()}," +
    " the last element is ${last()}"
}
println(firstAndLast)

>> The first element is one, the last element is three
```



## run(this) vs let(it)

```
fun main() {
    val str = "Hello"
    
    // this
    str.run {
        println("The receiver string length: $length")
        //println("The receiver string length: ${this.length}") // does the same
    }

    // it
    str.let {
        println("The receiver string's length is ${it.length}")
    }
}
```
run과 it은 둘 다 마지막 표현식을 반환하지만, run은 컨텍스트 객체가 ```this```이고,  
let은 컨텍스트 객체가 ```it```이다.




[참고 사이트]  
<a name="footnote_1">1</a>: [코틀린 Scope Functions 관련 공식문서](https://kotlinlang.org/docs/scope-functions.html#function-selection)  
