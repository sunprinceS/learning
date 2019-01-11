+++
date = "2019-01-05T08:59:24+08:00"
description = "Value-Based Algorithm - MC & TD"
tags = ["RL","NTU","CS294"]
title =  "Reinforcement Learning - Lec3"
topics = ["Reinforcement Learning"]
+++

前兩講 focus 在 policy-based 的 RL 演算法，直接 learn 一組參數去 parametrize
policy。而後續兩講則會 focus 在 value-based 的方法，想法是對每個 <span>$(s,a)$</span> 賦予一個好度值 (同樣可以 parametrized by <span>$\theta$</span>)，而所對應的 policy 則是去選擇 Given <span>$s$</span>，好度最高的 action <span>$a$</span> (w/ appropriate exploration)。

<!--more-->

## Basic Idea

## Monte-Carlo Learning

## Temporal Difference Learning

## Reference
<!--* [李宏毅老師的投影片] (http://speech.ee.ntu.edu.tw/~tlkagk/courses/MLDS_2018/Lecture/PPO%20(v3).pdf)-->
