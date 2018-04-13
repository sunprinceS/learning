+++
date = "2018-04-12T22:59:24+01:00"
description = "Linear Regression"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec9"
topics = ["Machine Learning Foundation"]
+++

在前幾講的討論中，我們討論了 binary classification 的問題，及這個問題在機器學習上的可行性。但很多時候，我們不希望機器只會說是或不是，亦即不希望它的 output space <span>$\mathcal{Y}$</span> 只是單純的 {<span>$1, -1$</span>}。舉例來說，給定一些資料，請你預測明天的股價，除了想預測會漲或會跌之外，到底漲多少或跌多少也是我們有興趣知道的事情，而這也是接下來兩講想解決的事情 - Regression。

<!--more-->

## Basic

想像我們今天有一把 data <span>$\mathcal{D}$</span>，其中的每一筆為 (<span>$\mathbf{x},y$</span>)的 pair，其中 <span>$x \in \mathbb{R}^d, y \in \mathbb{R}$</span>。之前的 binary classification 做的事情就是在 <span>$\mathcal{R}^d$</span>中找 hyper-plane 去將空間一分為二，而 regression 所做的事也是嘗試去找到這樣的一個 hyper-plane，同時去 minimize <span>$\mathcal{D}$</span> 上的每個點到其的距離 (e.g Euclidean distance)。

## Feasibility of Learning

上面提到的**距離**就是所謂 error。

<center><img src="/img/post/lr-ein.png" width="70%" style="border-radius: 0%;"></center>

可以看出 <span>$E_i$</span> 是 convex function，可以透由找 <span>$\nabla_w E_i = 0$</span>所對應的點找到我們要的 weight。

在 linear regression 中，這樣的 weight <span>$\mathbf{w}_{\text{LIN}}$</span> 是有 closed form 的。

<div>
\[
\mathbf{w} = (X^TX)^{-1}X^T \mathbf{y} = X^\dag \mathbf{y}
\]
</div>

從幾何的觀點來看，就是  <span>$\mathbf{y} \notin $</span> Span <span>$(X)$</span> ，亦即
找不到一個 <span>$\mathbf{w}$</span> 滿足 <span>$X \mathbf{w} = \mathbf{y}$</span>，所以退而求其次，將 <span>$\mathbf{y}$</span> 投影到 Span(<span>$X$</span>) 上 (which is <span>$\underset{\text{projection}\,  P}{\underbrace{X(X^TX)^{-1}X^T}} \mathbf{y}$</span>)，再去找出對應的 <span>$\mathbf{w}$</span> ，也就是上方的 <span>$\mathbf{w}_{\text{LIN}}$</span>。

另外，這裡的 hypothesis set 的 <span>$d_{VC}$</span>也跟之前的 linear perceptron 一樣。

<!--## Apply to Binary Classification-->

<!--既然有 closed form 這麼好的性質，那麼用 linear regression　求得一組係數後，再加個 ‵sgn‵ ，也就可以當成 binary classification 的 hypothesis 了。那麼數學上如何表示呢？-->

<!--其實兩者還是有些微的不同 - **error measure**-->

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/09_handout.pdf)
