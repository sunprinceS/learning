+++
title = "Meta Learning - Lec1"
date = "2020-02-09T17:04:09+08:00"
draft = true
tags = ["Meta Learning"]
topics = ["Meta Learning"]
description = "Introduction to Meta Learning"
+++

<!--more-->

<!--## Roadmap-->

<!--* Motivation-->
<!--* Some basics-->

## Motivation

在機器學習當道的今天，如影像/ 語音辨識甚至是下棋，玩Atari 等不涉及人類語言理解的任務，在資料量足夠的情況下，目前的演算法已經超越了人類水準。然而，相較於人類學習，機器的學習是完全沒有效率的，在 supervised learning 的框架下，它無法利用先前學習某些技能的經驗，讓自己在學習新任務上的效率更高，往往還是需要非常大量的資料才能將一個技能給重新學起，但這樣的訓練往往曠日費時，資料收集的成本也相當昂貴，因此探討如何利用善加已習得的技能，成了目前相當熱門的研究主題，也是這個系列所要探討的 - Meta Learning 。

## Meta Learning
Meta Learning，又稱為元學習，或者說「學習如何學習」，是建構於學習單一任務之上的一種想法。舉例來說，我們將習得一個技能/任務 (task) 描述為給定一個 dataset <span>$\mathcal{D}$</span>，及一個 loss function <span>$\mathcal{L}$</span>，所得到的一個模型 <span>$f_\theta$</span> ，那 meta Learning 想解決的問題是，給定很多這樣子的 task 們，在看到新的 task 的時候，能否(1) 用更少的資料, (2) 學的更好 (相比於沒有給前述 task 的情況)。什麼是 meta learning 其實也還眾說紛紜，廣義來說，想達成前述目標的方法，我認為都可以歸在其下，以下列舉幾個名詞

* Transfer Learning: Based on the learned model <span>$f_{\theta_i}$</span>, then transfer to target task (<i>sequential</i>)
* Self-supervised Learning: Utilize large unlabeled <span>$\mathcal{D}$</span> to exploit some hidden structure
* Multitask Learning: Learn multiple task $(\mathcal{D}_i, \mathcal{L}_i)$at the same time to get a better model (more on next section)

注意以上方法並非互斥， 只是略提每個方法的精要，如 multitask 的一個 task 可以是self-supervised 的 objective ，multitask learn 出來的 model 可以拿來 transfer 到新的 task 等等。

### Multitask Learning
這裡介紹一個我覺得位階與狹義的 meta learning 其中一支相同的方法 - multitask learning

如同前面的介紹提到的， multitask learning 就是同時訓練一個模型，而該模型可以完成複數個任務，這麼做除了可以避免我們對不同的任務 (儘管他們可能非常相似) 就要 train 一個模型，另外還有一些好處 (from [An Overview of Multi-Task Learning in Deep Neural Networks](https://arxiv.org/abs/1706.05098))

* Implicit data augmentation (任務之間可以共享 data)
* Attention focusing (透由設計不同 objective，去 guide model 的學習)
* Regularization (所 learn 出來的 model 是 generally 在 training task 上都好的，不會只單單在一個 task 上學到一個 trivial 的解)

#### Formulation & Challenges

Denote a task <span>$T_i = \lbrace p_i(x), p_i(y|x), \mathcal{L}_i \rbrace$</span>， we want to find a model parametrized by <span>$\theta$</span> satisfy

<div>
\[
\min_\theta \sum_{i=1}^T \mathcal{L}_i(\mathcal{D_i}, \theta)
\]
</div>

* <span>$\theta$</span> 可以切成 shared 及 task-specific 的部份 (會是一個 multihead 的架構，前面有 shared 的 encoder，搭配上 task-specific 的 head)，至於 shared 的部份，可以是 hard sharing (大家都一樣)，也可以是 soft sharing (thru weak constraint, e.g l2-norm)
* 怎麼去定義 task 之間的相似程度。或者更目標導向來說，怎麼確保 multi-learn 這些 task ，會比個別學還要好
* 怎麼訂每個 task 之間貢獻於 loss 的比例 (aka 係數)

### Back to Meta Learning
用前面提到的 multitask learning ，我們可以得到一個可以 perform 複數任務的 task，根據前面提到的 intuition，我們認為這個 model 相對來說是較沒有 overfit 在單一 training task ，也比較容易 transfer 到我們有興趣的 unseen task 上。 但 multitask 的 objective 只有保證該 model 在 training tasks 上可以表現的好，所 learn 到的參數可能不是適合 transfer 的，因此我認為所謂狹義的 meta learning，是在 evaluate 參數好度的時候，有把**之後要拿來 transfer** 這件事納入考慮 (去 match training/testing 的 scenario)。

Now, we split <span>$\mathcal{D}_i$</span> to <span>$\mathcal{D}^{tr}_i, \mathcal{D}^{te}_i$</span>. How can we use bunch of such <span>$D_i$</span> (unioned to be "meta-train") to learn a parameter (**meta-learning phase**) that can perform well on target task (adaptation phase)

* Meta-learning: <span>$\theta^\star = \arg \max_\theta \log p(\theta | \mathcal{D}_{\text{meta-train}})$</span>
* Adaptation: <span>$\phi^\star = \arg \max_\phi \log p(\phi | \mathcal{D}^{tr}, \theta^\star)$</span>

How can we condition on <span>$\theta^\star$</span> to find <span>$\phi^\star$</span>? That is, <span>$\phi^\star = f_{\theta^\star}(\mathcal{D}^{tr})$</span>

* Multitask learning (mentioned above): <span>$\theta$</span> is model weights of multask-trained model, <span>$f_\theta$</span> is SGD starting at <span>$\theta^\star$</span>
* Hyper-parameter optimization: <span>$\theta$</span> is hyperparam, <span>$f_\theta$</span> 為 hyper-param 為 <span>$\theta$</span> 的 model
* Network arch search (NAS): <span>$\theta$</span> is network arch., <span>$f_\theta$</span> 為架構長那樣的 model
* Black-box Adaptation: <span>$\theta$</span> is the parameter of outer model, this model will output the parameter based on the dataset it received (That
  is, <span>$\phi_i = f_\theta (\mathcal{D}^{tr}_i)$</span>)
* Optimization-based meta-learning (概念與 multiask learning transfer 相同，只差在 meta-learning phase 的 objective 是否有將要 transfer 這件事 model 進去)
