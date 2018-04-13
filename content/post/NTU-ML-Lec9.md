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

### Sufficient small <span>$E_{in}$</span>
上面提到的**距離**就是所謂 error。

<center><img src="/img/post/lr-ein.png" width="70%" style="border-radius: 0%;"></center>

可以看出 <span>$E_i$</span> 是 convex function，可以透由找 <span>$\nabla_w E_i = 0$</span>所對應的點找到我們要的 weight。

在 linear regression 中，這樣的 weight <span>$\mathbf{w}_{\text{LIN}}$</span> 是有 closed form 的。

<div>
\[
\mathbf{w} = (X^TX)^{-1}X^T \mathbf{y} = X^\dag \mathbf{y}
\]
</div>

從幾何的觀點來看，就是  <span>$\mathbf{y} \notin $</span> col <span>$(X)$</span> ，亦即
找不到一個 <span>$\mathbf{w}$</span> 滿足 <span>$X \mathbf{w} = \mathbf{y}$</span>，所以退而求其次，將 <span>$\mathbf{y}$</span> 投影到 col(<span>$X$</span>) 上 (which is <span>$\underset{\text{projection}\,  P}{\underbrace{X(X^TX)^{-1}X^T}} \mathbf{y}$</span>)，再去找出對應的 <span>$\mathbf{w}$</span> ，也就是上方的 <span>$\mathbf{w}_{\text{LIN}}$</span>。

### Generalization to <span>$E_{out}$</span>

回顧 binary classification，Out-sample 的 error 會被以下 bound 住

``$$
E_{out}(f) = E_{in}(f) + \mathcal{O}(\sqrt{\frac{d_{VC} \, \ln n}{N}})
$$``

對於 Regression 問題，我們還先要定義 <span>$d_{VC}$</span> 是什麼，以及所對應的 generalization bound。

關於 <span>$d_{VC}$</span> ， 原先的定義似乎不太適用，我們沒辦法定義在 <span>$\mathbb{R}$</span> 上的 **shatter** 究竟是怎麼一回事，但從自由度的觀點來看，有跟 linear preceptron 一樣的 dim 是很直覺的(就只是經過了 step 變換而已) (似乎有個叫 psuedo dimension 的主題就是將其推廣至 regression ，這裡就不繼續探討了)

先來回想一下，當初 classification 的 generalization bound 是怎麼 derive 的，假設資料是線性可分，我們在空間中 sample <span>$N$</span> 點，以此去找出可以將空間一分為二的最佳平面 (dichotomy) ，但因為我們只能 sample 有限的點，對沒 sample 到的那些，可以總是有個小惡魔做出跟我們相反的決策 claim 我們是錯的，而我們利用 Hoeffeding Equality 以及 hypothesis set 的幾何特性去說 <span>$E_i$</span> 跟 <span>$E_o$</span> 相去甚遠的機率很小。而如果有 flipping noise 的話，找 <span>$f(\mathbf{x})$</span> 則變成找 <span>$f(\mathbf{x}, y)$</span> 的 joint dist.

而今天在 Linear Regression，我們對資料的假設又更強了，假設今天有個 oracle 說，已知所有在 <span>$\mathbb{R}^3$</span> 中的 data 都落於一個二維平面上 (對應到 classification 線性可分的條件)，那麼我們跑 LR 所得到的結果，無論在 training 還是testing ， error 都是完美的 <span>$0$</span> (其實在這種情況下，跑個反矩陣運算就解決了...)。

所以這裡引進 noise 的討論就重要了(在後面會提到，除了隨機的雜訊外 (white noise 的概念)，今天 ground truth model 過於複雜使得 hypothesis set 無法完整 catch 所造成的誤差，也是 noise 的一種，所以我們必須 model ，畢竟我們其實沒有 oracle XD)。\\
我們假定 <span>$y = \mathbf{w^\star}^{T} \mathbf{x} + \epsilon$</span>，而這個 <span>$\epsilon$</span> 會讓資料開始從平面上跳走，幾何上來看就是加入了垂直於 col(<span>${X})$</span> 的分量。 (Note: 這裡把投影到 col(<span>$X$</span>) 的 矩陣特稱為 Hat matrix <span>$H$</span>)。
則 <span>$E_i$</span> 可以看成是 Noise <span>$\tilde{\mathbf{n}}$</span> 投影到 Null(<span>$X$</span>) 的分量，which is <span>$(I-H) \mathbf{y}$</span> \\
以下是圖解

<center><img src="/img/post/lr-proj.png" width="60%" style="border-radius: 0%;"></center>

``$$
E_{in} = \frac{1}{N}||\mathbf{y} - \mathbf{\hat{y}}||^2 = \frac{1}{N}||(I-H) \tilde{\mathbf{n}}||^2
$$``


**Lemma:** trace(<span>$AB$</span>) = trace(<span>$BA$</span>)

**Lemma:**  trace(<span>$H$</span>) = <span>$d+1$</span>, Assume <span>$X^TX$</span> is invertible\\
(白話來講就是他的 col. vector 們是 linear dependent)，故其 dim. 為 <span>$d+1$</span>

**proof:**\\
trace(<span>$H$</span>) = trace(<span>$X(X^TX)^{-1}X^T$</span>) =
trace(<span>$(X^TX)^{-1}X^TX$</span>) = trace(<span>$I$</span>) = <span>$d+1$</span>


#### Lemma
``$$
\boxed{E_{in} = \frac{1}{N}||\text{trace}(I-H) \mathbf{\tilde{n}}||^2 =
\frac{1}{N}(N-(d+1))||\mathbf{\tilde{n}}||^2}
$$``

**proof:**

<span>$||(I-H)\mathbf{\tilde{n}}||^2 = \mathbf{\tilde{n}^T}(I-H)^T(I-H)\mathbf{\tilde{n}}$</span> \\
因為 <span>$(I-H)$</span> 也是個投影矩陣，所以我們有其 symmetric 及冪等性兩個性質，\\
所以 <span>$= \mathbf{\tilde{n}^T}(I-H)\mathbf{\tilde{n}}$</span>，
結果為一個**值**，故亦等於其 trace，用跡數相乘的可交換性，我們可以得到
<span>$E_i$</span> 

再稍微推導一下， <span>$E_o$</span> 可以得到類似的式子 (但證明我看不懂了QQ)

``$$
\boxed{E_o = \text{noise level} \cdot (1 + \frac{d+1}{N})}
$$``



<!--``$$-->
<!--E_{in}(\mathbf{w}_{\text{LIN}}) = \frac{1}{N} || \mathbf{y} - \mathbf{\hat{y}} ||^2 = \frac{1}{N} || (I-H) \, \mathbf{\tilde{n}} || ^2-->
<!--$$``-->


所以 Generalization bound 可以寫成以下 (Assume Noise level is constant)

``$$
E_{out}(f) = E_{in}(f) + \mathcal{O}(\frac{d_{VC}}{N})
$$``

<!--## Apply to Binary Classification-->

<!--既然有 closed form 這麼好的性質，那麼用 linear regression　求得一組係數後，再加個 ‵sgn‵ ，也就可以當成 binary classification 的 hypothesis 了。那麼數學上如何表示呢？-->

<!--其實兩者還是有些微的不同 - **error measure**-->

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/09_handout.pdf)
