+++
date = "2018-11-02T10:23:24+08:00"
description = "Regularization"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec14"
topics = ["Machine Learning Foundation"]
+++

前一講提到了無論是 stochastic noise 或是 ground truth 過複雜而引進的 deterministic noise ，對本身就複雜 (VC dimension 高) 的 model 而言，overfitting 是很容易發生的，而解決之道其一就是這一講所要介紹的 Regularization。

<!--more-->

除了藉由 cleaning 或 augumentation 等改變原始 data distribution <span>$\mathcal{D}$</span> 的方法之外， regularization 派的方法主要藉由降低 hypothesis set 的複雜度來減少 <span>$\Omega$</span> 的 penalty term 以縮小 <span>$E_o$</span> 及 <span>$E_i$</span> 的差距。如以下：

<div>
\[
E_o(h) \leq E_i(h) + \Omega(\mathcal{H}) \quad \forall h \in \mathcal{H}
\]
</div>

Regularization 有很多種實現的方式，而這裡會介紹最常見的一種 - Weight Decay ，藉
由 prefer 在 <span>$\mathbf{0}$</span> 附近的 <span>$h$</span> (i.e <span>$||h||$</span> is small)來減少 overfitting 。

## General Idea

<div>
\[
Loss = TaskLoss(D;\mathbf{w}) + ||\mathbf{w}||^2
\]
</div>

到這裡可能多數人會有一個疑問，為什麼選擇 norm 小的 model 就會是好的選擇呢？

Heuristically 來說， Regularization 想解決的是 noise 的影響(non-smooth and high-freq)，而我們想 model 的那
些 ground truth **empirically** 來說參數都不太大 (strong hypothesis ... lol)，所對應的 surface 也是較為平滑的，這也是為什麼 weight decay 會有用的原因。

相信這大概沒有回答到問題，不如來算點數學吧☺

## Step back to simple (low-order) model

回顧前一講的兩個實驗，low-order 的 hypothesis <span>$\mathcal{H}_2$</span>受到 overfitting 的影響較
high-order 的 <span>$\mathcal{H}\scriptstyle 10$</span>來說較不嚴重，所以不妨限制
<span>$\mathcal{H} \scriptstyle 10$</span> 的某些係數為 <span>$0$</span> 來降 order 。

<center><img src="/img/post/stepback1.png" width="70%" style="border-radius: 0%;"></center>

不過這樣還蠻愚蠢的，那打從一開始用 <span>$\mathcal{H}_2$</span> 就好了😅

## Looser but Hard Constraint

所以我們稍稍放寬限制，限定只要係數不為 <span>$0$</span> 的 <span>$w_n$</span> 不要太多就好，如以下

<div>
\[
\sum_n^{10} 1\!\!1[w_n \neq 0] \leq 3
\]
</div>

這樣的好處是我們比原先 low order 的 <span>$\mathcal{H}_2$</span> 更具彈性，但同
時又比 <span>$\mathcal{H} \scriptstyle 10$</span> 還安全，然而， boolean
的 constraint 是很難 optimize 的 (which is NP-hard)，所以我們不妨將其轉成較好 optimize 的形式 (但同時又 align 我們原先的 motivation)

## Soft Constraint

Given <span>$C \geq 0$</span>, we have regularized <span>$\mathcal{H}( C )$</span>
with the following constraint

<div>
\[
\sum_n^{10} w_n^2 \leq C
\]
</div>

**Remark:** 當 <span>$C$</span> 足夠大的時候，其實對解完全不影響 (i.e 就 linear
regression 這種可以找 closed-form 的問題來說，只要 <span>$\sqrt{C}$</span> 的
高維球有把原先的 optimal sol <span>$\mathbf{w}_{lin}$</span> 包進去，
regularized term 就不會發揮作用)

## Geometric View

那麼在 regularization 下，找到的 <span>$\mathbf{w}_{reg}$</span> 有什麼特性呢
？從幾何上來看

<center><img src="/img/post/geo-regularization.png" width="40%" style="border-radius: 0%;"></center>

所有的合法解會在 <span>$\mathbf{w}^T \mathbf{w} = C$</span> 上的球移動，而停止的 criteria 是 <span>$\nabla E_{in}$</span> 已經再沒有在球上移動的切分量，亦即

<div>
\[
\mathbf{w}_{reg} \parallel - \nabla E_{in} 
\]
</div>

而我們想找到一個 <span>$\lambda > 0$</span> and <span>$\mathbf{w}_{reg}$</span>
滿足

<div>
\[
\nabla E_{in}(\mathbf{w}_{reg}) + \frac{2 \lambda}{N} = 0
\]
</div>

**Remark:** <span>$\lambda$</span> 稱為 **Lagrange Multiplier** ，是一個在
constraint 下 解 optimization 問題的 方法 ~~trick~~。

## Equivalent Augumented Error

要解上方那個 Equation 等價於 minimize 以下這個 objective

<div>
\[
E_{aug}(\mathbf{w}) = E_i(\mathbf{w}) + \frac{\lambda}{N} \mathbf{w}^T\mathbf{w}
\]
</div>

**Remark**: 對不同的 <span>$C$</span> ，均有對應的 <span>$\lambda$</span> ，而 <span>$E_{aug}$</span> 將 <span>$\sqrt{C}$</span>-hypersphere 這個 constraint 轉成藉由調控 <span>$\lambda$</span> 大小的 unconstrained optimization 問題，實務上也比較好做。

**Remark:** 技術上來說，在 unconstrained optimization 中，我們還是享有整個 <span>$\mathcal{H}$</span> 的 flexibility 。

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/14_handout.pdf)
