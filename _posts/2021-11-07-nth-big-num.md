---
layout: post
title:  "nλ²μ§Έ ν° μ"
author: ella
categories: [ Algorithm ]
image: assets/images/nth_big_num.jpg
tags: [featured]
---


π  
[Baekjoon 2075λ², nλ²μ§Έ ν° μ](https://www.acmicpc.net/problem/2075) 
 

  
![image info](/assets/images/n_1.png)  
![image info](/assets/images/n_2.png)    


## π€ nλ²μ§Έλ‘ ν° μλ₯Ό κ΅¬νλ €λ©΄?  
nκ°λ§ κ°μ§λ μ΅μ ν(min heap)μ μ΄μ©νμ¬, νμ΄ nκ°μ ν° μ«μλ€λ‘λ§ κ΅¬μ±λλλ‘ νμ μ«μλ€μ νλμ© λΉκ΅νμ¬ λ£κ±°λ λ£μ§ μλλ€. κ·Έλ¦¬κ³  μ΅μ’μ μΌλ‘ νμμ μμ νλλ₯Ό popνλ©΄ λλ€.
## π§ κ΅¬ν λ°©λ²  
ν μμ λͺ¨λ  μ«μ(nκ°)μ λν΄,
1. ν μμ μλ μ«μμ κ°μκ° nλ³΄λ€ μμΌλ©΄ νμ¬ μ«μ(temp)λ₯Ό νμ μ§μ΄λ£λλ€. 
2. ν μμ μλ μ«μμ κ°μκ° nλ³΄λ€ ν¬κ±°λ κ°μΌλ©΄, νμμ μ μΌ μμ μ«μ(min_num_in_queue)λ₯Ό λΊλ€.
    2-1. νμ¬ μ«μ(temp)κ° νμμ μ μΌ μμλ μ«μ(min_num_in_queue)λ³΄λ€ ν¬λ©΄ νμ νμ¬ μ«μλ₯Ό λ£λλ€.
    2-2. νμ¬ μ«μ(temp)κ° νμμ μ μΌ μμλ μ«μ(min_num_in_queue)λ³΄λ€ μμΌλ©΄ νμ μ μΌ μμλ μ«μλ₯Ό λ€μ λ£λλ€. 
3. νμμ κ°μ₯ μμ μ«μλ₯Ό κΊΌλΈλ€.(= nλ²μ§Έ ν° μ)

 
βοΈ κ΅¬ν μ½λ(python) βοΈ

```
import sys
import heapq

n = int(sys.stdin.readline())
queue = []
for _ in range(n):
    input_arr = list(map(int, sys.stdin.readline().split(' ')))
    for temp in input_arr:
        if len(queue) < n:
            heapq.heappush(queue, temp)
        else:
            min_num_in_queue = heapq.heappop(queue)
            if min_num_in_queue < temp:
                heapq.heappush(queue, temp)
            else:
                heapq.heappush(queue, min_num_in_queue)
print(heapq.heappop(queue))
```
