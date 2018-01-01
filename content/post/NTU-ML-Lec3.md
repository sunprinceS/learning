+++
date = "2018-01-01T19:02:37+01:00"
description = "Types of Learning"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec3"
topics = ["Machine Learning Foundation"]
+++

這講建構了一個大略的 ML 世界觀。

<!--more-->

## Different Output Space
1. Binary classification (<span>$|\mathcal{Y}| = 2$</span>)
2. Multiclass classification (<span>$|\mathcal{Y}| = K$</span>)
3. Regression (<span>$\mathcal{Y} \subset \mathbb{R}$</span>)

## Different Label
1. Supervised (w/ explicit label)
2. Unsupervised (e.g clustering)
3. Semi-supervised (combine above 2, owing to label cost issue)
4. Reinforcement (learn **partial** info. **sequentially**)

## Different Protocol
1. Batch learning (Given all known data once)
2. Online learning (can improve when seeing new data)
3. Active learning (interactively query label <span>$y_n$</span> of chosen <span>$x_n$</span>)，也是很常見的蒐集 data 方式(欸！？)，跟 Online Learning 類似，但差別在會 **主動** query

## Different Input Space
1. Concrete feature (利用human intelligence 抽取出有關連的特徵將 input 做簡化)
2. Raw feature (通常需要再做 feature engineering，但現今 DL 的前半部通常會一起 end-to-end train 成一個還不錯的 feature extractor)
3. Abstract feature (原先的 feature 沒有任何物理意義(e.g 可能只是 ID 而已)，需要做 feature conversion 轉成上述二者)

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/03_handout.pdf)
