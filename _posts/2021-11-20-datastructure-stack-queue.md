---
layout: post
title:  "Stack과 Queue"
author: jiwon
categories: [ Data Structure ]
image: assets/images/difference_s_q.png
---
📚  
# 개념 2️⃣ Stack vs Queue 

## [1] Stack
<p align="center"><img src="/assets/images/stack.png"></p>
* 선형 데이터 구조
* 삽입 : 리스트의 top 에서만 가능함
* 삭제 : 리스트의 top 에서만 가능함
* LIFO(Last In First Out) : 가장 나중에 넣은 것이 가장 먼저 나옴
* 스택에 원소를 삽입하는 행위 : push
* 스택에서 원소를 제거하는 행위 : pop
* 활용 예시
    * 웹 브라우즈 방문기록(뒤로 가기) : 가장 나중에 연 페이지부터 다시 보여줌
    * 역순 문자열 만들기 : 가장 나중에 입력된 문자부터 출력함
    * 실행 취소(undo) : 가장 나중에 실행한 것부터 실행 취소함
    * 후위 표기법 계산
    * 수식 괄호 검사 

## [2] Queue
<p align="center"><img src="/assets/images/queue.png"></p>
* 선형 데이터 구조
* 삽입 : 리스트의 rear 에서만 가능함
* 삭제 : 리스트의 front 에서만 가능함
* FIFO(First In First Out) : 가장 먼저 넣은 것이 가장 먼저 나옴 
* 큐에 원소를 삽입하는 행위 : enqueue
* 큐에서 원소를 제거하는 행위 : dequeue
* 활용 예시
    * 프린터의 인쇄 대기열
    * 은행 업무
    * 콜센터 고객 대기기간
    * BFS
    * 캐시(Cache) 구현

[참고 사이트]  
<a name="footnote_1">1</a>: [https://www.geeksforgeeks.org/difference-between-stack-and-queue-data-structures/](https://www.geeksforgeeks.org/difference-between-stack-and-queue-data-structures/)  
