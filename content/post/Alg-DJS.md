+++
date =  "2017-08-22T20:44:58+01:00"
description = "Implementation & Analysis"
draft = false
tags = ["Algorithm","Data Structure","NTU"]
title =  "Algorithm - Disjoint Set"
topics = ["Algorithm"]
+++

Disjoint Set 顧名思義，代表了一群兩兩交集為空的集合們，常用於處理依據某種關係將元素們「分類」的問題。我們的目標是去 maintain collection of disjoint sets <span>$S_1,S_2,\cdots,S_k$</span>，而每個 <span>$S_i$</span> (每個 category) 以一個代表值 (*representative*) 去紀錄。
<!--這個系列是末學大三時候修 Holin 演算法時所整理的筆記，在 KTH DD2458 的課程中，用到了不少學過的東西，也算彌補了當時不足了實做部份，順道以此為複習。-->

<!--more-->

## 支援的 Operations
1. `Make-Set(x)`: create a new set {<span>$x$</span>}
2. `Find(x)`: Find the leader of <span>$x$</span>'s set
3. `Union(x,y)`: Union <span>$x$</span>'s set and <span>$y$</span>'s set


## Linked List Implementation

<img src="/img/post/djs-linked-list.jpg" width="800px">

* `Make-Set(x)`: <span>$\mathcal{O}(1)$</span> (create `HEAD` and representative，也就是自己)
* `Find(x)`: <span>$\mathcal{O}(1)$</span> (back to `HEAD`，go to representative)
* `Union(x,y)`: <span>$\mathcal{O}(n)$</span> \\
可以發現 Union 非常慢，要依序將 <span>$S_2$</span> 中的元素依序指到 <span>$S_1$</span> 的 `HEAD` ，同時將 <span>$S_1$</span> 的最後一個元素之 `ptr` 指到原先 <span>$S_2$</span> 中的第一個。(當然可以在 `HEAD` 中多存一個指向最後一個元素的 `ptr` ，但複雜度還是 <span>$\mathcal{O}(m)$</span>, where <span>$m = |S_2|$</span>)

雖說單看 `Union` 的複雜度似乎不太妙，但考慮一系列操作( **Amortized Analysis** ，也是一種常見的分析複雜度方式)，同時利用上方提到的 trick ，在做 `Union` 時，都是去合併**較小**的那個集合，再來做分析。

### Lemma
Starting from 0 sets

<div>

\[
\text{operations} \left\{ \begin{array}{ll}
           n \quad \text{make-set} (\, \therefore \, n \; \text{elements})\\
           m \quad \text{find}\\
           \leq n-1 \quad \text{union}
        \end{array} \right.
\]

</div>

<span>$\Rightarrow \, \text{total cost}: \mathcal{O}(m+n \log n)$</span>\\
其中 <span>$n\log n$</span> 顯然是來自 `Union` 的 cost，而細看操作會發現，這亦為 <span>$\mathcal{\Theta}(\text{total number of times that element's top ptr changes})$</span>，\\
所以今天我們想證明，改動 `ptr` 的次數為 <span>$\mathcal{O}(n \log n)$</span>

**Claim**: If an element has changes `ptr` <span>$k$</span> times, then it belongs to a set of
size <span>$\geq 2^{k}$</span>

用數學歸納法可以簡單證明，假設 <span>$k=t$</span> 時成立，那在 <span>$k=t+1$</span> 時，如果該 set 還需改動 `ptr` ，表示其跟 size 比他大的 set 做 `Union` ，之後 size <span>$\geq 2^{t+1}$</span> ， Claim 依然成立 (<span>$k=0$</span> 時，只有 make-set， size 為 <span>$1$</span> 亦成立)

而利用該 Claim ，又已知 size of any set <span>$\leq n$</span>，所以任何元素都只能改動 `ptr` <span>$\leq \log n$</span> 次，所以 total `ptr` 改動次數 <span>$\leq
n \log n \quad \square$</span>

## Forest Implementation

<img src="/img/post/djs-forest.jpg" width="300px">

* `Make-Set(x)`: <span>$\mathcal{O}(1)$</span> (建一顆只有一個節點的樹，root指向自己)
* `Find(x)`: <span>$\mathcal{O}(\text{height})$</span> (trace back to root)
* `Union(x,y)`: <span>$\mathcal{O}(\text{height})$</span> (單純改變被合併集 root 的指向為 <span>$\mathcal{O}(1)$</span> ，但要先用 `Find` 找到 root)

稍微觀察一下發現，如果今天這個樹長的很傾斜(最爛的情況就是 skewed tree ，直接退化成 list)，那麼 height 為 <span>$\mathcal{O}(n)$</span> ，複雜度沒有任何改進。但若仿效前面 linked list 的想法，在做 `Union` 時，去合併比較小的 set ，則可以避免這個問題 (skewed tree會發生，就是每次都是拿一個 size 非 <span>$1$</span> 的 set 去接在 size 為 <span>$1$</span> 的 set 後面...)。

### Theorem: The height of every tree <span>$\leq \log n$</span>

**Claim:** every tree with height <span>$k$</span> has at least <span>$2^{k-1}$</span> elements

* <span>$k=1$</span>, trivial
* 假設 <span>$k=t$</span> 成立，考慮兩個高度同為 $t$ 的樹(如果不一樣的話，那合併後的樹高度為原先兩者中較高的那個，但元素個數又增加了，Claim 必成立)，各自至少有 <span>$2^{t-1}$</span> 個元素，合併後高度變為 <span>$t+1$</span> ，元素個數 <span>$\geq 2^{t-1} \times 2 = 2^{t}$</span>， Claim 依然成立

所以當已知每顆樹至多 <span>$n$</span> 個元素的情況下，我們可以證明其高度必
<span>$\leq \log n \quad \square$</span>

### Implementation

<script src="https://gist.github.com/sunprinceS/ba6415fdef340c1b97377d286a0ff402.js"></script>

## Reference
* [台大資工 - DSA](https://www.csie.ntu.edu.tw/~hsinmu/courses/_media/dsa_12spring/disjoint_set.pdf)
