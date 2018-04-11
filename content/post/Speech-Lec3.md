+++
date =  "2018-04-11T17:14:08+01:00"
description = "Feature Extraction#2 - MFCC"
tags = ["ASR","KTH","NTU"]
title =  "Speech Recognition- Lec3"
topics = ["Speech Processing"]
draft = false
+++

上一回介紹了 LPC ，來做為我們抽取 feature 的一個方法，今天要來談談目前最常被使用的 feature - MFCC 。

<!--more-->

## Background

LPC 所求得的係數用來 model 發聲時唇齒舌的構造所對應的 filter ，但這個 model 的假設是該 filter 為 all-pole (但我不知道為何不也 model zero ^^")，所以對某些音來說效果會沒這麼好 (e.g nasal (鼻音))。而今天所介紹的 MFCC 摒除了這個假設，並將人類聽覺的一些學理基礎 (filter bank) combine 進 model 當中。

**Remark:** 很簡單很簡單地來說， LPC 像是藉由觀察一個人的唇齒舌之位置，去猜測他說了什麼，而 MFCC 除了這點之外，還多模擬人類聽聲音的過程 (查閱文獻後，其似乎也只應用在 speech recognition 的 scenerio)。

## 流程


<center><img src="/img/post/mfcc-schedule.png" width="80%" style="border-radius: 0%;"></center>

以下會針對每個 component 做說明，另外，我把 step-by-step 跑出 MFCC feature 的 ipynb 放在[這裡](https://github.com/sunprinceS/Speech-Experiment/tree/master/MFCC)(資料來自 [KTH DT2119](https://www.kth.se/student/kurser/kurs/DT2119?l=en) 所提供的 [TIDIGIT](https://catalog.ldc.upenn.edu/ldc93s10))

### Pre-emphasis

在發聲的時候，高頻部份的聲音通常較弱 (尤其是 voiced speech)，因此為了 catch 到在高頻的 formant ，我們會讓訊號經過一個 High-Pass filter (下圖紅色線是有 pre-emphasis 過的)

<center><img src="/img/post/pre-emph.png" width="80%" style="border-radius: 0%;"></center>

那這個 filter 怎麼描述呢？

``$$
y[n] = x[n] - \alpha x[n-1]
$$``

``$$
H(z) = \frac{Y(z)}{X(z)} = 1-\alpha z^{-1}
$$``

Impulse Response 如以下

<center><img src="/img/post/hp-filter.png" width="60%" style="border-radius: 0%;"></center>

你也可以在 ipynb 中觀察 pre-emphasis 前後聲音訊號的差別 (後者聽起來比較清晰)。

### Hamming Windowing

在下一步中，我們要將 time-domain 的訊號經由 Fourier Transform 轉成 spectrum ，但今天訊號長度有限( 因為 sampling rate 有限的緣故)，轉出來的頻率值數目也要有限，所以是使用 DFT ，但做 DFT 時，數學上來說，我們是針對 infinite periodic time signal (將原先的 signal 往兩側 copy-paste 無限次)做 Fourier Transform，所以如果今天頭尾不連續的話，相當於是引進了 impulse response ，會讓頻譜多了無關的雜訊，因此常常 apply Hamming Window (如以下)，去讓頭尾都能接到 <span>$0$</span>。

<center><img src="/img/post/hamming-window.png" width="60%" style="border-radius: 0%;"></center>

**Remark:** 但也不是說 Rectangular window 就一定不行用啦...

### DFT & Mel-scale Filter Bank

這部份就是整個 MFCC 最精華的地方了，DFT 之後，對每個 frame 我們都有其相對應的頻譜 (一把 freq. 值)，正常人耳聽到聲音時，會有不同的受器去感知不同的頻率，經由電訊號傳導進而在腦中形成聲音。

我們可以把這些受器 model 成 bandpass filter (這裡是利用 triangular filter)。另外，它是 distribute logarithmically 於頻率軸上。 (也就是說，在低頻部份，所擁有的濾波器數量是比較多的，人類比較能夠辨別不同頻率的低音)

<center><img src="/img/post/mfbank-linear.png" width="80%" style="border-radius: 0%;"></center>

<center><img src="/img/post/mfbank-log.png" width="80%" style="border-radius: 0%;"></center>

再之後我們會對得到的結果取 magnitude 的平方再取 log ，取 log 之因也是因為人耳對 magnitude 的感知也是 logarithmic (所以分貝的定義才會是<span>$10 \log_{10}(\frac{P}{P_0})$</span>)


### IDFT (DCT)
<!--Discrete Cosine Transform (DCT) to decorrelate the filter bank coefficients and yield a compressed representation of the filter banks. Typically, for Automatic Speech Recognition (ASR), the resulting cepstral coefficients 2-13 are retained and the rest are discarded; num_ceps = 12. The reasons for discar-->
