+++
date = "2017-05-11T17:54:03+08:00"
description = "Hugo 101"
draft = false
tags = ["hugo","static site generator"]
title = "使用Hugo快速建立自己的靜態網站"
topics = ["Web Development"]

+++


像 Hugo 這類 static site generator 的好處是可以用簡單的語法(如 Markdown)來寫網站，其會根據寫好的模板，自己幫你渲染成 html ，而前人也已經寫好許多功能，如 topic , tag 管理等，算是集一般部落格的方便上手，與自幹 html 的 flexibility 於一身，而且整個網頁都是靜態的緣故，可以直接免費託管在 github page 等免費的網頁空間上，維護上可說是十分方便。

<!--more-->

## Basic
我們利用以下指令建置一個簡單的 Hugo-managed website 的 scaffold (先裝好 Hugo)。
```bash
hugo new site [資料夾名稱]
```
其中的 **config.toml** 定義了一些會套用到整個網站的參數，可以自己去設定。

**layout** 與 **themes** 資料夾下的眾 html ，則是定義了我們的 md 檔要如何被 render 成 html。

Github 上已經有非常多漂亮的 themes 可供使用，也附帶了許多部落格該有的管理功能，在 themes 下把他們 clone下來，並在 config.toml 的 theme 中設定該主題的名稱。(我是使用 [blackburn](https://github.com/sunprinceS/blackburn) 這個主題，但通常想做一些客製化的更動，比方說加入 $\KaTeX$ 或是加自己的 icon 之類，所以就 fork 了一份來維護囉！)

完成好基本的外觀設定後，我們可以利用以下指令新增文章
```bash
hugo new post/[文章名稱].md
```
如果打開剛剛產生的 md 檔，以現在這篇為例，會看到以下產生的內容
```markdown
+++
date = "2017-05-11T17:54:03+08:00"
description = ""
draft = true
tags = []
title = "文章名稱"
topics = []
+++
```
依樣畫葫蘆地填上各欄位的 attribute， 其中 draft 若為 true 的話，在生成 html 的過程中，是不會被 render 的，工作流程如以下

```bash
# 在 local 端同步顯示，確定文章顯示與內容符合所想
hugo server --buildDrafts
# hugo undraft content/post/[文章名稱].md # deprecated after hugo ver>=0.37
# 以後直接在 md 檔中修改即可！
```

## Git worktree
為了方便管理我們原先的 source 檔案與生成的 html 檔，我們將生成的 html 都放在單一個資料夾下(在 config.toml 中的 publishdir 設定)，我們想在 master 這個 branch 管理各種設定檔與 markdown，而 gh-pages 則是管理生成的 html ，那如何在同一個 repo 下完成這件事情呢？這時候 **git worktree** 就派上用場了！他可以讓我們藉由 ``cd`` 到不同的資料夾， checkout 至不同的 branch 上。

如前面所說，在 master 上，我們不希望 html 檔案們在這個 branch 被 track，所以先將 publishdir 加入 .gitignore 中。接著利用以下指令創建之後要用來管理 html 的 gh-pages 。
```bash
# gh-pages 和 master 是完全獨立的兩分支
git checkout --orphan gh-pages
git reset --hard
git commit --allow-empty -m "Initializing gh-pages branch"
git push origin gh-pages
git checkout master # 回到 master 上做事
```

使用 worktree 來 maintain gh-pages 這個 branch
```bash
git worktree add -B gh-pages [publishdir] origin/gh-pages
```
到此準備完成，在確認完我們的文章顯示與內容都符合我們的需求後，就可以正式 publish 了✌

## How to publish
以下是每產生一篇新的文章後，將文章 publish 的流程
```bash
hugo # generate html，這時候 publishdir下會 generate 新文章的 html
git add .
git commit -m "add new post [文章名稱]"
# publish on gh-pages
cd [publishdir] # 你會發現你已經在不同 branch 上了
git add .
git commit -m "add new post [文章名稱]"
cd ..
git push origin gh-pages # 大功告成！
```

## Clean

如果改動過標題，原先 publishdir 下已產生的檔案不會刪除，需利用以下指令刪除

```bash
hugo --cleanDestinationDir
```

## Clone to new workspace

如果想在不同的電腦間寫 blog 的話，請仿以下步驟

```bash
git clone --recurse-submodules -j8 [repo] # clone from remote
git co master # 因為我 default branch 是 gh-pages
git worktree add -B gh-pages public origin/gh-pages # 重新 set worktree
git submodule update --init # track submodule (因 [theme] 是另一個 repo 在維護的)
# 上述那步沒做的話， hugo server 後會發現什麼都沒 render ^^" (因為 [theme] 是空的)
# 而 hugo 並不會再次 render，所以 publish 到 github page 並不會有任何更新
```

## Post ChangeLog

* 2018/03/16: 更新 `hugo clean`, `hugo undraft` 的指令 (Hugo ver <span>$\geq$</span> 0.36)
* 2018/10/18: 新增如何 clone 到新的 workspace

## Reference
* [Quick Start](https://gohugo.io/overview/quickstart/)
* [Hosting on GitHub Pages](https://gohugo.io/tutorials/github-pages-blog/)
