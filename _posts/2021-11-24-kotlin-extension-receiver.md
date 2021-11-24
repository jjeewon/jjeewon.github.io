---
layout: post
title:  "확장 함수와 리시버"
author: jiwon
categories: [ Kotlin ]
image: assets/images/extension_title.png
---
🥑    
# 개념 5️⃣ Extension Funtion & Receiver  

      
코틀린은 클래스 상속이나 데코레이션 패턴을 이용하지 않고, 클래스의 기능을 확장할 수 있는 방법을 제공한다. 그 방법에는 확장 함수와 확장 속성이 포함된다. 코틀린의 코드 블록은 항상 어떤 리시버 타입을 가지고 있다. 코드 블록 내에서는 해당하는 리시버의 속성이나 함수에 바로 접근할 수 있다. 가장 간단한 예시를 보면 다음과 같다.
```
fun Int.toWon() : String{
    return toString() + "원"
    // return this.toString() + "원"
}

val money = 1000 
println(money.toWon())
>> 1000원 
```
```Int```에다가 **String을 반환하는 toWon**이라는 확장함수를 추가했다. ```Int```형 불변타입변수인 money에서 toWon()을 호출하니까 '1000원'이라는 ```String```이 잘 반환되는 것을 볼 수 있다.
그럼 다음 코드 블록을 생각해보자.
```
{
    toLong()
}
```
앞의 코드는 ```Int```를 상속받은 클래스 내의 함수와 같은 특수한 경우를 제외하면 말이 되지 않는 코드이다. 이 블록을 ```Int```를 받아 Long을 반환하는 함수 타입 ```(Int) -> Long```에 대입하면 다음과 같이 쓸 수 있다.
```
val intToLong: (Int) -> Long = { toLong() }
```
여기에서의 toLong()은 존재하지 않는 함수이기때문에 컴파일 오류가 난다. 이 함수는 ```Int```가 유일한 매개변수이기 때문에, 람다 블록 내에서 it 키워드로 ```Int``` 매개변수에 접근할 수 있다. 위의 코드를 수정한 올바른 코드는 다음과 같다.
```
val intToLong: (Int) -> Long = { it.toLong() } 
```
위의 코드처럼 써도 되지만, 만약 람다 블록이 중첩된다면 it이 중복됨으로 인해 문제가 발생할 수 있다. 경우에 따라서는 코드 블록 내에 it 키워드가 너무 많아지는 문제가 생기기도 한다. 
여기에서 바로 리시버가 등장하는데, 이 리시버를 통해 코드 블록의 ```Int``` 타입을 리시버로 가지는 함수 타입에 할당함으로써 it 없이 동작하게 만들 수 있다.
```
val intToLong: Int.() -> Long { toLong() }
```
확장 함수 블록의 내부에서는 확장하려고하는 클래스가 리시버 타입이 된다. 

```
fun MutableList<T>.swap(index1: Int, index2: Int){
    val tmp = this[index1] // 'this'는 MutableList의 인스턴스 
    this[index1] = this[index2]
    this[index2] = tmp
}
```
위 함수에서 함수 블록 내부의 this는 MutableList<Int>의 인스턴스이다. 블록 내부에서 this를 생략할 수 있으므로 this[index1]과 동일한 역할을 하는 함수 호출 this.get(index1) 대신에 get(index1)로 사용할 수도 있다.


