---
layout: post
title:  "μ½νλ¦°μ Scope Functions"
author: ella
categories: [ Kotlin ]
image: assets/images/kotlin_scope_functions.jpg
---
π₯    
# κ°λ 2οΈβ£ Kotlin Scope Funtions  

μ½νλ¦°μλ νΉμ  μ½λ λΈλ‘μ **ν κ°μ²΄μ μ€μ½ν** μμμ μ€ννκ³  μΆμ λ μ¬μ©ν  μ μλ λ€μ― κ°μ§μ λ²μ ν¨μ(Scope functions)κ° μλ€. λ²μ ν¨μλ κΈ°μ μ μΌλ‘ νΉλ³ν μ μ΄ μμ§ μμ§λ§ μ½λλ₯Ό μ’ λ κΉλνκ³  κ°λμ±μκ² λ§λ€μ΄μ€λ€. 5κ°μ λ²μ ν¨μλ λ κ°μ§μ κ΄μ μ μμ΄μ μ°¨μ΄μ μ΄ μλ€.
* λΈλ‘ μμμ μ»¨νμ€νΈ κ°μ²΄λ₯Ό μ§μΉ­νλ λ°©λ² 
* λ°νκ°  
μμ λ κ°μ§ κ΄μ μμ 5κ°μ§μ λ²μ ν¨μλ₯Ό μ€λͺνλ©΄ λ€μκ³Ό κ°λ€.   

## (1) let
* μ»¨νμ€νΈ κ°μ²΄κ° λ¬΅μμ μΌλ‘ **it**μ΄ λλ©°, it λμ  **λͺμμ μΈ λ³μλͺ**μ μ¬μ©ν  μ μμ
* **λ§μ§λ§ ννμ**μ κ²°κ³Όλ₯Ό λ°νν¨ 

## (2) apply
* μ»¨νμ€νΈ κ°μ²΄λ **this**κ° λ¨
* **μ»¨νμ€νΈ κ°μ²΄ μμ **μ λ°νν¨

## (3) run
* μ»¨νμ€νΈ κ°μ²΄λ **this**κ° λ¨
* **λ§μ§λ§ ννμ**μ κ²°κ³Όλ₯Ό λ°νν¨ 

## (4) also
* μ»¨νμ€νΈ κ°μ²΄κ° λ¬΅μμ μΌλ‘ **it**μ΄ λλ©°, it λμ  **λͺμμ μΈ λ³μλͺ**μ μ¬μ©ν  μ μμ 
* **μ»¨νμ€νΈ κ°μ²΄ μμ **μ λ°νν¨

## (5) with
* μ»¨νμ€νΈ κ°μ²΄λ **this**κ° λ¨
* **λ§μ§λ§ ννμμ κ²°κ³Ό**λ₯Ό λ°νν¨. 
* **ν¨μμ μΈμλ‘ κ°μ²΄**κ° νμνλ€λ μ μμ runκ³Ό λ€λ¦
 
κ³΅μ λ¬Έμ<sup>[1](#footnote_1)</sup>μ 5κ°μ§μ λ²μ ν¨μ μ€ μ΄λ€ κ²μ μ νν΄μΌ ν  μ§μ λν΄ κ°μ΄λκ° λμμλλ°, κ·Έ λ΄μ©μ λ€μκ³Ό κ°λ€.
* λμ΄ μλ κ°μ²΄μ λν΄ μ½λ λΈλ‘μ μ€νν  λ β‘οΈ let
* λ‘μ»¬ λ²μμμ λ³μλ‘μ ννμμ μ€νν  λ β‘οΈ let
* κ°μ²΄λ₯Ό μ΄κΈ°νν  λ β‘οΈ apply
* κ°μ²΄λ₯Ό μ΄κΈ°ννλ©΄μ κ²°κ³Όκ°μ κ³μ°ν  λ β‘οΈ run
* ννμμ΄ νμν μ€νλ¬ΈμΌ λ β‘οΈ run 
* λΆμμ μΈ ν¨κ³Ό β‘οΈ also
* κ°μ²΄μ ν¨μ νΈμΆμ κ·Έλ£Ήνν  λ β‘οΈ with 

## λμ΄ μλ κ°μ²΄μ λν΄ μ½λ λΈλ‘μ μ€νν  λ β‘οΈ let
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
## λ‘μ»¬ λ²μμμ λ³μλ‘μ ννμμ μ€νν  λ β‘οΈ let
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
## κ°μ²΄λ₯Ό μ΄κΈ°νν  λ β‘οΈ apply
apply μ μλ―Έ : βapply the following assignments to the object.β
```
val adam = Person("Adam").apply {
    age = 32
    city = "London"        
}
println(adam)

>> Person(name=Adam, age=32, city=London)
```
## κ°μ²΄λ₯Ό μ΄κΈ°ννλ©΄μ κ²°κ³Όκ°μ κ³μ°ν  λ β‘οΈ run
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
## λΆμμ μΈ ν¨κ³Ό β‘οΈ also
also μ μλ―Έ : β and also do the following with the object.β  
```
val numbers = mutableListOf("one", "two", "three")
    numbers
        .also { println("The list elements before adding new one: $it") }
        .add("four")
        
>> The list elements before adding new one: [one, two, three]
```
## κ°μ²΄μ ν¨μ νΈμΆμ κ·Έλ£Ήνν  λ β‘οΈ with 
with μ μλ―Έ : β with this object, do the following.β   
valueλ₯Ό returnνλ κ²μ κ·Έλ₯ λ¬΄μνκ³ , κ·Έλ£¨νν΄μ μΈ λ μ¬μ©νκΈ°λ νλ€.
```
val numbers = mutableListOf("one", "two", "three")
with(numbers) {
    println("'with' is called with argument $this")
    println("It contains $size elements")
}

>> 'with' is called with argument [one, two, three]
>> It contains 3 elements
```
## κ°μ²΄μ ν¨μλ νλ‘νΌν° μ΄μ©ν΄μ κ³μ°ν  λ β‘οΈ with 
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
runκ³Ό itμ λ λ€ λ§μ§λ§ ννμμ λ°ννμ§λ§, runμ μ»¨νμ€νΈ κ°μ²΄κ° ```this```μ΄κ³ ,  
letμ μ»¨νμ€νΈ κ°μ²΄κ° ```it```μ΄λ€.




[μ°Έκ³  μ¬μ΄νΈ]  
<a name="footnote_1">1</a>: [μ½νλ¦° Scope Functions κ΄λ ¨ κ³΅μλ¬Έμ](https://kotlinlang.org/docs/scope-functions.html#function-selection)  
