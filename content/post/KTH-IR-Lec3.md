+++
date =  "2018-01-31T23:14:08+01:00"
description = "Ranked Retrieval"
tags = ["IR","NLP","KTH"]
title =  "Information Retrieval - Lec3"
topics = ["Information Retrieval"]
draft = false
+++

前面提過 Boolearn Search 的其中一個問題在於， machine 是**無腦**地回傳文本，其回傳的順序並沒有任何意義 (可以試想一下使用 Google 搜尋時，一次動輒 <span>$10^6$</span> 數量級以上的文本量，如果無序的話，你想要檢閱查找相關文件是一件多痛苦的事，那麼這個 IR system 有跟沒有是差不多的😅)。因此，我們引進 **ranked retrieval** 的概念，目標是讓文本能以其跟搜尋 query 的關聯性大小來做排序。

<!--more-->

## Vector Space Model

這裡引進了在 NLP 各類應用中，相當常見的一個將離散的 symbol (word, sentence, document...) map 到數值空間的模型 - Vector Space Model (VSM)。對於 word ，目前最耳熟能詳的 Word Embedding 有 Word2Vec, GloVe ....等等，而 sentence vector ( word的集合) 除了簡單的 Bag-of-Word (BOW) ，亦有用各類型的 recurrent/recursive NN 去 compose vector 的方法，而這裡我們要處理的對象是 document (sentence 的集合)，考量到一般的 IR scenerio ，通常不太要用到 syntactic 或 detailed 的 semantic 的資訊，僅採用最簡單的 BOW 搭配 tf-idf 做 weighting 的方法。

**Remark**: 我的理解是，每個使用者心中想查詢 query 的 semantics 各有不同， in
general 而言，只要某個 term 與 query 有 match ，說他相關其實相當合理，反而是
NN-based 的方法做的一些 smoothing 性質 (比方說歌手/影星的名字在向量空間中會相當接近，因其
上下文都差不多的緣故) 反而不太適用於這個應用場合。

### tf-idf

* tf: term 在文本中出現的次數
* df: corpus 中有多少 document 含有此 term (可以想見該值越大，代表此 term 大概是
  一些 function word，但帶多少 infomation，像是 be Verb，轉折詞等等)

有許多不同的 weighting scheme
### Cosing Similarity

計算關聯性

## PageRank

除了文本當中具有的**interior 的性質** (e.g term freq.)， modern IR 中有鑑於 web 的蓬勃發展，各個文本 (網站) 之間的 link (**exterior 的性質**) 也是相當有價值的排序參考來源。

``$$
\text{PR}(d) = c(\sum_{s \in in(d)} \frac{\text{PR}(d)}{\text{out_deg}(d)}) + \frac{1-c}{\text{# of document}}
$$``

上述的式子描述的是一個 **random surfer model**，使用者從某一網站 (node) 開始，會 uniform 選擇其 out edge 連向的任一網站，而若該 node 為 sink (#out edge = 0)，則隨機訪問任意一個網站。要從這個遞迴式中解出 PR ，我們需要利用 Markov Chain 計算其 stationary prob. dist. ，同時為了保證該機率存在的唯一性，我們加入 <span>$c$</span> 這個 term ， 數學上是為了保證最後得到的 prob. dist. 所有 term 都是非零，以使唯一性存在。 直觀解釋上叫做 **boring probability** ，就是使用者有一定的機率不管當前所在的 node， random 從任一個網站開始，

**Remark**: Lab 中，我們會將兩個 score 作加權平均

### Implementation (Sparse Matirx Version)

```java
void iterate(int numberOfDocs, int maxIterations) {

   int numIter = 0;
   double[] a = new double[numberOfDocs];
   Arrays.fill(a, 1.0 / numberOfDocs); // uniform start
   double[] old_a = new double[numberOfDocs];
   int[] cnt = new int[numberOfDocs];

   double p;
   double eps; // measure the difference of previous iteration

   while (numIter++ < maxIterations) {
     old_a = Arrays.copyOf(a, a.length);
     Arrays.fill(cnt, 0);

     p = 0.0;
     for (int i = 0; i < numberOfDocs; ++i) {
       if (out[i] == 0) {
         p += old_a[i];
       }
     }
     Arrays.fill(a, p / numberOfDocs);


     for (int col = 0; col < numberOfDocs; ++col) {
       if (out[col] != 0) {
         for (int i = 0; i < numberOfDocs; ++i) {
           if (cnt[col] < out[col] && link.get(col).get(i) != null) {
             ++cnt[col];
             a[i] += (old_a[col] * (((1 - BORED) / out[col]) + (BORED / numberOfDocs)));
           } else { // non link
             a[i] += (old_a[col] * BORED / numberOfDocs);
           }
         }
       }
     }


     eps = 0;
     for (int i = 0; i < numberOfDocs; ++i) {
       eps += Math.abs(old_a[i] - a[i]);
     }
     if (eps < EPSILON) {
       break;
     }
  }
}
```
