+++
date =  "2017-08-18T21:19:19+02:00"
description = "An example of randomized rounding from LP"
draft = false
tags = ["Algorithm","Linear Programming","Randomized Algorithm","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Weighted Set Cover"
topics = ["Advanced Algorithm"]
+++

呈前一講，我們對**有限制的** WSC 問題有 <span>$l$</span>-approx. algorithm，但可能並不是太好(比方說 <span>$l=|U|$</span> 之類)，於是我們嘗試引進一些隨機性，雖然犧牲了 deterministic 的 approx. ratio ，有時候甚至會得到更爛的結果(甚至不滿足 constraint @@)，但可以證明在**大部分**時候，都可以得到一個**還不錯** (approx. ratio ISN'T too bad)的結果。

## Weighted Set Cover Problem

讓我們明確定義這個問題

Given <span>$U = \lbrace e_1,e_2,\cdots,e_n \rbrace, \, S_1,S_2,\cdots S_k \subseteq U \, \text{with} \, \text{cost}\,(S_i)$</span>

Want: Find a **min**-cost collection of subsets <span>$C$</span> such that <span>$\underset{C}{\bigcup} S_i = U$</span>

## Formulation

Let <span>$x_i = $</span>𝟙[subset <span>$S_i$</span> is chosen or not]

<div>
\[
\min \sum_i x_i \quad s.t. \begin{cases}
 \sum_{e_j \in S_i} x_i \geq 1 \;& , \forall j=1 \sim n \;(\because \text{每個element 都至少要被蓋到一次})\\
0  \leq x_i \leq 1 \; & , \cancel{x_i \in \mathbb{Z}} \;(\text{LP Relaxation})
\end{cases}
\]
</div>

利用 LP solver 得到的解 <span>$\mathbf{x}^{\star} = (x_1^{\star},x_2^{\star},\cdots,x_k^{\star})$</span> **當作選擇** <span>$\mathbf{S_i}$</span> **進 collection 的機率**

## Lemma

<div>
\[
\Pr[C \, \text{is a set cover} \, \land \, \text{cost}(C) \leq \mathbf{OPT} \cdot c_1 \, \log n] \geq \frac{1}{2}
\]
</div>

### cost 很大的機率

At each round, <span>$S_i$</span> will be chosen with prob. <span>$x_i^{\star}$</span> independently, and we repeat <span>$c \log n$</span> rounds

因此，

``$$
\mathbb{E}[\, \text{cost in 1 round} \,] = \, \sum_{i=1}^{k} \text{cost}(S_i) \cdot \Pr[ S_i \, \text{is chosen}] = \sum_{i=1}^{k} \, \text{cost}(S_i) \cdot x_i^{\star} \leq \mathbf{OPT}
$$``

``$$
\Rightarrow \mathbb{E}[\, \text{cost of Alg. output} \,] \leq c \log n \cdot \mathbf{OPT}
$$``

<div>
\[
\text{According to Markov Ineq.} \quad \boxed{\Pr[X \geq a] \leq \frac{\mathbb{E}[X]}{a}}
\]
</div>

``$$
\Rightarrow \boxed{\Pr[\, \text{cost of Alg. output} \, \geq \color{blue}{4} \, \mathbf{OPT} \cdot c \log n\,] \leq \frac{1}{\color{blue}{4}}}
$$``

### collection <span>$C$</span> 不是 set cover 的機率

For each elemetn <span>$e_j$</span>

<div>
\[
\Pr[e_j \, \text{is not covered in round 1}] \, = \underset{e_j \in S_i}{\prod}
(1 - x_i^{\star}) \underset{\text{見以下}}{\leq} \underset{e_j \in S_i}{\prod} e^{-x_i^{\star}} =
\exp(-\underset{e_j \in S_i}{\sum} x_i^{\star}) \underset{\text{constraint}}{\leq} e^{-1}
\]
</div>

<div>
\[
\text{之後很常用到的不等式} \; \boxed{ 1-x \leq e^{-x}} \; \forall x \geq 0
\]
</div>

所以在 <span>$c \log n$</span> 個 round 內， <span>$e_j$</span> 都不會被選到的機率 <span>$ \leq (\frac{1}{e})^{c \log n}$</span>，選擇適當的 <span>$c$</span> ，使得該機率 <span>$\leq \frac{1}{4n}$</span>，
再利用 Union Bound，將單一 element 不被選到的機率加總

<div>
\[
\Rightarrow \boxed{\Pr[\text{Alg. miss some element}] \leq \frac{1}{4}}
\]
</div>

再一次利用 Union Bound，將這兩件**壞事**的機率相加，可以得到其 <span>$\leq
\frac{1}{2}$</span>，得證！

※ 如果造得出 <span>$\frac{1}{2}$</span>，對於任意給定的常數 error tolerance
<span>$\epsilon$</span> ，我們都可以藉由調控 <span>$c$</span> (也就是要 run 多少
 round) 得到 (但怪怪的地方是說，因 approx. ratio 並非 constant ，而是
 <span>$\mathcal{\Theta}(\log n)$</span>，所以 <span>$c$</span> 直接被忽略，才可以
 在降低非法解的可能時，同時保證 approx. ratio)





