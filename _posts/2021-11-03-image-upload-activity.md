---
layout: post
title:  "ActivityLauncher로 갤러리에서 Image 가져오기"
author: jiwon
categories: [ Android, Image Upload, Sellers ]
image: assets/images/image_upload.png
---

ImageView를 클릭했을 때, 갤러리의 이미지를 가져와서 해당 이미지 뷰에 등록하는 기능을 개발해보자. 나는 네 개의 이미지 뷰에 각각 갤러리에서 받아온 비트맵 이미지를 세팅하는 기능을 구현해보았다. startActivityForResult 메소드가 deprecated 되었으므로,  registerForActivity를 통해 ActivityLauncher를 만들어서 안드로이드 앱에서 갤러리의 이미지를 가져오게 하였다.<sup>[1](#footnote_1)</sup> 



#### 갤러리에서 사진을 불러와서 이미지뷰에 세팅하는 기능 구현해보기

(1) 이미지 뷰 레이아웃 정의하기 
 
[activity_productRegistration.xml]
```
            <ImageView
                android:id="@+id/iv_1"
                android:layout_width="80dp"
                android:layout_height="80dp"
                app:layout_constraintLeft_toLeftOf="parent"
                app:layout_constraintTop_toTopOf="parent"
                app:layout_constraintRight_toLeftOf="@id/iv_2"
                android:src="@drawable/img_addphoto"
                />
            <ImageView
                android:id="@+id/iv_2"
                android:layout_width="80dp"
                android:layout_height="80dp"
                app:layout_constraintLeft_toRightOf="@id/iv_1"
                app:layout_constraintTop_toTopOf="@id/iv_1"
                app:layout_constraintRight_toLeftOf="@id/iv_3"
                android:src="@drawable/img_addphoto"
                />
            <ImageView
                android:id="@+id/iv_3"
                android:layout_width="80dp"
                android:layout_height="80dp"
                app:layout_constraintLeft_toRightOf="@id/iv_2"
                app:layout_constraintRight_toLeftOf="@id/iv_4"
                app:layout_constraintTop_toTopOf="@id/iv_2"
                android:src="@drawable/img_addphoto"
                />
            <ImageView
                android:id="@+id/iv_4"
                android:layout_width="80dp"
                android:layout_height="80dp"
                app:layout_constraintLeft_toRightOf="@id/iv_3"
                app:layout_constraintRight_toRightOf="parent"
                app:layout_constraintTop_toTopOf="@id/iv_3"
                android:src="@drawable/img_addphoto"
                />
```

위의 레이아웃을 화면에 띄워보면 다음과 같이 나온다.  

<p align="center"><img src="/assets/images/iv_before.png"></p>


(2) viewmodel에 클릭이벤트 정의하고 get 함수 정의하기

[ProductRegistrationViewmodel.kt]
```
private val imageViewClickEvent = SingleLiveEvent<Int>()
fun getImageFromGallery(num: Int){
        imageViewClickEvent.postValue(num)
    }
```

Int로 정의한 이유는, 데이터바인딩된 레이아웃에서 네 개의 이미지뷰를 'Int값'으로 이미지뷰를 판별하기 위해서이다. 

(3) 데이터바인딩한 레이아웃에서 이미지뷰 onClick으로 클릭 이벤트가 일어났음을 알려주기 

[activity_productRegistration.xml]
```
            <ImageView
                android:id="@+id/iv_1"
                .....
                .....
                android:onClick="@{()->productRegistrationViewModel.getImageFromGallery(1)}"   
                />
            <ImageView
                android:id="@+id/iv_2"
                .....
                .....
                android:onClick="@{()->productRegistrationViewModel.getImageFromGallery(2)}"   
                />
            <ImageView
                android:id="@+id/iv_3"
                .....
                .....
                android:onClick="@{()->productRegistrationViewModel.getImageFromGallery(3)}"   
                />
            <ImageView
                android:id="@+id/iv_4"
                .....
                .....
                android:onClick="@{()->productRegistrationViewModel.getImageFromGallery(4)}"   
                />
```

(4) 액티비티에서 뷰모델의 클릭이벤트를 구독하여, num번째 이미지 뷰 클릭 시 액티비티의 getImageFromGallery 함수에 num 넘기기

[ProductRegistrationActivity.kt]
```
productRegistraionViewModel.getImageViewClickEvent().observe(this, Observer { num ->
             getImageFromGallery(num)
         })
```

(5) registerAcitivtyForResult를 받은 결과 값(uri)를 처리하는 ActivityLauncher 객체 getContent 정의
 
[ProductRegistrationActivity.kt] 
```
val getContent = registerForActivityResult(ActivityResultContracts.GetContent()) { uri: Uri? ->
        uri?.let{
            val bitmap = getBitmapFromUri(uri)
            bitmap?.let {
                when(productRegistraionViewModel.current_iv_id){
                    1 -> binding.iv1.setImageBitmap(bitmap)
                    2 -> binding.iv2.setImageBitmap(bitmap)
                    3 -> binding.iv3.setImageBitmap(bitmap)
                    4 -> binding.iv4.setImageBitmap(bitmap)
                }
            }
        }
    }
    
fun getImageFromGallery(imageNum: Int){
        productRegistraionViewModel.current_iv_id = imageNum
        getContent.launch("image/*")
    }
```

'image/*'로 input 타입이 지정된 getContent를 통해 갤러리에서 사용자가 이미지를 선택하면, 콜백 람다에서 그 이미지의 uri를 받아 비트맵 객체로 만들어준다. 비트맵 객체는 getImageFromGallery에서 받아놓은 current_iv_id 값을 통해 현재 이미지 뷰의 비트맵이미지로 설정한다.



(6) 안드로이드 에뮬레이터에서 테스트 해보기 
 
<p align="center"><img src="/assets/images/upload_gallery.png"></p>
에뮬레이터에서 첫 번째 이미지 뷰를 클릭했을 때, 다음과 같이 갤러리에 저장된 사진들이 나온다. 나는 미리 에뮬레이터에서 카메라로 사진 한 개를 찍어놨기 때문에, 갤러리에서 사진 한 개만 보인다.

<p align="center"><img src="/assets/images/iv_first_clicked.png"></p>

사진을 선택하면, 다음과 같이 첫 번째 이미지 뷰에 선택된 사진이 설정된다.
다만, 원래 이미지 뷰에 있던 이미지랑 크기가 안맞는데 이것은 차후에 Glide 라이브러리를 통해서 크기를 조정해봐야겠다.  

 
[참고 사이트]  
<a name="footnote_1">1</a>: [안드로이드 getActivityForResult 관련 공식 문서](https://developer.android.com/training/basics/intents/result)  


