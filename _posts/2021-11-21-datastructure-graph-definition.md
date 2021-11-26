---
layout: post
title:  "Graph에 대해 이해하기"
author: ella
categories: [ Data Structure ]
image: assets/images/graph_title.png
---
📚  
# 개념 5️⃣ Graph  

그래프(graph)는 노드(node)와 간선(노드를 연결, edge)을 하나로 모아놓은 자료구조이다. 그래프의 노드 간에는 2개 이상의 경로가 존재할 수 있으며, (루트 노드/부모 노드/자식 노드)라는 개념이 없다. 순회는 BFS 또는 DFS로 이루어지며, 순환/비순환 그래프로 나누거나 방향/무방향 그래프로 나눌 수 있다. 

## 그래프를 구현하는 두 가지 방법 
## (1) 인접 리스트(Adjacency List)
* 각각의 정점에 인접한 정점들을 리스트로 표현한다. 
* 배열의 각 인덱스마다 존재하는 리스트에 인접리스트를 표현한다.
인접 리스트 예)
```
adj = [[] for _ in range(5)]
adj[0] = [1,2,4]
adj[1] = [0,3]
adj[2] = [0]
adj[3] = [1,4]
adj[4] = [0,3]
```
<p align="center"><img src="/assets/images/graph_1.png"></p>  


## (2) 인접 행렬(Adjacency Matrix)
* a 정점과 b 정점이 인접행렬이라면 adj[a][b] = true / adj[b][a] = true로 표현함
* a 정점과 b 정점이 인접행렬이 아니라면 adj[a][b] = false / adj[b][a] = false 로 표현함
인접 리스트 예)
```
adj = [[false]*(5) for _ in range(5)]
adj[0][1] = true
adj[0][2] = true
adj[0][4] = true
adj[1][3] = true
adj[3][4] = true
adj[1][0] = true
adj[2][0] = true
adj[4][0] = true
adj[3][1] = true
adj[4][3] = true
```
<p align="center"><img src="/assets/images/graph_1.png"></p>  

## 🌟 그래프와 트리의 차이점
<p align="center"><img src="/assets/images/treevsgraph.png"></p>




[참고 사이트]  
- [https://techdifferences.com/difference-between-tree-and-graph.html](https://techdifferences.com/difference-between-tree-and-graph.html)
