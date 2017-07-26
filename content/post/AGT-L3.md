+++
date = "2017-05-24T00:27:07+08:00"
description = "Myerson's Lemma"
draft = false
tags = ["Game Theory","Algorithm","Open Course","Stanford"]
title = "Algorithmic Game Theory - Lecture3"
topics = ["Algorithmic Game Theory"]

+++

上一講提到了怎樣的 auction 是設計者所希望看到的，有足夠的誘因使參與者做出我們希望看到的決策 (**DSIC** property)，且此決策所造成的結果為設計者認定的 optimal ，同時又能簡單到能在多項式時間完成。接下來會提供一個更 general 的方式去設計機制，而不單單只是限定於 single-item auction 而已。

<!--more-->

### Property of Awesome Auction

From [Lecture2](https://sunprinces.github.io/learning/2017/05/algorithmic-game-theory---lecture2/)

1. Incentive guarantee: DSIC property
2. Computational efficiency: **POLY** time algorithm
3. Performance guarantee: maximize the **social surplus**

在設計 mechanism 時，我們拆成以下步驟: 

1. 假設 bidder 會誠實報價，則我們該如何決定誰拿到 good (allocation rule) ，使得 (2), (3) 成立。
2. 假設存在上述 rule ，那該如何定價 (payment rule) 使得 (1) 成立。

而這個 lecture 探討的為 step2，同時也透露了怎樣的 allocation rule 才會使對應的 payment rule 存在。

### Single-Parameter Environments

首先我們將 single-item auction 推廣到更一般的 auction。

*  n bidders, with valuation <span>$v_i$</span> (<span>$v_i$</span> 表示 bidder 的 max willingness for **1 unit** stuff)
* feasible allocation <span>$X = ( x_1,x_2,\cdots,x_n ) $</span> (<span>$x_i$</span> 表示 i-th bidder 可以拿到幾單位的 stuff ， 在 single-item auction 中，X 為一個只有一個 slot 為 1，其他全 0 的 vector)

### Allocation and Payment Rules

**Sealed-bid auction** :

1. 收集 bids <span>$\vec{b} = (b_1, b_2, \cdots, b_n)$</span>
2. **[allocation rule]** Choose a feasible allocation <span>$\vec{x}(b) \in X \subseteq \mathbb{R}^n$</span>
3. **[payment rule]** Choose payments <span>$\vec{p}(\vec{b}) \in \mathbb{R}^n$</span>

Quasilinear utility model

<div>
\[
u_i(\vec{b}) = v_i \cdotp x_i(\vec{b}) - p_i(\vec{b}) ~~~~~\text{subject to} ~p_i(\vec{b}) \in [0 , \stackrel{\color{blue}{s.t \; U \, \geq \, 0}}{b_i \cdotp x_i(\vec{b})}] ~~\forall i
\]
</div>

<!--<span>$p_i$</span> 的 constraint 保證了 non-negative utility-->

<u>**Definition**</u>

**Implementable Allocation Rule**

> An allocation rule <span>$\vec{x}$</span> for single-parameter environment is **implementable**<br/> if <span>$~\exists$</span> payment rule <span>$\vec{p}$</span> such that the auction is **DSIC**

**Monotone Allocation Rule**

> An allocation rule <span>$\vec{x}$</span> for single-parameter environment is **monotone** <br/>
if for every bidder <span>$i$</span> and other bids <span>$b\scriptscriptstyle{-i}$</span>,
the allocation rule <span>$x_i(z,b\scriptstyle{-i}$</span><span>$)$</span> to <span>$i$</span> is **non-decreasing** in its bid <span>$z$</span>

直觀意義上就是說喊價喊的越高，可以得到較多的 stuff，稍後的定理會證明以上兩個關於 allocation 的性質是等價的。

### Myerson's Lemma

Fix a single-parameter environment

1. An allocation rule <span>$\vec{x}$</span> is implementable **iff** it is monotone
2. If <span>$\vec{x}$</span> is monotone, then <span>$\exists$</span> a **unique** payment tule such that the sealed-bid mechanism <span>$(\vec{x},\vec{p})$</span> is **DSIC** (WLOG, assume <span>$p_i(b_i) = 0$</span>)
3. The payment rule is given by explicit formula

### Proof

**利用 DSIC 的性質 (誠實為 dominant strategy)，去列出不等式**


 Fix bidder <span>$i$</span> and other bids <span>$b\scriptstyle{-i}$</span>，<span>$0 \leq y < z$</span>

> case 1: 'underbidding' (<span>$v_i$</span> 為 <span>$z$</span> , 實際喊價 <span>$y$</span>)

<div>
\[
  \stackrel{\color{blue}{U~ \text{of bidding}~ z}}{z \cdotp x(z) - p(z)} \quad \geq  \quad \stackrel{ \color{blue}{U~ \text{of bidding}~ y}}{z \cdotp x(y) - p(y)}
\]
</div>

> case 2: 'overbidding' (<span>$v_i$</span> 為 <span>$y$</span> , 實際喊價 <span>$z$</span>)

<div>
\[
  \stackrel{\color{blue}{U~ \text{of bidding}~ y}}{y \cdotp x(y) - p(y)} \quad \geq  \quad \stackrel{ \color{blue}{U~ \text{of bidding}~ z}}{y \cdotp x(z) - p(z)}
\]
</div>

> 在兩個 case 下都需要滿足，移項整理可得 

<div>
\[
\stackrel{\color{blue}{}}{z \cdotp [x(y) - x(z)] \enspace \leq \enspace \stackrel{\color{blue}{\Delta p}}{p(y) - p(z)} \enspace \leq y \enspace \cdotp [x(y) - x(z)]}
\]
</div>

> 利用夾擠定理，使得在某實數軸上每一點，[<span>$p(\cdotp)$</span>](#payment) 都只有唯一的值

> '<span>$\Rightarrow$</span>'  : <span>$\vec{x}$</span> is monotone <span>$\Rightarrow$</span> x is implementable: <br/>

>> Fix <span>$z$</span> and take the limit <span>$y \rightarrow z$</span>, 假設 <span>$\vec{x}(\cdotp)$</span> is piecewise constant function

>> If there is no jump in <span>$\vec{x}$</span> at <span>$z$</span>, <span>$\Delta p = 0$</span>

>> If there is a jump of magnitude <span>$h$</span> at <span>$z$</span>, &nbsp; (<span>$\Delta p$</span> at <span>$z$</span>) = (<span>$z \cdotp$</span> jump in <span>$x$</span> at <span>$z$</span>)

>> monotone 保證了 jump > 0，所以可以寫下 payment formula 為

<div>
\[
  p_i(b_i,b_{-i}) = \sum_{j=1}^{l} z_j \cdotp \text{jump in} \; x_i( \cdotp ,b_{-i}) \; \text{at} \; z_j
\]
</div>

>> 其中 <span>$z_1, z_2, \cdots, z_l$ 為 $x_i(\cdotp,$$b\scriptstyle{-i}$$)$</span> 在 <span>$[0, b\scriptstyle{-i}$$]$</span> 的跳躍點

>> <a name="payment"><font color="#777">[Note]</font></a> 可以將 payment formula 推廣到一般的 non-decreasing <span>$\vec{x}(\cdotp)$</span>

<div>
\[
  p_i(b_i,b_{-i}) = \int_0^{b_i} z \cdotp \frac{d}{dz} x_i (z, b_{-i})dz
\]
</div>

>> (2), (3)被證明

> '<span>$\Leftarrow$</span>': if <span>$\vec{x}$</span> is NOT monotone <span>$\Rightarrow$</span> x is NOT implementable (there is no way DSIC)

>> Just pick the points <span>$a < b$</span>, but <span>$x(a) > x(b)$</span> as <span>$y$</span> and <span>$z$</span>,<br/> then the inequality of <span>$\Delta p$</span> is broken


下圖以圖示證明 mechanism <span>$(\vec{x},\vec{p})$</span> is **DSIC** (the figure is from the [lecture](http://theory.stanford.edu/~tim/f13/l/l3.pdf))
{{% fluid_img src="/img/post/Myerson.png" alt="Myerson's Lemma" %}}
