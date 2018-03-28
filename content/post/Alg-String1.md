+++
title = "Algorithm - String Note 1"
date = "2018-03-23T15:44:33+01:00"
draft = true
tags = ["Algorithm","Programming","CodeForce","Data Structure"]
topics = ["Algorithm"]
description = "Trie"
+++

寫 CodeForce 寫到一題需要利用字典樹 (*Trie*) 來處理的題目，以此紀錄一下。

<!--more-->

Trie 是一種專門用來處理字串的樹型資料結構，核心想法是將字串儲存於樹中節點的轉移。

<center><img src="/img/post/trie-example.png" width="40%" style="border-radius: 0%;"></center>

## 性質

每個節點下的所有子孫，有共同前綴。目前在以下場合用過

* 找 LCP
* 字串匹配類: e.g 給定一字串， <span>$O(|s|)$</span> 找到於 trie 中最長前綴匹配的字串

在每個節點，maintain 一個 `cnt` 變數去記其子孫(i.e 擁有此前綴的字串)的數目
(`insert`/`delete` -> +1/-1)。

比較為人詬病的是記憶體的用量，可以想一想一個小例子(e.g haohao , a, b，假定
<span>$\Sigma = 26$</span>，要花 <span>$(1+(6+1+1))\Sigma$</span> byte 的空間)
