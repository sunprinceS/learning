+++
date = "2017-10-14T00:24:36+08:00"
description = "Power of 2-choices"
draft = false
tags = ["Algorithm","Data Structure","Randomized Algorithm","NTU"]
title = "Advanced Algorithm - Hash Table - Lec3"
topics = ["Advanced Algorithm"]

+++

在 [第一講](https://sunprinces.github.io/learning/2017/05/advanced-algorithm---hash-table---lec1/)中，我們證明了有很高的機率，任意 slot 中 element 的個數均為 <span>$\mathcal{\Theta}(\frac{\ln n}{\ln (\ln n)})$</span> ，而這一講想討論的是，如果今天我們手上有兩個 hash function (from same <span>$\mathcal{H}$</span>)可以選，每次我們就看哪一個 <span>$h$</span> 回傳的 slot 中元素比較少，就將 element hash 到 slot，那麼現在 <span>$\mathbb{E}[\, \text{max num of elements in any slots} \, ]$</span> ?

<!--more-->

## Chernoff Bound

<span>$n$</span> 個 identical R.V. 的相加，想 measure 其偏離平均值的機率

Let <span>$X_1, X_2, \cdots, X_n$</span> be independent binary R.V with <span>$\mathbb{E}[X_i] = p_i$</span>

<span>$X = \sum_i X_i$</span>, <span>$\; \mu = \mathbb{E}[X] = \sum_i p_i$</span>
<!--<span>$\; p = \frac{\mu}{n} = \text{avg}(p_i)$</span>-->

<div>
\[
\boxed{\Pr[X \geq e \mu] \leq e^{- \mu}}
\]
</div>

## Power of 2 choices

Throw <span>$n$</span> balls into <span>$n$</span> bins, \\
For every ball, we pick 2 bins uniformly at random, throw the ball into the bin with less load

Let <span>$B_k = | \;\lbrace i \, | \,b_i \geq k \rbrace\;|$</span> (有 <span>$\geq k$</span> 顆球的 bin 數)\\
<!--<span>$\beta_k$</span> 為 <span>$B_k$</span> 的 upper bound (with <span>$\Pr[\underset{\text{event} \, \mathcal{E}_k}{\underbrace{B_k > \beta_k}}] \leq \frac{1}{n^2}$</span>)，\\-->
<span>$\beta_k$</span> 為 <span>$B_k$</span> 的 upper bound ; <span>$ \quad \mathcal{E}_k$</span> 為 <span>$\beta_k > B_k$</span> (上限符合要求) 的 event
\\
假定我們已經有了 <span>$B_k \leq \beta_k$</span> 這個條件 (<span>$\mathcal{E}_k$</span> 成立)，該如何 inductively 去找 <span>$\beta \scriptstyle k+1$</span> ?


1. For any balls <span>$j$</span>, we say <span>$j$</span> is <span>$\color{red}{red}$</span> if the number of balls below it <span>$\leq k$</span>

2. <span>$\Pr[ \, j \, \text{is red} \,] \leq (\frac{\beta_k}{n})^2$</span> (兩個 bin 球數都要 <span>$\geq k$</span>)

3. <span>$\mathbb{E}[B\scriptstyle k+1 \normalsize \, | \mathcal{E}_k] \leq \mathbb{E}[\text{num of red balls} \, | \mathcal{E}_k] \leq n \cdot (\frac{\beta_k}{n})^2$</span> \\
(第一個等號因為有紅色球的 bin 一定有 <span>$\geq$</span> <span>$k+1$</span> 個球，後面那個等號是因為 Union Bound)

4. Using <span>$\color{blue}{\text{Chernoff Bound}}$</span>， <span>$\Pr[B \scriptstyle k+1 \normalsize \geq e \frac{\beta_k^2}{n} | \mathcal{E}_k] \leq \exp(-\frac{\beta_k^2}{n})$</span> \\
\\
(如果 <span>$\exp(-\frac{\beta_k^2}{n}) \leq \frac{1}{n^2}$</span>) ， 則我們可以選 <span>$\beta \scriptstyle k+1  \normalsize = e \, \frac{\beta_k^2}{n}$</span>\\
整理一下此遞迴式，我們有了以下關係式

``$$
\boxed{\frac{\beta_{k+m}}{n} = \frac{1}{e} \, (\frac{e \beta_k}{n})^{2^m}}
$$``
 接下來要選定初始值 <span>$k$</span>，就可以確定 <span>$\beta$</span> 的值了，\\
 注意到 <span>$\beta_6 \leq \lfloor \frac{n}{6} \rfloor \leq \frac{n}{2e}$</span> (顯然會超過 6 顆球
 的bin數 一定會小於均分的 case)\\
 代回上式，可得

``$$
\frac{\beta_{6+m}}{n} \leq \frac{1}{e} \, \frac{1}{2^{2^m}}
$$``

當 <span>$m = \mathcal{O}(\ln \ln n)$</span>， we can find that <span>$\beta \scriptstyle 6+m \normalsize < 1$</span> \\
(亦即超過 <span>$\mathcal{O}(1) + \ln \ln n $</span> 顆球的 bin 出現的機率極小)

這裡只的機率很小是指 <span>$\Pr[\neg \, \mathcal{E}_m]$</span> 很小，根據上方的遞迴式，我們可以給出一個明確的上界

### Fact

``$$
\boxed{\Pr[ \neg \, \mathcal{E}_{k+1}] \leq \Pr[\neg \, \mathcal{E}_{k+1} \,| \, \mathcal{E}_k] \cdot \Pr[\mathcal{E}_k] + \Pr[\neg \, \mathcal{E}_k]}
$$``

**proof.**

``$$
\begin{align}
\text{RHS} \, &= \, \Pr[\neg \, \mathcal{E}_{k+1} \,| \, \mathcal{E}_k] \cdot \Pr[\mathcal{E}_k] + \Pr[\neg \, \mathcal{E}_k] \\
& = \Pr[\neg \, \mathcal{E}_{k+1} \, \land \, \mathcal{E}_k] + \Pr[\neg \, \mathcal{E}_k]\\
& = \underbrace{\Pr[\neg \, \mathcal{E}_{k+1} \, \land \, \mathcal{E}_k] + \Pr[\neg \, \mathcal{E}_k \, \land \, \neg \, \mathcal{E}_{k+1}]}_{\Pr[\neg \, \mathcal{E}_{k+1}]} + \Pr[\neg \, \mathcal{E}_k \, \land \, \mathcal{E}_{k+1}] \\
& \geq \text{LHS}
\end{align}
$$``

<span>$\Rightarrow \Pr[\neg \, \mathcal{E} \scriptstyle k \normalsize] \leq \frac{1}{n} \quad$</span> (利用 <span>$\Pr[\neg \, \mathcal{E}_6 ] = 0$</span>，遞迴求出)



## More Discussion

### <span>$l$</span>-choice

第 2 步變為 <span>$\Pr[ \, j \, \text{is red} \,] \leq (\frac{\beta_k}{n})^l$</span> (<span>$l$</span> 個 bin 球數都要 <span>$\geq k$</span>)

``$$
\boxed{\frac{\beta_{k+m}}{n} = \frac{1}{e} \, (\frac{e \beta_k}{n})^{l^m}}
$$``

當 <span>$m = \mathcal{O}(\frac{\ln \ln n}{\ln l})$</span>， we can find that <span>$\beta \scriptstyle 6+m \normalsize < 1$</span> ，但其實還是在同個 order 

### What if <span>$\beta_k$</span> is so small

That is, 第 4 步的 bound 並不足夠小，也就是隨著 <span>$\beta_k$</span> 的 decay， <span>$\exp(-\frac{\beta_k^2}{n})$</span> 終會 <span>$ > \frac{1}{n^2}$</span> <span>$\Rightarrow \beta_k < \sqrt{2n \ln n}$</span> \\
Pick 使得此式成立的 min. <span>$k^\star$</span>， <span>$k^\star = \frac{\ln \ln n}{\ln 2} + \mathcal{O}(1)$</span>

可以證明有 bin 超過 <span>$k^\star + 2$</span> 的機率足夠小 (但證明我沒看懂QQ)
