+++
date =  "2018-05-20T16:45:08+01:00"
description = "Neural Turing Machine (NTM)"
tags = ["Machine Learning","RNN"]
title =  "Beyond RNN - Lec1"
topics = ["Beyond RNN"]
draft = true
+++

這個系列會 focus 在近年來各式各樣我覺得有趣的 RNN 變形，以及相關的實做。

這篇文章的相關程式碼，放在[這裡](https://github.com/sunprinceS/Neural-Turing-Machine)。

<!--more-->

最近剛好在學習 PyTorch，便想找個適合的題目來練手，但一昧地 implement 和寫扣有些乏味，希望自己能同時鞏固 programming 又學習新的學理知識，也因此有了這個系列 - Beyond RNN。

有接觸過 DL 的讀者，想必對 RNN (Recurrent Neural Network) 並不陌生，它主要應用在資料有順序性的 scenerio (e.g 語音,文字,股票走勢(?😛) ...)，除了 single cell 的變形如 LSTM (Long Short Term Memory) 和 GRU (Gated Recurrent Unit)之外，另外有許多將其 equip 在不同的架構上，強化其功能的許多延伸。

## Introduction

今天所要帶來的是 [Neural Turing Machine](https://arxiv.org/abs/1410.5401) ，是 DeepMind 在 2014 年
所發表的研究。

RNN-type 的 network ，之所以能處理 sequence 的問題，關鍵在它 keep 了 hidden
state (對應到 LSTM 的 <span>$h,c$</span>... )，可以理解成當作到目前為止，**所有 input 的
representation** ，而其又會隨著新的 input 進來而有所改變。

## Motivation

一個很自然的問題是，對於很長的那些 sequence ，我們需要開多大的 hidden state 去
maintain？ 很直覺的應該是 dimension 要更大，可以想成我們要用一條幾千維的 vector 來當成我們的 hidden state。

就像 **CNN** 的 motivation 一樣，我們希望我們的 model 能引進多一點先備的資訊，對於影像，直接把每個 pixel 展平成 1D vector，其 performance 比不上將其 treat 成 2D Tensor ，考慮鄰近 pixel 的 額外 information。(工商時間: 這裡有我之前當 TA 時出給同學的[練習](https://sunprinces.github.io/ML-Assignment3/)，在 FER 這個 dataset 上比較 CNN 跟 DNN 的 performance 優劣)

而 NTM 的 idea 在於引進 extra 的 memory ，也可以理解成把原先那個上千維的 1D
hidden vector，折成 2D Tensor 作為 memory，並仿效現在電腦的架構，構造其與 input
interact 的 mechanism，把 sequence 先後的資訊給考慮進來。

## Basic Structure
<center><img src="/img/post/ntm-structure.png" width="50%" style="border-radius: 0%;"></center>

讀寫 memory 這件事分成兩個步驟，內容及位置。仔細想想，「位置」在 memory 中，應該
是離散的存在，該如何把從哪裡讀/寫這件事變成可以用 NN 可以 train 呢？想法就是讓其
**可微**，在這裡的作法是每次在讀寫時，不是對 specific 位置的 memory cell 做操作，而
是對一個 specific distribution <span>$w$</span> 的 memory cell 做操作 (簡單來說，就是同時考慮所有
的 memory cell，但根據裡頭內容的不同，決定其所佔的 weight)。

## Read & Write
假設時間點 <span>$t$</span> ，我們有了一個 address distribution <span>$w_t$</span>，讀出來的內容很簡單就是

<div>
\[
\mathbf{r}_t \leftarrow \sum_i w_t(i)\mathbf{M}(i)
\]
</div>

```python
 def read(self, w):
        """ Read memory corresponding to the address weighting
        Arguments:
            w: shape = (batch_size,N)
        Outputs:
            shape = (batch_size,M)
        """
        return torch.matmul(w.unsqueeze(1), self.memory).squeeze(1)
```

寫入 memory 就是

<div>
\[
\mathbf{M}_t(i) \leftarrow \tilde \mathbf{M}_t(i)[\mathbf{1} - w_t(i) \mathbf{e}_t] + w_t(i)\mathbf{a}_t
\]
</div>

```python
def write(self, w, e, a):
        """ Erase/Add memory corresponding to the address weighting
        Arguments:
            w: shape = (batch_size,N)
            e,a: shape = (batch_size,M)
        """
        self.prev_mem = self.memory
        erase = torch.matmul(w.unsqueeze(-1), e.unsqueeze(1))
        add = torch.matmul(w.unsqueeze(-1), a.unsqueeze(1))
        self.memory = self.prev_mem * (1 - erase) + add
```

## Address Mechanism

我覺得這部份就是 NTM 中最精華的一段了，討論了如何得到 address distribution
<span>$w_t$</span>，所用到的概念，也是之後 attention-based model 的 motivation。

### Content-based Attention

根據 memory cell 現在所紀錄的內容與 controller 的 output key vector <span>$\mathbf{k}$</span> (亦即 <span>$f$</span>(input))做比較，決定該 memory cell 所要佔的比重。

<div>
\[
w_c(i) \leftarrow \frac{e^{\beta K[\textbf{k},\textbf{M}(i)] } }{ \sum_{j} e^{ \beta K[\textbf{k},\textbf{M}(j)] } }
\]
</div>

**Note:** <span>$K[u,v]$</span>是 similarity 的 measure，一般用 cosine
similarity

### Location-based Attention

As title，我們從上一步中，得到了 <span>$w_c$</span>，但根據不同的 input ，又再多
考慮這些 moemory cell 與其鄰近者的 distribution 。

#### Interpolation

考慮上一個時間點 address weighting 的影響

<div>
\[
\textbf{w}_{t} \leftarrow g \textbf{w}_{t} + (1-g) \textbf{w}_{t-1}
\]
</div>

#### Convolution Shift
將某一位置的 memory cell 之 weighting 上，再考慮其與鄰近 memory cell 的
distribution (在此我們考慮左右各一個 memory，如果 weight 是 <span>$(0,0,1)$</span> 的話，意思
就是原先該 cell 得到的所有 weight 都 apply 給其右邊的 cell)

<div>
\[
w(i) \leftarrow w(i+1) s(-1) + w(i)s(0) + w(i-1)s(1)
\]
</div>

#### Reweighting - Sharpening
<div>
\[
w(i) \leftarrow \frac{w(i)^{\gamma}}{\sum_{j}w(j)^{\gamma}}
\]
</div>


綜合起來，整個 mechanism 是這樣運作的。
<center><img src="/img/post/ntm-address-mechanism.png" width="80%" style="border-radius: 0%;"></center>

```python
def address(self, k, beta, g, s, gamma, w_prev):
       # Content focus
       wc = self._similarity(k, beta)

       # Location focus
       wg = self._interpolate(w_prev, wc, g)
       w_hat = self._shift(wg, s)
       w = self._sharpen(w_hat, gamma)

       return w

   def _similarity(self, k, beta):
       cos_sim = F.cosine_similarity(self.memory + 1e-16, k.unsqueeze(1) + 1e-16, dim=-1)
       w = F.softmax(beta * cos_sim , dim=1)
       return w

   def _interpolate(self, w_prev, wc, g):
       return g * wc + (1 - g) * w_prev

   def _shift(self, wg, s):
       #consider 3 locations together
       conved = torch.cat([wg[:,-1:],wg,wg[:,:1]],dim=1) #pad in the beginning and end
       result = F.conv1d(conved.unsqueeze(1),s.unsqueeze(1))
       return torch.cat([result[i:i+1,i,:] for i in range(self.batch_size)])

   def _sharpen(self, w_hat, gamma):
       w = w_hat ** gamma
       w = torch.div(w, torch.sum(w, dim=1).view(-1, 1) + 1e-16)
       return w
```

## Experiment & Discussion

相關實驗可以在 [ipython notebook](https://github.com/sunprinceS/Neural-Turing-Machine/blob/master/NTM-Copy-Analysis.ipynb) 找到

### Copy Task

這是我隨機用長度為 3 ~ 20 的 sequence 訓練的結果，並拿長度為 30 的 sequence 當作
validation data。
<center><img src="/img/post/ntm-copy-cost-0320.png" width="80%" style="border-radius: 0%;"></center>
其實到 20k 以後，就可以 fit training data 了，但對於更長的 sequence 之 generalization ，偶爾會有爛掉的可能。

從 output 的 posterior 來看，也可以看出 model 其實蠻肯定的

<center><img src="/img/post/ntm-copy-pred-0320.png" width="80%" style="border-radius: 0%;"></center>

一個有趣的問題是，如果用更短的 sequence 做 training 呢？比方說 3 ~ 10 的 sequence
，其還能夠類推到長度為 30 的 sequence 嗎？

<center><img src="/img/post/ntm-copy-cost-0310.png" width="80%" style="border-radius: 0%;"></center>
<center><img src="/img/post/ntm-copy-pred-0310-L20.png" width="80%" style="border-radius: 0%;"></center>
<center><img src="/img/post/ntm-copy-pred-0310-L30.png" width="80%" style="border-radius: 0%;"></center>

實驗結果看起來是不行😅。
而且長度 20 的 sequence 也學不起來，這樣是否代表 NTM 沒有 generalization 的能力呢？

讓我們把 train 在 長度為 3 ~ 20 的 sequence 之 model，拿來 predict 在長度 <span>$\geq 20$</span>的 sequence 上。

<center><img src="/img/post/ntm-copy-error-curve.png" width="60%" style="border-radius: 0%;"></center>

看起來它是有學到 generalization 的，而且對於 memory 已經不夠放的部份，也不是一口
氣爛掉，還是有盡量記到一些值。

**Remark:** model generalization 的能力與其看過的 training data 有關，但應該不是一個
簡單的線性關係而已。


### Some Training Detail

* Memory 的使用及 R/W weight 變化

<center><img src="/img/post/ntm-memory-use.png" width="100%" style="border-radius: 0%;"></center>

<center><img src="/img/post/ntm-head-weight.png" width="100%" style="border-radius: 0%;"></center>

* 這裡的 NTM ，contoller 在輸出 final output 時，是同時考慮了 read head 的值，以
  及 current input。在 prediction 階段， current input 都是 dummy 的 <span>$\mathbf{0}$</span> vector，但如果不考慮它的話，NTM 無法 train 起來，推測可能是在某個 range 都為 0 的 vector ，對 model 而言就是僅有讀而沒有寫的指令(如同上方所提到的 R/W mode 切換)。

```python
def forward(self,x):
       """NTM forward"""
       prev_reads,prev_ctrl_state,prev_heads_state = self.prev_state
       inp = torch.cat([x] + prev_reads,dim=1)
       ctrl_outp,ctrl_state = self.controller(inp,prev_ctrl_state)
       reads = []
       heads_state = []
       for head,prev_head_state in zip(self.heads,prev_heads_state):
           if head.is_read_head():
               r,head_state = head(ctrl_outp,prev_head_state)
               reads += [r]
           else:
               head_state = head(ctrl_outp,prev_head_state)
           heads_state += [head_state]

       # Retrieve output according to current reads
       inp2 = torch.cat([x] + reads, dim=1)
       o = F.sigmoid(self.fc(inp2)) # range: [0,1]
       self.prev_state = (reads,ctrl_state,heads_state)

       return o, self.prev_state
```

## Reference

* [Distill 上的文章](https://distill.pub/2016/augmented-rnns/)
* [原始論文](https://arxiv.org/abs/1410.5401)
