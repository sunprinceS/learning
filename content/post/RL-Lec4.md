+++
date = "2019-01-06T15:59:24+08:00"
description = "Value-Based Algorithm - Training tips"
tags = ["RL","NTU","CS294"]
title =  "Reinforcement Learning - Lec4"
topics = ["Reinforcement Learning"]
+++

在這講中，要討論的是 non-tabular case 的問題 (就是上一講中無法保證收斂的那些QQ)，我們選定 Neural Network 作為拿來 approximate <span>$Q(s,a)$</span> 的 function family，且不是用一般 regression 的方法找 <span>$\phi$</span> (畢竟它的 target <span>$y$</span> 也只是中間產物，並非 optimal Q)，而是 N-step 的 gradient descent。

<!--more-->

## Online Q-learning Alg. (N = 1)

1. Take action to collect data 
2. <span>$y\_i = r(s\_i,a\_i) + \gamma \max\_{a^\prime} Q\_\phi (s_i^\prime,a_i^\prime)$</span>
3. <span>$\phi \leftarrow \phi - \alpha \frac{dQ\_\phi}{d\phi}(s\_i,a\_i)(Q_\phi(s\_i,a\_i) - y\_i)$</span>

### Issue

* <span>$y\_i$</span> relate to current param <span>$\phi$</span>
  <span>$\rightarrow$</span> non-stable

* Q value 通常會 overestimate (因 max 的選擇，會 prefer overestimate 的 noise)

## Double DQN (Target Network)

簡單來說，就是使用兩個 Q value NN，一個用來產生 target <span>$y\_i$</span> ，一個用來選擇 action ，實務上不會真的 train 兩個 network，而是用不同時間點的 network 參數 <span>$\phi$</span>, <span>$\phi^\prime$</span> (等 <span>$\phi$</span> 更新過幾輪之後，再 copy 給 <span>$\phi^\prime$</span>)

<div>
\[
y = r(s,a) + \gamma Q_{\textcolor{red}{\phi^\prime}}(s^\prime, \arg\max_{a^\prime} Q_\textcolor{blue}{\phi}(s^\prime,a^\prime))
\]
</div>

### Some intuition

* 在更新的過程中， target 會是 fixed 的，有助於 training 的穩定性
* <span>$Q\_\phi$</span>, <span>$Q\_{\phi^\prime}$</span> 引進的 noise 同樣都是
  overestimate 的機率較原先小，更能準確的估 Q value

<center><img src="/img/post/double-dqn.png" width="80%" style="border-radius: 0%;"></center>

## Prioritized Replay

一般來說，為了 **batch training** 需要，除了同時放多個 worker 下去更新<span>$\phi$</span> 外，亦會將 sample 出的 training data collect 起來，存到 replay buffer 裡頭，每次 sample 一個batch 出來 update Q network 

除了 uniformly sample from replay buffer 之外，也可以用以下 criteria 來決定sample 的 preference

* large TD error 者 (需要加強訓練的 sample)
* large Q value 者 (imply 好的經驗)


## Multi-step

balance between TD (step = 1) and MC (step <span>$\rightarrow \infty$</span>)

## Noisy Net

Instead of <span>$\epsilon$</span>-greedy when choosing action, add noise to <span>$Q_\phi$</span> (will explore in consistent way)

<!--## Distributional Q-function-->

## Reference

* [CS294 - Lec8] (http://rail.eecs.berkeley.edu/deeprlcourse/static/slides/lec-8.pdf)
