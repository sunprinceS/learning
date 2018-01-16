+++
date =  "2018-01-16T23:14:08+01:00"
description = "Warmup / Greedy Algorithms / Dynamic Programming"
tags = ["IR","NLP","KTH"]
title =  "Information Retrieval - Lec1"
topics = ["Information Retrieval"]
+++

這個系列主要會紀錄自己在 KTH 修習 [Information Retrieval System](https://www.kth.se/student/kurser/kurs/DD2476?l=en) 的一些筆記，備忘用，不會像之前的 Post 那麼詳細，但還是會加點自己的 remark 這樣。

<!--more-->

## Difference between Database

1. structured data v.s. unstructured data
2. deterministic v.s. inexact

DB 處理的對象是滿足選定資料結構的 structured data ，而 IR 則包含了將真實世界中的訊息 (which is unstructured) 轉換成我們容易處理的資料結構之步驟。\\
承上，該 parse 成何種資料結構隨著應用場景及需求而不同，又比方說在 query 時， DB 須滿足特定 rule ，而 IR 則是要處理在 **Natural Language** level 的 query，另外，像是計算 document 與 query 的相關程度也隨著需求不同而改變 (which is inexact)。

**Remark:** In brief, DB 是蘊含在 IR system 中的一個模塊。

## Key Idea

### Inverted Index

像是參考書背後的索引，會列出哪些名詞在哪些章節、第幾頁出現，在閱讀的時候，可以想成是看到一個 page <span>$\rightarrow$</span> word/phrase 的 mapping ， Inverted Index 則是建構一個**反向**的 mapping。

### Ranking
決定當我們有大量的 return 結果時，該如何以怎樣的排序呈現給使用者。 (Ja...  Nowadays, the most successful ranking algorithm in the world is Google...lol)

### Evaluation

* Precision

<div>
\[
p = \frac{|\text{ret} \, \bigcap \, \text{rel}|}{|\text{ret}|}
\]
</div>

回傳的結果中，真的有關係的比例

* Recall

<div>
\[
r = \frac{|\text{ret} \, \bigcap \, \text{rel}|}{|\text{rel}|}
\]
</div>

關於有關連的那些文本，有被回傳結果的比例，實務上是無法計算的(我們不知道相關的文本到底有哪些)

Remark: 這兩個 criteria 其實有點互相矛盾，比方說得到 <span>$100 \%$</span> recall 的一個方法是回傳全部的文本，便可確保沒有漏網之魚 (但這太 87)。而得到僅可能高的precision 的方法則是只回傳那些 <span>$100 \%$</span> 確定關聯的文本 (但如此回傳的量太少，不夠使用者抉擇)

## Why IR matter

* How many times you use Google Search each day? 😉
* Get high ranking on the returened list is essential (gaming the ranking
  algorithm...lol)
* The returened consequence framed our mind to what they show... (which is not a
  good part, I think)


## A first example - Boolean Search

構建一個 Term-document matrix <span>$M \in \mathbb{R}^{W \times D}$</span>，
<span>$W$</span>
為 query 的單詞數， <span>$D$</span> 為文本數。\\
<span>$M[w][d]= $</span> 𝟙[word <span>$w$</span> appears
in document <span>$d$</span>]


### Advantage

Useful, simple, fast and intuitive in doing boolean operation \\
(often used in **well-defined** documents )

實做細節等 lab 1寫完再來補上XD

### Disadvantage

* How to formulate query (the result can be too many or too few, depending on
  the query)
* Lack of **ranking** of returned result
* Make a little unreasonable assumption: **every terms are equally important**

**Remark:** 一個比較偏 DB 風格的 IR 方法 ，在將 documents 轉成方才定義的那種 term-document matrix 時，丟失了太多資訊。在 query 時處理的也非 user-friendly 的自然語言。
