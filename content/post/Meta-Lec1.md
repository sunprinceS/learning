+++
title = "Meta Learning - Lec1"
date = "2020-02-09T17:04:09+08:00"
draft = false
tags = ["Meta Learning"]
topics = ["Meta Learning"]
description = "Introduction to Meta Learning"
+++

<!--more-->

<!--## Roadmap-->

<!--* Motivation-->
<!--* Some basics-->

## Motivation

在機器學習當道的今天，如影像/ 語音辨識甚至是下棋，玩Atari 等不涉及人類語言理解的任務，在資料量足夠的情況下，目前的演算法已經超越了人類水準。然而，相較於人類學習，機器的學習是完全沒有效率的，在 supervised learning 的框架下，它無法利用先前學習某些技能的經驗，讓自己在學習新任務上的效率更高，往往還是需要非常大量的資料才能將一個技能給重新學起，但這樣的訓練往往曠日費時，資料收集的成本也相當昂貴，因此探討如何利用已習得的技能，成了目前相當熱門的研究主題，也是這個系列所要探討的 - Meta Learning 。

## Meta Learning
Meta Learning，又稱為元學習，或者說「學習如何學習」，是建構於學習單一任務之上的一種想法。舉例來說，我們將習得一個技能/任務 (task) 描述為給定一個 dataset <span>$\mathcal{D}$</span>，及一個 loss function <span>$\mathcal{L}$</span>，所得到的一個模型 <span>$f_\theta$</span> ，那 meta Learning 想解決的問題是，給定很多這樣子的 task 們，在看到新的 task 的時候，能否(1) 用更少的資料, (2) 學的更好 (相比於沒有給前述 task 的情況)。什麼是 meta learning 其實也還眾說紛紜，廣義來說，想達成前述目標的方法，我認為都可以歸在其下，以下列舉幾個名詞

* Transfer Learning: Based on the learned model <span>$f_{\theta_i}$</span>, then transfer to target task (<i>sequential</i>)
* Self-supervised Learning: Utilize large unlabeled <span>$\mathcal{D}$</span> to exploit some hidden structure
* Multitask Learning: Learn multiple task $(\mathcal{D}_i, \mathcal{L}_i)$at the same time to get a better model (bias cancelling)

注意以上方法並非互斥， 只是略提每個方法的精要，如 multitask 的一個 task 可以是self-supervised 的 objective ，multitask learn 出來的 model 可以拿來 transfer 到新的 task 等等。

### Multitask Learning
這裡介紹一個我覺得位階與狹義的 meta learning 相同的方法 - multitask learning
