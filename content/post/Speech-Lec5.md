+++
date =  "2018-04-19T16:45:08+01:00"
description = "Hidden Markov Model"
tags = ["ASR","KTH","NTU"]
title =  "Speech Recognition- Lec5"
topics = ["Speech Processing"]
draft = false
+++

我覺得自己之前做的[投影片](https://drive.google.com/file/d/152rJZPdCl1k4IqLgr7frwWXeE6lCHjO0/view?usp=sharing)真的蠻好的XD (自己講)，因此這一講中，只會偏重在原本寫的比較簡略的 Learning Problem ， Evaluation 及 Decoding Problem 僅會附上程式碼及做定義。

<!--more-->

## 3 Problems of HMM

Given observation sequence <span>$X$</span> and model parameter <span>$\Theta$</span>

<span>$\Theta$</span> 包含了以下幾個 component: <span>$\pi$</span> (initial
probability), <span>$A$</span> (transition matrix), <span>$\phi$</span>
(emission probability)

* Evaluation Problem: <span>$\Pr(X|\Theta)$</span>，即是一開始我們想求的，該
  sequence 與特定 HMM match 的 likelihood，可用於 isolated word recognition 。

* Decoding Problem: Find the optimal state sequence <span>$\Pr(Z|X,\Theta)$</span> (在之後的 continuous ASR ，我們需要知道一路走來的 state sequence，才能據此還原出對應的 word sequence)

* Learning Problem: Find the optimal parameter <span>$\Theta^\star$</span>,
  which satisfies some criteria

## Evaluation Problem

Solution: Forward Algorithm

``$$
\alpha_t(i) \triangleq \Pr(x_1,x_2,\cdots,x_t \land z_t = s_i | \Theta)
$$``

因為在最後一個 frame 中， <span>$\alpha_T(i)$</span> 跟 <span>$\alpha_T(j) \, (i \not = j)$</span> 中的事件互斥，所以我們可以對 <span>$\Pr(X|\Theta)$</span> marginalize over states

``$$
\Pr(X|\Theta) = \sum_{i=1}^M \alpha_T(i)
$$``

```python
logalpha = np.zeros((num_frame,num_state))

for j in range(num_state):
    logalpha[0][j] = log_startprob[j] + log_emlik[0][j]

for i in range(1,num_frame):
    for j in range(num_state):
        logalpha[i][j] = logsumexp(logalpha[i-1,:]+log_transmat[:,j]) +log_emlik[i][j]

return logalpha
```


## Decoding Problem

Solution: Viterbi Algorithm

``$$
\delta_t(i) \triangleq \max_{Z_{-t}} \Pr(Z_{-t},X \land z_t = s_i | \Theta)
$$``

```python
logdelta = np.zeros((num_frame,num_state))
ptr = np.zeros((num_frame,num_state),dtype=np.int8) # the first row is dummy
best_path = np.zeros(num_frame,dtype=np.int8)

for j in range(num_state):
    logdelta[0][j] = log_startprob[j] + log_emlik[0][j]

for i in range(1,num_frame):
    for j in range(num_state):
        tmp = logdelta[i-1,:] + log_transmat[:,j]
        logdelta[i][j] = np.max(tmp) + log_emlik[i][j]
        ptr[i][j] = np.argmax(tmp)

best_path[-1] = np.argmax(logdelta[-1,:])
for i in range(2,num_frame+1):
    best_path[-i] = ptr[-i+1][best_path[-i+1]]

return np.max(logdelta[-1,:]), best_path
```
**Remark:** 亦可利用 Viterbi 算出來的機率，去近似  forward algorithm 所計算的結果 (從下圖 Viterbi 的best path 之走勢與 logalpha 的變化可略知一二)

<center><img src="/img/post/viterbialpha.png" width="90%" style="border-radius: 0%;"></center>

## Learning Problem

Solution: Baum-Welch Algorithm (a special case of EM algorithm)

一個 HMM model <span>$\Theta = \lbrace \pi, A, \phi \rbrace$</span>
我們想根據 data 去調整 <span>$\Theta$</span> ，使得其能 exploit 去選出較好的參數
<span>$\Theta^\prime$</span>，而所謂的**好**，可能是

* fit data (e.g likelihood, posterior) -> ML, MAP criteria
* classification performance (discriminitive training)

在此我們考慮 ML (*Maximum Likelihood*) criteria ，也就是想找到
<span>$\Theta^\star = \underset{\Theta}{\arg \max} \Pr(X|\Theta)$</span> 

對 HMM 來說，直接從 <span>$\Theta$</span> 是不能直接去估 <span>$X$</span> 的分佈的，必須要先確定所對應的 state sequence <span>$Z$</span> 才行，但今天難點就在 state 是 **hidden** 的。\\
EM Algorithm (Expectation-Maximization) 常用來解決這類問題，大概的思想就是:

* E-step: 利用現在的 parameter <span>$\Theta$</span> 去算背後 state 分佈的**期望值** (白話來說就是假設知道了 state)
* M-step: 知道了 state 的分佈，去估計 HMM 的參數 (to **maximize** some criteria)。

E, M iteratively 交錯去更新參數。

### E-step

先定義一些值

#### Backward Probability

跟 forward probability <span>$\alpha_t(i)$</span> 很類似，不過是考慮後半

``$$
\beta_t(i) \triangleq \Pr(x_{t+1},x_{t+2},\cdots,x_{T}|z_t = s_i, \Theta)
$$``

<center><img src="/img/post/forwardbackward.png" width="70%" style="border-radius: 0%;"></center>

```python
logbeta = np.zeros((num_frame,num_state))

for j in range(num_state-1,-1,-1):
    logbeta[num_frame-1][j] = 0

for i in range(num_frame-2,-1,-1):
    for j in range(num_state):
        logbeta[i][j] = logsumexp(logbeta[i+1,:]+log_emlik[i+1,:]+log_transmat[j,:])

return logbeta
```

#### Posterior Probability

##### Gamma Probability
``$$
\gamma_t(i) \triangleq \Pr(z_t = s_i | X,\Theta) = \frac{\Pr(z_t = s_i ,X | \Theta)}{\Pr(X|\Theta)} = \frac{\alpha_t(i) \beta_t(i)}{\underbrace{\sum_j \alpha_T(j)}_{\text{Evaluation Problem 算過}}}
$$``

**Remark:** <span>$\sum_{t=1}^{T-1} \gamma_t(i)$</span>: <span>$\mathbb{E}[X$</span>中有拜訪過 <span>$s_i$</span><span>$]$</span>，同時也是在 <span>$s_i$</span> 中**有發生 transition** 的期望值。



##### Epsilon Probability
``$$
\epsilon_t(i,j) \triangleq \Pr(z_t = i \land z_{t+1} = j | X,\Theta)
$$``

``$$
\epsilon_t(i,j) = \frac{\alpha_t(i) A_{ij} \phi_j(x_{t+1}) \beta_{t+1}(j)}{\Pr(X|\Theta)}
$$``


**Remark:** <span>$\sum_{t=1}^{T-1} \epsilon_t(i,j)$</span>: <span>$\mathbb{E}[s_i \rightarrow s_j]$</span> 

**Note:** 上述這兩個機率都是 conditioned on 整個 observation sequence <span>$X$</span> ，而非只有那個時間點的 <span>$x_t$</span>
### M-step

#### Initial Probability

``$$
\pi_i = \gamma_1(i)
$$``

#### Transition Probability

``$$
A_{ij} = \frac{\mathbb{E}[s_i \rightarrow s_j]}{\mathbb{E}[\text{# of visit}\, s_i]}
$$``

#### Emission Probability

前面提到，我們利用 GMM 來 model emission probability (mixture 數夠多可以近似任何的連續函數，我想也是有 optimization feasibility 的考量)

在選定該時間點對應到哪個 state 後，還要在從一把 gaussian 中抽出一個去 sample
observation <span>$x_t$</span>。(pretty hierachical XD)\\
<!--簡單討論起見，先將 mixture 數目設為兩個，-->

``$$
\phi_j(x) = \sum_i w_{jk} \mathcal{N}(x;\mu_k,\sigma_k^2) \quad(w_{jk} \, \text{為 k-th Gaussian 在 state j 的 GMM 中所佔的比例})
$$``

要重估的參數有每個 gaussian 的 mean 和 covariance 以及 在某個 mixture 的比例
<span>$w_{jk}$</span>

**Note:** 上面的 <span>$x_t, \mu, \sigma^2$</span> 都可以是向量形式

**Remark:** 我們假設 <span>$d \times d$</span> 的 covariance matrix 是 diagonal
(based on 前面抽 feature 時，用 DCT uncoorelate feature 的各個 dimension)

##### Posterior Probability of choosing k-th mixture

``$$
\begin{aligned}
\gamma_t(j,k) &= \Pr(z_t = s_j \land \text{choose k-th mixture in state j} |z_t = s_j, X, \Theta)\\
&= \underbrace{\quad \gamma_t(j) \quad}_{\text{gamma probability}} \cdot \underbrace{\frac{w_{jk}\mathcal{N}(x_t;\mu_{jk},\sigma_{jk}^2)}{\sum_m w_{jm}\mathcal{N}(x_t;\mu_{jm},\sigma_{jm}^2)}}_{\text{k-th mixture 佔的比例}}
\end{aligned}
$$``

#### Update Rule

``$$
\begin{aligned}
w_{jk} &= \frac{\mathbb{E}[\text{# of choosing k-th mixture in state j}]}{\mathbb{E}[\text{# of visit}\, s_j]} = \frac{\sum_t \gamma_t(j,k)}{\sum_t \gamma_t(j)}\\
\mu_{jk} &= \frac{\sum_t \gamma_t(j,k) x_t}{\sum_t \gamma_t(j,k)}\\
\sigma_{jk}^2 &= \frac{\sum_t \gamma_t(j,k)(x_t - \mu_{jk})^T (x_t - \mu_{jk})}{\sum_t \gamma_t(j,k)}
\end{aligned}
$$``

**Remark:** 跟一般沒有跟 HMM bind 在一起的 Gaussian Mixture 更新基本上一樣，但

* sample 數 <span>$N$</span> 改為 <span>$\mathbb{E}[\text{number of visit}\, s_j]$</span> (因為這個 mixture 不是每次都會被抽到，要拜訪 <span>$s_j$</span> 才是它負責的); 
* <span>$\gamma(j,k)$</span> 多了一個下標 <span>$t$</span> (時間 <span>$t$</span> 在 state <span>$j$</span> 且抽中該 Gaussian 的期望值)，表示其會隨 time evolve

**Remark:** 而 GMM 的更新，就是根據想 maximize 的 criteria 定義 loss function ，去微分求極值發生點。

