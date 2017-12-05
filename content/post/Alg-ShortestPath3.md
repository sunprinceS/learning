+++
date =  "2017-09-08T22:29:20+01:00"
description = "All Pairs Shortest Path - Floyd Warshall & Johnson Algorithm"
draft = false
tags = ["Algorithm","Graph Theory","NTU"]
title =  "Algorithm - Graph Theory - Lec 4"
topics = ["Algorithm"]
+++

前兩講討論的是給定起點，問到其他點的最短距離，稱為單源最短路徑問題，而現在我們想考慮 <span>$V$</span> 中兩兩點對彼此間的最短距離，又稱全點對最短路徑問題。

<!--more-->

一個簡單的想法是對每一個點都跑一遍單源最短路徑的 Algorithm，複雜度如下：

* Bellman-Ford: <span>$\mathcal{O}(V*EV) = \mathcal{O}(EV^2)$</span>
* Dijkstra (W/ Fib. Heap): <span>$\mathcal{O}(V*(E+V \log V)) = \mathcal{O}(EV + V^2 \log V)$</span>

**Note: Dijkstra 不能處理負邊**

## Floyd-Warshaall Algorithm

在此我們發展一個專用於找全點對最短路徑的演算法。核心想法是將 <span>$u \rightarrow v$</span> 的路徑依照經過的中繼點做分類(有/無經過)，一次增加一個可用的中繼點，並更新所有路徑。

### 優點 (與 [Bellman-Ford](https://sunprinces.github.io/learning/2017/08/algorithm---graph-theory---lec-2/) 一樣):
* 可以處理有負邊的圖
* 檢測是否存在負環

### Dynamic Programming

<div>
\[
d_{ij}^k \, : \, \text{the cost of shortest path from} \, v_i \rightarrow v_j \, \text{using only} \, \{v_1, v_2,\cdots,v_k\}
\]
</div>

<div>
\[
d_{ij}^k = \min\{\underbrace{d_{ij}^{k-1}}_{\text{\color{blue}{DOESN'T go through}} \, \color{blue}{v_k}}, \underbrace{{d_{ik}^{k-1} + d_{kj}^{k-1}}}_{\text{\color{blue}{go through}} \,\color{blue}{v_k} \,\text{\color{blue}{exactly once}}}\}
\]
</div>

<span>$\displaystyle \Rightarrow$</span> Final answer: <span>$\displaystyle d_{ij}^n$</span>

複雜度為 <span>$\mathcal{O}(V^3)$</span>

### Implementation
<script src="https://gist.github.com/sunprinceS/dea6b01e77cb3fe1f40c6779569d592e.js"></script>

## Johnson's Algorithm

相比於 Bellman-Ford 跑 <span>$|V|$</span> 次的複雜度， Floyd-Warshall 可算是樂勝了；但對 Dijkstra 卻不盡然，如果說今天圖上的邊不多時(e.g <span>$|E| = \mathcal{O}(V \log V)$</span>)，跑 Dijkstra <span>$|V|$</span> 次的複雜度還比較好。\\
**Note: 完全圖: <span>$|E| = \mathcal{O}(V^2)$</span>**

但注意到，[Dijkstra](https://sunprinces.github.io/learning/2017/09/algorithm---graph-theory---lec-3/) 只能處理沒有負邊的圖，所以直接使用是不行的。而 Johnson's Algorithm 的核心概念在於**重新調整邊的權重使其為非負，再來對每個節點跑Dijkstra** 。

### 更改邊權重

Want: 不影響最短路徑及負環的位置

方法: 對於圖上每個節點賦予一個權重 <span>$h(v)$</span> ，而每條邊的權重更新為

<div>
\[
w^{\prime} (u,v) = w(u,v) + h(u) - h(v)
\]
</div>

#### Correctness

* <span>$s \rightarrow v$</span> 的最短路徑不變

<div>
\[
\begin{aligned}
w^{\prime} (s,v) & = w^{\prime}(s,v_1) + w^{\prime}(v_1,v_2) + \cdots + w^{\prime}(v_{k-1},v)\\
& = [w(s,v_1) + h(s) - h(v_1)] + [w(v_1,v_2) + h(v_1) - h(v_2)] + \cdots + [w(v_{k-1},v) + h(v_{k-1}) - h(v)] \\
& = [w(s,v_1) + h(s) - \cancel{h(v_1)}] + [w(v_1,v_2) + \cancel{h(v_1)} - \cancel{h(v_2)}] + \cdots + [w(v_{k-1},v) + \cancel{h(v_{k-1})} - h(v)] \\
& = [w(s,v_1) + w(v_1,v_2) + \cdots + w(v_{k-1},v)] + h(s) - h(v)\\
& = w(s,v) + \underbrace{h(s) - h(v)}_{\text{定值}}\\
\end{aligned}
\]
</div>

* 負環還會是負環 (∵ <span>$h(s) - h(v) = 0 \, \Rightarrow \, w^{\prime}(s,v) = w(s,v)$</span>)

那麼接下來的問題是，該如何 assign <span>$h(v)$</span> ，使得邊的權重均為非負呢？

### 決定節點權重
我們回到 Bellman-Ford 完成後(所有點均不能再被 relax )來看 ，滿足的不等式為

<div>
\[
\begin{aligned}
d(u) + w(u,v) & \geq d(v) \; \forall \,u,v \in V \\
\Rightarrow w^{\prime}(u,v) & \geq 0
\end{aligned}
\]
</div>

移項之後可以發現，用**任一起點到其他節點的最短路徑值作為 <span>$h$</span>** ，可以滿足邊權重為非負的要求😄

### Combine Together

1. 先用 Bellman-Ford 跑一遍去賦予節點值，並以此更新權重
2. 對每個點跑 Dijkstra ， 並減去 <span>$h(s) - h(t)$</span> 得到原先的最短路徑值

**實做細節: 考慮 <span>$G$</span> 並非 connected，我們額外加一個超級起點連接到所有節點(W/ 權重 0)，確保每個邊都可被更新到**。

複雜度: <span>$\mathcal{O}(VE + V^2 \log V)$</span>


