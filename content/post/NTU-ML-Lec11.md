+++
date = "2018-04-14T06:59:24+01:00"
description = "Linear Models for Classification"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec11"
topics = ["Machine Learning Foundation"]
+++

總結目前學到的 3 個 linear model。

<!--more-->

## Linear Models for Binary Classification

前面學到的 regression ，其 error (經過 const. scale) 後都會 <span>$\geq$</span>
0-1 error，因此利用有效率的演算法，求出 regression 的 model 後， apply step
function 來作為 binary classifier 也是一種做 classification 的方法。

**Remark:** error bound 相對比較鬆，可以想成為了提高效率所作的 accuracy trade-off。

<center><img src="/img/post/l-comp.png" width="70%" style="border-radius: 0%;"></center>

## Acceleration on Gradient Descent - Stochastic GD
之前在找 <span>$E_i$</span> 下山的方向時， <span>$\nabla_w E_i$</span> 要iterate 過所有資料點才能得到，但我們不妨只 sample 幾個資料點即可(期望值一樣)，但實務上比較不穩定。

**Remark:** PLA 可以想成 SGD 的 一個特例 (只 sample 一個資料點，且 <span>$\eta = 1$</span>)

## Binary Classification to Multiclass

可以使用 1-vs-all (OVA) 或 1-vs-1 (OVO) 的方法

* OVA: 有 <span>$M$</span> 個 class 的話，要 train <span>$M$</span> 個 binary
  OVA 的 classifier (each with <span>$|\mathcal{D}| = N$</span> )，但資料 unblanced，結果可能 unstable。

* OVO: 要 train <span>$\binom{M}{2}$</span> 個分類器 (assume equally distributed, each size is <span>$\frac{2N}{M}$</span>)，結果比較 stable ， 但 prediction 時要 run 過 <span>$\mathcal{O}(M^2)$</span> 使其比較沒效率。

**Remark:** OVO 在 training 時，反而是比較有效率的 ！

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/11_handout.pdf)
