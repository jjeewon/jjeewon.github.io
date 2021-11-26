---
layout: post
title:  "ArrayList와 LinkedList"
author: ella
categories: [ Data Structure ]
image: assets/images/arrlinked.jpeg
---
📚  
# 개념 1️⃣ ArrayList vs LinkedList 

자바 List 인터페이스의 구현체에는 Stack, Vector, ArrayList, LinkedList가 있다. 오늘은 이것들 중에서 가장 대표적인 List 인터페이스 구현체인 ArrayList와 LinkedList의 특징과 이 둘의 차이점에 대해서 알아보자.<sup>[1](#footnote_1)</sup>  
## [1] ArrayList
* 중복 허용
* 순서 유지 
* 인덱스로 원소 관리함 
* 배열을 추가, 삭제하는 메소드가 존재한다는 점에서 배열과 구별됨(배열은 크기가 지정되면 고정)
* 용량이 꽉찬 상태에서 원소를 추가했을 때, 배열이 동적으로 늘어나는게 아니라, 더 큰 용량의 배열을 만들어서 옮김

### (1) add(E element)
* 배열의 마지막에 원소를 추가하기 

### (2) add(int index, E element)
* 원소를 index 위치에 추가하기
* index 위치에 데이터를 넣으려면 index 위치부터 그 뒤에 있는 원소들을 한칸씩 미뤄서 중간에 공간을 만드는 작업을 해야하기 때문에 시간이 많이 걸림  

### (3) remove(int index)
* index 위치의 원소를 삭제하기
* 마지막 원소를 삭제하면 O(1)이지만, 중간이나 처음의 원소를 삭제하면 빈공간을 다시 채워야하므로 시간이 많이 걸림  

### (4) get(int index)
* index에 해당하는 원소 찾아오기
* index에 해당하는 원소를 O(1)로 그냥 찾아올 수 있기 때문에 탐색에 매우 유리함   


## [2] LinkedList
* 내부적으로 양방향의 연결리스트로 구성되어 있어서 참조하려는 원소에 따라 처음부터 순방향 또는 역순으로 순회하는 리스트
* 배열의 단점을 보완하기 위해서 LinkedList가 고안됨

### 🧐 배열의 단점 🧐
* 크기를 변경할 수 없음
    * 크기를 변경하려면 새로운 배열을 생성해서 복사해야 함
    * 실행 속도를 향상시키기 위해서는 초기 용량을 크게 설정해놔야 하는데, 이것이 메모리 낭비가 될 수 있음
* 비순차적으로 데이터를 추가/삭제할 때 시간이 오래 걸림
    * 데이터를 순차적으로 추가하거나, 마지막에서 데이터를 삭제하는 건 빠름
    * 배열의 중간에서 데이터를 추가하려면, 빈 공간을 만들기 위해 해당 인덱스부터 그 뒤에 있는 데이터들을 뒤로 밀어야 함
    * 배열의 중간에서 데이터를 삭제하려면, 빈 공간을 채우기 위해 해당 인덱스 뒤에 있는 데이터들을 앞으로 채워야 함 

### (1) add(E element)
* 배열의 마지막에 원소를 추가하기  
* LinkedList는 인덱스가 없음
* 따라서 원소를 추가하기 위해서는 Head서부터 마지막까지 찾아가야하므로, 시간이 오래걸림

### (2) add(int index, E element)
* 원소를 index 위치에 추가하기 
* Head서부터 해당 위치까지 탐색해서 찾아가야하므로, 시간이 걸림   

### (3) remove(int index)
* index 위치의 원소를 삭제하기
* 삭제하려는 원소 앞,뒤의 참조 상태를 바꾸기만 하면 돼서 시간복잡도가 O(1)임 

### (4) get(int index)
* index에 해당하는 원소 찾아오기
* index에 해당하는 원소를 찾기 위해서는 Head에서부터 쭉쭉 검색해나가야 하므로, 최악의 경우 시간복잡도가 O(n)임

# <center>--------------------</center>

|🌟|   ArrayList   |   LinkedList   |
|--|:--:|:--:|
|**탐색**|   효율적(인덱스 기반)|비효율적(헤드에서부터 쭉쭉)|
|**추가/삭제**|   비효율적(빈 칸 채워야 함)|효율적(앞뒤 노드 참조만 바꿔주면 됨)|  

<p align="center"><img src="/assets/images/arrlinkedop.png"></p>

[참고 사이트]  
<a name="footnote_1">1</a>: [https://devlog-wjdrbs96.tistory.com/64](https://devlog-wjdrbs96.tistory.com/64)  
<a name="footnote_1">2</a>: [https://medium.com/zero-equals-false/arraylist-vs-linkedlist-f8c5099153b5](https://medium.com/zero-equals-false/arraylist-vs-linkedlist-f8c5099153b5)  

