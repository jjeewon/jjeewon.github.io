---
layout: post
title:  "GET과 POST의 차이점"
author: ella
categories: [ Network ]
image: assets/images/getpost_title.png
---
🌏      
# 개념 1️⃣ GET vs POST  

      
**HTTP**는 Hypertext Transfer Protocol의 약자로서, **클라이언트와 서버 간의 커뮤니케이션**을 위해 설계된 프로토콜이다. 예를 들어, A 클라이언트(브라우저)가 서버에게 HTTP **request**를 보낸다고 생각해보자. 이에 따라 서버는 클라이언트의 request에 대해 response를 보내게 된다. 여기서의 response는 request에 대한 상태 정보(status information)와 요청된 내용을 포함할 수 있다. HTTP 메소드에는 총 7개(**GET**, **POST**, PUT, HEAD, DELETE, PATCH, OPTIONS)가 있는데, 오늘 정리해볼 메소드는 **GET**과 **POST**이다. 

## (1) GET Method
GET 메소드는 **특정 리소스에다가 데이터를 요청할 때** 사용된다. GET은 HTTP 메소드 중에서 가장 일반적인 메소드 중 하나이다.
### 🧐 GET request의 특징 
- 캐시될 수 있음
- 브라우저 기록에 남음
- 북마크될 수 있음
- 민감한 데이터(개인 정보 등) 처리할 때는 사용하면 안됨
- 길이 제한이 있음
- only **request data**에만 사용됨(**데이터 수정 불가능**)  
  
  ```
/test/demo_form.php?name1=value1&name2=value2
  ```
👉 GET request에서는 다음과 같이 GET request의  ```URL```에 **query string(name/value 쌍)**이 같이 딸려간다.

## (2) POST Method
POST 메소드는 **리소스를 create하거나 update하기 위해서 서버에 데이터를 보낼 때** 사용된다. POST도 GET와 함께 HTTP 메소드 중에서 가장 일반적인 메소드 중 하나이다.
### 🧐 POST request의 특징 
- 절대 캐시되지 않음
- 브라우저 기록에 남지 않음
- 북마크될 수 없음
- 길이 제한이 없음

  ```
POST /test/demo_form.php HTTP/1.1
Host: w3schools.com
name1=value1&name2=value2
  ```
 👉 POST request에서는 다음과 같이 서버로 보내는 데이터가 HTTP request의 ```request body```에 저장된다. 
  
  
## 🌟 GET vs POST 차이점 총정리 🌟  
<p align="center"><img src="/assets/images/getpost.png"></p>




[참고 사이트]  
[https://www.w3schools.com/tags/ref_httpmethods.asp](https://www.w3schools.com/tags/ref_httpmethods.asp)  
