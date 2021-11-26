---
layout: post
title:  "Activity Lifecycle 이해하기"
author: ella
categories: [ Android ]
image: assets/images/activity_lifecycle.jpg
---
🤖  
# 개념 1️⃣ Activity Lifecycle  

  
Activity Lifecycle이란 무엇일까? 액티비티가 생성(created), 소멸(destroyed)되고 시스템이 자원을 회수하는 전체 lifetime 동안 일어날 수 있는 액티비티 state의 집합이다.<sup>[1](#footnote_1)</sup>  사용자가 기기에서 여러 앱을 사용할 때, 액티비티는 원래 state와는 다른 state로 이동하게 된다. Activity Lifecycle은 7개의 콜백 메소드로 구성되며, 그 내용은 다음과 같다.<sup>[2](#footnote_2)</sup> 

<p align="center"><img src="/assets/images/al_1.png"></p>

## [1] onCreate() 
onCreate 콜백 메소드는 시스템이 액티비티를 만들어서 ```Created``` state가 되면 실행된다. 이 메소드에서 기본적인 앱 셋업 로직을 수행하고, 액티비티 전체 생명주기 동안 딱 한번만 실행된다. 

### [onCreate 사용 예]
- ui 선언하기(cf. setContentView(R.layout.xxx))  
- 데이터를 리스트에 bind하기  
- 액티비티에 뷰모델 묶기  
- 클래스 범위 변수 초기화해주기  

### [onCreate의 특징]
- 파라미터로 ```savedInstanceState```를 받음  
- ```savedInstanceState```는 액티비티가 이전에 저장한 state를 가지고 있는 Bundle 객체임   
- 만약에 해당 액티비티가 전에 생성된 적이 한 번도 없고 처음으로 생성하는 거면, Bundle 객체의 값은 null임  

## [2] onStart()
액티비티가 ```Started``` state에 진입했을 때, 시스템이 호출하는 콜백메소드이다. 이 메소드는  앱이 ```foreground``` 로 진입해서 interactive하게끔 준비시켜서, 액티비티가 사용자에게 ```visible```하게 해준다.

### [onStart 사용 예]
- 앱이 UI를 maintain하는 코드를 초기화할 떄 

### [onStart의 특징]
- 액티비티가 ```Started``` state로 오면, 액티비티의 lifecycle에 묶인 lifecycle-aware 컴포넌트들은 ```ON_START``` 이벤트 받음
- 메소드가 빨리 끝남(```Created``` 상태처럼 ```Started``` 상태에서 액티비티가 유지되지 않음)  

## [3] onResume()  
```onState()``` 콜백이 끝난 후, ```Resumed``` state로 진입했을 때, 시스템이 호출하는 콜백메소드이다. ```Resumed``` state로 진입하면, 액티비티가 foreground에 보여지고, 시스템이 ```onResume()``` 콜백을 호출한다. 사용자와 상호작용할 수 있는 state임. ```Resumed``` state는 앱으로부터 포커스를 뺏기는 일(cf. 전화 수신, 유저가 다른 액티비티로 이동, 스크린이 꺼짐 등)이 벌어지기 전까지 유지한다.

### [onResume 사용 예]

### [onResume의 특징]
- 액티비티가 ```Resumed``` state로 오면, 액티비티의 lifecycle에 묶인 lifecycle-aware 컴포넌트들은 ```ON_RESUME``` 이벤트 받음. 
- ```ON_RESUME```은 액티비티가 visible하고 forecround에 있을 때, lifecycle 컴포넌트들의 function을 활성화시킬 수 있는 이벤트임
- 인터럽트가 발생하면, 액티비티는 ```Resumed```에서 ```Paused``` state로 바뀜
- ```Paused``` state에서 ```Resumed``` state로 액티비티가 되돌아오면 시스템이 다시 한번 ```onResume()```을 호출하므로, ```onPause()```에서 release한 컴포넌트들을 ```onResume()```에서 초기화해줘야 됨

## [4] onPause()
사용자가 해당 액티비티에서 나가서 액티비티가 더이상 foreground에 있지 않을 때 시스템이 호출하는 메소드이다. 

### [onPause의 사용 예]
- 액티비티가 ```Paused``` state에 있는 동안 유지되면 안되는 기능을 중지시키거나 조정할 때
- 금방 resume할 수 있는 기능을 중지시키거나 조정할 때 
- 인터럽트가 들어왔을 때
- 안드로이드 7.0(API level 24+)에서 multi-windoe 모드에서 multi app들이 돌아갈 때(앱들 중 하나만 focus가지므로 다른 앱들은 시스템이 pause시킴)
- dialog같은 새로운 반투명 액티비티가 열렸을 때(원래 액티비티가 부분적으로는 보이지만, focus는 없을 때 ```paused``` 상태로 남아있음)

### [onPause]의 특징  
 - 보통은 핸드폰 배터리 수명에 영향을 끼치는 시스템 리소스, 센서(cf. GPS) 등을 자원을 해제하는데 사용할 수 있음
 - 하지만, multi-window 모드에서는 ```paused```된 액티비티가 fully visible하므로 multi-window 모드를 위해서는 ui와 관련된 리소스들을 해제하기 위해서 ```onPause()``` 대신에, ```onStop()```을 사용하는게 좋음
 - ```onPause()```는 operation들을 저장할 정도로 오랫동안 실행되는 게 아니라서 앱, 사용자 데이터, 네트워크 호출, 데이터베이스 트랜잭션하는 코드를 넣으면 안됨
 - 시간 오래 걸리는 코드는 ```onPause()```가 아니라 ```onStop()```에서 해야 함
 
## [5] onStop()   
액티비티가 사용자에게 더이상 보이지 않을때, Stopped state로 진입하고, 시스템이 ```onStop()``` 콜백을 호출한다. 
 
### [onStop의 사용 예]
 - 새로 시작된 액티비티가 전체 스크린에 보일 때 
 - 액티비티가 running을 완전히 끝낼 때 
 - 애니메이션을 일시 중지할 때
 - 디테일한 위치 업데이트에서 대략적인 위치 업데이트로 전환할 때
 - db에 정보 저장할 때
 
### [onStop의 특징]
  - 이 메소드에서 필요없는 리소스들을 해제하고 조절해야 함
  - CPU가 많이 들어가는 작업을 수행하려면 ```onStop()```에서 해야 함
  - Stopped state에 진입하더라도, 액티비티 객체는 메모리에 계속 있으면서 모든 state랑 member정보를 유지하지만, window manager에 attached되있진 않음 
  - 액티비티가 stop되었는데 시스템이 메모리를 recover할 필요가 있으면, 해당 액티비티를 포함하는 프로세스를 파괴할 수도 있음
  - 액티비티가 stop된 상태에서 시스템이 액티비티가 있는 프로세스를 파괴하더라도, 시스템은 Bundle에 View 객체의 state(cf. EditText의 텍스트)를 가지고있다가 사용자가 다시 액티비티로 돌아오면 저장함 
  - ```Stopped``` state일때, (1) 사용자랑 다시 상호작용하기 위해 액티비티가 come back하거나, (2) 액티비티가 완전히 끝나고 사라져버린다.
  - 만약에 (1)처럼 액티비티가 돌아온다면, 시스템은 onRestart()를 호출한다.
  - 만약에 (2)처럼 액티비티가 끝난다면, 시스템은 onDestroy()를 호출한다. 
  
## [6] onDestroy()
  액티비티가 파괴되기 전에 onDestroy()가 호출된다.
  
### [onDestroy의 사용 예]
   - 사용자가 액티비티를 완전히 닫을 때
   - 액티비티에서 ```finish()```가 호출되었을 때
   - device rotation이나 multi-window 모드와 같은 configuration 변경으로 인해 시스템이 액티비티를 일시적으로 파괴할 때
   
### [onDestroy의 특징]
   - 액티비티가 끝나게 되면, ```onDestroy()```는 액티비티가 받는 마지막 lifecycle 콜백임
   - 만약 configuration 변화로 인해서 ```onDestroy()```가 호출되면, 시스템은 즉시 새 액티비티 인스턴스를 만들어서 새로운 configuration에서의 새 인스턴스에서 onCreate()를 호출함.
   - 뷰모델을 가지고 있을 때, (1) configuration 변화로 인해 액티비티가 새로 생성되면, 새 액티비티 인스턴스로 뷰모델이 보존되니까 뷰모델에서 따로 처리해줄 필요가 없지만, (2) configuration 변화가 아니라 액티비티가 재생성되지 않는다면 뷰모델이 ```onCleared()```를 호출해서 destroy되기 전에 데이터를 없앨 수 있게 해줌
   - ```onStop()```에서 해제시키지 않은 모든 리소스들을 여기서 해제해줘야 함
  

 

[참고 사이트]  
<a name="footnote_1">1</a>: [Android Activty Lifecycle 의 정의](https://google-developer-training.github.io/android-developer-fundamentals-course-concepts-v2/unit-1-get-started/lesson-2-activities-and-intents/2-2-c-activity-lifecycle-and-state/2-2-c-activity-lifecycle-and-state.html)  
<a name="footnote_2">2</a>: [안드로이드 공식 문서(The activity lifecycle)](https://developer.android.com/guide/components/activities/activity-lifecycle)  



