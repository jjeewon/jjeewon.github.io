---
layout: post
title:  "Stackκ³Ό Queue"
author: ella
categories: [ Data Structure ]
image: assets/images/difference_s_q.png
---
π  
# κ°λ 2οΈβ£ Stack vs Queue 

## [1] Stack
<p align="center"><img src="/assets/images/stack.png"></p>
* μ ν λ°μ΄ν° κ΅¬μ‘°
* μ½μ : λ¦¬μ€νΈμ top μμλ§ κ°λ₯ν¨
* μ­μ  : λ¦¬μ€νΈμ top μμλ§ κ°λ₯ν¨
* LIFO(Last In First Out) : κ°μ₯ λμ€μ λ£μ κ²μ΄ κ°μ₯ λ¨Όμ  λμ΄
* μ€νμ μμλ₯Ό μ½μνλ νμ : push
* μ€νμμ μμλ₯Ό μ κ±°νλ νμ : pop
* νμ© μμ
    * μΉ λΈλΌμ°μ¦ λ°©λ¬ΈκΈ°λ‘(λ€λ‘ κ°κΈ°) : κ°μ₯ λμ€μ μ° νμ΄μ§λΆν° λ€μ λ³΄μ¬μ€
    * μ­μ λ¬Έμμ΄ λ§λ€κΈ° : κ°μ₯ λμ€μ μλ ₯λ λ¬ΈμλΆν° μΆλ ₯ν¨
    * μ€ν μ·¨μ(undo) : κ°μ₯ λμ€μ μ€νν κ²λΆν° μ€ν μ·¨μν¨
    * νμ νκΈ°λ² κ³μ°
    * μμ κ΄νΈ κ²μ¬ 

## [2] Queue
<p align="center"><img src="/assets/images/queue.png"></p>
* μ ν λ°μ΄ν° κ΅¬μ‘°
* μ½μ : λ¦¬μ€νΈμ rear μμλ§ κ°λ₯ν¨
* μ­μ  : λ¦¬μ€νΈμ front μμλ§ κ°λ₯ν¨
* FIFO(First In First Out) : κ°μ₯ λ¨Όμ  λ£μ κ²μ΄ κ°μ₯ λ¨Όμ  λμ΄ 
* νμ μμλ₯Ό μ½μνλ νμ : enqueue
* νμμ μμλ₯Ό μ κ±°νλ νμ : dequeue
* νμ© μμ
    * νλ¦°ν°μ μΈμ λκΈ°μ΄
    * μν μλ¬΄
    * μ½μΌν° κ³ κ° λκΈ°κΈ°κ°
    * BFS
    * μΊμ(Cache) κ΅¬ν

[μ°Έκ³  μ¬μ΄νΈ]  
<a name="footnote_1">1</a>: [https://www.geeksforgeeks.org/difference-between-stack-and-queue-data-structures/](https://www.geeksforgeeks.org/difference-between-stack-and-queue-data-structures/)  
