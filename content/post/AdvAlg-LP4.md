+++
date =  "2017-09-16T20:49:15+02:00"
description = "An example of deterministic rounding from LP"
draft = false
tags = ["Algorithm","Linear Programming","Approximation Algorithm","NTU"]
title =  "Advanced Algorithm - Metric Facility Problem"
topics = ["Advanced Algorithm"]
+++

接續[前面幾講](https://sunprinces.github.io/learning/tags/linear-programming/)，一個常用的技巧是，利用 LP solver 得出來的解 <span>$\mathbf{x}^{\star}$</span>， 拿去做 rounding 推出原先問題的解 <span>$\mathbf{x}^{\prime}$</span> (with 一個還不錯的 approx. ratio)。而這講會講述一個較為複雜的問題 - Metric Facility Problem 。

<!--more-->


