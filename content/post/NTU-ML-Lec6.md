+++
date = "2018-01-02T19:02:37+01:00"
description = "Theory of Generalization"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec6"
topics = ["Machine Learning Foundation"]
+++

前一講中講述了 break point 的存在，壓制了 growth function <span>$m_H(N)$</span> 的成長速度，於這一講中，我們想嚴格地說明只要存在 break point ，<span>$m_H(N)$</span> 便會是 **POLY(N)** 。

<!--more-->

## Bounding function <span>$B(N,k)$</span>

為了方便討論各種不同的 hypothesis set ，我們僅利用 break point 去描述一個 hypothesis set 
(做點分群這樣。)，而 <span>$B(N,k)$</span> 為其 upper bound ，表示在 <span>$N$</span> 筆資料點中，任意 <span>$k$</span> 個點不能被 shatter 的條件下， dichotomies set size <span>$m_H(N)$</span> 最大能為多少。

<div>
\[
B(N,k) \geq m_H(N) \quad \forall \, \mathcal{H} \, \text{with break point at} \, k
\]
</div>

而現在我們要來填 <span>$B(N,k)$</span> 這個 table ，從中找一些規律幫助我們證明
<span>$B(N,k) \in \, \mathbf{POLY}$</span>。

### Trivial case

<img src="/img/post/b_table1.png" width="70%" style="border-radius: 0%;">

* when <span>$k = 1$</span>， <span>$B(N,1) = 1$</span>
* when <span>$N < k$</span>， <span>$B(N,k) = 2^N$</span> (the break point condition is useless)
* when <span>$N = k$</span>， <span>$B(N,k) = 2^N - 1$</span> (just remove arbitary 1 dichotomy)

### Inductive case N > k

就像 DP 在做的事情，我們希望能透由前方的 entry (e.g <span>$B(N-1,?), B(?,k-1)$</span> 去連結 <span>$B(N,k)$</span>)。\\
舉 <span>$B(4,3)$</span> 為例，我們可以找到一組 size 最大的 dichotomies set <span>$\mathcal{H}(\mathbf{x}_1, \mathbf{x}_2, \cdots, \mathbf{x}_N)$</span>，並對它根據 <span>$\mathbf{x}_1 \sim \mathbf{x}_3$</span>是否成對出現做分類，如以下：

<img src="/img/post/b_table2.png" width="70%" style="border-radius: 0%;">

並分別令 $2\alpha, \beta$ 為其 dichotomy 的數量

<img src="/img/post/b_table3.png" width="35%" style="border-radius: 0%;">


對此分成兩種 case 討論：

**Case1**: 僅看 <span>$\mathbf{x}_1 \sim \mathbf{x}_3$</span>，重複出現的只看一
遍，根據 任意 <span>$k$</span> 點不能被 shatter 這個條件，我們知道 <span>$\alpha
+ \beta \leq B(3,3)$</span> (或者說 <span>$\textcolor{orange}{B(N-1,k)}$</span>)。

**Case2**: 看那些 <span>$\mathbf{x}_1 \sim \mathbf{x}_3$</span> 成對出現的
dichotomies ，其 <span>$\mathbf{x}_4$</span> 必是一正一反，是故在 <span>$\mathbf{x}_1 \sim \mathbf{x}_3$</span> 中，不能有任意 <span>$k-1$</span> 個點可被 shatter (否則搭配 <span>$\mathbf{x}_4$</span> ， <span>$k$</span> 個點便可被 shatter 了)。所以有 <span>$\alpha < B(3,2)$</span> (亦即 <span>$\textcolor{purple}{B(N-1,k-1)}$</span>)

綜合以上，得到 <span>$B(4,3) = \textcolor{orange}{2 \alpha} + \textcolor{purple}{\beta} \leq \textcolor{orange}{B(3,3)} + \textcolor{purple}{B(3,2)}$</span>

<div>
\[
\boxed{B(N,k) \leq B(N-1,k) + B(N-1,k-1)}
\]
</div>

### Sauer's Lemma

``$$
B(N,k) \leq \sum_{i=0}^{k-1} \binom{N}{i}
$$``

**proof**:
用數學歸納法簡單可證

而據此，只要 break point <span>$k$</span>存在，我們便有

<div>
\[
m_H(N) \leq B(N,k) \in \mathcal{O}(N^{k-1}) \in \mathbf{POLY(N)}
\]
</div>

### Lemma

<div>
\[
B(N,k) \in \mathcal{\Theta}(N^{k-1})
\]
</div>

**proof**:

先證 <span>$B(N,k) \geq \sum_{i=0}^{k-1} \binom{N}{i}$</span>，

首先構造一個 任意 <span>$k$</span> 點不被 shattered 的 dichotomies set，\\
把 <span>$B(N,k)$</span> summation 拆開，可以寫為 <span>$\binom{N}{0} + \binom{N}{1} + \cdots + \binom{N}{k-1}$</span>，分別代表了將 label `x` assign 到 <span>$i$</span> 個數據點的可能 (對應到一種 dichotomy)，而根據這樣的構造方式，沒有一個 dichotomy 會將 <span>$k$</span> 個數據點都 label 成 `x` (至多 <span>$k-1$</span>)，而若想 shatter <span>$k$</span> 個數據點的話，我們需要有它們 `o`, `x` 的各種組合 (但今天全 `x` 的不存在)，所以如此的 dichotomies set 滿足限制，當然或許可以再增加新的 dichotomy (但根據前個部份的討論，也是知道不行啦XD)，總之我們證明了以下敘述正確

<div>
\[
B(N,k) \geq \sum_{i=0}^{k-1} \binom{N}{i}
\]
</div>


## Vapnik-Chervonenkis (VC) bound

<div>
\[
\boxed{\Pr[\exists \, h \in \mathcal{H} \: s.t. \: |E_i(h) - E_o(h)| > \epsilon] \leq 2 \cdot \textcolor{red}{2}m_H(\textcolor{blue}{2}N) \cdot \exp(-2 \cdot \textcolor{purple}{\frac{1}{16}}\epsilon^2N)}
\]
</div>

when <span>$\textcolor{red}{\text{N is large enough}}$</span>


### Step1: <span style="color: red">Replace <span>$E_o$</span> by <span>$E_i^{\prime}$</span></span>
詳細的證明我還是有看沒有懂😅 ...\\
要如此做的精神在於 <span>$E_i(h)$</span> 可能的值，只有 <span>$m_H(N)$</span> 個，但 <span>$E_o(h)$</span> 的值卻有無限個，而這不利於後續的分析，所以我們以跟 sample <span>$\mathcal{D}$</span> 時一樣的 distribution 去 sample 出新的 <span>$N$</span> 個數據點 <span>$\mathcal{D}^{\prime}$</span>，作為 validation data (**有限**個)，以 <span>$h$</span> 在其上的表現 <span>$E_i^{\prime}(h)$</span> 來 estimate <span>$h$</span> 在未知 data 上的表現 <span>$E_o(h)$</span> 。

簡單來說就是，我們想利用 <span>$|E_i(h) - E_i^{\prime}(h)|$</span> 很大的機率去bound 住 <span>$|E_i(h) - E_o(h)|$</span>很大的機率

<div>
\[
\boxed{\Pr[ \, \exists \, h \in \mathcal{H} \; s.t. \:
|E_i(h) - E_o(h)| > \epsilon] \leq \textcolor{red}{2}\Pr[\, \exists \, h \in \mathcal{H} \; s.t. \:
|E_i(h) - \textcolor{red}{E_i^{\prime}(h)}| > \textcolor{red}{\frac{\epsilon}{2}}]}
\]
</div>

Given <span>$h$</span> ，<span style="color:red">當 <span>$N$</span> 很大</span>的時候， <span>$E_i(h)$</span>及 <span>$E_i^{\prime}(h)$</span> <span>$\sim \mathcal{N}(E_o(h),1)$</span>

我想等價於給定兩個常態分佈 <span>$X,Y \sim \mathcal{N}(0,1)$</span> ，想問
<span>$\Pr[|X-Y| > \epsilon]$</span> 的一些不等式，以 <span>$\Pr[|X| > c \cdot \epsilon]$</span> 表示
之。

### Step2: <span style="color: blue">Decompose <span>$\mathcal{H}$</span> by kind</span>
承 step1，我們現在的 dichotomies 是作用在 <span>$\textcolor{blue}{2}N$</span> 個
數據點上 (無論 <span>$h$</span> 是踩到 training 還是 validation data 的雷，都算是 **BAD**)

### Step3: <span style="color: purple">Use Hoeffding w/o replacement</span>
Hoeffding Ineq. 亦會給出與W/ replacement 相同的結果，但現在我們可以算出那個平均錯誤率 <span>$\mu$</span> 到底為多少，就是 <span>$E_i, E_i^{\prime}$</span> 的平均，
所以稍微改動一下Step1 得到的式子

<div>
\[
|E_i(h) - E_i^{\prime}(h)| > \frac{\epsilon}{2} \; \Leftrightarrow \; |E_i(h) -
\underset{\textcolor{purple}{\mu}}{\underbrace{\frac{E_i(h) + E_i^{\prime}(h)}{2}}}| > \textcolor{purple}{\frac{\epsilon}{4}}
\]
</div>


## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/06_handout.pdf)
