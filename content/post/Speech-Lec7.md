+++
date =  "2018-04-23T13:45:08+01:00"
description = "Deep Learning & ASR"
tags = ["ASR","KTH","NTU"]
title =  "Speech Recognition- Lec7"
topics = ["Speech Processing"]
draft = true
+++

之後回來再把這個坑給補齊

<!--more-->

## HMM training

## Discriminative Training with DNN

常見的 Discrimivative Criteria 有 MSE 和 Cross Entropy (等價於 minimize KL Divergence)兩種。\\
通常我們利用 senone 當作 training 的 target ，最後一層 layer 的 neuron 數為
number of senone ， 每個 neuron output 為 posterior probability of such senone
class given frame。

### Some modification to consider dependencies

* 考慮頻譜的 context 作為 DNN 的 input
* 利用 RNN 去 model 這個特性

## Initialization (Pre-training)

基本想法是同時 exploit generative 及 discriminative model

### Restricted Boltzman Machine

### Deep Belief Networks (DBN)

事實上就是 stacked RBM

<!--## (Typical) Training Procedure-->

## End-to-end training - Connectionist Temporal Classification
