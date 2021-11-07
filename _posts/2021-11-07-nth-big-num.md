---
layout: post
title:  "n번째 큰 수"
author: jiwon
categories: [ Algorithm ]
image: assets/images/nth_big_num.jpg
tags: [featured]
---


📑  
[Baekjoon 2075번, n번째 큰 수](https://www.acmicpc.net/problem/2075) 
 

  
![image info](/assets/images/tree_1.png)  
![image info](/assets/images/tree_2.png)  
![image info](/assets/images/tree_3.png)  

## 🤔 n번째로 큰 수를 구하려면?  
n개만 가지는 최소 힙(min heap)을 이용하여, 힙이 n개의 큰 숫자들로만 구성되도록 표의 숫자들을 하나씩 비교하여 넣거나 넣지 않는다. 그리고 최종적으로 힙에서 원소 하나를 pop하면 된다.
## 🧐 구현 방법  
표 안의 모든 숫자(n개)에 대해,
1. 힙 안에 있는 숫자의 개수가 n보다 작으면 현재 숫자(temp)를 힙에 집어넣는다. 
2. 힙 안에 있는 숫자의 개수가 n보다 크거나 같으면, 힙에서 제일 작은 숫자(min_num_in_queue)를 뺀다.
    2-1. 현재 숫자(temp)가 힙에서 제일 작았던 숫자(min_num_in_queue)보다 크면 힙에 현재 숫자를 넣는다.
    2-2. 현재 숫자(temp)가 힙에서 제일 작았던 숫자(min_num_in_queue)보다 작으면 힙에 제일 작았던 숫자를 다시 넣는다. 
3. 힙에서 가장 작은 숫자를 꺼낸다.(= n번째 큰 수)

 
✏️ 구현 코드(python) ✏️

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
