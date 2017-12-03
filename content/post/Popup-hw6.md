+++
date =  "2017-10-31T08:32:38+01:00"
description = "Graphs II"
tags = ["Programming","Graph Theory","Kattis","KTH"]
title =  "Problem Solving and Programming- hw6"
topics = ["Online-Judge"]
+++

<a href="https://kth.kattis.com/problemgroups/632" target="_blank">題目連結</a>

<!--more-->

## pA - GopherII

今天有 <span>$n$</span> 隻老鼠和 <span>$m$</span> 個洞，當老鷹來的時候，老鼠們要躲到洞裡才不會被吃掉，但有以下限制。

* 一個洞只能容納一隻老鼠
* 老鼠只能跑到一定範圍以內的洞，太遠跑不到

想問說最後有幾隻老鼠會被吃掉。\\
這題頗水，根據題目給定的座標及限制，建一個二分圖去對老鼠和洞們做匹配，算幾隻老鼠沒被配到。

<span>$|E| = nm$</span> , <span>$|V| = n+m$</span>，用最大流求二分匹配的話，複雜度
<span>$O(VE(V+E))$</span> \\
(不過題目沒給輸入範圍，應該就是怎樣都會過...lol)

## pB - XYZZY
今天有 <span>$n$</span> 個房間，每個房間蘊含著可正可負的能量(?)，一個冒險者想從房間 <span>$0$</span> 走到房間  <span>$n-1$</span> 。他一開始有能量 <span>$100$</span> 點，每經過一個房間便會獲得該房間的能量，而當他走到最終房間或是能量用完時，遊戲結束，想問說他有沒有可能走到最終房間。這題目包含了一些假設讓我們比較好解。

* 在房間之間移動不耗費能量
* 同一個房間可以走好幾次(可以一直拿能量)
* 只在乎到不到的了，不在乎步數最少
* 最後一個房間蘊含能量 <span>$0$</span> (所以你到它時，能量一定要
  <span>$\mathbf{> 0}$</span>，等號不成立)

我們先把題目轉成有向圖，每個節點 <span>$v$</span> 代表一個房間，其出邊上的
weight 為該房間蘊含的能量。今天我們想去 maxmize 到每一點還保有的能量，然後看
`energy[n-1]` 是否大於 <span>$0$</span> ？

是一個求單源最短路的變形，而因為有負邊，所以只能用 [Bellman-Ford](http://localhost:1313/learning/2017/08/algorithm---graph-theory---lec-2/) 來做。
而有些名詞要做些修正。

* `NEG_CYCLE` <span>$\rightarrow$</span> `POS_CYCLE` (可以在正能量迴圈中繞，能量源源不絕)
* `UNREACHABLE` <span>$\rightarrow$</span> energy <span>$= 0$</span>

`Relax` 也要改一下，<span>$\forall e=(u,v)$</span>

```python
if(energy[u] != 0 && energy[u] + e.w > energy[v])
  relax energy[v] = energy[u] + e.w
```

先用 `bfs` 從終點拓展，看哪些房間到的了它，在 Bellman-Ford 跑第二個迴圈偵測正環時，<span>$\forall e=(u,v)$</span>

```python
if exist POS_CYCLE at u:
  if 終點 is reached by u:
    return True
```
如果上述判式在迴圈都沒成立，則 check `energy[n-1]` 是否 <span>$> 0$</span>

## pC - Risk

給定一張圖，裡頭的點有些是你的，有些是敵人的，而在每個點上有一些士兵。\\
給定圖中這些點與點相鄰與否的關係，及起始圖上每個點你的士兵有多少，在每個士兵至多移動一次的情況下(留在原地或移動到鄰近且屬於你的點)，想 maximize 與敵軍相鄰最弱點的士兵數，且每個點至少要留守一個士兵。\\
Note:同一點的士兵們，不用一起移動，可分別決定要到哪個鄰近點或留守(有點廢話，但一開始卡了一下)

這題我是用最大流的想法來解，假設最弱點士兵數為 <span>$k$</span>，建一張屬於你的點的二分圖，如果相鄰的話，該邊的 capacity 就是原先在該點的士兵數(當然自己也要連到自己，因為可以留在原地)。最後將這些點連到 sink ，而當中邊的權重，如果該點不與敵軍相鄰，capacity 為 1，如果與敵軍相鄰則是 <span>$k$</span>，然後檢查是否滿流(滿流代表走一次的情況下，所有與敵軍相鄰點的士兵數均 <span>$\geq k$</span>)，然後用二分搜去找出最大的 <span>$k$</span>。複雜度為 <span>$\mathcal{O}(\log ( \text{army})n n^2(n + n^2)) = \mathcal{O}(\log ( \text{army}) n^5)$</span>

這題也很棒！如果推廣可以移動 <span>$m$</span> 步的話，就在加 <span>$m$</span> 個
layer，如果有相鄰的話，第二層之後，最後一層連到 sink 之前，就設權重為 <span>$\sum_{army}$</span> 

## pD - Full Tank
開車旅行，當中有 <span>$n$</span> 個城市，城市間有道路相連，每個城市有加油站，且單位油價各不相同，給定起點及終點，想找一條**花費油錢最小的行程**。

感覺就是個求單源最短路徑的問題，但 criteria 略有不同 (但油錢多少跟距離也正相關)，因為邊權非負，不妨嘗試用 [Dijkstra](https://sunprinces.github.io/learning/2017/09/algorithm---graph-theory---lec-3/) 的想法解解看。但我們現在需要多考慮現存油量 <span>$f$</span> 這個變數。

``$$
cost[i][f]: \quad \text{min. cost when arriving } \, i\text{-th city with fuel}
\, f
$$``

```cpp
struct State{
  int idx,fuel,cost;
  State(int idx,int fuel,int cost):idx(idx),fuel(fuel),cost(cost){}
  bool operator<(const State& rhs) const {return cost > rhs.cost;}
};
```
而 state 的轉移(或說`Relax`)有兩個選擇：

* 在原地加 <span>$1$</span> 單位的油
* 花費 <span>$d_e$</span> 單位的油，移動到別的城市

跟 Dijkstra 一樣 maintain 一個 priority queue，每次 pop out 出一個 state 做
`Relax` ，直到第一次 pop out 出的城市為終點，或queue 變為 empty (卻還沒成功，代表起點終點不連通)
而 Relax 的 condition 就是上述兩個狀態轉移的方式。

* 加一單位的油

```cpp
if(cost[i][f+1] > cost[i][f] + prices[i]) 
  relax(cost[i][f+1])
```

* 移動到鄰近城市
<span>$\forall j \in N(i)$</span>

```cpp
if(cost[j][f-e.weight] > cost[i][f])
  relax(cost[j][f-w.weight])
```

關於複雜度，state 總數為 <span>$nC$</span> ( <span>$C$</span> 為油箱的 capacity)，如果用
binary heap 的話，

* `Extract-Min`: <span>$\mathcal{O}(\log (nC))$</span> ，需要至多 <span>$nC$</span> 次
* `INS`(state不會重複，cost 會嚴格遞增，不是用 `Decrease`): <span>$\mathcal{O}(\log (nC))$</span>，需要至多 <span>$2mC$</span> 次
