+++
date =  "2017-08-31T17:02:52+01:00"
description = "Shortest Path - Bellman-Ford Algorithm"
draft = false
tags = ["Algorithm","Graph Theory","NTU"]
title =  "Algorithm - Graph Theory - Lec 2"
topics = ["Algorithm"]
+++

圖的一個常見應用場合是想找出某兩點之間的最短(/長)路徑，此時的邊權重又視為兩節點之間的距離。
<!--一般來說，這兩個問題都是 NPC ，只有加上某些限制後，才會是在多項式時間可解的問題 (最短路 ─ 不存在負環；最長路  ─ 不存在正環)。-->

<!--more-->
## Shortest Path Problem

Given a graph <span>$G$</span>, a starting vertex <span>$s$</span> and edge cost <span>$c_e$</span>\\
Want: Find the path with min. total cost from <span>$s \rightarrow v$</span> for some <span>$v$</span>

## Bellman-Ford Algorithm

### 優點:

* 可以處理有負邊的圖
* 能偵測負環 ( *Negative Cycle* )

### Relax (鬆弛)

最短路徑演算法中常看到的名詞，先看程式碼

```python
def relax(u,v):
  if d[v] > d[u] + w(u,v):
    d[v] = d[u] + w(u,v) # 當前最短路的 cost
    π[v] = u # back-tracking
```

簡單來說就是如果遇到 cost 更小的路徑，就更新。\\
這也建構於最短路徑問題的特性，若從 <span>$s$</span> 到 <span>$v$</span> 的最短路徑中經過 <span>$u$</span> ，那麼可以確定子路徑 <span>$s \rightarrow u$</span> 亦為其兩點間的最短路。

### Psuedo Code

```python
# Init
for v in G(V):
  d[v] = ∞ 
  π[v] = NULL
d[s] = 0

# Iterate |V| - 1 times
for i in range(|V| - 1):
  for e(u,v) in E:
    relax(u,v)
```

複雜度為 <span>$\mathcal{O}(VE)$</span>

### 一些觀察

* 實際上也不用跑到 |V| - 1 次才停止，在某一回合如果沒有更新，那麼往後的回合也不可能更新了，此時便可以停止。

* 遍歷邊的順序，影響了需要幾回合才能找到最短路，那麼最佳的順序是什麼呢?
  (<span>$\rightarrow$ see [Dijkstra](https://sunprinces.github.io/learning/2017/09/algorithm---graph-theory---lec-3/)</span>)

### Correctness

* 假設圖上沒有負環，the shortest path from <span>$s \rightarrow v$</span> has <span>$\leq |V|-1$</span> edges

*__proof__* : By contradiction，若用了 <span>$\geq |V|$</span> 條邊，該路徑必存在 cycle (某些點被重複拜訪)，但負邊不存在，這麼做只是徒增 cost ( 故可把 cycle 拔掉 )，得證。

* 跑 <span>$|V| - 1$</span> 個回合必可找到最短路，考慮 <span>$s \rightarrow v $</span> 的最短路徑，

<div>
\[
s \stackrel{c_1}{\longrightarrow} v_1 \stackrel{c_2}{\longrightarrow} v_2 \stackrel{c_3}{\longrightarrow} \cdots v_{k-1} \stackrel{c_k}{\longrightarrow} v
\]
</div>

After `relax`( s , <span>$v_1$</span> )  in round 1，d[<span>$v_1$</span>] = <span>$c_1$</span>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`relax`(<span>$v_1$</span>, <span>$v_2$</span> )  in round 2，d[<span>$v_2$</span>] = <span>$c_1 + c_2$</span>\\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span>$\cdot$</span>\\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span>$\cdot$</span>\\
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<span>$\cdot$</span>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`relax`(<span>$v_{k-1}$</span>, <span>$v$</span>) in round <span>$k$</span>，d[<span>$v$</span>] = <span>$c_1 + c_2 + \cdots + c_k$</span>

可知在 <span>$k$</span> 回合內必可完成更新，又 <span>$k \leq |V| - 1$</span>

由上方這個 Claim ，如果 d[<span>$v$</span>] 在 <span>$|V|$</span> round 有所改動的話，代表了負環存在



### Implementation

<script src="https://gist.github.com/sunprinceS/5c93f48db7d3b476f3829becbc2f2743.js"></script>

做兩次 Bellman-Ford ，偵測出所有負環。\\
第二次 Bellman-Ford 中，邏輯判斷將d[<span>$v$</span>] 設成 <span>$- \infty$</span> 的情況共有兩個

* <span>$- \infty$</span> 的傳染性:

```cpp
if(dist[e.edge.first] == INT_MIN)
        dist[e.edge.second] = INT_MIN;
```

* negative cycle 存在於 <span>$s \rightarrow v$</span> 的最短路中，則將 d[<span>$v$</span>] 設成 <span>$- \infty$</span>

```cpp
if(dist[e.edge.first] != INT_MAX && dist[e.edge.first] + e.weight < dist[e.edge.second])
        dist[e.edge.second] = INT_MIN;
```

為什麼還需要再跑 <span>$|V| - 1$</span> 次？\\
是因為要將 <span>$- \infty$</span> 傳染遍整個環，需要負環長度個回合，而最大的負環長度 <span>$\leq |V| - 1$</span>。
