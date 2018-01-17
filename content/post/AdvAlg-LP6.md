+++
date =  "2017-09-29T21:19:19+02:00"
description = "3-Approx. Algorithm (Primal-Dual method)"
draft = false
tags = ["Algorithm","Linear Programming","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Metric Facility Location Revisited"
topics = ["Advanced Algorithm"]
+++

前面提到過一個較為複雜的優化問題， [Metric Facility Location Problem](https://sunprinces.github.io/learning/2017/09/advanced-algorithm---metric-facility-location-problem/) ，並給出了一個 4-Approx. Algorithm ，於這講中，我們要來利用前面提到的 Primal-Dual method 來設計演算法。

<!--Note: 我覺得真的有點複雜 😂 (整理筆記的時候)-->

<!--more-->

## Rewrite Dual form

### Primal LP

``$$
\min \sum_{i \in F}y_i f_i + \sum_{i \in F, \, j \in C} x_{ij} c_{ij} \; s.t. \, 
\begin{cases} 
\sum_{i \in F}x_{ij} \geq 1, \; \forall j \in C \; (\text{乘上} \, \color{red}{\alpha_j})\\
y_i \geq x_{ij}, \; \forall i \in F, \, j \in C \; (\text{乘上} \, \color{green}{\beta_{ij}})\\
x_{ij}, \, y_i \geq 0 
\end{cases}
$$``

### Dual LP

``$$
\min \sum_{j \in C} \color{red}{\alpha_j} \; s.t. \, 
\begin{cases}
\sum_{j \in C} \color{green}{\beta_{ij}} \leq f_i \; \forall i \in F \,
(y_i \, \text{的 objective}) \\
\color{red}{\alpha_j} - \color{green}{\beta_{ij}} \leq c_{ij} \; \forall i \in
F, j \in C \, (x_{ij} \, \text{的 objective}) \\
\alpha_j ,\beta_{ij} \geq 0
\end{cases}
$$``

### 對於 Dual var 的直觀理解

把 <span>$\alpha_j$</span> 想成是 clinet <span>$j$</span> 所付的錢，而 <span>$\beta \scriptstyle ij$</span> 表示其貢獻給 facility <span>$i$</span> 的，先把原先條件中的不等式改為等式 (complementary slackness)，第一條式子，表示被開的 facility is fully paid for。而第二條式子，把 <span>$\alpha_j$</span> 拆成兩部份，一部分給 facility ，另一部份則用於支付 <span>$c \scriptstyle ij$</span> 的 coneection cost 。

## Algorithm

### Phase I (Increase <span>$\alpha$</span> and <span>$\beta$</span>)

Initially, set <span>$\alpha_i$</span> and <span>$\beta \scriptstyle ij$</span>
to <span>$0 \; \forall i,j$</span>

1. Increase all **uncovered** <span>$\alpha_j$</span> with same rate until
<span>$\alpha_i = c\scriptstyle ij$</span> for some <span>$i,j$</span>， call <span>$\color{blue}{(i,j) \, \text{is tight}}$</span>

2. When <span>$(i,j)$</span> is tight, set <span>$\beta \scriptstyle ij
   \normalsize = \alpha_j - c \scriptstyle ij$</span> (僅可能緩慢地讓
   <span>$\beta$</span> 隨 <span>$\alpha$</span> increase ，在滿足第一條
   constraint 的情況下，不要讓 <span>$\alpha$</span> 的增長太快碰到第二條
   constraint)

Repeat above procedure, until <span>$\color{blue}{\underset{j \in C}{\sum}\beta
\scriptstyle ij \normalsize = f_i \, \text{for some} \, i}$</span>

Then, facility <span>$i$</span> becomes **temporarily opened**，\\
<span>$\forall j$</span> such that <span>$(i,j)$</span> is tight and <span>$j$</span> is not **covered**, temporarily connect <span>$j$</span> to <span>$i$</span>, 之後的 Phase I 中， <span>$\alpha_j$</span> is fixed。 And we say <span>$\color{blue}{\text{facility} \, i \, \text{is client} \, j \text{'s witness}}$</span>

Repeat above procedure until all clients are covered

**Remark:** 如果在 facility <span>$i$</span> 被開之後，有其他的 client <span>$j^\prime$</span> 使其 connection 為 tight，我們也稱 facility <span>$i$</span> 為 client <span>$j^\prime$</span> 的 witness (**Note: 往後** <span>$\alpha \scriptstyle j^\prime$</span> **會 fixed ，且** <span>$\beta \scriptstyle ij^\prime \normalsize = \alpha \scriptstyle j^\prime \normalsize - c \scriptstyle ij^\prime \normalsize = 0$</span>**，不會再更動**)

### Phase II (First remove all closed facilities and edges not tight)

**Def.**: if <span>$\beta \scriptstyle ij \normalsize > 0$</span> and <span>$\beta \scriptstyle ij^\prime \normalsize > 0$</span>, we call <span>$i, i^\prime$</span> are <span>$\color{blue}{\text{conflict}}$</span>

(clinet <span>$j$</span> 同時有付錢給兩者，均有貢獻 <span>$\beta \scriptstyle \star j$</span>)

Pick any **maximal** set of temporarily opened facilities <span>$\mathbf{I}$</span> that AREN'T
conflicting

1. open <span>$\mathbf{I}$</span>
2. If client <span>$j$</span>'s witness <span>$\in I$</span>, connect directly
3. If not, connect indirectly to any conflicting <span>$i \in I$</span>


## Correctness

### Complementary Slackness Revisited

1. <span>$\forall i \in F\, , \; y_i = 0 \lor \underset{j \in C}{\sum} \beta \scriptstyle ij \normalsize = f_i$</span> \\
(對有開的 facility, <span>$\sum \beta \scriptstyle ij \normalsize  = f_i$</span>) <span>$\Rightarrow$</span> 符合！

2. <span>$\color{red}{\forall i \in F \, j \in C, \; x \scriptstyle ij \normalsize  = 0 \lor \alpha_j - \beta \scriptstyle ij \normalsize = c \scriptstyle ij}$</span>

3. <span>$\forall j \in C\, , \; \alpha_j  = 0 \lor \underset{i \in F}{\sum} x \scriptstyle ij \normalsize = 1$</span>\\
(對所有的 client 而言，其只會被一個 facility serve) <span>$\Rightarrow$</span> 符合！

4. <span>$\forall i \in F, j \in C\, , \; \beta \scriptstyle ij \normalsize = 0
   \lor y_i = x \scriptstyle ij$</span>\\
(對那些 directly connected 的 clinet，也就是 <span>$\beta \scriptstyle ij \normalsize \not = 0$</span> 者，<span>$j$</span> 是由 <span>$i$</span> serve\\
但注意到為 <span>$0$</span> 者也可能是被 directly connect 的) <span>$\Rightarrow$</span> 符合！

**Remark:** 1,2 分別為一組， 3,4 為另一組 complementary slackness，而現在我們要
relax 第一組 3 倍 (因為對 indirectly connected 的那些 clinet ， <span>$\color{red}{2.}$</span> 不成立)

**proof:**\\
<span>$\because \, \text{indirectly connected} \, \therefore \, \beta
\scriptstyle ij \normalsize = 0$</span>

So what we want to prove now is <span>$\boxed{c \scriptstyle ij \normalsize \leq 3 \alpha_j}$</span>

Because the problem is **metric**, 

<center><img src="/img/post/facility_conflict.jpg" width="30%" style="border-radius: 0%;"></center>

``$$
\begin{align}
c_{ij} & \leq c_{ij^\prime} + c_{i^\prime j ^\prime} + c_{i^\prime j}\\
& \leq \alpha_{j^\prime} + \alpha_{j^\prime} + \alpha_j \; (\because \, \text{for directly connected} \, \alpha = \beta + c \geq c\\
& \leq 3 \alpha_j \; (\because \alpha_j \geq \alpha_{j^{\prime}})
\end{align}
$$``

**Note**

如果 <span>$i, i^\prime$</span> conflict， <span>$(i,j)$</span> 又不 tight ，代表必定存在 <span>$j^\prime$</span> 使得 <span>$(i,j^\prime), \, (i^\prime,j^\prime)$</span> 均為 tight 。\\
如果說是 <span>$i^\prime$</span> 比較早開，那麼 <span>$\alpha_j, \alpha \scriptstyle j^\prime$</span> 會相等 (同時 fixed) ; \\
但若是 <span>$i$</span> 比較早開，<span>$\alpha \scriptstyle j^\prime$</span> 比 <span>$\alpha_j$</span> 提早被 fix ，一定比較小 <span>$\Rightarrow \alpha_j \geq \alpha \scriptstyle j^\prime$</span>


故我們可以將 <span>$\color{red}{2}$</span> 式 relax 成以下

``$$
\frac{1}{3} c_{ij} \leq \alpha_j - \beta_{ij} \leq c_{ij}
$$``

<span>$\Rightarrow$</span> <span>$\color{red}{\text{3-Approx. Algorithm}}$</span> ✌️

## Reference

* [原始論文](https://www.cc.gatech.edu/~vazirani/k-median.pdf)
