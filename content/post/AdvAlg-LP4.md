+++
date =  "2017-09-16T20:49:15+02:00"
description = "6-Approx. Algorithm (Deterministic rounding from LP)"
draft = false
tags = ["Algorithm","Linear Programming","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Metric Facility Location Problem"
topics = ["Advanced Algorithm"]
+++

接續[前面幾講](https://sunprinces.github.io/learning/tags/linear-programming/)，一個常用的技巧是，利用 LP solver 得出來的解 <span>$\mathbf{x}^{\star}$</span>， 拿去做 rounding 推出原先問題的解 <span>$\mathbf{x}^{\prime}$</span> (with 一個還不錯的 approx. ratio)。而這講會講述一個較為複雜的問題 - Metric Facility Location Problem (**MFL**)。

<!--more-->

## Metric Facility Location Problem

問題大概是這樣的，想像你要規劃一個都市，你可以在某些地點建造一些設施 (*facility*)，當然，在不同的地點建造會有不同的成本，目地是要服務居民，或者說是客戶 (*client*) ， 同時需要建造道路使得居民能通到對應設施(隨著距離不同，也有不同成本)，目標是讓所有的居民都**至少被一個**設施給服務，並去 minimize 總成本。正式的 formulation 如以下：

Denote <span>$f_i$</span> be the cost of opening facility <span>$i$</span>, and <span>$c \scriptstyle ij$</span> be the cost of connecting facility <span>$i$</span> and client <span>$j$</span>


Let <span>$y_i = $</span>𝟙[facility <span>$i$</span> is open], <span>$x
\scriptstyle ij$</span> <span>$ = $</span> 𝟙[client <span>$j$</span> is served by facility
<span>$i$</span>]

Objective is:

``$$
\require{cancel}
\min \sum_{i \in F}y_i f_i + \sum_{i \in F, \, j \in C} x_{ij} c_{ij} \; s.t. \, 
\begin{cases}
\sum_{i \in F}x_{ij} \geq 1, \; \forall j \in C \; (\because \text{client 都要被
served})\\
y_i \geq x_{ij}, \; \forall i \in F, \, j \in C \; (\because \text{facility 要開了才能 serve})\\
x_{ij}, \, y_i \in \cancel{\lbrace 0,1 \rbrace} \mathbb{R}^{+} \bigcup \lbrace 0
\rbrace
\end{cases}
$$``

而 **metric** 表示 <span>$c \scriptstyle ij$</span> 間滿足三角不等式 (i.e <span>$c \scriptstyle ij$</span> <span>$\leq c\scriptstyle ik$</span> <span>$+ c\scriptstyle kj$</span>)

Run 過一遍 LP solver ，我們現在有 <span>$\mathbf{x}^{\star}, \mathbf{y}^{\star}$</span>，將其拿來做 rounding 。

### Some setting

**1**. For each client <span>$j$</span>， compute the average serving cost
   <span>$c_j = \underset{i \in F}{\sum} x \scriptstyle ij$</span> <span>$c \scriptstyle ij$</span> \\
**2**. Pick up the set <span>$N_j = \lbrace i  \, | \, c \scriptstyle ij$</span> <span>$\leq 2 c_j \rbrace$</span> \\
**3**. <span>$\forall i \in F$</span>

<div>
\[
\text{Let} \, y_i^{\prime} = 2y_i^{\star}\; \text{and} \; x_{ij} = \begin{cases} 2 x_{ij}^{\star}  \normalsize, \; \text{if} \, i \in N_j \\ 
0, \; \text{otherwise} \end{cases}
\]
</div>

### Correctness of the above setting

**Lemma:** 

``$$
\text{Fix} \, i , \mathbf{x}_i^{\star} =
(x_{i1}^{\star},x_{i2}^{\star},\cdots,x_{in}^{\star}) \; \text{is a prob.
distribution and } \, \sum_{i \in N_j} x^{\star}_{ij} \geq \frac{1}{2}
$$``

**Proof:**

如果 <span>$\underset{i \in F}{\sum} x \scriptstyle ij \normalsize > 1$</span>，我們總是可以將其減到 <span>$1$</span> ，得到更小的 cost 。因此可以將 <span>$\mathbf{x}^{\star}$</span> 視為一機率分佈 (就把他當作 <span>$i$</span> serve <span>$j$</span> 的機率好了)。而直覺告訴我們，若 facility 距離太遠 (<span>$c \scriptstyle ij $</span>太大)，其機率應該要很小。根據 [Markov Ineq](https://sunprinces.github.io/learning/2017/08/advanced-algorithm---weighted-set-cover/#markov_ineq)，<span>$\forall j \in C$</span>

``$$
\sum_{i \not \in N_j}x_{ij}^{\star} = \Pr[X \geq c_j] = \Pr[X \geq 2 \mathbb{E}[X]] \leq \frac{1}{2}
$$``

所以 setting 的第 3 步還是有符合 constraint 。


### Algorithm

1. Pick client <span>$j$</span> with minimum <span>$c_j$</span> (i.e 平均服務成
   本最小的顧客)
2. Let facility <span>$k$</span> be the **cheapest** facility in <span>$N_j$</span> (單看 <span>$f_i$</span>)
3. Open the facility <span>$k$</span>\\
4. Close all other facilities in <span>$N_j$</span> (<span>$j$</span> 已
經有 <span>$k$</span> serve 了)\\
5. <span>$\forall j^{\prime} \, $</span> where <span>$N_j \bigcap N \scriptstyle j^{\prime}$</span> <span>$ \not = \varnothing$</span>，connect client <span>$j^{\prime}$</span> to facility <span>$k$</span>

Find the minimum <span>$j^{\prime \prime}$</span> which hasn't been served.\\
Repeat the above procedure until every client has been served

**6 - Approx. ratio**:

> **Connection cost**:

``$$
\begin{align}
c_{kj^\prime} &\leq c_{lj^\prime} + c_{lj} + c_{jk} \; (\because \, \text{三角不等式}) \\
& \leq 2 c_{j^\prime} + 2c_j + 2c_j \; (\because l \in N_{j^\prime}, \, l,k \in N_j)\\
& \leq 6 c_{j^\prime} \; (\because c_j \, \text{is current min.}) \\
& \Rightarrow \, \text{Total conn. cost} \, \leq \sum_{j}6 c_j \leq 6
\sum_{j}\underset{\color{red}{\text{OPT}_{\text{LP}}}}{\underbrace{\sum_{i} x_{ij}^{\star} c_{ij}}}
\end{align}
$$``

> **Facility cost**: 

``$$
\begin{align}
f_k &\leq \sum_{i \in F}x^{\prime}_{ij} \cdot f_k \; (\because \, \text{the 1st constraint})\\
& \leq \sum_{i \in F} y_j^{\prime} f_k \; (\because \, \text{the 2nd constraint})\\
& \leq \sum_{i \in N_j} (y^{\prime} \cdot f_i)
\; (\because \, N_j \, \text{以外全部歸 0}, \, k \, \text{為} N_j \, \text{中最便宜者})\\
& \leq \sum_{i \in N_j} (2y^{\star}\cdot f_i)\\
& \leq 2 \underset{\color{red}{\text{OPT}_{\text{LP}}}}{\underbrace{\sum_{i \in
N_j}y_i^{\star} \cdot f_i}}
\end{align}
$$``

> **Remark:** 

> 如果沒有做 Algorithm 的 4 & 5 步，在計算 total facility cost 的時候，對 <span>$\in N_j$</span>中的 facility ，便不能只看 <span>$k$</span> 而已(因為可能對其他 client 最便宜的 facility 會在之後被開起來)，如此要寫出其 bound 便不太簡單了... 直覺來想也是，既然 facility 都開了，儘量讓他去 serve 更多人，而不是單為每一個 client 去開對應的 facility (還要再加上 connection cost，可能都不是最優了...)，還不如將那些跟 facility **不太遠** 的 client 以 6 倍的 approx. ratio 連起來(Note: 不太遠這件事，由 <span>$N_j \bigcap N \scriptstyle j^{\prime}$</span> <span>$ \not = \varnothing$</span> 看出，而之所以可行，則是因為三角不等式 somehow 反應了概念上的 **距離** ，既然交集非空，代表離 <span>$j^\prime$</span> **算近** 的一些 facility 也落在 <span>$N_j$</span>，表示其跟 <span>$j$</span> 不遠，既然跟 <span>$j$</span> 不遠，也不會跟 <span>$k$</span> 太遠，所以再證 connection cost 的時候，其中一個不等式的推導也用到此條件)

``$$
\Rightarrow \, \text{Total cost} \, \leq 6 \, \color{red}{\text{OPT}_{LP}} \leq \, 6 \, \mathbf{OPT}
$$``

**Improvement**: 

將原先 <span>$N_j$</span> 定義中的 2 倍圈改為 <span>$\beta$</span> 倍。

``$$
\text{Total cost} \, \leq \, \underset{\text{改動 Markov Ineq. 的係數}}{\underbrace{\frac{\beta}{\beta - 1}\sum_{i \in F}y_i^{\star}f_i}}  + \underset{\beta \, \text{倍圈}}{\underbrace{3 \beta \sum c_{ij}x_{ij}^{\star}}}
$$``

Let <span>$\frac{\beta}{\beta - 1} = 3 \beta$</span>，可得 <span>$\beta = \frac{4}{3}$</span>，代入得到 Approx. Ratio 為 <span>$\textcolor{red}{4}$</span> 。
