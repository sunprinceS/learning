+++
date = "2018-01-04T17:13:44+01:00"
description = "The VC Dimension"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec7"
topics = ["Machine Learning Foundation"]
+++

於前一講中，我們證明了 growth function 為 **POLY(N)** iff break point 存在，而這件事可以說明在 training data 上的表現不會和 testing data 差的太多 <span>$E_i(h) \approx E_o(h)$</span>  (if 數據點夠多 <span>$N$</span> )。而這一講中，我們由 break point 引進 **VC Dimension** <span>$d \scriptscriptstyle VC$</span> ，來當作衡量 <span>$\mathcal{H}$</span> 複雜度的一個指標。

<!--more-->

## VC Dimension

<div>
\[
\text{largest N for which }\, m_H(N) = 2^N
\]
</div>

其實就是 (minimum) break point <span>$k$</span> 的前一點罷了。

## General PLA (w/ higher dimension <span>$d$</span>)

``$$
\boxed{d_{VC} = d + 1}
$$``

**proof**:

* <span>$d \scriptscriptstyle VC$</span> <span>$\geq d + 1$</span>:\\
 That is, for hypothesis <span>$\mathcal{H}$</span>， <span>$\exists$</span> some
 <span>$d+1$</span> 個數據點 can be shattered，所以我們就構造一組這樣的
 <span>$\mathcal{D}$</span> 就可以得證了！

> <img src="/img/post/dvc_1.png" width="70%" style="border-radius: 0%;">

> 一個可以選擇的數據是在原點及單位正交基底向量 (簡單起見，選擇 <span>$\mathbf{e}_i$</span>)
(會這樣想，可以回到[第五講](https://sunprinces.github.io/learning/2017/12/ntu-machine-learning---lec5/)中提到，那些退化的 case 反而是不能被 shatter 的特例，所以我們應該是要找能展出整個 input space 的基底才是)

> 而 **shatter** 表示什麼呢？表示 given 一組任意的 label <span>$\mathbf{y}$</span>，我們都能找到一組係數與 <span>$X$</span> 乘一乘後得到它。

> <img src="/img/post/dvc_2.png" width="80%" style="border-radius: 0%;">

> 因為 <span>$\det(X) = 1$</span>，所以其 invertible !


* <span>$d \scriptscriptstyle VC$</span> <span>$\leq d + 1$</span>: \\
That is, for hypothesis <span>$\mathcal{H}$</span>， <span>$\forall d+2$</span>
個數據點都不能被 shatter 。

> 而這裡的想法是利用反證法，一樣構造一組 <span>$d+2$</span> 個 row 的資料矩陣 <span>$X$</span> ，先假設存在一組 <span>$\mathbf{w}$</span> 滿足我們所給定的 label assignment。\\
但因為 `null`(<span>$X$</span>) <span>$\not = 0$</span>，所以最後一筆數據不能完全隨心所欲的 assign ，而是 depend on 前 <span>$d+1$</span> 個，而正是利用此矛盾點，在最後加入一個使得整筆資料不能被 shatter 的老鼠屎 (從 2D 的例子來看，就是找出會讓資料們變成線性不可分的點，在更高維也是)，便能得證！

### Exploiting Linear dependence

> Assume the labels for previous <span>$d+1$</span> points are <span>$\lbrace \textcolor{blue}{\texttt{o}},\textcolor{red}{\texttt{x}},\cdots,\textcolor{red}{\texttt{x}} \rbrace$</span>

> ``$$
\mathbf{x}_{d+2} = \color{blue}{a_1}\mathbf{x}_1 + \color{red}{a_2}\mathbf{x_2}
+ \cdots + \color{red}{a_{d+1}}\mathbf{x}_{d+1}
$$``

> Then, multiply by <span>$\mathbf{w}^T$</span>, we can find contradiction if the
label for <span>$\mathbf{x} \scriptscriptstyle d+2$</span> is <span>$\textcolor{red}{\texttt{x}}$</span>

> <img src="/img/post/dvc_3.png" width="70%" style="border-radius: 0%;">

## Interpretation of VC Dimension

* **Effective** Degree of Freedom (<span>$\approx$</span> number of free params)
* Model Complexity

We can rewrite the VC Bound as follows

<img src="/img/post/vc_1.png" width="70%" style="border-radius: 0%;">

<span>$\textcolor{purple}{\text{with prob.}  \, \geq 1 - \delta }$</span>， The
error tolerance <span>$\epsilon$</span> will be 

``$$
\sqrt{\frac{8}{N}\ln(\frac{4 \, (2N)^{d_{VC}}}{\color{purple}{\delta}})}
$$``

And we also call it **penalty for model complexity** <span>$\Omega(N,\mathcal{H},\delta)$</span>

``$$
\boxed{E_o(g) \leq E_i(g) + \underset{\color{red}{\Omega(N,\mathcal{H},\delta)}}{\underbrace{\sqrt{\frac{8}{N}\ln(\frac{4 \, (2N)^{d_{VC}}}{\delta})}}}}
$$``

**Conclusion**: powerful <span>$\mathcal{H}$</span> IS NOT always good! \\
不能只追求在 training data 上能完全 fit ，而引進過高的模型複雜度導致 **overfitting** 。


<img src="/img/post/vc_2.png" width="90%" style="border-radius: 0%;">

**Some Remark**: VC bound 因為處理的情況很 general ，其是相當 **LOOSE** 的，實務上要達到夠小的 error rate ，不一定需要其所給出所需要的那麼多 data 量

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/07_handout.pdf)
