+++
date = "2017-10-05T00:20:42+08:00"
description = "Perfect Hashing"
draft = false
tags = ["Algorithm","Data Structure","Randomized Algorithm","NTU"]
title = "Advanced Algorithm - Hash Table - Lec2"
topics = ["Advanced Algorithm"]

+++

Hashing 的一個 criteria 是希望儘可能不要發生 collision (也就是 Worst Case 也在 <span>$\mathcal{O}(1)$</span> lookup)，今天假設我們已知元素的個數，該如何設計一個 hash function 使得不會有任何 collition 發生？


<!--more-->

### *Def.*
<center>A **Perfect Hash Function** is a hash function w/o collision</center>

想法的共通性是利用空間換取時間。

## Naive method - <span>$\mathcal{O}(N^2)$ space method 
**(w/ <span>$\mathcal{O}(1)$</span>time)</span>**

Let the number of slots be <span>$N^2$</span>, then

任意從 2-Universal Hash Family <span>$\mathcal{H}$</span> 中抽一個 <span>$h$</span> 出來
``$$
\Pr_{h \in \mathcal{H}}[\,\text{collision happened}\,] = \binom{N}{2} \cdot \frac{1}{N^2} \leq
\frac{1}{2}
$$``

抽取次數的期望值為 <span>$2$</span> (成功率的倒數)

## <span>$\mathcal{O}(N)$ space method 
**(w/ <span>$\mathcal{O}(N)$</span> time)</span>**

1. 從 <span>$\mathcal{H}$</span> 中抽出 <span>$h$</span> 將 <span>$N$</span> 個
   element hash 到 <span>$N$</span> 個 slot 當中 (Note: 這裡會發生 collision) \\
Denote <span>$m_i$</span> be the number of elements with hash value <span>$= i$</span>

2. Redo Step1. until <span>$\sum m_i^2 \leq 4 N$</span>

3. For each slot <span>$i$</span> with number of elements <span>$m_i$</span>\\
Pick a hash function from 2-Universal Hash family <span>$\mathcal{H}_i: \, \text{slot} \, i \rightarrow \lbrace 0, 1, \cdots, m_i^2 - 1\rbrace$</span>\\
Repeat until no collision happened\\
Note: Need to do for every slots (from <span>$0 \sim N-1$</span>)

所以最後每個元素 <span>$x$</span>的鍵值會是一個 pair (<span>$h(x),h_{h(x)} \normalsize(x)$</span>) ，所需總 slot 數 <span>$\leq 4 N$</span>

### Running Time 

先來看 Step3.，跟上面 Naive method 的方法一樣，每個 slot 抽取 no collision hash function 的次數期望值 <span>$\leq 2$</span>

再來看 Step1. ， Number of total collision is 

``$$
\sum_{i=1}^N \frac{(m_i)(m_i - 1)}{2} = \frac{1}{2}\sum_{i=1}^N m_i^2 - \frac{N}{2}
$$``

又已知

``$$
\mathbb{E}[\,\text{# of collisions}\,] \leq \binom{N}{2} \cdot \frac{1}{N} = \frac{N-1}{2}
$$``

綜合兩式，可得

``$$
\mathbb{E}[\, \sum_{i=1}^N m_i^2 \,] \leq 2 N
$$``

根據 [Markov Ineq.](https://sunprinces.github.io/learning/2017/08/advanced-algorithm---weighted-set-cover/#markov_ineq)

``$$
\Pr[\, \sum_{i=1}^N m_i^2 \, > 4 N] \leq \frac{1}{2}
$$``

<span>$\Rightarrow$</span> 抽取次數的期望值 <span>$\leq 2$</span>
