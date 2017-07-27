+++
date =  "2017-07-11T23:36:01+08:00"
description = "Trimming & Discretization "
tags = ["Algorithm","Advanced Algorithm","NTU"]
title =  "Advanced Algorithm - Subset Sum Problem"
topics = ["Advanced Algorithm"]
+++

Subset Sum Problem 與 Knapsack Problem 相同，也是一個 NPC 問題(可以想成 Knapsack Problem weight 均為 1 的特例)。而在 Knapsack Problem 中，我們發展出**等差** 的rounding 技巧，犧牲精確度去換取更低的時間複雜度，而這一講中，將利用**等比**的方式去做 rounding 。
<!--more-->

## Algorithm

Computer <span>$S_i$</span> : possible subset sum of \{<span>$v_1, v_2, \cdots ,v_i$</span>\}

<span>$S_0 = $</span>\{<span>$0$</span>\}

<span>$S_1 = $</span>\{<span>$\color{red}{S_0}, \color{blue}{S_0+v_1}$</span>\} = \{<span>$\color{red}{0}, \color{blue}{v_1}$</span>\}

<span>$S_2 = $</span>\{<span>$\color{red}{S_1}, \color{blue}{S_1+v_2}$</span>\} = \{<span>$\color{red}{0}, \color{red}{v_1}, \color{blue}{v_2}, \color{blue}{v_1+v_2}$</span>\}

.\\
.\\
.\\
<span>$S_{n} = $</span> <span>$\color{red}{S\scriptstyle{n-1}}$</span> <span>$\bigcup \, (\color{blue}{S\scriptstyle{n-1}}$</span> <span>$\color{blue}{+ \, v\scriptstyle{n-1}}$</span> <span>$)$</span>

由 <span>$S_{i}$</span> 計算 <span>$S\scriptstyle{i+1}$</span> 以 <span>$\mathsf{MergeSort}$</span> 來實做，並移除 <span>$>W$</span>的元素和 (複雜度為 <span>$\mathcal{O}(|S_i|)$</span>)，又 <span>$|S_i| < W \, (\forall i=1 \sim n)$</span>， 所以 Running time 為 <span>$\mathcal{O}(nW)$</span>。

可以發現複雜度與 <span>$W$</span> 成正比，故充其量這個 Algorithm 也只是 psuedo-polynomial 而已，與前面的想法類似，造成複雜度的主因在每一個 Merge 的過程中，我們留下了太多元素(而 Merge 的複雜度又與元素個數呈線性)，如果**只取幾個代表性的元素**(e.g <span>$|S_i| = \mathcal{O}(\log W)$</span>)，便可將複雜度再往下壓。

{{% fluid_img src="/img/post/trim.png" alt="Trimming Procedure" %}}

Pick <span>$x_1$</span>, remove elements <span>$\leq \frac{x_1}{(1-\frac{\epsilon}{n})}$</span> in the list (say, <span>$x_2 \, \text{and} \, x_3$</span>). \\
Then, pick <span>$x_4$</span> remove elements <span>$\leq \frac{x_1}{(1-\frac{\epsilon}{n})}$</span> in the list. Repeat this procedure until the last element.

### PsuedoCode for Trimming

```python
def trim(s_list,delta):
  ret = [] # trimmed list
  last = 0

  for elm in s_list:
    if elm > last * delta:
      ret.append(elm)
      last = elm
  return ret
```

Now, <span>$|S_i| \leq \log_M W = \mathcal{O}(\frac{n\ln W}{\epsilon})
\Rightarrow \text{Total running time: } \mathcal{O}(\frac{n^2 \ln W}{\epsilon})$</span>

### Correctness Check

{{% fluid_img src="/img/post/trim_approx.png" alt="Trimming Approximation" %}}

可以將 Trimming 視為改小某一 range 中的元素們(將其改至該 range 內第一個元素)，OPT 在 trimming & merge 的過程中不斷地被改小，最多最多會被等比縮小 <span>$n$</span> 次。

<div>
\[
  \text{Output} \, \geq \, \text{OPT} \cdotp (1-\frac{\epsilon}{n})^n \stackrel{\color{blue}{n=1}}{\geq} \, \text{OPT} \cdotp (1-\epsilon)
\]
</div>

