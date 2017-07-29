+++
date =  "2017-07-01T22:56:26+08:00"
description = "Rounding Data & Dynamic Programming "
tags = ["Algorithm","Rounding Data & DP","Approximation Algorithm","NTU"]
title = "Advanced Algorithm - Knapsack Problem"
topics = ["Advanced Algorithm"]
+++

背包問題為一個典型的最佳化問題，這裡我們考慮最基本的 0/1 - Knapsack Problem 。簡單的 formulation 如下:

<!--more-->

We have <span>$n$</span> items, each one with value <span>$v_i$</span> and
weight <span>$w_i$</span>. The weight limit of the backpack is <span>$W$</span>.

<div>
\[
\text{Goal: Select set of items } S \, \text{such that} \sum_{S}v_i \, \text{is
maximized subject to } \sum_{S}w_i \leq W
\]
</div>

## 2-Approx Algorithm
若今天物品是可分割的話，有一個簡單的 greedy 方法可以找到最佳解 (依據<span>$\frac{v_i}{w_i}$</span>作排序，選擇單位重量價值最高的物品，塞到背包塞不下為止)。而在 0/1 - Knapsack 中，也可利用此方法找到 2-Approx 的解。

### Algorithm
Sort items according to <span>$ \frac{v_i}{w_i}$</span> in decending order. 
Choose items in that order until constraint DOESN'T satisfy, then
pick <span>$max(v_1 + v_2 + \cdots +v_i,$ $v\scriptstyle{i+1}$$)$</span>

## DP-based Algorithm
概念是給定欲達到的價值(<span>$v$</span>)，從物品的子集(item 1 ~ i)中，找到所需重量最小的物品集合之重量 ( <span>$A[i,v]$</span> )
### Algorithm

Let <span>$A[i,v]$</span>: min. weight chosen from items 1 ~ i such that <span>$\sum \text{value} \geq
v$</span>

**initial condition:** <span>$A[0,v] = (v \leq 0\,)?\,0:\infty $</span>

**recursive relation**: <span>$A[i,v] = \min(\stackrel{\color{blue}{i\,\text{is not picked}}}{A[i-1,v]},\stackrel{\color{blue}{i\,\text{is picked}}}{A[i-1,v-v_i]+w_i})$</span>

Then search the n-th column (along the value axis), <br/>
we can find <span>$v^{\star} \; \text{such that}\; A[n,v^{\star}] \leq W \land
A[n,v^{\star}+1] > W$</span>

The time complexity is <span>$\Theta(n \, v^{\star})$</span>

然而，我們已知 Knapsack Problem 為 NPC 問題，目前並不存在已知的 polynomial-time
的解法，但為何 DP 的複雜度卻不是指數呢？

### Psuedo-Poly Algorithm [Weakly NP-Hard]
> Running time is poly. in the input size under **unary representation** (not
> binary <span>$\uparrow$</span>)

一般而言，我們會以二進位制儲存數字，所以 input size 應為 <span>$\sum \log{v_i} + \sum \log{w_i}$</span>，然而方才的演算法卻會一個一個地掃過 value axis ，直到找到解為止，最壞情況下(<span>$W$</span>相當寬鬆)，複雜度為 <span>$\Theta(n \, \sum{v_i})$</span></br>
<span>$\Rightarrow$</span> Running time 為 input 的指數。\\
(另外，若想用 unary representation 來儲存 instance ，付出的代價即是空間複雜度變為
原先input size 的指數)

### Strong NP-Hard
> NP-Hard even all numerical values are **POLY**(sizeof input) *e.g* Exactly Set
> Cover

## (1+ <span>$\epsilon$</span>)-Approx Algorithm

剛剛 DP 的方法壞就壞在必須線性掃過 value axis, 我們是否能過不要檢查每一個
<span>$v$</span> ，而是**跳著檢查某些 <span>$v$</span> 值**就好了呢？從上述的直覺，發展出 **rounding** 這個 scheme，犧牲一些精確度(<span>$1\,+\, \epsilon$</span>)，將複雜度壓回 polynomial。

### Algorithm
Let <span>$b = \frac{\epsilon}{2n}\max_i v_i$</span>

Now, consider the rounded instance <span style="color: green"><span>$\text{0-1 Knapsack}^{\prime}$</span></span> , <span>$\hat{w_i} = w_i \, , \hat{W} = W \, , \color{blue}{\hat{v_i} = \lceil \frac{v_i}{b} \rceil \cdotp b}$</span>

<span>$\Rightarrow$</span> Running time for solving <span style="color: green"><span>$\text{0-1 Knapsack}^{\prime}$</span></span> is <span>$\Theta(n \cdotp \frac{v^{\star}}{b}) = \Theta(\frac{n^3}{\epsilon})$</span>

<div>
\[
\because \frac{v^{\star}}{b} \leq \frac{\sum v_i}{b} \leq \frac{n \cdotp \max_i v_i}{b} =
\frac{2n^2}{\epsilon}
\]
</div>

#### Correctness Check

value(OPT) in 0-1 Knapsack\\
<span>$\leq$</span> value(OPT) in <span style="color: green"><span>$\text{0-1 Knapsack}^{\prime}$</span> </span> <span>$(\because$ 進位)</span>\\
<span>$\leq \text{value}$</span> (<span style="color: green"><span>$\text{OPT}^{\prime}$</span></span>) in <span style="color: green"><span>$\text{0-1 Knapsack}^{\prime}$</span> </span>\\
<span>$\leq \text{value}$</span> (<span style="color: green"><span>$\text{OPT}^{\prime}$</span></span>) in 0-1 Knapsack + <span>$nb$</span> (直接就加一
個刻度)\\
= value(<span style="color: green"><span>$\text{OPT}^{\prime}$</span></span>) +
<span>$\frac{\epsilon}{2} \stackrel{\color{blue}{\leq \text{value(OPT)}}}{\max_i v_i}$</span>

<span>$\Rightarrow$</span> value(<span style="color: green"><span>$\text{OPT}^{\prime}$</span></span>) <span>$\geq \, (1-\frac{\epsilon}{2})$</span> value(OPT)\\
<span>$\Rightarrow$</span> value(<span style="color: green"><span>$\text{OPT}^{\prime}$</span></span>) <span>$\geq \, (\frac{1}{1 + \epsilon})$</span> value(OPT) ( <span>$\because \color{red}{1-\frac{\epsilon}{2} \geq \frac{1}{1+\epsilon}}$</span> )

### Improvement version

上題的 <span>$b = \frac{\epsilon}{2n}\tilde{V}$</span> 可以想成離散化 <span>$v$</span> 值的刻度大小，我們可否在保持( <span>$ 1 + \epsilon$</span> ) - approx. ratio 的前提下，**跳大格一點**( <span>$b$</span> 取大一點)。從上式的推導可以發現，若想保持 approx. ratio ，<span>$\tilde{V}$</span> 必須小於 <span style="color: blue">value(OPT)</span>，而想降低 running 的 order ， <span>$\tilde{V}$</span> 需要跟 <span>$v^{\star}$</span> **差不多** order 。也就是我們需要**估計**一個比 value(OPT) 小，但不會差太多的 <span>$\tilde{V}$</span> , <span>$\tilde{V} = \Theta(v^{\star})$</span>。 (而且估計的 running time 不能高於之後 derive 出來的複雜度)

#### Greedy Algorithm to estimate <span>$\tilde{V}$</span>

<div>
\[
 \text{Pick} \; \tilde{V} = \max(v_1 + v_2 + \cdots +v_i, v\scriptstyle{i+1})
\]
</div>


### Poly-time Approximation Scheme (*PTAS*)
> Given <span>$\epsilon > 0$</span>, can find (<span>$1 +
> \epsilon$</span>)-approx. algorithm w/ running time is poly. for
> constant <span>$\frac{1}{\epsilon}$</span>


### Fully Poly-time Approximation Scheme (*FPTAS*)
> Given <span>$\epsilon > 0$</span>, can find (<span>$1 +
> \epsilon$</span>)-approx. algorithm w/ running time is poly. for
> constant <span>$\frac{1}{\epsilon}$</span> and <span>$n$</span> \\
> *e.g* 剛剛提到的 Rounded DP-based Algorithm
