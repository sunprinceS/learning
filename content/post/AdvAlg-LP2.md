+++
date =  "2017-08-14T20:49:15+02:00"
description = "An example of deterministic rounding from LP"
draft = false
tags = ["Algorithm","Linear Programming","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Vertex Cover Problem"
topics = ["Advanced Algorithm"]
+++

這一講會展示將問題轉化為線性規劃的 form (但可能是 ILP)，利用 LP solver 得到解，做 **LP relaxation** 並證明這個解不會太差 (approx. ratio 不太大)。

<!--more-->

## Vertex Cover Problem

Given a graph <span>$G$</span>, a vertex cover is a set of vertices <span>$S$</span>\\
s.t. <span>$\forall (u,v) \in E, \; \text{either} \, u \in S \, \text{or} \, v \in S$</span>

Want: Find the vertex cover <span>$S$</span> with <span>$\min$</span> # of nodes

## Formulation

For each <span>$v_i \in V$</span>, assign a variable <span>$x_i = $</span>𝟙[<span>$v_i$</span> is chosen]

<div>
\[
\min \sum_i x_i \quad s.t. \begin{cases}
 x_i  + x_j \geq 1 \;& , \forall (v_i,v_j) \in E\\
0  \leq x_i \leq 1 \; & , \textcolor{red}{x_i \in \mathbb{Z}}
\end{cases}
\]
</div>

<span>$x_i \in \mathbb{Z}$</span> 這個條件，讓問題是困難的 ILP (which is NPC) ，在此我們把拿掉 (**LP relaxation**)，轉成 LP 。

## 2-Approx Algorithm
Run 過 LP solver 後，可得到一組滿足限制式的解 <span>$\mathbf{x}^{\star}$</span>
\\
但這並不是原問題的答案，我們需要利用它找出滿足條件的 <span>$\mathbf{x}^{\prime}$</span>，
有個簡單的演算法

<div>
\[
\text{choose all}\, v_i \, \text{subject to} \; x_i^{\star} \geq 0.5
\]
</div>

### Correctness

<span>$\because x_i + x_j \geq 1$</span>，<span>$x_i, x_j$</span> 中至少有一個
<span>$\geq \frac{1}{2}$</span>，所以每條邊至少會有一個端點被選

### Approx. Ratio

<div>
\[
\text{cost}(x_i^{\prime})\leq \textcolor{red}{2} \sum x_i^{\star} \leq 2 \mathbf{OPT}
\]
</div>

Worst Case 就是所有 <span>$x_i = \frac{1}{2}$</span>，那麼便會每條邊的兩個端點都會被選起來。

## 推廣
可以視為 Set Cover Problem with every element is covered by <span>$\leq l$</span> 個 subset 的特例 (<span>$l = 2$</span>)，\\
由此也可推知Set Cover Problem 的 <span>$l$</span>-approx algorithm。
