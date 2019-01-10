+++
date = "2019-01-11T10:59:24+08:00"
description = "Actor-Critic Algorithm"
tags = ["RL","NTU","CS294"]
title =  "Reinforcement Learning - Lec7"
topics = ["Reinforcement Learning"]
+++

Lec1 - Lec4  分別介紹了 Policy-based 及 Value-based 的 RL algorithm ，而這一講要
介紹的 Actor Critic 則是同時用到了兩個演算法的部份，並在 biased 與否 (準不準) 及 variance
高低 (好不好 train) 提供一個可以調控的 hyperparameter 讓我們選擇。

<!--more-->

## Motivation

目前為止介紹的 RL 演算法，想法都是在利用**有限的 sample** 去估測於 state <span>$s$</span> 做出 <span>$a$</span> 這個 action 相對於其他 action 的好度，也就是 <span>$A^\pi(s,a)$</span> (想成 <span>$Q^\pi(s,a)$</span> with normalization)。

Policy-based 的方法，並不 explicit 算出這個值，但在 update <span>$\theta$</span> (which parametrize that policy) 時，利用了 final reward <span>$r(\tau)$</span> (with some modification like baseline and causality to reduce variance) implicit 地當作 <span>$Q(s,a)$</span> 的 sample，雖然是個 unbiased 的估計，但未來的變數太多， variance 太大的問題是 Policy Gradient 難 train 的原因。

而 Value-based 的方法，則是 explicitly 算出這個值，而所對應的 policy 即是<span>$\epsilon$</span>-greedy 地選擇 action (with some modification to make it
easier to train and **more unbiased**)，但缺點在於...(待補)，以及很難 generalize 到 continuous action/state 的問題上。

而 Actor-Critic 則是想結合兩者，一樣利用 <span>$\theta$</span> 去 parametrize policy，但利用 Value-based 的方法如 MC 或 TD 來近似 <span>$Q^\pi(s,a)$</span>

## Advantage Actor-Critic

原先 Policy Gradient 的  <span>$\nabla_\theta J(\theta)$</span>

``$$
J(\theta) = \sum_n \sum_t \nabla_\theta [\log \pi_\theta(a_t|s_t)]
\color{red}{[\sum_{t^\prime = t}^{T_n} \gamma^{t^\prime - t} r_{t^\prime}^n - b ]}
$$``

紅色部份的第一項為 期望值為 <span>$Q^\pi(s,a)$</span>，我們不妨就直接拿 Q value 來代表，而 normalize 用的第二項很直覺地用 <span>$V^\pi(s)$</span> 來代替 (在這個 state 做所有 action 的期望值)

**Remark:** 在估 <span>$Q,V$</span> 時，我們可以在 MD (#step <span>$\rightarrow \inf$</span>) 與 TD (#step=<span>$1$</span>)間，做 accurate 與 bias 的 tradeoff。

at that state)，待 survey！

### Fact

``$$
Q^\pi(s_t,a_t) = \mathbb{E}_\pi[r_t + V^\pi(s_{t+1})]
$$``

類似的概念，也可以用 Value Function 去 approx Q function。比如以下

``$$
Q^\pi(s_t,a_t) = r_t + V^\pi(s_{t+1})
$$``

以上是 1-step 的 case，很容易可以推廣到 multi-step (同樣是 bias 與 variance
的取捨)。

**Remark:** 也可以都用 <span>$Q$</span> function 來表示 ( 對應的 <span>$V$</span> 即是 Q value summation over action)

**Remark:** 實務上在 <span>$V^\pi(s_{t+1})$</span> 會加上一個係數 <span>$\gamma$</span> 作為 discount factor

綜合以上，以 1-step 的估計為例

``$$
J(\theta) = \sum_n \sum_t \nabla_\theta [\log \pi_\theta(a_t|s_t)]
\color{red}{[r(s_t,a_t) + \gamma V^\pi(s_{t+1}) - V^\pi(s_t))]}
$$``

**Remark:** <span>$\gamma$</span> 也可以看成是一個 reduce variance 的技巧，畢竟越未來的事變數越多，variance 越大，所以當前決定中，越久之後才得到的 reward 不該影響這時候的決定太多。

## Some tips



### Shared Feature of policy and value approximator
<center><img src="/img/post/aac.png" width="70%" style="border-radius: 0%;"></center>

### Async. Advantage Actor Critic (A3C)

平行化多個 agent 的學習，也是藉由增加 sample 量去減低 variance ，但參數的更新也會因 offline 的關係，出現 bias。

## Reference
* [李宏毅老師的投影片] (http://speech.ee.ntu.edu.tw/~tlkagk/courses/MLDS_2018/Lecture/AC.pdf.pdf)
