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

這裡假設讀者熟悉 RL 的基本概念 (env/action/state 的交互作用及相關名詞)。\\
所謂的 policy <span>$\pi$</span> 是 Given state <span>$s$</span>，採取 action
<span>$a$</span> 的機率。而我們以一組參數 <span>$\theta$</span> 去描述。

<div>
\[
\pi_\theta(a,s) = \mathbb{P}[a|s]
\]
</div>

Given 一個固定的 policy 的 agent (或者又叫做 actor)，我們讓其去與環境互動，每玩一次(一個 episode) 會收集到一個序列 {<span>$s_1,a_1,\cdots,s_T,a_T$</span>}，稱為 trajectory <span>$\tau$</span>

## Evaluation Problem: Goodness of <span>$\pi$</span>

那現在我們的目標是要找出一組最好的 <span>$\theta^{\ast}$</span>。\\
而所謂最好，則是要去 maximize final reward，也就是以下

``$$
\mathbb{E}_{\tau \; \sim \; \pi_\theta(\tau)} [r(\tau)] = \int_\tau
\pi_\theta(\tau) r(\tau) d\tau
$$``

**Note:** <span>$\pi_\theta(\tau)$</span> 是指 Given policy，特定 trajectory 被 sample 出來的機率

## Learning Prolem: How to update <span>$\theta$</span> 

利用 **Graient Descent**。

``$$
\begin{align}
\nabla_\theta J(\theta) &= \int \color{blue}{\nabla_\theta[\pi_\theta(\tau)]} r(\tau) d\tau\\
&= \int \color{blue}{\pi_\theta \nabla_\theta[\log \pi_\theta(\tau)]}r(\tau) d\tau \\
&= \mathbb{E}_{\tau \; \sim \; \pi_\theta(\tau)} \nabla_\theta[\log\pi_\theta(\tau)]r(\tau) \\
&\approx \sum_n \nabla_\theta[\log\pi_\theta(\tau)]r(\tau) \quad (\text{sampling})
\end{align}
$$``

直覺來說，相同於傳統 supervised learning 的 setting ，但更新的步伐大小，跟 reward 成線性關係 (i.e try & error，哪個方向好就走多一點，weighted by reward)

而實務上更新的時候，我們是對每一個 <span>$(a,s)$</span> 做更新 (因 <span>$\pi$</span> 能管的也只有 Given <span>$s$</span>，選擇採取 <span>$a$</span> 的機率)

``$$
\nabla_\theta [\log \pi_\theta (\tau)] = \sum_t \nabla_\theta [\log \pi_\theta
(a_t | s_t)]
$$``

**Remark:** Conveninet identity <span>$\nabla f(x) = f(x) \nabla \log f(x)$</span>

**Remark:** 在最後的 sampling approximation 中，我們是以 <span>$r(\tau)$</span> (final reward) 來當作 <span>$Q^{\pi_\theta}(s,a)$</span>的估計值。之後的Actor-Critic Algorithm 會介紹其他估計值 (在 variance 跟 accuracy 與否間做 trade-off)。

## Policy Gradient 101 - REINFORCE algorithm
1. Fix policy, sample {<span>$\tau_i$</span>} from <span>$\pi(a|s)$</span>
2. calculate <span>$\nabla_\theta J(\theta)$</span>
3. <span>$\theta \leftarrow \theta + \alpha \nabla_\theta J(\theta)$</span>

### Drawback

* Online policy，need to resample when <span>$\theta$</span> is updated
  <span>$\Rightarrow$</span> Data collection 費時
* Sample is finite <span>$\Rightarrow$</span> variance 大，難 train

## Appendix
### Policy Gradient Theorem

For any differentialble policy <span>$\pi_{\theta}(a,s)$</span>, for any of the policy objective functions <span>$J = J_1,J \scriptstyle avR$</span>...,\\
the policy gradient is

<div>
\[
\nabla_\theta J(\theta) = \mathbb{E}_{\pi_\theta}[\nabla_\theta \log \pi_\theta(s,a)Q^{\pi_\theta}(s,a)]
\]
</div>

基本款我們利用 expected reward 來作為 <span>$Q^{\pi_\theta}$</span> 的 unbiased sample

## Reference

* [Policy Gradient Theorem 的證明](https://papers.nips.cc/paper/1713-policy-gradient-methods-for-reinforcement-learning-with-function-approximation.pdf)
