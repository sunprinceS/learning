+++
date = "2019-01-05T08:59:24+08:00"
description = "Value-Based Algorithm - MC & TD"
tags = ["RL","NTU","CS294"]
title =  "Reinforcement Learning - Lec3"
topics = ["Reinforcement Learning"]
+++

前兩講 focus 在 policy-based 的 RL 演算法，直接 learn 一組參數去 parametrize
policy。而後續兩講則會 focus 在 value-based 的方法，想法是算出 <span>$V^\pi(s), Q^\pi(s,a)$</span> (同樣可以 approximately parametrized by <span>$\theta$</span>)，而所對應的 policy 則是去選擇 Given <span>$s$</span>，好度最高的 action <span>$a$</span> (w/ appropriate exploration)。

<!--more-->

## Monte-Carlo Learning

回顧 Lec1，<span>$V^\pi(s)$</span> 的定義是 expected return from state <span>$s$</span>， Monte-Carlo 的想法是直接 simulate 一個 trajectory from state <span>$s$</span>，然後去 reward 是多少， Value function 每一個 episode 更新一次。

<div>
\[
V(s_t) \leftarrow V(s_t) + \alpha(r(\tau) - V(s_t))
\]
</div>


* Variance is high
* Unbiased estimate

## Temporal Difference Learning

Use **bootstrapping** to estimate final reward by <span>$r(s,a) + V(s \scriptscriptstyle t+1 \textstyle)$</span>，每走一步Value
function 都會更新

<div>
\[
V(s_t) \leftarrow V(s_t) + \alpha(\underbrace{r(s,a) + \gamma V(s_{t+1})}_{\text{estimated} \; r(\tau)} - V(s_t))
\]
</div>

* Variance is low
* Biased estimate
* Efficient (and even suitable for non-episodic game)

**Remark:** 

* 如此的迭代，又稱為 value iteration (如果過程中有 explicitly 表示出 policy ，又叫做 policy iteration)

* 如前所述，這裡的 policy 是根據 <span>$V(s \scriptscriptstyle t+1 \textstyle)$</span> 或 <span>$Q(s,a)$</span> 決定 (直接 greedily 選 argmax)

* 在 training 時，會希望增加 對 env. 的 exploration
  * <span>$\epsilon$</span>-greedy: 加上一個<span>$\epsilon$</span> 的機率選擇其他非 argmax 的 action
  * Boltzmann exploration: follow <span>$\exp(Q(s,a))$</span>，並可用 temperature <span>$T$</span> (a hyperparameter) 調整 exploration/exploition 的比重

* 用 <span>$V$</span> 做 criteria 的話，需要先知道 MDP 的 transition (但通常我們不知道)，\\
所以一般來說是利用 <span>$Q^\pi(s,a)$</span> 當成 criteria，也是眾所周知的 **Q-learning** (Q-value iteration) 

<div>
\[
Q(s_t,a_t) \leftarrow Q(s_t,a_t) + \alpha (r(s_t,a_t) + \gamma Q(s_{t+1},a_{t+1}) - Q(s_t,a_t))
\]
</div>


* 一般在 <span>$|\mathcal{S}|$</span>, <span>$|\mathcal{A}|$</span> 不小的情況下，會用 function family (parameterized by <span>$\phi$</span>) 去表示 <span>$Q(s,a)$</span>，而如何找到 <span>$\phi$</span> 則是一個 regression problem

<div>
\[
\phi \leftarrow \arg \min_\phi \sum_n \sum_t ||Q_\phi(s^n_t,a^n_t) - y^n_t||
\]
</div>


**Note:** <span>$\alpha$</span> 表示 value 隨新 data 更新的敏感度

## Convergence to optimal policy (tabular case)

條件:

* MDP is known
* <span>$|\mathcal{S}|, |\mathcal{A}|$</span> is small

**Remark** 

所對應的 optimal value function 為下列 equation (先不管 <span>$\alpha$</span>)

<div>
\[
\begin{aligned}
V_{\ast}(s) &= \max_\pi V_\pi(s) = \max_a Q_{\ast}(s,a)\\
Q_{\ast}(s,a) &= \max_\pi Q_\pi(s,a) = r(s,a) + \sum_{s^\prime}
\mathbb{P}[s^\prime|a,s] V_{\ast}(s)
\end{aligned}
\]
</div>


* 由上方 equation 可知 <span>$Q\_\ast$</span>  是上述 TD learning iteration operator <span>$\mathcal{B}$</span> 的 fixed point (前提是知道 <span>$\\mathbb{P}[s^\prime|s,a]$</span>)，<span>$Q\_\ast = \mathcal{B} Q\_\ast$</span>

* <span>$\mathcal{B}$</span> is a *contraction*, <span>$||\mathcal{B}Q -
  \mathcal{B} \bar{Q}||\_{\infty} \leq \gamma ||Q - \bar{Q}||\_{\infty}$</span>

由 **Contraction Mapping Theorem** 可知，<span>$Q(s,a)$</span> 會以<span>$\gamma$</span> 的 rate converge 到 <span>$Q\_\ast$</span>

## Non-convergence to optimal policy (approx. <span>$Q\_\phi(s,a)$</span> case)

現在我們把一個條件放寬，假設 <span>$|\mathcal{S}|$</span>, <span>$|\mathcal{A}|$</span> 大到無法以 table 儲存，只能以一個 function family 去表示 <span>$Q\(s,a)$</span>

在做 regression 找當前 iteration 最好的 <span>$\phi$</span> 時，其實引進了一個 projection operator <span>$\Pi$</span> \
(project on function family, **with l2-norm**)

可以證明 <span>$\Pi$</span> 也是一個 *contraction* (w.r.t l2-norm)，但
<span>$\mathcal{B}$</span> 卻是 contraction w.r.t <span>$\infty$</span>-norm \
所以 <span>$\mathcal{B}\Pi$</span> 並不是 contraction，也就不保證會收斂。

**Remark:** 有人可能會想說，那這樣不如就把 regression 改成 w.r.t <span>$\infty$</span>-norm  的 optimization 問題，但這樣的 optimization 是 intractable 的 (belong to NPC)

