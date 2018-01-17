+++
date = "2018-01-17T22:59:24+01:00"
description = "Noise and Probabilistic Target"
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec8"
topics = ["Machine Learning Foundation"]
+++

前面幾講的討論中，我們都假設有一個 ground truth function <span>$f$</span> 去 generate data，且我們所拿到的 <span>$\mathcal{D}$</span> 是乾淨的，然而在真實世界中，無可避免地會遇到 **noise** 加在 data 上，如此情況下 learning 是否還是可行？ (VC bound 是否還是 work ?) 另外，前方討論時，我們都以 0/1-error 作為衡量 model 好壞的 metric，如果換成不同的 metric 呢？

<!--more-->


## Noise 

再度回到以前的 bin model， model noise 的方式是，今天抽取出一個 ball 時，他的顏色不再是是一個固定的顏色，而是一個機率分佈 <span>$\sim \Pr[y | \mathbf{x}]$</span> (i.e given input <span>$\mathbf{x}$</span>， label <span>$y$</span> is probabilistic rather than deterministic)。對應到 learning ，則是今天 learn 的 target 不再是一個deterministic 的 <span>$y = f(\mathbf{x})$</span>，而是一個機率分佈 <span>$\Pr[y | \mathbf{x} ]$</span> (deterministic 可以想成機率分佈為只在一個值為 <span>$1$</span>，其他全 <span>$0$</span> 的 special case)

而因為 Hoeffeding Ineq. hold for arbitary, unknowm target function，所以 VC
bound 還是成立(從相同的 joint distribution <span>$\sim \Pr(\mathbf{x},y)$</span> sample， <span>$\mathcal{D}$</span> 夠大的情況下，在 <span>$\mathcal{D}$</span> 衡量的量 (e.g error) 跟真實世界不會差太多)，但 learning 的同時， 在 noisy 的情況下，我們可能找不到夠好的 hypothesis <span>$h$</span> 使得 <span>$E_i$</span> 足夠小 (e.g 如果原先線性可分的資料中有 noise ，使其線性不可分，那麼便只能借助 Pocket Learning Algorithm，除了 running time 比較長之外，也不保證一定能找到跟沒有 noise 情況下， PLA 找到的最佳解一樣好的解)


## Error Measure
隨著應用場景不同，想學的 target 不同， error metric 亦有所不同 (而如何選定合適的
objective function 又是另一門學問了 ...lol)，簡言之，對大多數的 hypothesis set
<span>$\mathcal{H}$</span> 及 error metric ， VC theory 均會成立。

講義中給出了一個 Weighted 0/1 error 的 metric ，可以利用 virtual copy 的概念去將新的演算法 reduce 回原先介紹的 PLA (類似的 reduce 想法可以推廣到其他 error metric)。

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/08_handout.pdf)
