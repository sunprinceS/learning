+++
date = "2017-05-27T17:06:53+08:00"
description = "Algorithmic Mechanism Design"
draft = false
tags = ["Game Theory","Algorithm","Open Course","Stanford"]
title = "Algorithmic Game Theory - Lecture4"
topics = ["Algorithmic Game Theory"]

+++

上一講利用 [Myerson's Lemma](https://sunprinces.github.io/learning/2017/05/algorithmic-game-theory---lecture3/) 闡述了給定在「好」的 allocation rule 下，該如何定價，使得 auction 具有 DSIC property; 而這講則要說明該如何 derive 出「好」的 allocation rule。

<!--more-->
<!--<u>**Corollary**</u>: Here is an awesome auction for sponsored search-->

### Knapsack Auction

上一講定義了 single-parameter environment ，其中的 allocation set <span>$X$</span> 需滿足元素和 <span>$ \leq k$</span> (in single-item, <span>$k=1$</span>)。這裡再將此推廣成更一般的形式。

* bidder <span>$i$</span> has **public** size <span>$w_i$</span> and **private** valuation <span>$v_i$</span>
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
& \text{It is easy to check that} \, \vec{x^{\prime}} \cdotp \vec{b} > \vec{x} \cdotp \vec{b} \quad \square
\end{aligned}
\]
</div>

但目前為止看起來非常完美，但真是如此嗎？別忘了我們希望整個 mechanism 能在 **POLY** time 內結束，然而，尋找 max allocation 的問題卻是一個 NPC ...QQ，也就是說，對一般的 k-item auction 而言，不存在多項式時間的 mechanism。所以類似於在 approximation algorithm 所學的，我們的 objective 如以下。

> <font color="blue">Relax optimal surplus constraint as little as possible, subject to DSIC and POLY-time constraint</font>

利用 Myerson's Lemma，以下敘述也等價

> <font  color="blue"> Design POLY-time alg. and monotone allocation rule as close as possible to maximize the social surplus</font>

至此，在 approximation alg. 中學到的當前 state-of-the-art 的演算法，可用於解決 AGT 的問題 (W/ 還不錯的 ratio )，但有件事情需要注意

> The DSIC/monotone constraint that approx. mechanism should satisfy may cause additional surplus loss

因為我們需要保證 DSIC ，因此對某些 approx. algorithm ，我們可能需要犧牲 approx. ratio 去 maintain 這個性質。 (e.g **black-box reduction**)

以下定理證明用於 Knapsack problem 的 2-approx algorithm 所設計的 allocation rule 不會造成額外的 cost ，同時保持 monotone 的性質。

**[Theorem]** The greedy allocation rule for Knapsack auction is monotone

> <u>2-approx Algorithm</u>: &nbsp; Sort bidders according to <span>$ \frac{b_i}{w_i}$</span> in decending order <br/>
Choose bidders in that order until constranit DOESN'T satisfy,</br> then
pick <span>$max(v_1 + v_2 + \cdots +v_i,$ $v\scriptstyle{i+1}$$)$</span>

> For every bidder <span>$i$</span>, <span>$\exists$</span> critical bid <span> $b^{\star}$</span> (depending on its <span>$w_i$</span> and <span>$b\scriptstyle{-i}$</span>) s.t <span>$x_i$</span> jump from 0 to 1 。

> It is easy to check that such allocation rule for arbitary bidder <span>$i$</span> is monotone 0-1 curve 。<br/>
