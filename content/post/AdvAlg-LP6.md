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
