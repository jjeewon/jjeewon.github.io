---
layout: post
title:  "Fragment Lifecycle 이해하기"
author: jiwon
categories: [ Android ]
image: assets/images/fragment_lifecycle.jpg
---
🤖  
# 개념 2️⃣ Fragment Lifecycle  

저번 포스트였던 액티비티 생명주기에 이어서, 이번에는 프래그먼트의 생명주기에 대해 알아보자! 프래그먼트의 생명주기는 크게 10개의 파트로 나누어 볼 수 있다.<sup>[1](#footnote_1)</sup>  
 


<p align="center"><img src="/assets/images/fragment_view_lifecycle.png"></p>

## [1] Fragment CREATED
<p align="center"><img src="/assets/images/fl_1.png"></p>
* 프래그먼트가 ```CREATED``` state로 도달하면, FragmentManager에 추가되고 ```onAttach()```가 호출된다. 
여기서 프래그먼트의 ```SavedStateRegistry```를 통해 프래그먼트 자체와 관련하여 저장했던 state를 불러오면 된다. 
프래그먼트의 뷰는 여기서 만들어지지 않고, 프래그먼트 뷰와 관련된 모든 state는 뷰가 만들어지진 후에 불러와야한다. 
이 transition은 ```onCreate()``` 콜백을 호출한다. 이 콜백은 이전에 ```onSavedInstanceState()```에서 저장했던 state를 가지고 있는 ```savedInstanceState``` 번들 인자를 가진다.  
```savedInstanceState```는 처음에 프래그먼트가 만들어졌을 때는 null 값이다. ```onSavedInstanceState```를 오버라이드하지 않더라도 프래그먼트가 다시 만들어질 때마다 항상 non-null 값이다.  

## [2] Fragment CREATED + View INITIALIZED
<p align="center"><img src="/assets/images/fl_2.png"></p>
* 프래그먼트 뷰의 ```Lifecycle```은 프래그먼트가 유효한 View 인스턴스를 가지고있을 때만 생성된다.
대부분, 적절한 타이밍에 자동적으로 뷰를 inflate하는 ```@LayoutId```를 가지는 fragment constructors를 이용할 수 있다.
아니면, ```onCreateView()```를 오버라이드해서 프래그먼트 뷰를 프로그래밍으로 inflate해주거나 만들어줄 수 있다.
만약에 non-null인 뷰와 함께 프래그먼트뷰가 초기화된다면, 그 뷰는 프래그먼트에 설정되고 getView()를 통해 뷰를 리턴받을 수 있다. 그러면 ```getViewLifecycleOwnerLiveDate()```가 프래그먼트 뷰에 상응하는 새로운 INITIALIZED LifecycleOwner와 함께 업데이트된다. 

## [3] Fragment + View CREATED  
<p align="center"><img src="/assets/images/fl_3.png"></p>
* 프래그먼트 뷰가 만들어지고 난 후, 전에 state 저장한거 있으면 복구되고,
뷰의 Lifecycle은 ```CREATED``` state로 이동한다.
뷰 lifecycle owner는 ```ON_CREATE``` 이벤트를 observeer들에게 emit한다. 여기서 개발자는 반드시 프래그먼트 뷰와 관련된 부가적인 state들을 복구해야 한다.
여기 transition에서는 ```onViewStateRestored()```를 호출한다. 
 
## [4] Fragment + View STARTED
<p align="center"><img src="/assets/images/fl_4.png"></p>
* Lifecycle-aware 컴포넌트들을 프래그먼트의 ```STARTED``` state에서 묶어놓는게 좋다.
왜냐하면 이 state는 프래그먼트 뷰가 available한 것을 보장하기 때문에, 프래그먼트 뷰가 생성되면 프래그먼트의 child FragmentManager에 FragmentTransaction을 수행하기에 안전하다.
만약에 프래그먼트 뷰가 non-null이면, 프래그먼트 뷰의 Lifecycle이 ```STARTED```로 이동한 후 바로 프래그먼트 뷰의 Lifecycle도 ```STARTED```로 이동한다.  
프래그먼트가 STARTED되면, onStart() 콜백 메소드가 호출된다. 
* ```ViewPager2```같은 컴포넌트는 offscreen 프래그먼트의 maximum Lifecycle을 ```STARTED```로 설정한다.

## [5] Fragment + View RESUMED
<p align="center"><img src="/assets/images/fl_5.png"></p>
* 프래그먼트가 visible하면, 모든 Animator와 Transition 효과가 끝나고, 프래그먼트는 사용자와 상호작용할 준비가 되있게 된다. 
프래그먼트의 Lifecycle은 ```RESUMED``` state로 이동하고, ```onResume()``` 콜백 메소드가 호출된다.

```RESUMED```으로의 transition은 사용자가 이제 프래그먼트랑 상호작용할 수 있다는걸 알려주는 신호이다. 
RESUMED되지 않은 프래그먼트에서는 manual하게 뷰에 focus를 주면 안되고, ```handle input method visibility```하려고 시도해서는 안된다.

## [6] Downward state transitions
* 프래그먼트가 downward에서 더 밑에있는 라이프사이클 state로 내려가면, Lifecycle.Event가 프래그먼트 뷰의 Lifecycle에 의해 observer로 emit된다. 그리고 초기화되면 프래그먼트의 Lifecycle을 follow하게 된다. 
프래그먼트의 lifecycle 이벤트가 emit된 이후에는, 프래그먼트가 관련된 lifecycle callback을 호출한다.

## [7] Fragment + View STARTED
<p align="center"><img src="/assets/images/fl_6.png"></p>
* 사용자가 프래그먼트를 떠나기 시작할 때, 프래그먼트가 여전히 visible 상태이고, 프래그먼트와 프래그먼트 뷰를 위한 Lifecycle이 ```STARTED``` state로 되돌아가고 ON_PAUSE 이벤트를 observer들에게 emit한다.
그러고나면 프래그먼트는 ```onPause()``` 콜백 메소드를 호출한다.

## [8] Fragment + View CREATED
<p align="center"><img src="/assets/images/fl_7.png"></p>
* 프래그먼트가 더이상 안보이게 되면, 프래그먼트와 뷰를 위한 Lifecycle들이 ```CREATED``` state로 이동하고, observer로 ```ON_STOP``` 이벤트를 emit한다. 
이 state transition은 스탑된 부모 액티비티나 프래그먼트에 의해서도 촉발되고, 부모 액티비프래그먼트의 state 저장할 때도 촉발된다. 
이 행동은 프래그먼트의 state가 저장되기 전에 ```ON_STOP``` 이벤트가 호출되는 것을 보장해준다.
이것은 ```ON_STOP``` 이벤트가 (자식 FragmentManager에 FragmentTransaction을 수행하기에 안전한)마지막 포인트가 되도록 만든다. 

<p align="center"><img src="/assets/images/stop_save_order.png"></p>

그림에서 보이는 것처럼 ```onStop()``` 콜백의 순서와 ```onSaveInstanceState()```에 state를 저장하는 것은 API 레벨에 따라 달라진다. 
* 28 이전 API 레벨들은 ```onSaveInstanceState()```가 호출된 후 ```onStop()```가 호출된다.
* 28 이상 API 레벨들은 반대로 ```onStop()```이 호출된 후 ```onSaveInstanceState()```이 호출된다. 

## [9] Fragment CREATED + View DESTROYED
<p align="center"><img src="/assets/images/fl_8.png"></p>
* animations and transitions 가 다 끝나고, 프래그먼트 뷰가 윈도우에서 ```detach```된 후에, 프래그먼트 뷰의 Lifecycle이 ```DESTROYED``` state로 이동하고 observer들에게 ```ON_DESTROY``` 이벤트를 emit한다.
그러고 난후 프래그먼트는 ```onDesgroyView()``` 콜백 메소드를 호출한다. 
이 때, 프래그먼트 뷰가 lifecycle의 끝에 도달하고 ```getViewLifecycleOwnerLiveData()```가 null값을 리턴하게 된다. 
그리고 프래그먼트뷰가 ```garbage collecteded``` 될 수 있도록 프래그먼트 뷰에 붙은 모든 reference들이 이 지점에서 제거되어야 한다.  

## [10] Fragment DESTROYED
<p align="center"><img src="/assets/images/fl_9.png"></p>
* 프래그먼트가 제거되거나, FragmentManager가 파괴되면, 프래그먼트의 Lifecycle이 ```DESTROYED``` state로 이동되고 observer들에게 ```ON_DESTROY``` 이벤트를 보낸다. 
이 때, 프래그먼트는 lifecycle의 끝에 도달한다. 

 

[참고 사이트]  
<a name="footnote_1">1</a>: [안드로이드 공식 문서(The fragment lifecycle)](https://developer.android.com/guide/fragments/lifecycle)  
