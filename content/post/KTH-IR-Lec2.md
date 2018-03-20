+++
date =  "2018-01-22T23:14:08+01:00"
description = "Preprocessing & Indexing"
tags = ["IR","NLP","KTH"]
title =  "Information Retrieval - Lec2"
topics = ["Information Retrieval"]
draft = true
+++

在做 [Indexing](https://sunprinces.github.io/learning/2018/01/information-retrieval---lec1/) 之前，我們需要將文本從各方來源中抽取出來，這些來源的 **格式** 相當多元(像是 html, md 等等 markup 或是與圖片相雜的 data ，可能還需要處理 encoding 的問題)，反正就是挺亂的，需要做一些 processing 後，才可以用強大的 NLP tool 來統一處理。

<!--more-->

## Tokenization

我們對 **Token** 的定義是，在 byte stream 中 **能構成意義的最小單元**。\\
(這樣講其實有點不精確，可以把這步想成是決定哪些 byte 要連在一起看，\\
白話來說，並以英文為例就是哪些 character 要連在一起看，哪些標點符號(e.g 。, `<space>`)、哪些 markup 要拿掉/或不拿掉。如果單看這個定義會覺得跟後方提到的方法在處理的對象及輸出有點小衝突😅，畢竟我們 pipeline 最終目標就是抽出一個所有文本中的 minimal meaning unit set ，但要直接從 byte stream level 做到這件事太難了，這步是 pipeline 的第一步而已)。

以英文來說，一般會將標點符號或空白視為分隔符，但像是 hypen 連字(e.g. state-of-the-art)，網址(e.g. https://hao123.com \)，電話號碼...等，往往意義是不可拆的，所以需另外建構一些 rule (也 depend on 應用)去篩選，常用的表示方法則是 **Regular Expression** 。 e.g 
`https?:\/\/\S{2,300}`、`\d+[,.:-]\d+[,.:-]\d+`、`[a-zA-Z\d_.]+@[a-zA-Z\d.]+`。
(你可以依此去 generate 幾個 sample ，來推測這些 regrex 的處理對象是哪些嗎？)





**Remark:** 到這步你可以想見文本當中已經沒有那些困擾的符號 (有的話也是與 word 們 一起 bind 成一個概念看待)了。

## Normalization

我們一般來說不希望字典過大增加太多 overhead， 希望將概念相同的字 mapping 到同一個表示 (過來的好幾步都是做這件事)，這裡做的是一個初步的嘗試 (e.g.大小寫, 變音)。

## Morpheme

<div>
\[
\text{一個 word 當中能構成意義的最小單位}
\]
</div>

有以下幾中類別：

* stem: 詞幹 (**通常 mapping 成它**)
* affix: 可分為前綴(e.g un-, im-, ...)、後綴(e.g -ly, -s, -ed) (不同語言還有其他不同的類別...lol)

除此之外，還有各種由舊有詞複合(compound)起來表現的新詞 (e.g smartphone)，而像一些歐洲語言還有將一長串的字依一些 rule 構成一個超長詞的規則... (And such kind of language is called **morphologically rich**，但能夠表達的概念也不會比人家多...)\\
e.g 德文的 `Lebensversicherungsgesellschaftsangestellter` (life insurance company employee)

有了 morpheme 的概念後，我們有更多的事可以做。

## Lemmatization

將像是所有格、形容詞、副詞、第三人稱等通通 map 成 lemma (**the base form**)。以英文來說，會需要做 POS (part-of-speech) tagging 去抓詞性，才知道該怎麼 map ，而像剛剛提到的那些歐洲語言，還需要做些分析後，去把當中的 lemma 一個個抓出來(可以利用 FSM)。

### Alternative method: Stemming

不管三七二十一，直接抓詞幹 (犧牲精確度但節省時間成本)。

Example: Porter's Algorithm (a **rule-based** method)

**Remark:** 到這裡為止，我們已經大幅的減少了字典的 size 。

## Stop words

也是 rule-based ，有些字太常出現了(可能每個文本，甚至每個句子都有)，沒什麼語義上
的價值，稱為 stopword ，可以從字典中移掉。

(但有時候也不那麼絕對... e.g To be or not to be 😂) ，目前的潮流還是保留。

**回顧:** 如同前面所說，整個 preprocessing 要做的就是從文本中抽出一個 minimal meaning unit set （這裡我都叫他字典)，滿足利用這個字典中的每個 entry 能 rebuild 出全部文本 (or with 足夠小的語意 loss) 的條件，**每一步要做多大的化簡除了是 precision 和 processing time,recall 的 trade-off 外，也是適當地 model NLP 的 ambiguousness** 。
