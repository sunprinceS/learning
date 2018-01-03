+++
date = "2017-12-28T23:02:37+01:00"
description = "Feasibility of Learning"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec4"
topics = ["Machine Learning Foundation"]
+++

這一講要討論的是**機器學習真的是可行的嗎**？能推廣到所有 scenerio 嗎？或是只有部份？是否需要某些假設才能證明可行呢？

<!--more-->

## 前言

課堂中給出了一個 learning puzzle 的例子，簡單來說，對於那些 training data 以外的資料，總是有個小惡魔設計與我們找到的 <span>$g$</span> 之 predction，恰恰相反的 label ，來反駁 ML 並沒有學到任何事情(儘管在 training data 上全部 align 了...)。對我們未知的部份，我們只能任人擺佈，但這些未知，也正是需要 ML 發揮價值的地方。

## Infer sth unknown
讓我們先跳出 learning 的框架，想一想從 training data 中，我們能否去推估關於未知部份的一些統計量？從已知連向未知的過程中，嘗試解決(partially)上方遇到的困難。
這裡以簡單的 bin model 作為介紹。

### Bin Model
A bin consist many <span>$\textcolor{orange}{\text{orange}}$</span> and <span>$\textcolor{green}{\text{green}}$</span> balls \\
Want: estimate the portion of <span>$\textcolor{orange}{\text{orange}}$</span> balls <span>$\mu$</span>

先隨機抓取一把球，算出當中 <span>$\textcolor{orange}{\text{orange}}$</span> ball 的比例 <span>$\upsilon$</span>，是否能以<span>$\upsilon$</span> 去 estimate 未知的 <span>$\mu$</span>？

### Hoeffding's Inequality
<div>
\[
\Pr[|\upsilon - \mu| > \textcolor{red}{\epsilon}] \leq 2 \,
\exp(-2\textcolor{red}{\epsilon}^2\textcolor{blue}{N})
\]
</div>

### Probably approximately correct (PAC)
When <span>$\Pr[|\upsilon - \mu| > \textcolor{red}{\epsilon}]$</span> is small enough,\\
we say the statement <span>$\upsilon = \mu$</span> is **PAC** (which can
be achieved by sampling large <span>$\textcolor{blue}{N}$</span>)

### Connect with learning framework
Given fixed hypothesis function <span>$\textcolor{orange}{h}(x)$</span>

* <span>$h$</span> is <span>$\textcolor{orange}{\text{wrong}}$</span>，mark as
  <span>$\textcolor{orange}{\text{orange}}$</span>
* <span>$h$</span> is <span>$\textcolor{green}{\text{correct}}$</span>，mark as
  <span>$\textcolor{green}{\text{green}}$</span>

bin 可以想成是 input space <span>$\mathcal{X}$</span>，但我們僅從中 sample 出
<span>$\mathcal{D}$</span> 作為 training data ，當<span>$|\mathcal{D}|$</span> (i.e <span>$\textcolor{blue}{N}$</span>) 夠大時，可以說 <span>$\textcolor{orange}{h}$</span> 在 training data 與未知的世界表現是 **probably** consistent 的 (<span>$\Pr[|E_i(h) - E_o(h)| > \textcolor{red}{\epsilon}]$</span> is sufficiently small)。

## How to find sufficiently good <span>$h$</span>
我們的目標是要找一個 <span>$E_o(h)$</span> 夠小的 <span>$h$</span> ，而能推估<span>$E_o(h)$</span> 的唯一已知量便是 <span>$E_i(h)$</span> 了，而 Hoeffding Ineq. 告訴我們對於單一 <span>$h$</span>，這件事是 PAC。但 <span>$\mathcal{H}$</span> 不會只有一個 <span>$h$</span> ，學習的流程是對每一個 <span>$h$</span> 都去做 sample，並從中挑選出 sample error rate <span>$E_i(h)$</span> 最小者。然而，這件事情會放大 Hoeffding Ineq. 的 upper bound。舉例來說，對 150 個公平銅板，各作 5 次隨機試驗，其中有一個銅板連續 5 次都是正面的機率 > 99% ，但我們能因此便說該銅板正面的機率為 1 嗎？我們把這類使得對給定銅板(<span>$\textcolor{orange}{h}$</span>) <span>$E_i$</span> 跟 <span>$E_o$</span> 差很多的試驗(<span>$\mathcal{D}$</span>)稱作 <span>$\textcolor{red}{\text{BAD}} \, \text{sample}$</span>(也就是說，雖然單看一個銅板，<span>$\Pr[\textcolor{red}{\text{BAD}}]$</span> 很小，但對 150 個銅板而言， <span>$\Pr[\textcolor{red}{\text{BAD}}]$</span> 便超高。

從 learning 的角度來看，就是給定 <span>$\mathcal{D}$</span>， 它對各個 <span>$\textcolor{orange}{h}$</span> 為 <span>$\textcolor{red}{\text{BAD}}$</span> 的機率雖小，但對 <span>$\mathcal{H}$</span> 中至少一個 <span>$\textcolor{orange}{h}$</span> 是 <span>$\textcolor{red}{\text{BAD}}$</span> 的機率卻是不小的。

``$$
\begin{align}
\Pr_D[\color{red}{\text{BAD}} \, \text{for} \, \mathcal{H}] &= 
\Pr_D[\color{red}{\text{BAD}} \, \text{for}\, h_1 \, \lor \color{red}{\text{BAD}} \, \text{for}\, h_2  \, \lor \, \cdots \, \color{red}{\text{BAD}} \, \text{for}\, h_M ] \\
& \leq \Pr_D[\color{red}{\text{BAD}} \, \text{for}\, h_1] \, + \, \Pr_D[\color{red}{\text{BAD}} \, \text{for}\, h_2]  \, + \, \cdots \, \Pr_D[\color{red}{\text{BAD}} \, \text{for}\, h_M] \, (\because \, \text{Union Bound}) \\
& \leq 2 \, M \, \exp(-2 \color{red}{\epsilon}^2 \color{blue}{N}) (\because \, \text{Hoeffding Ineq.})
\end{align}
$$``


<span>$\Rightarrow$</span> learning is possible when <span>$|\mathcal{H}|$</span> is finite and <span>$E_i(h)$</span> is small \\
所謂 **possible** 是指，在 data 足夠的情況下，是可以找到 <span>$E_o$</span>
probably approximately and sufficiently small 的 <span>$\textcolor{orange}{h}$</span> 。

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/04_handout.pdf)
