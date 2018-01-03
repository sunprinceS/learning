+++
date = "2017-12-30T19:02:37+01:00"
description = "Training vs Testing"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec5"
topics = ["Machine Learning Foundation"]
+++

上一講提到了在 hypothesis set <span>$\mathcal{H}$</span> 為有限，且sample size <span>$|\mathcal{D}|$</span> 足夠大的情況下，是 PAC-learnable 的。但當 <span>$|\mathcal{H}| \, \rightarrow \, \infty$</span>的時候呢？ (e.g 2D linear perceptron)

<!--more-->

## Recap of Feasibility of Learning

1. <span>$E_o(g) \approx E_i(g)$</span> ?
2. <span>$E_i(g) \rightarrow 0$</span> ?

Hoeffding Ineq. 對於再複雜的 ground truth function <span>$f$</span> 及 hypothesis function <span>$h$</span> 均是成立的，1. 據此必是正確，但這僅能 verify 我們得到的結果，還是沒有告訴我們在複雜的 <span>$f$</span> (usually 不能只依靠一個 hypothesis candidate <span>$h$</span>，而是必須從 size 無限大的 hypothesis set <span>$\mathcal{H}$</span> 去挑選)下，學習是否可行？

## Some observation: Does the Union Bound too loose?

From 前一講最後，

<div>
\[

\Pr_D[\text{BAD} \, \text{for} \, \textcolor{orange}{\mathcal{H}}] \leq 2 \, M \, \exp(-2 \epsilon^2 N)
\]
</div>

其中，<span>$M$</span> 是來自 Union Bound 將各個 <span>$\underset{D}{\Pr}\,[\text{BAD} \, \text{for} \, \textcolor{orange}{h_n}]$</span> 做加總，等號成立於事件彼此互斥。

事件彼此互斥？也就是說會讓 <span>$h_n$</span> 壞掉的 <span>$\mathcal{D}$</span> 各自不同，但想想這其實不合理。就拿 perceptron 來說好了，若 <span>$h_1 \approx h_2$</span> ，幾何上來說，這兩個 hyperplane 其實是非常**靠近**的，對大部分的數據點，均會給出一樣的 perdiction ，所以會讓 <span>$h_1$</span> 壞掉的 <span>$\mathcal{D}$</span> ，也 **probably** 會讓 <span>$h_2$</span> 壞掉。

## Effective number of Hypotheses

首先，我們先將 <span>$h$</span> 們做適當的分群，從數據點的角度來看，會 predict 出相同結果的 hypothesis set 視為**相同**的一群 (詳細範例可以看講義👀)，對 binary classification 來說，每一群又稱為一個二元分類器 (*dichotomy*)。\\
給定數據點 <span>$\mathbf{x}_1 \sim \mathbf{x}_N$</span>，我們可以定義不同的 dichotomy ， <span>$\mathcal{H}(\mathbf{x}_1,\mathbf{x}_2,\cdots,\mathbf{x}_N)$</span> 為這些 dichotomies 的集合(跟原先的 <span>$\mathcal{H}$</span> 有何不同呢？可以想成若 <span>$h_i, h_j$</span> 在 training data 上結果相同，則只會對應到當中的 **一個** dichotomy)。而我們想以這個集合的大小 (也就是分群數)，去取代原先的 <span>$M$</span> 。

### Growth Function <span>$m_H$</span>

可以發現，隨著 sample 的 <span>$\mathcal{D}$</span> 不同，分群的結果也會不同，
in order to generalization ，我們定義 growth function <span>$m_H$</span> 為當中
的 upper bound ，也是我們之後要拿來取代 <span>$M$</span> 的量。

``$$
m_H(N) = \underset{\mathbf{x}_1,\mathbf{x}_2,\cdots,\mathbf{x}_N \in \mathcal{X}}{\max} |\mathcal{H}(\mathbf{x}_1,\mathbf{x}_2,\cdots,\mathbf{x}_N)|
$$``

其中 <span>$m_H(N) \leq 2^N$</span> (which is easy to see)

## <span>$m_H$</span> is POLY?

回到最上方的式子，隨著 sample size <span>$N$</span> 的增長，其壓低壞事發生機率的能力是 **EXP** ，所以我們只要能證明 <span>$m_H$</span> 為 **POLY** ，便可以 claim 說，在 <span>$N$</span> 足夠大的情況下， PAC-learnable (even <span>$\mathcal{H}$</span> is infinite)!

可以在講義看到幾個簡單的例子(👀 1D perceptron, 1D interval, 2D contour)，並求出其 growth function 的 analytical form，但很多時候要寫下這個 form 並不那麼簡單，然而，其實我們關心的只是它的 order，或者說他是否為 **POLY** 罷了。為了方便討論，以下做幾個定義。

### Shatter

<div>
\[
\text{If} \: \mathcal{H} \: \text{can \textbf{shatter} N points} \Leftrightarrow m_H(N) = 2^N
\]
</div>

解釋是說，所擁有的 dichotomies ，可以應付training data中所有可能的分群情況。\\
(<span>$\exists \, h $</span>  s.t <span>$h(\mathbf{x}_i) = f(\mathbf{x}_i) \; \forall i=1 \sim N$</span>)，直覺來想，這代表了這個 Hypothesis set 很強，但同時也表示了需要搜索的數量很多，呈現在 growth function 為 **EXP** 。


### Break Point

然而，很多時候我們對資料有些洞見，知道他滿足某種性質，不一定要找那種可以應付所有情況的 Hypothesis set，拿 Linear perceptron 所處理的對象為例，我們假設資料是線性可分，那麼便不需要用到 convex set 這麼強的 Hypothesis set 了 (所謂殺雞焉用牛刀)

<div>
\[
\text{If no data set of size } \, k \, \text{can be \textbf{shattered} by} \, \mathcal{H}, \;  k \, \text{is the \textbf{break point} for } \, \mathcal{H}
\]
</div>


簡單來說就是， <span>$\exists \; k \in \mathbb{N} \, s.t. \, m_H(k) < 2^{k}$</span> ,which is **POLY**\\
(當 <span>$N \geq k$</span> 時，開始出現 <span>$\mathcal{H}$</span> 無法處理，或者說不考慮的情況)

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/05_handout.pdf)
