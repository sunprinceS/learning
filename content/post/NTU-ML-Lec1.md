+++
date = "2017-12-20T09:02:37+01:00"
description = "The Learning Problem"
draft = false
tags = ["Machine Learning","NTU"]
title =  "NTU Machine Learning - Lec1"
topics = ["Machine Learning Foundation"]
+++

其實是大一升大二的暑假，閒來無事找的開放式課程，那時也做了一些筆記，沒想到兩三年過後，自己做專題也走向了這個領域XD，於是順手把它整理上來囉！

<!--more-->

## What is Machine Learning

ML 簡單來說，是希望於機器上實現如同人類的學習，目標是習得一些如何**決策**的技能(e.g Chatbot 根據之前的對話內容，下一個時間點應該要回什麼；下棋的過程中，下一步棋應該怎麼走；收到一串野外鳥叫的錄音，該如何辨別出當中有哪些品種...)。通常會適用的問題主要有以下特徵。

1. 存在 pattern (問題也才有被 **解決** 的意義)
2. 不存在明確的定義 <span>$\rightarrow$</span> non-programmble
3. 有 data (不然從人類的角度看，就是不用學習就會的本能了 lol)



## Components of Learning

* input <span>$\vec{x} \in \mathcal{X}$</span>
* output <span>$y \in \mathcal{Y}$</span>
* **target function** <span>$f: \mathcal{X} \rightarrow \mathcal{Y}$</span>
* data <span>$\mathcal{D} = \lbrace(\vec{x_1},y_1),(\vec{x_2},y_2)\cdots,(\vec{x_N},y_N)\rbrace$</span>
* hypothesis set <span>$\mathcal{H}$</span> (從中選出接近 <span>$f$</span> 的 function <span>$g$</span>)

<img src="/img/post/ml-arch.png" width="800px">

其中 <span>$\mathcal{A}$</span> 和 <span>$\mathcal{H}$</span> 合稱為 *learning model*

### 補記
這裡提到的 <span>$\vec{x}$</span> 經歷了**將真實世界中的數據轉成數值向量**的過程，考慮到計算複雜度，有些時候我們不能餵進原始數據，所以抽取適當的特徵 (*feature*) 便是一個重要的技巧 (好比推薦系統常見的一種作法是將消費者與產品的某些屬性化成數值向量再去算匹配度，但無法用此數值向量 rebuild 出消費者或產品，然而這種 ambiguity 也正是在這個任務中所需要的)；但這也不是一定，在影像辨識裡，深度學習是直接餵進 raw image 去做 classification ， 相當於是利用 NN 將 feature extraction 及 classification 這兩個任務 end-to-end 地串接在一起。

## And more

在 2017 的今日，又經過了這麼多年的發展，幾乎每個領域都可以看到 ML 的身影，在只需用到**一種**技能的決策上，機器已經超越了人類 (日趨成熟的語音，影像辨識及其衍生出的各個分支(e.g [breast cancer prediction](https://research.googleblog.com/2017/03/assisting-pathologists-in-detecting.html))、甚至連搜索空間極大的圍棋也被攻破)、也改變了許多職業、領域的運作。Think with Google 就提到了許多其在推薦系統上的應用(推播最適廣告的決策，就像是在了解每個消費者呀！)，而股票買賣等需要高速決策的領域也因為 ML 的引進而改變 (Zero-sum game 必定有輸有贏，但當大家都用了同套 AI 的時候，誰又該輸該贏呢？)

但在需要用到複數技能(真實世界中絕大部分問題)及在學習時學會舉一反三(人類學習的重要關鍵)，則還有相當長的一段路要走(但長不代表久，who knows the speed XD)，像是聊天型 Chatbot 回話的決策，除了要考慮先前的對話內容，還要判斷使用者的眼神、心情、語氣、甚至個性等等(所謂社交技巧)，但我們只會將問題簡化成僅從對話內容去推出一二 (所以常常覺得它們笨笨的XD)；又或著像前述的 breast cancer prediction，文末也提到了關於其他可能異常症狀的觀察，機器是無法回答的 (但當然也是可以跑好幾個分類器，一一去做 binary classification 啦...但對某些未被定義到的異常症狀就無法判斷了)；而像學習技能的推廣，如打電動的時候，人類通常精通某遊戲後，相似類型的遊戲也可能很快上手(甚至連不同類型遊戲也會比較快上手...但這就有點複雜了)，但就我所知，現在的 RL 只要 reward 或環境稍有不同，便需要重新訓練，而訓練完精通新的後，原本會的遊戲又變得不會玩了...

身處在一個時代的浪頭，思考得真的要有新的維度，才能生存下來呢！ (筆記打一打變成心情抒發了XD)

## Reference

* [林軒田老師的講義](https://www.csie.ntu.edu.tw/~htlin/course/ml15fall/doc/01_handout.pdf)
