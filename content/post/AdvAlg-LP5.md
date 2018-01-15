+++
date =  "2017-09-23T21:19:19+02:00"
description = "An example of primal-dual method"
draft = false
tags = ["Algorithm","Linear Programming","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Weighted Set Cover Revisited"
topics = ["Advanced Algorithm"]
+++

呈之前的討論，對於 WSC 問題，我們有了利用 LP solver 去對解做 [deterministic rounding](https://sunprinces.github.io/learning/2017/08/advanced-algorithm---vertex-cover-problem/) 及 [randomized rounding](https://sunprinces.github.io/learning/2017/08/advanced-algorithm---weighted-set-cover/) 的演算法，解法的共通點是必須要先 run 過 LP solver (雖然理論上是 **POLY** ，實務上現行的 solver 也很有效率)，但我們仍想問說，是否存在不須使用 LP solver 的演算法呢？ 而這也是這講所要提到的 **Primal-Dual Method** 。 

<!--more-->

至於實務上的好處嘛...，節錄官方(?)說法如以下:

> While linear programs are efficiently solvable, and algorithms for them are quick in practice, **special purpose algorithms** are often much **faster**

<!--而我自己的一點點感覺是，如此證明 ratio 可以用更簡便的方式，去用上題目所給的已知條件 ( 利用-->
<!--**relaxed** complentary slackness 直接 include 進來 ) 。-->


## Rewrite to Dual Form

As [primal-dual LP](http://localhost:1313/learning/2017/08/advanced-algorithm---linear-programming/) mentioned, the dual LP is

``$$
\max \sum_{j=1}^{n} \alpha_j \quad s.t. \begin{cases}
\sum_{e_j \in S_i} \alpha_j \leq \text{cost}(S_i) \; \forall i \\
\alpha_j \geq 0
\end{cases}
$$``

## Correctness of Approx. Ratio

利用 Weak Duality Theorem, <span>$\forall \, \text{feasible} \, \mathbf{x}, \mathbf{\alpha}$</span>我們有

``$$
\sum_{i=1}^m \, \text{cost}(S_i) \cdot x_i \color{red}{\geq} \sum_{(i,j): e_j
\in S_i} \, \alpha_j \cdot x_i \color{green}{\geq} \, \sum_{j=1}^{n} \, \alpha_j
$$``

雖然 form 看起來簡單一些了，但要找出符合 complentary slackness 的 dual OPT 還是很難，所以我們做些 relaxation。

### Relaxed Complementary Slackness
``$$
\begin{cases}
\color{red}{\geq}: \forall i, \, \text{either} \, x_i = 0 \, \text{or} \,
\boxed{\color{red}{\frac{1}{A} \, \text{cost}(S_i)} \leq \sum_{j: e_j \in S_i} \alpha_j \, \leq
\text{cost}(S_i)}\\
\color{green}{\geq}: \forall j, \, \text{either} \, \alpha_j = 0 \, \text{or} \,
\boxed{\color{green}{B} \, \geq \, \sum_{i: e_j \in S_i} x_i \, \geq \, 1}
\end{cases}
$$``

Then, we have

``$$
\boxed{\sum_j \alpha_j \, \leq \, \sum_{i}\text{cost}(S_i) \cdot x_i \leq \color{red}{A}\color{green}{B}
\sum_{j}\alpha_j}
$$``


如我們之前對題目做的假設，如果每個 element 都被 <span>$\leq l$</span> 個 subset
蓋住的話，相當於是設 <span>$A = 1, B = l$</span>，可以 derive 出一個 <span>$l$</span>-approx. algorithm。

## Algorithm - Primal-Dual Method

### Core Concept
> 慢慢更動 dual solution 的 variable <span>$\alpha_j$</span> 並搭配 complentary slackness (儘量去 satisfy，不行就 relax 得更鬆) ，利用此去轉換出 primal solution 。

### 一個可行的 Algorithm

Initially, <span>$\alpha_j = 0 \; \forall j$</span>

1. Pick any uncovered element <span>$e_j$</span>
2. Increase <span>$\alpha_j$</span> until <span>$\exists \, i \; s.t \; \underset{e_j \in S_i}{\sum} \alpha_j \, = \, \text{cost}(S_i)$</span> <span>$\Rightarrow \, \color{green}{ S_i \, \text{is tight}}$</span> (<span>$\color{red}{\text{更動 dual sol. } \, \land \, \text{搭配 C.S.}}$</span>)
3. Add all <span>$\color{green}{\text{tight}} \, S_i$</span> to set cover
   (<span>$\color{red}{\text{轉換出 primal sol.}}$</span>)

Repeat above procedure until all elements are covered
