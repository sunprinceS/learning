+++
date = "2017-05-11T17:54:03+08:00"
description = ""
draft = false
tags = ["hugo","static site generator"]
title = "使用Hugo快速建立自己的靜態網站"
topics = ["網頁開發"]

+++

一直以來，看到網路上許多大神的個人網站實在相當精彩，架一個自己個人網站的念頭一直都有。這學期剛好時間比較多，想說給自己先起個頭，紀錄一些學習上，思想上的思緒，算是個人的數位典藏吧XD。

<!--more-->

仔細想了一下架構，覺得把像書評/影評之類的文章，與一些 Paper Report ，開放式課程的筆記放在一起有些混雜，故目前的規劃是有一個 [landing page](https://sunprinces.github.io/) 當作最一開始的入口，上頭有一些 tab 分別連到其他網站，諸如 [life](https://sunprincelife.wordpress.com/) 紀錄一些感悟，及書籍與電影的觀後感， [travel](https://sunprinces.github.io/#travel) 使用了 Google Map API 最基本的功能，用來紀錄自己旅行的足跡，當中的每個小卡片可以連到 life 中的文章( Romance for engineer ♡ )，最後則是當前這個網站， learning ，用來紀錄自己專業上學習的一些筆記(數學，演算法等等)，而透由在網路上分享，更是一種精鍊的過程，也可以讓自己的理解更加穩固和透徹吧！於是便動手尋找適合的平台，一般的 blog 顯然不符合我的需求，至少得要有 code block ，還有讓我用美美的 <span>$\KaTeX$</span> 吧XD！有許多大牛的大陸 CSDN 博客覺得太醜，直接自己硬幹 html 又覺得許多時間會花在調適一下跟技術與知識無關的東西上頭@@，日前意外發現 LeoMao 大神 star 了一個 hugo 的 Repo ，研究了一下應該會拿來當日後的技術及學習筆記。希望這篇是一個好的開始 :D

像 Hugo 這類 static site generator 的好處可以用簡單的語法(如 Markdown )來寫網站，後頭會根據寫好的模板，自己幫你渲染成 html ，而前人也已經寫好許多功能，如 topic , tag 管理等，算是集一般部落格的方便上手，與自幹 html 的 flexibility 於一身，而且整個網頁都是靜態的緣故，可以直接免費託管在 github page 上，可說是十分方便。

以下是簡單的工作流程

```bash
hugo new post/[文章名稱].md # 產生一篇新的文章
hugo server --buildDrafts # 在local端同步顯示
```

在確認我們的文章顯示與內容都符合我們的需求後，就可以正式 commit 了✌

```bash
hugo undraft content/post/[文章名稱].md # 不再是草稿囉
hugo # generate

# 第一次需要，將generate出來的html跟src放在同個資料夾下，但在不同 branch上管理
git worktree add -B gh-pages public origin/pages

git add . & git commit -m "new post" & git push
git checkout gh-pages
git add . & git commit -m "new post" & git push
```
