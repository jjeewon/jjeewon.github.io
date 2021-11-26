---
layout: post
title:  "Hash Table이란?"
author: ella
categories: [ Data Structure ]
image: assets/images/ht_title.png
---
📚  
# 개념 5️⃣ Hash Table  

해쉬테이블은 key-value 쌍으로 저장하는 데이터 구조의 타입이다. ```hash function```을 통해 ```key```에 산술연산을 하게 되는데, 그 결과를 보통 ```hash value``` 혹은 ```hash``` 라고 부른다. ```hash value```는 해쉬테이블에서 key-value 쌍의 ```index```가 된다. 
<p align="center"><img src="/assets/images/ht_1.png"></p>  
## Hash Table의 구성 요소 = Hash function + Array
### (1) Hash function
* Hash function은 key-value 쌍의 인덱스를 결정함
* 좋은 Hash table을 구성하기 위해서는, hash function을 효율적으로 만들어야 함 
* Hash function은 one-way여야 함. 즉, key가 hash에서 도출될 수 있으면 안됨
* Hash funtion은 같은 hash에서 서로 다른 key들을 만들어내면 안됨

### (2) Array
* array는 테이블에서 key-value entry들을 가지고 있음
* array의 크기는 데이터의 양에 맞게 세팅되어야 함 

## Hash table에서의 Collision과 해결방법
* 두 개의 key가 같은 인덱스에 매핑되면, collision이 일어남
* Collision을 회피하는 방법은 크게 세 가지가 있음
* Linear probing, Chaining, Resizing the hash table

### (1) Linear probing
* 만약에 한 쌍이 이미 occupied된 슬롯에 해쉬되었다면, 테이블에서 다음으로 빈 슬롯의 자리를 ```linear```하게 검색한다.

### (2) Chaining
* 여기서 hash table은 ```linked list```의 array임
* 같은 인덱스에 매핑되는 모든 key를 해당 인텍스에 ```linked list node```로 저장한다.
<p align="center"><img src="/assets/images/ht_2.png"></p>

### (3) Resizing the hash table
* hash entry들을 서로 퍼져서 떨어져있게 하기 위해서, hash table의 사이즈를 증가시킴
* ```threshold``` 값은 resizing하기 전에 해쉬테이블에서 엔트리가 차지할 수 있는 비율임
* 밑에 그림에서 threshold 값이 0.6이라고 했을때, 3/5 = 0.6으로 threshold 값을 넘게되므로 size를 10으로 늘려준 것을 볼 수 있다.
<p align="center"><img src="/assets/images/ht_3.png"></p>

## 🌟 시간복잡도 정리표 
<p align="center"><img src="/assets/images/ht_4.png"></p>





[참고 사이트]  
- [https://www.educative.io/edpresso/what-is-a-hash-table](https://www.educative.io/edpresso/what-is-a-hash-table)
