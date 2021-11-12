---
layout: post
title:  "안드로이드 Design Pattern 이해하기"
author: jiwon
categories: [ Android ]
image: assets/images/adp.jpg
---
🤖  
# 개념 3️⃣ Android Design Pattern  

저번 포스트였던 프래그먼트 생명주기에 이어서, 이번에는 안드로이드 디자인 패턴에 대해 알아보자! 안드로이드 디자인 패턴으로는 MVC, MVP, MVVM 세 가지의 패턴이 많이 알려져있다.<sup>[1](#footnote_1)</sup>  
 

## [1] MVC 디자인 패턴
* 애플리케이션의 구조를 ```Model```, ```View```, ```Controller``` 측면으로 관심사를 분리한 것
* 웹에서 사용하는 가장 유명한 UI 패턴
<p align="center"><img src="/assets/images/pattern_1.jpeg"></p>
### (1) Model
* 애플리케이션의 비즈니스 로직, 사용되는 데이터를 다루는 영역
* 표현 형식에 의존하지 않음
* 예시) 
    - 데이터베이스의 Entity를 담당하는 POJO 클래스
    - SQLite, Room, Realm  

### (2) View 
* 사용자에게 표현되는 영역
* ```Model```에서 얻은 데이터를 ```View```에서 표현함
* 예시)
    - 액티비티, 프래그먼트  
    
### (3) Controller 
* ```Model```과 ```View```에 의존함
* ```View```에서 입력을 받음
* 특정 이벤트가 발생할 때, ```Model```/```View```를 변경할 수 있음
* 예시)
    - 사용자가 전화번호부에 전화번호를 등록함
    - ```View```에서 입력받은 정보를 ```Controller```가 ```Model```로 전달하여 DB에 입력함
    - 이 때, ```Model```의 상태가 바뀌면 ```Model```은 등록된 ```View```에 자신의 상태가 바뀐 것을 알림
    - ```View```는 거기에 맞게 사용자에게 ```Model```의 상태를 보여줌  
    
* Activity와 Fragment는 ```View```의 역할을 하지만, ```Controller```의 역할을 하기도 함 

### MVC 패턴의 장점 
* 직관적임
* ```Controller```가 '```Model```에서 데이터를 얻어서 ```View```에 표현'하는 모든 과정을 중계함
* 구조가 단순하고 직관적이라서 받아들이기 적용하기 쉬움
* 소규모 앱에 적용 시 개발기간이 단축됨
* 거의 모든 코드가 Activity, Fragment와 같은 ```Controller```에 작성되어 코드를 파악하기 쉬움

### MVC 패턴의 단점
* Activity, Fragment가 ```View``` + Controller의 역할을 동시에 하는 구조임
* 코드량이 점진적으로 증가할 수 밖에 없음
* 많은 코드가 하나의 클래스에 작성되면 스파게티 코드가 되어 유지보수비 증가함
* Contoller는 ```View```와 ```Model```에 의존적이고, ```View```는 ```Model```에 의존적이므로 결합도가 높아서 유닛테스트가 거의 불가능함

## [2] MVP 디자인 패턴
* UI와 Business Logic이 Activity, Fragment에 공존하는 MVC 패턴과는 다름
* Activity, Fragment의 UI & Business Logic을 분리하는데 집중함
* ```Model```과 ```View```의 역할은 MVC 패턴과 동일함
* Controller 대신 ```Presenter``` 개념을 통해 UI 코드와 Business Logic을 분리함으로써 유닛 테스트 가능하게 됨 

<p align="center"><img src="/assets/images/pattern_2.jpeg"></p>
### (1) Model
* 애플리케이션의 비즈니스 로직, 사용되는 데이터를 다루는 영역
* 표현 형식에 의존하지 않음
* 예시) 
    - 데이터베이스의 Entity를 담당하는 POJO 클래스
    - SQLite, Room, Realm  

### (2) View 
* 사용자에게 표현되는 영역
* ```Model```에서 얻은 데이터를 ```View```에서 표현함
* 예시)
    - 액티비티, 프래그먼트  
    
### (3) Presenter 
* ```View```와 ```Model```의 인스턴스를 가지고 있음
* ```View```와 ```Model```을 연결해주는 역할
* ```View```와 1:1 관계를 가짐

### MVP 패턴의 장점 
* ```View```와 ```Model``` 간에 의존성이 없음
* UI와 Business Logic을 분리하여 유닛테스트가 수월함

### MVP 패턴의 단점
* ```View```와 ```Presenter``` 간에 의존성이 높음
* ```View```와 ```Presenter```가 1:1 관계를 유지해야하므로, ```Presenter```를 재사용할 수 없음
* ```View```가 늘어날 때마다 ```Presenter```도 같이 많아짐
* 애플리케이션의 기능이 추가될수록, ```Presenter```가 거대해짐

## [3] MVVM 디자인 패턴
* MVP 디자인 패턴은 ```Presenter```가 ```View```에 어떤 일을 요청하는지 명백히 확인가능했음
* but MVP 디자인 패턴은 ```View```와 ```Presenter```가 강하게 결합한다는 문제가 있었음
* MVVM 디자인 패턴은 Databinding, LiveData 또는 RxJava와 같이 Observable 타입을 이용해서 ```Presenter```가 View에 강하게 연결되어 있던 점을 끊는데 집중함 
* 여기서는 ```Presenter``` 대신 ```ViewModel```이라는 구성 요소를 사용함 

<p align="center"><img src="/assets/images/pattern_3.jpeg"></p>
### (1) Model
* 애플리케이션의 비즈니스 로직, 사용되는 데이터를 다루는 영역
* 표현 형식에 의존하지 않음
* 예시) 
    - 데이터베이스의 Entity를 담당하는 POJO 클래스
    - SQLite, Room, Realm  

### (2) View  
* 사용자에게 표현되는 영역
* ```Model```에서 얻은 데이터를 ```View```에서 표현함
* 예시)
    - 액티비티, 프래그먼트  
    
### (3) ViewModel
* ```View```에 표현할 데이터를 Observable 타입으로 관리함
* ```View```들이 ```ViewModel```에 데이터를 구독 요청하여 화면에 나타냄
* ```ViewModel```과 ```View```는 1:N 관계임
* ```View```에 대한 의존성을 갖지 않고 느슨히 연결되도록 'DataBinding' 라이브러리가 필수적으로 사용됨
* 생명주기나 사용자와의 상호작용을 통해 ```Model```에 데이터를 요청함
* Model로부터 받은 데이터를 가공하여 Observable한 타입 형태로 ```ViewModel```에 저장함
* ```View```와 ```ViewModel```은 DataBinding을 해야 함
* 데이터 상태가 변경되면, 해당 데이터를 구독하는 ```View```에 변경사항을 통지하여 ```View```가 갱신되게 함

[참고 서적]  
<a name="footnote_1">1</a>: [안드로이드 아키텍처를 알아야 앱 개발이 보인다](http://www.yes24.com/Product/Goods/89958199)  
