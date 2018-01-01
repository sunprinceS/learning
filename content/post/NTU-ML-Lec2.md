+++
date = "2018-01-01T14:02:37+01:00"
description = "Learning to Answer Yes/No"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec2"
topics = ["Machine Learning Foundation"]
+++


<!--more-->

於前一講講述了 ML 的 framework ，於這一講中，我們會利用一個簡單的 learning model ， 去解決 binary classification 的問題。

## Hypothesis Set: Linear Perceptron

<div>
\[
\textcolor{red}{h}(x) = \texttt{sgn}(\sum_{i=1}^{d}\textcolor{red}{w_i}x_i - \textcolor{red}{\text{threshold}}) = \texttt{sgn}(\textcolor{red}{\mathbf{w}^T}\mathbf{x})
\]
</div>

其中，<span>$\mathbf{x} \in \mathcal{X}, \mathcal{X} = \lbrace 1 \rbrace \times \mathbb{R}^d $</span> (把 threshold 放進 <span>$\mathbf{w}$</span> 的第 0 維，以簡便表示)

其實就是一個將空間一分為二的 hyperplane 。

## Learning Algorithm

### Objective

<div>
\[
  \text{learned function} \, g \approx \text{ground truth function} \, f
\]
</div>

至少在看過的 training data 上做到一致，亦即

<div>
\[
g(x_n) = f(x_n) = y_n \quad \forall (x_n,y_n)\in \mathcal{D} 
\]
</div>

### How to select <span>$g$</span> from <span>$\mathcal{H}$</span>

隨便抽取一個起始權重，去看看在 training data 上是否會有與 label mismatch 的情況
，若有的話，便去修正權重。

<div>
\[
 \text{If} \enspace \texttt{sgn}(\textcolor{red}{\mathbf{w}_t^T}\mathbf{x}_n) \not = y_n ,
 \enspace \text{then} \enspace \textcolor{orange}{\mathbf{w}_{t+1}} \leftarrow
 \textcolor{red}{\mathbf{w}_t} + y_n\mathbf{x}_n
\]
</div>

### Correctness of Updating
若有錯誤發生，表示 <span>$y_n\underset{\text{predict}}{\mathbf{w}_t^T\mathbf{x}_n} < 0$</span>，\\
目標是將其更正為 <span>$> 0$</span> 。 我們想確定更新 <span>$\mathcal{w}$</span> 的過程中，<span>$y_n\mathbf{w}_t^T\mathbf{x}_n$</span> 會 **遞增**, which is trivial to prove


### Running Time
承上，<span>$\mathbf{w}$</span> 的更新，會使得它更接近 ground truth，也就是其單位向量的內積越來越接近 <span>$1$</span>。

#### Growth of <span>$||\mathbf{w}||$</span> ISN't too fast

``$$
\begin{align}
||\mathbf{w}_{t+1}||^2 &= ||\mathbf{w}_t + y_n \mathbf{x}_n || ^2 \\
&= ||\mathbf{w}_{t}||^2 + 2\underset{\leq 0}{\color{blue}{y_n\mathbf{w}_t^T\mathbf{x}_n}} + ||y_n\mathbf{x}_n||^2\\
&\color{blue}{\leq}  ||\mathbf{w}_{t}||^2 + \color{blue}{0} + \color{red}{||y_n\mathbf{x}_n||^2} \\
&\color{red}{\leq} ||\mathbf{w}_{t}||^2 + \color{red}{\max_j ||x_j||^2}
\end{align}
$$``

<div>
\[
\Rightarrow ||\mathbf{w}_T||^2 \leq \underset{=0}{||\mathbf{w}_0||^2} + T \times \max_j
||x_j||^2
\]
</div>

<div>
\[
\Rightarrow \boxed{||\mathbf{w}_T|| \leq \sqrt{T} \times \max_j||x_j||}
\]
</div>

#### Convergence after T rounds
假設 <span>$\mathbf{w}_0 = \mathbf{0}$</span>，並觀察到 ground truth
<span>$\mathbf{w}_f$</span> 滿足 <span>$\boxed{y_n\mathbf{w}_f^T\mathbf{x}_n \geq \min_n y_n\mathbf{w}_f^T\mathbf{x}_n \geq 0}$</span>

``$$
\require{cancel}
\begin{align}
\mathbf{w}_f^T\mathbf{w}_T &= \cancel{\mathbf{w}_f^T\mathbf{w}_{T-1}} + y_{n_T}\mathbf{w}_f^T\mathbf{x}_{n_T} \\
\cancel{\mathbf{w}_f^T\mathbf{w}_{T-1}} &= \cancel{\mathbf{w}_f^T\mathbf{w}_{T-2}} + y_{n_{T-1}}\mathbf{w}_f^T\mathbf{x}_{n_{T-1}} \\
.\\
.\\
\cancel{\mathbf{w}_f^T\mathbf{w}_1} &= \underset{=0}{\mathbf{w}_f^T\mathbf{w}_{0}} + y_{n_1}\mathbf{w}_f^T\mathbf{x}_{n_{1}} 
\end{align}
$$``

<div>
\[
\Rightarrow \boxed{\mathbf{w}_f^T\mathbf{w}_T  \geq T \times \min_n y_n\mathbf{w}_f^T\mathbf{x}_n}
\]
</div>

因此，

``$$
1 \geq \frac{\mathbf{w}_f^T\mathbf{w}_T}{||\mathbf{w}_f|| \, ||\mathbf{w}_T||}
\geq \frac{T\times \underset{n}{\min} y_n\mathbf{w}_f^T\mathbf{x}_n}{||\mathbf{w}_f|| \, ||\mathbf{w}_T||} \geq \sqrt{T} \times \frac{\stackrel{\color{blue}{\rho}}{\overbrace{\underset{n}{\min}y_n\frac{\mathbf{w}_f^T}{||\mathbf{w}_f||}\mathbf{x_n}}}}{\underset{\color{red}{R}}{\underbrace{\underset{j}{\max}||x_j||}}}
$$``


<div>
\[
\Rightarrow \boxed{T \leq \frac{R^2}{\rho^2} = \text{const.}}
\]
</div>



<!--T &\leq \frac{\mathbf{w}_f^T\mathbf{w}_T}{\underset{n}{\min} y_n\mathbf{w}_f^T\mathbf{x}_n} \times \frac{||\mathbf{w}_f|| \, ||\mathbf{w}_T||}{||\mathbf{w}_f||\,||\mathbf{w}_T||}\\-->
<!--& \leq 1 \times \frac{}{}-->

### Limitation & Modification
1. 資料必須是線性可分的(*Linear Separable*)，亦即對資料不能一無所知，如此方能做此假設。
2. 對 running time 僅知道會在有限時間內結束，但還是不知道要跑多久

另外，有時候資料夾雜了一些雜訊，使得空間因少數數據點而無法線性可分，\\
此時我們利用 Pocket PLA 去找出一個 **還不錯** 的 <span>$g$</span> ，使得 label mismatch 的數據點為最少。\\
簡單來說，就是每更新一次 <span>$\mathbf{w}$</span>，便去跑過**所有**的數據點，來看是否比前一個更好，\\
再 run 過一定回合數後中止，running time 為<span>$\mathcal{O}(|\mathcal{D}|T)$</span>，不保證找到最佳解。


## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/01_handout.pdf)
