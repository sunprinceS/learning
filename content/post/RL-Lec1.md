+++
date = "2019-01-01T11:59:24+08:00"
description = "Policy-based Algorithm"
tags = ["RL","NTU","CS294"]
title =  "Reinforcement Learning - Lec1"
topics = ["Reinforcement Learning"]
+++

今年1 月的目標想複習三下時學的 RL，主要的參考教材為李宏毅老師的 DRL 8 講及 Sergey 在
Berkeley 開的 CS294。

先讓我們從 Policy Gradient 開始吧！

<!--more-->

## Terminology

這裡假設讀者熟悉 RL 的基本概念 (env/action/state 的交互作用及相關名詞)。

### Policy
Policy <span>$\pi$</span> 為 given state <span>$s$</span>，採取 action <span>$a$</span> 的機率。而我們可以以一組參數 <span>$\theta$</span> 去描述。

<div>
\[
\pi_\theta(a,s) = \mathbb{P}[a|s]
\]
</div>

Given 一個固定的 policy 的 agent (或者又叫做 actor)，我們讓其去與環境互動，每玩一次(一個 episode) 會收集到一個序列 {<span>$s_1,a_1,\cdots,s_T,a_T$</span>}，稱為 trajectory <span>$\tau$</span>，並會得到一個 final reward <span>$r(\tau)$</span>

### Value Function
**Given policy**, expected return from state <span>$s$</span>

<div>
\[
V^\pi(s) = \mathbb{E}_\pi[r(\tau) | s_t = s]
\]
</div>

**Note:** <span>$\pi$</span> 是指 Given policy，特定 trajectory 被 sample 出來的機率

### Action-Value Function
**Given policy**, expected return when taking <span>$a$</span> at <span>$s$</span> ，之後稱其為 Q function

<div>
\[
Q^\pi(s,a) = \mathbb{E}_\pi[r(\tau)|s_t = s, a_t = a]
\]
</div>

## Goal
我們想找到一個最好的 policy 去 maximize 最後得到 reward 的期望值 ( optimize 的目標)

<!--### Evaluation Problem (Optimal value function)-->
### Optimal value function
<div>
\[
\begin{aligned}
V_{\ast}(s) &= \max_\pi V_\pi(s) = \max_a Q_{\ast}(s,a)\\
Q_{\ast}(s,a) &= \max_\pi Q_\pi(s,a) = r(s,a) + \sum_{s^\prime}
\mathbb{P}[s^\prime|a,s] V_{\ast}(s)
\end{aligned}
\]
</div>

**Note: exploration 只在 training 的時候做，一旦摸清整個環境(i.e optimal policy
is claimed)**，在決定時便不用再加上隨機性了 (so that's why I directly write down
<span>$\max$</span>)

### Decoding Problem (how to construct such policy)

Only considered in non policy-based method, more discussion on <span>$\rightarrow$</span> Lec3

### Learning Problem

而後續討論的演算法，都是圍繞著如何找出這樣符合optimal value equation 的 policy。

* DP-based **Planning**: value iteration, policy iteration
  * Discrete action/state and <span>$|\mathcal{A}|,|\mathcal{S}|$</span> is small (i.e tabular): \
  * MDP is known
  * **Contraction Mapping Theorem** 可以 prove 最佳解的存在性) <span>$\rightarrow$</span> see Lec3
* Value-based **Learning**: Monte-Carlo RL, Temporal-Difference (bootstrapping) RL
  * In generate, discrete action (since we need to choose argmax from
    <span>$\mathcal{A}$</span>)
  * MDP is unknown
  * Tradeoff between biased or not and low/high variance
* Policy-based **Learning**: REINFORCE
  * Continuous action/state (since we usually use NN as hypothesis set)
  * MDP is unknown
  * Variance reduction trick (see <span>$\rightarrow$</span> Lec2)
  * Combined with value-based algorithm: Actor-Critic (see <span>$\rightarrow$</span> Lec5)

<!--## Evaluation Problem: Goodness of <span>$\pi$</span>-->

<!--那現在我們的目標是要找出一組最好的 <span>$\theta^{\ast}$</span>。\\-->
<!--而所謂最好，則是要去 maximize final reward，也就是以下-->

<!--``$$-->
<!--\mathbb{E}_{\tau \; \sim \; \pi_\theta(\tau)} [r(\tau)] = \int_\tau-->
<!--\pi_\theta(\tau) r(\tau) d\tau-->
<!--$$``-->


## How to update <span>$\theta$</span> in Policy Gradient

Optimal policy 所對應到的 <span>$\theta$</span> 滿足以下

<div>
\[
\theta^{\ast} \, = \,  \arg \max_\theta \mathbb{E}_{\tau \sim \pi_\theta} r(\tau) \, = \, \arg \max_\theta \sum_t [\mathbb{E}_{(s_t,a_t) \sim \pi_\theta(s,a)} [r(s_t,a_t)]]
\]
</div>


利用 **Graient Descent**。

``$$
\begin{align}
\nabla_\theta J(\theta) &= \int \color{blue}{\nabla_\theta[\pi_\theta(\tau)]} r(\tau) d\tau\\
&= \int \color{blue}{\pi_\theta \nabla_\theta[\log \pi_\theta(\tau)]}r(\tau) d\tau \\
&= \mathbb{E}_{\tau \; \sim \; \pi_\theta(\tau)} \nabla_\theta[\log\pi_\theta(\tau)]r(\tau) \\
\end{align}
$$``


直覺來說，相同於傳統 supervised learning 的 setting ，但更新的步伐大小，跟 reward 成線性關係\\
(i.e try & error，哪個方向好就走多一點，weighted by reward)

但上述的 <span>$\nabla_\theta J(\theta)$</span>無法很直覺地用於 policy 的更新，所以我們做了以下改動

* Policy Gradient Theorem 告訴我們

For any differentialble policy <span>$\pi_{\theta}(a,s)$</span>, for any of the policy objective functions <span>$J = J_1,J \scriptstyle avR$</span>...,\

<div>
\[
\nabla_\theta J(\theta) = \mathbb{E}_{\pi_\theta}[\nabla_\theta \log \pi_\theta(s,a)Q^{\pi_\theta}(s,a)]
\]
</div>

* 我們是對每一個 <span>$(a,s)$</span> 做更新 (因 <span>$\pi$</span> 能管的也只有 Given <span>$s$</span>，選擇採取 <span>$a$</span> 的機率)

<div>
\[
\nabla_\theta [\log \pi_\theta (\tau)] = \sum_t \nabla_\theta [\log \pi_\theta
(a_t , s_t)]
\]
</div>

* 在大多數情況，無法窮舉所有可能的 <span>$\tau$</span> 去求期望值，只能用
  sampling 去估計

<div>
\[
\boxed{
\nabla_\theta J(\theta) \approx \sum_n \sum_t \nabla_\theta[\log\pi_\theta(a_t,s_t)]\underbrace{\,r(\tau)\,}_{
Q^{\pi_\theta}(s,a)}}
\]
</div>

**Note**: 在最基本的 policy gradient 中，我們用 <span>$r(\tau)$</span> 來作為<span>$Q^\{\pi_\theta}(s,a)$</span> 的 unbiased estimation。

### Drawback

* Online policy，need to resample when <span>$\theta$</span> is updated
  <span>$\Rightarrow$</span> Data collection 費時
* Sample is finite <span>$\Rightarrow$</span> variance 大，難 train 。

## Reference

* [Policy Gradient Theorem 的證明](https://papers.nips.cc/paper/1713-policy-gradient-methods-for-reinforcement-learning-with-function-approximation.pdf)
