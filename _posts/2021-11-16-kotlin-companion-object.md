---
layout: post
title:  "μ½νλ¦°μ companion object & Singleton"
author: ella
categories: [ Kotlin ]
image: assets/images/kotlin_companionobject_object.jpg
---
π₯    
# κ°λ 3οΈβ£ companion object & Singleton  

## companion object  
μ½νλ¦°μλ μλ°μλ λ€λ₯΄κ² ```static``` ν€μλκ° μλ€. λμ μ λ€μκ³Ό κ°μ΄ ```companion object```{...} λΈλ‘ μμ μ μ  λ©€λ²λ€μ μΆκ°ν  μ μλ€.
```
class WithStaticmember {
    companion object{
        val hello = "world"
    }
}

println(WithStaticMember.hello)

>> world
```
companion object μμ μ μΈλ λ©€λ²λ€μ μ½νλ¦° μ½λμμ μλ°μ static λ³μλ₯Ό μ¬μ©νλ―μ΄ μΈ μ μλ€.

## Singleton
μ±κΈν€ κ°μ²΄λ₯Ό λ§λλ μ ν΅μ μΈ λ°©λ²μΌλ‘λ, ```private``` μμ±μλ₯Ό λ§λ€κ³  ```companion object```μ κ°μ²΄λ₯Ό λ°ννλ μ μ  ν¨μλ₯Ό λ§λ€μ΄μ μ¬μ©νλ λ°©λ²μ΄ μλ€. νμ§λ§ μ½νλ¦°μλ ```object``` ν€μλλ₯Ό μ΄μ©ν΄μ λ³΄λ€ κ°λ¨νκ² μ±κΈν€ κ°μ²΄λ₯Ό λ§λ€ μ μλ€.
```
object SingletonClass{
    // ...
}
```
```object```λ‘ μ μΈν ν΄λμ€λ μ μΈκ³Ό ν¨κ» κ°μ²΄κ° μμ±λλ€. ```object```λ‘ μ μΈλ ν΄λμ€λ ```val singleton = SinglethonClass()```μ κ°μ΄ λ€μ κ°μ²΄νν  μ μλ€.

