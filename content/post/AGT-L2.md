+++
date = "2017-05-19T09:51:12+08:00"
description = "Mechanism Design Basics"
draft = false
tags = ["Game Theory","Algorithm","Open Course","Stanford"]
title = "Algorithmic Game Theory - Lecture2"
topics = ["Algorithmic Game Theory"]

+++

Lecture1 提到了我們需要透由好的 mechanism，使得 agent 與 designer 的 incentive 彼此 aligned。這講從最簡單的 **Single-item Auction** 開始，探討該如何設計合理的 mechanism。

<!--more-->

### Single-Item Auction

1 seller wants to sell 1 good.


There are n strategic bidders want to buy the good with their own preferences (unknown to the seller).

在討論之前，我們先以簡單的 model 來描述 bidder 的行為。

Let **valuation** <span>$v_i$</span>: bidder i's max willingness-to-pay for the item 

定義 *quasilinear utility model* <span>$U$</span> 如以下

<div>
\[
U = \left\{ \begin{array}{ll}
           0, \text{if not win} \\
           v_i - p, \text{if wins at price} ~p
        \end{array} \right.
\]
</div>

而 bidder 的 objective is to maximize <span>$U$</span>

### Sealed-Bid Auction

對 designer 而言，需要設計適合的機制。而這裡所謂適合，即是能獲得整體[最大收益](#p2)(包括 seller 和 bidder)

而Sealed-Bid Auction 有以下幾個步驟:

1. Each bidder <span>$i$</span> **privately** communicates a bid <span>$b_i$</span> to the seller

2. The seller decide who can get the good

3. The seller decide the selling price

(2) 很直觀來想，就是**給出價最高的 bidder** (more selection rule will come later)，
這裡主要討論 (3), eBay的作法是以下的 second-price auction (得標者付第二高價的 bid 即可)。

### Second-Price (Vickery) Auction

1. [**strong incentive guarantees**] It is dominant-strategy incentive-compatible (**DSIC**)



2. <a name="p2"><font color="#777">[**computational efficiency**]</font></a> The auction can be implemented in <span>$\mathbf{POLY}$</span> time (indeed, we want it to be **linear**)

3. <a name="p3"><font color="#777">[**strong performance guarantees**]</font></a> If bidder report truthfully, then the auction maximizes the **social surplus**

<div>
\[
\sum\limits_{i=1}^n \mathbb{1}[\text{bidder i wins}],

\text{subject to } \sum\limits_{i=1}^n \mathbb{1}[\text{bidder i wins}] \lneq 1
\]
</div>

回顧 [Lecture1](http://localhost:1313/learning/2017/05/algorithmic-game-theory---lecture1/)，可以發現最一開始講到的 3 個部份一一對應 XD

從 bidder 的觀點來看， **DSIC** property，保證了誠實是最佳策略 (無論其他的 bidder 如何報價)，因此對 seller 來說，只要組成是**完全理性**的 bidder，便可預測其行為 (這裡先忽略 collusion 或非理性的情況 :p)。
#### <u>Claim</u> 

#### Every bidder has a dominant strategy: set its bid <span>$b_i = v_i$<span>, and guaranteed to have utility <span> $\gneq 0$</span>.
<!--現在我們想證明此策略可 maximize <span>$U$</span>。-->

考慮 bidder i, let <span>$B = max_{j \neq i}b_j$</span>

<div>
\[
\text{payoff of bidder i} = \left\{ \begin{array}{ll}
           v_i - B, \text{if} ~b_i > B \\
           0, \text{otherwise}
        \end{array} \right.
\]
</div>

若為了拿到 good，而高報 bid (i.e <span>$b_i > v_i$<span>)，有以下 cases:

1. <span>$B < v_i$</span> or <span>$B > b_i$</span>，與誠實報的 payoff 相同。

2. <span>$v_i < B < b_i$</span>， bidder 會贏得 good ，但 payoff 是負的☹，而誠實報的 payoff 為0 (然後輸掉)，故會選擇誠實。

同理，低報 bid 比不上誠實報 (直覺來想就是，低報可能使原先會贏的拍賣，到最後反而將商品拱手讓人 :p)

對那些沒拿到 good 的人， <span>$U = 0$</span>; 而贏家 (imply <span>$v \gneq B$<span>) 的 utility <span>$~U = v -B \gneq 0 ~\blacksquare$</span>

3 個 property的 [(3)](#p3) 可簡單地用 greedy 常見的方法證明，而 [(2)](#p2) 對seller而言只要簡單的掃過一遍 bid vector就可決定誰贏得 auction 及成交價格，複雜度為 <span>$\mathcal{O}(n)$</span>

#### 思考

另一種可能的定價方式為 **first-price auction**，在這個情況下， winner 需付自己所喊的價碼，所以其傾向於會低報，但問題是該低報多少，又跟其他 bidder 有關... (留著之後來想XD)
<!--若 <span>$v_i < B $<span>， bidder 會誠實 report <span>$b_i = v_i$</span> (事前不知道會輸，但如果低報，原先能拿到的 good 反而可能被搶走，所以想報越高越好); 若 <span>$v_i > B$</span>-->
