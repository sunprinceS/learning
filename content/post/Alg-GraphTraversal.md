+++
date =  "2017-08-25T10:08:13+01:00"
description = "Traversal & Application"
draft = false
tags = ["Algorithm","Graph Theory","NTU"]
title =  "Algorithm - Graph Theory - Lec 1"
topics = ["Algorithm"]
+++

給定一張圖，要如何讀取當中的資訊呢？簡單來說就是從一給定點開始，依某種順序去拜訪相鄰(有關係)的點，最後走完所有點，收集完圖中的資訊。以下簡介兩種簡本的 traversal 方法以及應用。

<!--more-->

## Breadth-First Search (BFS)
從某一節點開始，依序走訪所有相鄰且未拜訪過的節點，由走訪的節點進行先廣後深的搜尋(搜尋會像同心圓般地擴散開來)，通常以 **queue** 來實做。

### psuedo code

```python
def bfs(G,s):
  Q = Queue(s); # put start into queue

  time = 0 # 紀錄節點離開queue的時刻
  leave_time[s] = time

  visited[s] = True

  while Q is not empty:
    time++
    u = dequeue(Q)
    leave_time[u] = time

    for v in adj(u):
      if visited[v] is False:
        visited[v] = True
        Q.push(v)
```
上圖迴圈中的 `if` 比較會執行 <span>$\mathcal{O}(\text{Total size of adj. list})$</span>，至多 <span>$2|E|$</span>，\\
所以total 複雜度為<span>$\mathcal{O}(V+E)$</span> (把圖讀進來)。\\
觀察 `leave_time` 可以發現 BFS 會**優先走過離起點最近的點**，而這個特性可以幫助我們解決一些圖論上的問題(e.g 找出每一點到根節點的最小距離)。

## Depth-First Search (DFS)
從某一節點開始，探索相鄰的節點，並儘可能深地探索，直到該節點下的所有子孫節點全部遍歷過後，再 backtrace 回前一個節點，重複此步驟直到遍歷全部的節點。通常以 **stack** 來實做，亦即recursive call 某 function。其中，以 DFS 產生的搜索生成樹，我們稱之為 DFS tree 。

### psuedo code

```python 
def dfs(G,v):
  mark v as visited
  for u in adj(v):
    if u is not visited:
      dfs(u)
```
複雜度為 <span>$\mathcal{O}(V+E)$</span>

根據拜訪的先後順序，我們可以定義幾種 order ，且這些 order 實務上能用來解決問題，\\
比如 `pre-ordering` (拜訪的先後順序)、`post-ordering` (節點從 stack 離開的先後順序)。

### 一些相關定義
在 DFS 有向圖的過程中，我們將所遇到的邊進行分類

1. Tree Edge: DFS Tree 上的邊
2. Back Edge: 連回 DFS tree 上祖先的邊
3. Forward Edge: 連接 DFS Tree 上子孫的 non Tree Edge
4. Cross Edge: otherwise ， 在 DFS Tree 上的一顆子樹連接另一棵子樹的邊

**Note**: 後兩者在無向圖中不存在(<span>$\because$</span> Back Edge 和 Forward Edge 角色變為一樣；且 Cross Edge 若存在，那麼其必可直接連至另一子樹繼續 dfs ，變為 Tree Edge ，故不可能存在)

### General DFS
考慮圖 <span>$G$</span> 不是 connected 的情況，定義 general DFS 來遍歷個節點，同時每個節點在 DFS 時，引進兩個全域變數 <span>$t,s$</span>。
```python
t=0
s=NULL

def dfs(v):
  mark v as visited
  for u in adj[v]:
    if u is not visited:
      dfs(u)
  leader(v) = s
  leave_time(v) = t
  t++
  
for v in G(V):
  if v is not visited:
    s=v
    dfs(v)
```
## Application - Finding Strongly Connected Component
### 關於連通的一些定義
1. <span>$u$</span> is <span style="color:blue"> connected </span> to <span>$v$</span> <span>$\Leftrightarrow$</span> <span>$\exists$</span> a path from <span>$u$</span> to <span>$v$</span>
2. A <span style="color:blue"> connected component</span> of an undirected graph: **maximal** set of connected vertices\\
(已經無法再增添新的節點，並同時保持兩兩節點 connected 的關係)
3. An <span style="color:blue"> undirected graph is connected </span> <span>$\Leftrightarrow$</span> there is **exactly 1** connected component
4. A <span style="color:blue"> Tree </span> is an connected **undirected** graph with no simple cycles
5. <span style="color:blue"> Strongly Connected Component (SCC)</span> of a **directed graph**:\\
  A maximal set <span>$S$</span> of vertices s.t <span>$\forall u,v \in S, u \,
  \text{is connected to}\, v$</span>

### SCC 的用途
將任意有向圖收縮成 DAG，化簡問題。

**Example**: [待補]

### Kosaraju's Algorithm
整體概念就是先對反圖 <span>$G^{rev}$</span> 做 general DFS，標註每個節點的finish_time (可以想成把 connected info 擷取出來)，接著再依該時間戳記，由高者對 <span>$G$</span> 做 general DFS，每次遍歷的一群即為一個 SCC。

1. Let <span>$G^{rev} = (V,E^{rev}), \text{其中} \, (u,v) \in E \Leftrightarrow (v,u) \in E^{rev}$</span>
2. Run general DFS on <span>$G^{rev}$</span>
3. Run general DFS on <span>$G$</span>, **Starting from nodes with highest f(v)** \\
  if leader(u) = leader(v) <span>$\Rightarrow$ </span> <span style="color:blue"> <span>$u,v$</span> in same SCC</span>

### 一些觀察
不難發現到，反圖與原圖的 SCC 是一樣的 (可以從 SCC 的定義中得出)，在 run general DFS 第一遍 (於 <span>$G^{rev}$</span>)時，每個節點的 leader 其實已經設定好，但這並不能用作最終答案，因為此時 <span>$leader(v) = u$</span> 的意義為在 <span>$G^{rev}$</span> 上，可以由<span>$u$</span> 出發到達的點 (也就是在 <span>$G$</span> 上，由誰出發可以到達 <span>$u$</span>)，但不能確定在原圖上從 <span>$v$</span> 出發可以到達 <span>$u$</span> ，所以才需要再跑第二次 general DFS 來找出 SCC (而依據<span>$f(v)$</span> ，則確保了我們依循 topological reverse order，不會有**跑到下一個 SCC** 的情況發生)

### psuedo code
```python
def kosaraju(G,SCC_list):
  GT = G with reversing edges
  general_dfs(GT)

  for v in G(V) with decending finish_time stamp in GT:
    if v is not visited:
      DFS(v,G) with leader = v then make a set S (with every element whose leader is v)
      SCC_list.append(S)

```

## Application - Topological Sort
### Def. Directed Acyclic Graph (DAG，有向無環圖)
有向圖中的特例，所有的邊都朝著同一個方向延伸，所有節點可定義**先後次序**(也就是下方定義的拓樸排序)
(e.g 玩遊戲時，將所有關卡視為一個個節點，要先破完 A 關卡或 B 關卡，才可以打後續E關卡，每種可能的破關順序即是一種 Topological order)

### Topolocial Order (拓樸排序)
在有向圖中，找到一個節點的排序 <span>$v_1,v_2,\cdots,v_n$</span>，使得任意有向邊
<span>$e(v_i,v_j) \in E$</span>， <span>$v_i$</span> comes before <span>$v_j$</span> in the ordering 。

### DFS Algortihm
Run DFS on <span>$G^{rev}$</span> \\
<span>$f(v)$</span> 由小到大的順序即為 Topological order，複雜度
<span>$\mathcal{O}(V+E)$</span>\\
**Note**: 若是 run 在 <span>$G$</span> 上，那麼 <span>$f(v)$</span> 由大到小的順序亦是 Topological order
