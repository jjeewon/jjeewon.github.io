---
layout: post
title:  "Android Image Upload Logic 구현"
author: ella
categories: [ Android, Image Upload, ContentResolver, Sellers ]
image: assets/images/file_upload.jpeg
---

프로젝트에서 이미지를 서버로 전송해야 하는 부분이 생겼다. 스트링 데이터만 전송해보았고, 이미지를 서버에 전송해 본 경험이 없어서 이에 대해 공부하고 기억하기 위해 포스트를 작성하기로 했다. 이미지나 파일을 전송하기 위해서는 먼저 ‘Multipart’에 대해 알아야 한다.  

#### Multipart란?

웹 클라이언트가 요청을 보낼 때, http 프로토콜의 바디에 데이터를 ‘여러 부분’으로 나눠서 보내는 것이다. 서버에 파일을 업로드할 때, http 프로토콜의 바디에 파일 정보를 담아서 전송하는데, 파일을 한번에 여러개 전송하면 body 부분에 파일이 여러개의 부분으로 연결되어 전송한다.<sup>[1](#footnote_1)</sup>

#### 안드로이드에서 업로드 로직 구현해보기<sup>[2](#footnote_2)</sup>

(1) 매니페스트 파일에서 저장소 퍼미션 주기<sup>[3](#footnote_3)</sup> 

![image info](/assets/images/file_permission.png)

```
public static final String WRITE_EXTERNAL_STORAGE
```
: Allows an application to write to external storage

```
public static final String READ_EXTERNAL_STORAGE
```
: Allows an application to read from external storage 
 
 
(2) MediaType 객체 만들기
```
val mediaType = "multipart/form-data".toMediaTypeOrNull()
```
MediaType : HTTP 요청이나 바디에 사용될 컨텐츠의 타입을 지정하는 객체. 파일 업로드할 때는 일반적으로 multipart/form-data 타입을 사용함

(3) RequestBody 객체 만들기 
```
 val body = imageFile.asRequestBody(mediaType)
```
RequestBody : HTTP 요청의 바디를 생성. 여기에서의 바디는 MediaType과 imageFile로 구성됨 

(4) MultipartBody.Part 객체 만들기
```
val multipartBody = MultipartBody.Part.createFormData("image", imageFile.name, body)
```
createFormDatecreateFormData("서버에서 받는 키값 String", 파일 이름 String, 파일 경로를 가지고 있는 RequestBody 객체) : 멀티파트 요청의 바디 파트를 생성함

(5) 서버와 통신하는 서비스 만들기
```
interface ProductImageUploadService {  
    @Multipart
    @POST("/api/v1/product_images")
    fun uploadProductImage(@Part images: MultipartBody.Part) : 
     Single<ApiResponse<ProductImageUploadResponse>>  
}
```
⚠️ @Multipart 어노테이션을 붙여줘야 MultipartBody.Part를 파라미터로 넣을 수 있음

(6) Retrofit으로 통신 테스트 해보기 

```
private fun requestImageUpload(images: MultipartBody.Part){
        compositeDisposable.add(productImageUploadService.uploadProductImage(images)      
            .subscribeOn(Schedulers.io())
            .subscribe(::onImageUploadResponse))
    }
private fun onImageUploadResponse(response: ApiResponse<ProductImageUploadResponse>) {
        if (response.success) {
            Log.d("imageregistration", "이미지 업로드 성공")
        } else {
            Log.d("imageregistration", response.message + "?: 알 수 없는 오류가 발생했습니다.")         
        }
    }
```

[참고 사이트 및 책]  
<a name="footnote_1">1</a>: [https://codingnotes.tistory.com/73](https://codingnotes.tistory.com/73)  
<a name="footnote_2">2</a>: [[터닝포인트]코틀린으로 쇼핑몰 앱 만들기 : 코틀린의 기본 문법부터 실무 프로젝트까지](https://www.coupang.com/vp/products/1448669625?itemId=2495343201&vendorItemId=70488512934&src=1042503&spec=10304982&addtag=400&ctag=1448669625&lptag=10304982I2495343201&itime=20211102124551&pageType=PRODUCT&pageValue=1448669625&wPcid=16352463134882023358112&wRef=&wTime=20211102124551&redirect=landing&gclid=CjwKCAjwoP6LBhBlEiwAvCcthCknVcLW4ouH-pP0tEcXL2JXcBipFweNbQoUKVCP2-DU6bbKiJJKEhoCZnAQAvD_BwE&campaignid=&adgroupid=&isAddedCart=)  
<a name="footnote_3">3</a>: [안드로이드 Manifest.permission 공식 문서](https://developer.android.com/reference/android/Manifest.permission)




