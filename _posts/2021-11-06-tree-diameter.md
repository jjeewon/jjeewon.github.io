---
layout: post
title:  "트리의 지름"
author: jiwon
categories: [ Algorithm ]
image: assets/images/diameter_of_tree.jpg
tags: [featured]
---

 
📑  
[Baekjoon 1967번, 트리의 지름](https://www.acmicpc.net/problem/1967) 
 

  
![image info](/assets/images/tree_1.png)  
![image info](/assets/images/tree_2.png)  
![image info](/assets/images/tree_3.png)
## 🤔 트리의 지름이란?  
트리에 존재하는 모든 경로들 중에서 가장 긴 것의 길이
## 🧐 트리의 지름 구하는 방법  
1. 임의의 노드(a)에서 가장 거리가 먼 노드(b)를 찾는다.
2. (b) 노드에서 가장 거리가 긴 노드까지의 길이를 구한다.   

➡️  '한 노드에서 다른 모든 노드들 간의 거리'를 구하기 위해 [dijkstra 알고리즘](https://en.wikipedia.org/wiki/Dijkstra%27s_algorithm)을 사용했다.
 
✏️ 구현 코드(python) ✏️

```
import sys
import heapq

INF = int(1e9)
def dijkstra(graph, start, distance):
    queue = []
    heapq.heappush(queue, (0, start))
    distance[start] = 0
    while queue:
        dist, now = heapq.heappop(queue)
        if distance[now] < dist:
            continue
        for i in graph[now]:
            cost = dist + i[1]
            if cost < distance[i[0]]:
                distance[i[0]] = cost
                heapq.heappush(queue, (cost, i[0]))
    return distance

n = int(sys.stdin.readline())
graph = [ [] for _ in range(n+1) ]
for _ in range(n-1):
    a, b, c = map(int, sys.stdin.readline().split(' '))
    graph[a].append((b, c))
    graph[b].append((a, c))

distance = [INF] * (n+1)
dijkstra(graph, 1, distance)
arr = [(-distance[i],i) for i in range(1,n+1)]
start_index = sorted(arr)[0][1]
distance = [INF] * (n+1)
dijkstra(graph, start_index, distance)
arr = [(-distance[i],i) for i in range(1,n+1)]
result = -sorted(arr)[0][0]
print(result)
```


