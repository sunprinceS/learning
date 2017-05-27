+++
date = "2017-05-27T17:06:53+08:00"
description = "Algorithmic Mechanism Design"
draft = false
tags = ["Game Theory","Algorithm","Open Course","Stanford"]
title = "Algorithmic Game Theory - Lecture4"
topics = ["Algorithmic Game Theory"]

+++

上一講利用[Myerson's Lemma](https://sunprinces.github.io/learning/2017/05/algorithmic-game-theory---lecture3/)闡述了給定在「好」的 allocation rule 下，該如何定價，使得 auction 具有 DSIC property; 而這講則要說明該如何 derive 出「好」的 allocation rule。

<!--more-->
<!--<u>**Corollary**</u>: Here is an awesome auction for sponsored search-->

### Knapsack Auction

上一講定義了 single-parameter environment ，其中的 allocation set <span>$X$</span> 需滿足元素和 <span>$ \leq k$</span> (in single-item, <span>$k=1$</span>)。這裡再將此推廣成更一般的形式。

* bidder i has **public** size <span>$w_i$</span> and **private** valuation <span>$v_i$</span>
* The allocation set <span>$X$</span> contains 0-1 vectors subject to <span>$\sum_{i=1}^n w_i \, x_i \leq W$</span>

我們想要找的 allocation 需能最大化 social surplus

<div>
\[
  \vec{x}(\vec{b}) = \arg \max_{\vec{x}} \sum_{i=1}^{n} b_i \, x_i
\]
</div>

若想說明其具備 DSIC property，我們可以先**證明該 allocation rule 為 monotone** ，再利用 Myerson's Lemma 得出其 payment rule

**[Theorem]** 

<div>
\[
 \text{If} \: \vec{x}(\cdotp,\vec{b}_{-i}) \: \text{is NOT monotome in } [0,b_{max}] \Rightarrow \exists \: \vec{x^{\prime}} \quad \text{s.t} \: \sum_{i=1}^{n} \: b_i \, x^{\prime}_i \: > \: \sum_{i=1}^{n} \: b_i \, x_i
\]
</div>

<span>$\underline{~Proof.}$</span>

<div>
\[
\begin{aligned}
& \text{For bidder} \, i, \text{we construct an example that} \, \beta_1 < \beta_2 = b_i \, \text{, but} \, \vec{x}(\beta_1) > \vec{x}(\beta_2) \\
& \text{we pick} \, \vec{x^{\prime}}(\beta_2) = \vec{x}(\beta_1) \, \text{and} \, \vec{x^{\prime}}(\beta_1) = \vec{x}(\beta_2) \\
& \text{It is easy to check that} \, \vec{x^{\prime}} \cdotp \vec{b} > \vec{x} \cdotp \vec{b}
\end{aligned}
\]
</div>

但目前為止看起來非常完美，但真是如此嗎？別忘了我們希望整個 mechanism 能在 **POLY** time 內結束，然而，尋找 max allocation 的問題卻是一個 NPC ...QQ，也就是說，對一般的 k-item auction 而言，不存在多項式時間的 mechanism。類似於在 approximation algorithm 所學的，我們的 objective 如以下。

> <font color="blue">Relax optimal surplus constraint as little as possible, subject to DSIC and POLY-time constraint</font>

利用 Myerson's Lemma，以下敘述也等價

> <font  color="blue"> Design POLY-time alg. and monotone allocation rule as close as possible to maximize the social surplus</font>

至此，在 approximation alg. 中學到的當前 state-of-the-art 的演算法，可用於解決 AGT 的問題，但有件事情需要注意

> The DSIC/monotone constraint that approx. mechanism should satisfy cause no additional surplus loss

**[Theorem]** The greedy allocation rule for Knapsack auction is monotone

