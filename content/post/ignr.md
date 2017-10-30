+++
date =  "2017-08-20T08:01:34+01:00"
description = "Introduction"
draft = false
tags = ["git","小工具"]
title =  "利用Ignr自動生成.gitignore"
topics = ["工具"]
+++

有在使用 git 的人，應該都知道我們很多時候我們不希望 git 去 track 某些檔案，也不在意他們的改動情況(但有時候手滑還是會不小心 checkin 進去...😅)， 像是競賽的測資， c++ 編譯時的 .o 檔，binary executable 等等，這時候會加上 **.gitignore** 於工作目錄下達成我們的目的。

<!--more-->

目前我自己用的有兩種作法:

### Global gitignore

在家目錄下的 .gitconfig 加入如下設定，之後創建的每個工作目錄都會去抓家目錄下的
.gitignore 。

```Ini
[core]
  excludesfile=~/.gitignore
```

通常是放一些 reference 的 `pdf`、`pptx` 等等。
(之前還有 vim 的暫存檔，`swp` 、`swo`，但後來直接在 vimrc中 `set dir=~/vim_tmp` ，改變暫存檔的儲存路徑，所以也就不會在工作目錄看到惹。)

### Ignr 客製化每個 repo 的 gitignore

根據所開發 project 語言及框架的不同， .gitignore 也理當不同，但全部放到 global 下看起來頗蠢，但每開一個 repo 就要去 google 相對應的 .gitignore 複製貼上也沒好到哪去😅。

而這也是這篇要介紹的 [Ignr](https://github.com/Antrikshy/ignr.py) ，在命令列直接從[gitignore.io](https://www.gitignore.io/)抓取特定的 .gitignore 模板。

安裝也很簡便 (但目前只有支援 python2 ，所以要裝在 py2 下)
```bash
pip2 install ignr
```

用的時候也很簡便，而其他指令可以去repo看更詳盡的敘述，這裡就不贅述了。

```bash
ignr -n c c++ cmake 
```
