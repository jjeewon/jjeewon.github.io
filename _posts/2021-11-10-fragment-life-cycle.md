---
layout: post
title:  "Fragment Lifecycle ์ดํดํ๊ธฐ"
author: ella
categories: [ Android ]
image: assets/images/fragment_lifecycle.jpg
---
๐ค  
# ๊ฐ๋ 2๏ธโฃ Fragment Lifecycle  

์ ๋ฒ ํฌ์คํธ์๋ ์กํฐ๋นํฐ ์๋ช์ฃผ๊ธฐ์ ์ด์ด์, ์ด๋ฒ์๋ ํ๋๊ทธ๋จผํธ์ ์๋ช์ฃผ๊ธฐ์ ๋ํด ์์๋ณด์! ํ๋๊ทธ๋จผํธ์ ์๋ช์ฃผ๊ธฐ๋ ํฌ๊ฒ 10๊ฐ์ ํํธ๋ก ๋๋์ด ๋ณผ ์ ์๋ค.<sup>[1](#footnote_1)</sup>  
 


<p align="center"><img src="/assets/images/fragment_view_lifecycle.png"></p>

## [1] Fragment CREATED
<p align="center"><img src="/assets/images/fl_1.png"></p>
* ํ๋๊ทธ๋จผํธ๊ฐ ```CREATED``` state๋ก ๋๋ฌํ๋ฉด, FragmentManager์ ์ถ๊ฐ๋๊ณ  ```onAttach()```๊ฐ ํธ์ถ๋๋ค. 
์ฌ๊ธฐ์ ํ๋๊ทธ๋จผํธ์ ```SavedStateRegistry```๋ฅผ ํตํด ํ๋๊ทธ๋จผํธ ์์ฒด์ ๊ด๋ จํ์ฌ ์ ์ฅํ๋ state๋ฅผ ๋ถ๋ฌ์ค๋ฉด ๋๋ค. 
ํ๋๊ทธ๋จผํธ์ ๋ทฐ๋ ์ฌ๊ธฐ์ ๋ง๋ค์ด์ง์ง ์๊ณ , ํ๋๊ทธ๋จผํธ ๋ทฐ์ ๊ด๋ จ๋ ๋ชจ๋  state๋ ๋ทฐ๊ฐ ๋ง๋ค์ด์ง์ง ํ์ ๋ถ๋ฌ์์ผํ๋ค. 
์ด transition์ ```onCreate()``` ์ฝ๋ฐฑ์ ํธ์ถํ๋ค. ์ด ์ฝ๋ฐฑ์ ์ด์ ์ ```onSavedInstanceState()```์์ ์ ์ฅํ๋ state๋ฅผ ๊ฐ์ง๊ณ  ์๋ ```savedInstanceState``` ๋ฒ๋ค ์ธ์๋ฅผ ๊ฐ์ง๋ค.  
```savedInstanceState```๋ ์ฒ์์ ํ๋๊ทธ๋จผํธ๊ฐ ๋ง๋ค์ด์ก์ ๋๋ null ๊ฐ์ด๋ค. ```onSavedInstanceState```๋ฅผ ์ค๋ฒ๋ผ์ด๋ํ์ง ์๋๋ผ๋ ํ๋๊ทธ๋จผํธ๊ฐ ๋ค์ ๋ง๋ค์ด์ง ๋๋ง๋ค ํญ์ non-null ๊ฐ์ด๋ค.  

## [2] Fragment CREATED + View INITIALIZED
<p align="center"><img src="/assets/images/fl_2.png"></p>
* ํ๋๊ทธ๋จผํธ ๋ทฐ์ ```Lifecycle```์ ํ๋๊ทธ๋จผํธ๊ฐ ์ ํจํ View ์ธ์คํด์ค๋ฅผ ๊ฐ์ง๊ณ ์์ ๋๋ง ์์ฑ๋๋ค.
๋๋ถ๋ถ, ์ ์ ํ ํ์ด๋ฐ์ ์๋์ ์ผ๋ก ๋ทฐ๋ฅผ inflateํ๋ ```@LayoutId```๋ฅผ ๊ฐ์ง๋ fragment constructors๋ฅผ ์ด์ฉํ  ์ ์๋ค.
์๋๋ฉด, ```onCreateView()```๋ฅผ ์ค๋ฒ๋ผ์ด๋ํด์ ํ๋๊ทธ๋จผํธ ๋ทฐ๋ฅผ ํ๋ก๊ทธ๋๋ฐ์ผ๋ก inflateํด์ฃผ๊ฑฐ๋ ๋ง๋ค์ด์ค ์ ์๋ค.
๋ง์ฝ์ non-null์ธ ๋ทฐ์ ํจ๊ป ํ๋๊ทธ๋จผํธ๋ทฐ๊ฐ ์ด๊ธฐํ๋๋ค๋ฉด, ๊ทธ ๋ทฐ๋ ํ๋๊ทธ๋จผํธ์ ์ค์ ๋๊ณ  getView()๋ฅผ ํตํด ๋ทฐ๋ฅผ ๋ฆฌํด๋ฐ์ ์ ์๋ค. ๊ทธ๋ฌ๋ฉด ```getViewLifecycleOwnerLiveDate()```๊ฐ ํ๋๊ทธ๋จผํธ ๋ทฐ์ ์์ํ๋ ์๋ก์ด INITIALIZED LifecycleOwner์ ํจ๊ป ์๋ฐ์ดํธ๋๋ค. 

## [3] Fragment + View CREATED  
<p align="center"><img src="/assets/images/fl_3.png"></p>
* ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ ๋ง๋ค์ด์ง๊ณ  ๋ ํ, ์ ์ state ์ ์ฅํ๊ฑฐ ์์ผ๋ฉด ๋ณต๊ตฌ๋๊ณ ,
๋ทฐ์ Lifecycle์ ```CREATED``` state๋ก ์ด๋ํ๋ค.
๋ทฐ lifecycle owner๋ ```ON_CREATE``` ์ด๋ฒคํธ๋ฅผ observeer๋ค์๊ฒ emitํ๋ค. ์ฌ๊ธฐ์ ๊ฐ๋ฐ์๋ ๋ฐ๋์ ํ๋๊ทธ๋จผํธ ๋ทฐ์ ๊ด๋ จ๋ ๋ถ๊ฐ์ ์ธ state๋ค์ ๋ณต๊ตฌํด์ผ ํ๋ค.
์ฌ๊ธฐ transition์์๋ ```onViewStateRestored()```๋ฅผ ํธ์ถํ๋ค. 
 
## [4] Fragment + View STARTED
<p align="center"><img src="/assets/images/fl_4.png"></p>
* Lifecycle-aware ์ปดํฌ๋ํธ๋ค์ ํ๋๊ทธ๋จผํธ์ ```STARTED``` state์์ ๋ฌถ์ด๋๋๊ฒ ์ข๋ค.
์๋ํ๋ฉด ์ด state๋ ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ availableํ ๊ฒ์ ๋ณด์ฅํ๊ธฐ ๋๋ฌธ์, ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ ์์ฑ๋๋ฉด ํ๋๊ทธ๋จผํธ์ child FragmentManager์ FragmentTransaction์ ์ํํ๊ธฐ์ ์์ ํ๋ค.
๋ง์ฝ์ ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ non-null์ด๋ฉด, ํ๋๊ทธ๋จผํธ ๋ทฐ์ Lifecycle์ด ```STARTED```๋ก ์ด๋ํ ํ ๋ฐ๋ก ํ๋๊ทธ๋จผํธ ๋ทฐ์ Lifecycle๋ ```STARTED```๋ก ์ด๋ํ๋ค.  
ํ๋๊ทธ๋จผํธ๊ฐ STARTED๋๋ฉด, onStart() ์ฝ๋ฐฑ ๋ฉ์๋๊ฐ ํธ์ถ๋๋ค. 
* ```ViewPager2```๊ฐ์ ์ปดํฌ๋ํธ๋ offscreen ํ๋๊ทธ๋จผํธ์ maximum Lifecycle์ ```STARTED```๋ก ์ค์ ํ๋ค.

## [5] Fragment + View RESUMED
<p align="center"><img src="/assets/images/fl_5.png"></p>
* ํ๋๊ทธ๋จผํธ๊ฐ visibleํ๋ฉด, ๋ชจ๋  Animator์ Transition ํจ๊ณผ๊ฐ ๋๋๊ณ , ํ๋๊ทธ๋จผํธ๋ ์ฌ์ฉ์์ ์ํธ์์ฉํ  ์ค๋น๊ฐ ๋์๊ฒ ๋๋ค. 
ํ๋๊ทธ๋จผํธ์ Lifecycle์ ```RESUMED``` state๋ก ์ด๋ํ๊ณ , ```onResume()``` ์ฝ๋ฐฑ ๋ฉ์๋๊ฐ ํธ์ถ๋๋ค.

```RESUMED```์ผ๋ก์ transition์ ์ฌ์ฉ์๊ฐ ์ด์  ํ๋๊ทธ๋จผํธ๋ ์ํธ์์ฉํ  ์ ์๋ค๋๊ฑธ ์๋ ค์ฃผ๋ ์ ํธ์ด๋ค. 
RESUMED๋์ง ์์ ํ๋๊ทธ๋จผํธ์์๋ manualํ๊ฒ ๋ทฐ์ focus๋ฅผ ์ฃผ๋ฉด ์๋๊ณ , ```handle input method visibility```ํ๋ ค๊ณ  ์๋ํด์๋ ์๋๋ค.

## [6] Downward state transitions
* ํ๋๊ทธ๋จผํธ๊ฐ downward์์ ๋ ๋ฐ์์๋ ๋ผ์ดํ์ฌ์ดํด state๋ก ๋ด๋ ค๊ฐ๋ฉด, Lifecycle.Event๊ฐ ํ๋๊ทธ๋จผํธ ๋ทฐ์ Lifecycle์ ์ํด observer๋ก emit๋๋ค. ๊ทธ๋ฆฌ๊ณ  ์ด๊ธฐํ๋๋ฉด ํ๋๊ทธ๋จผํธ์ Lifecycle์ followํ๊ฒ ๋๋ค. 
ํ๋๊ทธ๋จผํธ์ lifecycle ์ด๋ฒคํธ๊ฐ emit๋ ์ดํ์๋, ํ๋๊ทธ๋จผํธ๊ฐ ๊ด๋ จ๋ lifecycle callback์ ํธ์ถํ๋ค.

## [7] Fragment + View STARTED
<p align="center"><img src="/assets/images/fl_6.png"></p>
* ์ฌ์ฉ์๊ฐ ํ๋๊ทธ๋จผํธ๋ฅผ ๋ ๋๊ธฐ ์์ํ  ๋, ํ๋๊ทธ๋จผํธ๊ฐ ์ฌ์ ํ visible ์ํ์ด๊ณ , ํ๋๊ทธ๋จผํธ์ ํ๋๊ทธ๋จผํธ ๋ทฐ๋ฅผ ์ํ Lifecycle์ด ```STARTED``` state๋ก ๋๋์๊ฐ๊ณ  ON_PAUSE ์ด๋ฒคํธ๋ฅผ observer๋ค์๊ฒ emitํ๋ค.
๊ทธ๋ฌ๊ณ ๋๋ฉด ํ๋๊ทธ๋จผํธ๋ ```onPause()``` ์ฝ๋ฐฑ ๋ฉ์๋๋ฅผ ํธ์ถํ๋ค.

## [8] Fragment + View CREATED
<p align="center"><img src="/assets/images/fl_7.png"></p>
* ํ๋๊ทธ๋จผํธ๊ฐ ๋์ด์ ์๋ณด์ด๊ฒ ๋๋ฉด, ํ๋๊ทธ๋จผํธ์ ๋ทฐ๋ฅผ ์ํ Lifecycle๋ค์ด ```CREATED``` state๋ก ์ด๋ํ๊ณ , observer๋ก ```ON_STOP``` ์ด๋ฒคํธ๋ฅผ emitํ๋ค. 
์ด state transition์ ์คํ๋ ๋ถ๋ชจ ์กํฐ๋นํฐ๋ ํ๋๊ทธ๋จผํธ์ ์ํด์๋ ์ด๋ฐ๋๊ณ , ๋ถ๋ชจ ์กํฐ๋นํ๋๊ทธ๋จผํธ์ state ์ ์ฅํ  ๋๋ ์ด๋ฐ๋๋ค. 
์ด ํ๋์ ํ๋๊ทธ๋จผํธ์ state๊ฐ ์ ์ฅ๋๊ธฐ ์ ์ ```ON_STOP``` ์ด๋ฒคํธ๊ฐ ํธ์ถ๋๋ ๊ฒ์ ๋ณด์ฅํด์ค๋ค.
์ด๊ฒ์ ```ON_STOP``` ์ด๋ฒคํธ๊ฐ (์์ FragmentManager์ FragmentTransaction์ ์ํํ๊ธฐ์ ์์ ํ)๋ง์ง๋ง ํฌ์ธํธ๊ฐ ๋๋๋ก ๋ง๋ ๋ค. 

<p align="center"><img src="/assets/images/stop_save_order.png"></p>

๊ทธ๋ฆผ์์ ๋ณด์ด๋ ๊ฒ์ฒ๋ผ ```onStop()``` ์ฝ๋ฐฑ์ ์์์ ```onSaveInstanceState()```์ state๋ฅผ ์ ์ฅํ๋ ๊ฒ์ API ๋ ๋ฒจ์ ๋ฐ๋ผ ๋ฌ๋ผ์ง๋ค. 
* 28 ์ด์  API ๋ ๋ฒจ๋ค์ ```onSaveInstanceState()```๊ฐ ํธ์ถ๋ ํ ```onStop()```๊ฐ ํธ์ถ๋๋ค.
* 28 ์ด์ API ๋ ๋ฒจ๋ค์ ๋ฐ๋๋ก ```onStop()```์ด ํธ์ถ๋ ํ ```onSaveInstanceState()```์ด ํธ์ถ๋๋ค. 

## [9] Fragment CREATED + View DESTROYED
<p align="center"><img src="/assets/images/fl_8.png"></p>
* animations and transitions ๊ฐ ๋ค ๋๋๊ณ , ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ ์๋์ฐ์์ ```detach```๋ ํ์, ํ๋๊ทธ๋จผํธ ๋ทฐ์ Lifecycle์ด ```DESTROYED``` state๋ก ์ด๋ํ๊ณ  observer๋ค์๊ฒ ```ON_DESTROY``` ์ด๋ฒคํธ๋ฅผ emitํ๋ค.
๊ทธ๋ฌ๊ณ  ๋ํ ํ๋๊ทธ๋จผํธ๋ ```onDesgroyView()``` ์ฝ๋ฐฑ ๋ฉ์๋๋ฅผ ํธ์ถํ๋ค. 
์ด ๋, ํ๋๊ทธ๋จผํธ ๋ทฐ๊ฐ lifecycle์ ๋์ ๋๋ฌํ๊ณ  ```getViewLifecycleOwnerLiveData()```๊ฐ null๊ฐ์ ๋ฆฌํดํ๊ฒ ๋๋ค. 
๊ทธ๋ฆฌ๊ณ  ํ๋๊ทธ๋จผํธ๋ทฐ๊ฐ ```garbage collecteded``` ๋  ์ ์๋๋ก ํ๋๊ทธ๋จผํธ ๋ทฐ์ ๋ถ์ ๋ชจ๋  reference๋ค์ด ์ด ์ง์ ์์ ์ ๊ฑฐ๋์ด์ผ ํ๋ค.  

## [10] Fragment DESTROYED
<p align="center"><img src="/assets/images/fl_9.png"></p>
* ํ๋๊ทธ๋จผํธ๊ฐ ์ ๊ฑฐ๋๊ฑฐ๋, FragmentManager๊ฐ ํ๊ดด๋๋ฉด, ํ๋๊ทธ๋จผํธ์ Lifecycle์ด ```DESTROYED``` state๋ก ์ด๋๋๊ณ  observer๋ค์๊ฒ ```ON_DESTROY``` ์ด๋ฒคํธ๋ฅผ ๋ณด๋ธ๋ค. 
์ด ๋, ํ๋๊ทธ๋จผํธ๋ lifecycle์ ๋์ ๋๋ฌํ๋ค. 

 

[์ฐธ๊ณ  ์ฌ์ดํธ]  
<a name="footnote_1">1</a>: [์๋๋ก์ด๋ ๊ณต์ ๋ฌธ์(The fragment lifecycle)](https://developer.android.com/guide/fragments/lifecycle)  
