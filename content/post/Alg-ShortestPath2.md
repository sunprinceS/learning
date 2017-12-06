+++
date =  "2017-09-03T21:28:37+01:00"
description = "Sigle Source Shortest Path - Dijkstra Algorithm"
draft = false
tags = ["Algorithm","Graph Theory","NTU"]
title =  "Algorithm - Graph Theory - Lec 3"
topics = ["Algorithm"]
+++

呈前一講的討論，我們有了個求單源最短路徑的演算法[Bellman-Ford](https://sunprinces.github.io/learning/2017/08/algorithm---graph-theory---lec-2/) (複雜度為 <span>$\mathcal{O}(VE)$</span> )，現下我們考慮額外的限制條件(邊權重為非負)，想找出是否存在更有效率的演算法

<!--more-->

## 三角不等式

<div>
\[
\forall u,v,x \in V, \, \text{we have} \, \delta(u,v) \leq \delta(u,x) +
\delta(x,v)
\]
</div>

其中 <span>$\delta(u,v)$</span> 表示 <span>$u, v$</span> 間的**最短距離**。\\
此為 Dijkstra 所根據的直觀原理，也是其為何不能處理負邊問題的原因(只有正邊時顯然成立)。

之前討論在 [Bellman-Ford](https://sunprinces.github.io/learning/2017/08/algorithm---graph-theory---lec-2/) 中的一些觀察討論到 `relax` 邊的順序，決定了演算法運行的時間。而根據上方的論述，我們可以找到一個最優的訪問邊之順序。簡單來說，我們將節點分為兩個集合，一個集合表示最短路已被設好的節點，另一個表示還未確定者，而每一次都從**還未確定者中，選出當前 d 值最小的節點**來做 `relax`。

根據三角不等式，這樣的 Greedy 策略是沒有問題的，因我們選出的節點 <span>$v$</span> ，所對應的 d[<span>$v$</span>] 已經不可能再更動了 (無法找到經由其他點的路徑使得距離成本 <span>$<$</span> d[<span>$v$</span>])。

## Psuedo Code
其實跟 Bellman-Ford 非常相像。

```python
# Init
for v in G(V):
  d[v] = ∞ 
  π[v] = NULL
d[s] = 0

Pick u which d[u] is MIN
remove u
for v in adj[u]:
  relax(u,v)
```

### Complexity Analysis

通常以 heap 來實做，但效率如何則取決於我們使用何種 heap。需要

1. <span>$\mathcal{\Theta}(V)$</span> `INS`
2. <span>$\mathcal{\Theta}(V)$</span> `Extract-Min`
3. <span>$\mathcal{\Theta}(E)$</span> `Decrease(v,*)`

若用 binary heap 的話，複雜度為 <span>$\mathcal{\Theta}(E \log E + V\ log V) = \mathcal{\Theta}((E+V) \log V)$</span> \\
若用 Fibonacci heap 的話，均攤複雜度為 <span>$\mathcal{\Theta}(E + V \log V)$</span>

## Implementation
在 C++ 中以 `priority_queue` 來實做 heap。
在 Relax 時，會希望用 `Decrease`，而非 insert 一個新的 vertex 進去之因，是為了不希望 `priority_queue` 中有太多元素(別忘了 `ExtractMin`，也就是 `pop()` 需要成本)，但實做上因為不能透由 iterator 去 traverse 看 queue 中 是否已經存在該 vertex ，還是得把可能已經在 `queue` 中的 vertex 給放進去...

但如果不管該 vertex 是否已 `pop` 出來，還繼續去檢查它的每個邊，看是否可以 `Relax` (透由上方的討論，我們知道這不可能)的話，就是浪費了。如此 `queue` 中至多 <span>$\mathcal{\Theta}(E)$</span> 個元素，每 `pop` 出一個 vertex ，至多要做 <span>$\mathcal{\Theta}(V)$</span> 次檢查是否可以 `Relax` 的判斷 (最差就完全圖的情況)，所以複雜度 total 為 <span>$\mathcal{\Theta}(E \log E + EV )$</span>。但如果我們注意到這件事，不再去對已經 `pop` 過的邊做 `Relax` 與否的檢查，那麼複雜度則會降為 <span>$\mathcal{\Theta}(E \log E + V^2)$</span> ，而這也是下方 code 中， `b_set` 的功能。 ( <span>$\mathcal{\rightarrow}$</span> [練習](https://kth.kattis.com/problems/shortestpath1)，如果你 running time 超過 0.3 秒的話，大概就是寫成 <span>$\mathcal{O}(EV)$</span> 了😅)。 

<script src="https://gist.github.com/sunprinceS/dea6b01e77cb3fe1f40c6779569d592e.js"></script>

## Reference
* [Visualization] (https://www.cs.usfca.edu/~galles/visualization/Dijkstra.html)
