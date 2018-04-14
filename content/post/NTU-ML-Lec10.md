+++
date = "2018-04-13T12:59:24+01:00"
description = "Logistic Regression"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec10"
topics = ["Machine Learning Foundation"]
+++

Linear Model 的核心在於 feature 分量的 weighted sum <span>$\mathbf{w}^T \mathbf{x}$</span>， 在 binary classification ，我們用 step function 將其二分 (<span>$\mathcal{Y} = \lbrace \, -1,1\,\rbrace$</span>)，而在 linear regression 中，我們將其直接作為輸出。而這一講要介紹的則是將 <span>$\mathbf{w}^T \mathbf{x}$</span> 通過一個 nonlinear function mapping 到[<span>$0,1$</span>]，賦予他機率的意義 (<span>$\Pr[y = +1 | \mathbf{x}]$</span>)。

<!--more-->

## Logistic Function

``$$
h(\mathbf{x}) = \theta(\mathbf{w}^T \mathbf{x}), \quad \theta(s) = \frac{e^s}{1+e^s}
$$``

一個 step function 的 soft 版本。


## Error Measure

而正常來說，我們所擁有的資料，並不是 feature 對 probability 的 pair ，而是跟binary classification 一樣的資料，也就是說 data 是由以下的 target 產生。

``$$
\Pr[y|\mathbf{x}] = \begin{cases}
f(x) \;&, y = +1\\
1-f(x) \; &, y = -1
\end{cases}
$$``

所以接下來的問題是，我們想優化的 <span>$h(x)$</span> 跟 <span>$f(x)$</span> 有什麼關係。
給定一組 data <span>$\mathcal D = \lbrace (\mathbf{x}_1,+1),\cdots,(\mathbf{x}_N,-1) \rbrace$</span>，我們可以算它被**產生**的機率有多少，而既然他會被我們 sample 到，代表機率一定不小，同樣地，如果 <span>$h(x)$</span> 學得好的話， behavior 要跟 <span>$f(\mathbf{x})$</span> 差不多，所以我們不妨考慮由 <span>$h(x)$</span> 去 generate <span>$\mathcal{D}$</span> 的**可能性**(likelihood)，並去 maximize 它，表示讓他跟 <span>$f(x)$</span> 越像。

logistic 函數有一個性質是 <span>$\theta(-x) = 1 - \theta(x)$</span>，所以上方所講的 likelihood 可以表示成以下，

<center><img src="/img/post/logis_likelihood.png" width="70%" style="border-radius: 0%;"></center>

等價於 minimize 以下，

``$$
\frac{1}{N} \sum_n \ln(\frac{1}{\theta(y_n\mathbf{w}^T\mathbf{x}_n)})
$$``

定義 <span>$E_{in}$</span> 為

``$$
\boxed{E_{in} = \sum_n \underbrace{\ln(1 + \exp(-y_n \mathbf{w}^T \mathbf{x}_n))}_{\text{cross-entropy error}}}
$$``

## Feasibility of Learning

### Sufficient small <span>$E_{in}$</span>

可以證明<span>$E_i(\mathbf{w})$</span> 是 convex，代表可以用 <span>$\nabla_w E_i(\mathbf{w}) = 0$</span>  來找到 weight <span>$\mathbf{w}^\star$</span>。

<center><img src="/img/post/logis_ein.png" width="70%" style="border-radius: 0%;"></center>

但跟在 linear regression 不同的是，這裡 <span>$\mathbf{w}$</span> 被包在
<span>$\theta(\cdot)$</span> 裡頭，而非單純的線性組合，不存在 closed form 的解可以一步到位。

### Gradient Descent

在這裡我們使用 Gradient Descent 去 iteratively 求解。

``$$
\mathbf{w}_{t+1} \leftarrow \mathbf{w}_t - \eta \nabla E_{in}(\mathbf{w}_t)
$$``

**Remark:** <span>$||\nabla E_i||$</span> 包含了每次 step 大小的資訊，在陡峭的地方，我們一次順勢滑落比較多，而當趨於平緩時，則減小步伐。 

**Remark:** 如果跑了 <span>$D$</span> 輪，時間複雜度為 <span>$\mathcal{O}(ND)$</span>，不保證找到最佳解(雖然知道谷底存在，但不知道怎麼走到)。

### Generalization to <span>$E_{out}$</span>

待補

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/10_handout.pdf)
