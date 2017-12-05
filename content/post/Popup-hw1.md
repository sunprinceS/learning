+++
date =  "2017-09-12T08:32:38+01:00"
description = "Warmup / Greedy Algorithms / Dynamic Programming"
tags = ["Programming","Greedy","DP","Kattis","KTH"]
title =  "Problem Solving and Programming- hw1"
topics = ["Online-Judge"]
+++

在 KTH 交換時選到的神課！想說把題解稍微紀錄一下 ✌️\\
<a href="https://kth.kattis.com/problemgroups/617" target="_blank">題目連結</a>

<!--more-->

## pA - Spiderman's Workout
蜘蛛人要爬上爬下！**依序**給一串要爬的距離數列 <span>$d_1,d_2,\cdots,d_n$</span>，每次可以選爬上或下，但有以下目標。

1. 爬完 <span>$d_n$</span> 後要回到地面，否則就是非法解。

2. 不能爬得比地面低。

3. 在過程中，希望能 minimize 經歷的最高高度。

思考一下，可以用 **DP** 來解。

``$$
H[i][h] = \text{min. distance have experienced in order to achieve} \; h \; \text{at step} \;i
$$``


轉移式如以下:


``$$
H[i][h] = \max(h, \min (\underset{\color{blue}{爬上}}{H[i-1][h-d_i]}, \underset{\color{blue}{爬下}}{H[i-1][h+d_i]}))
$$``

初始 state 為 <span>$H[1][d_1]$</span> (一開始只能爬上)\\
最後輸出 <span>$H[n][0]$</span>，如果是 `INT_MAX` 的話，代表不存在任何方法走到
這個 state，輸出 `IMPOSSIBLE`。



## pB - Help
給定兩個由 word 們組成的字串，當中有些 word 可以互相替代 (以 `<[pattern]>` 表示)，想問這兩個字串是否相等( 兩個字串對彼此的替代 word 是分開看的)。
一開始直接迴圈吃了 WA，仔細想想以下例子

\<d\> \<d\> \<b\> \<b\> b\\
&nbsp;&nbsp;e &nbsp;&nbsp;\<c\> \<c\> \<a\> \<a\>

和

\<d\> \<d\> \<b\> \<b\> b\\
&nbsp;&nbsp;b &nbsp;&nbsp;\<c\> \<c\> \<a\> \<a\>

`<[pattern]>` 不是簡單的幾層替代，要用**遞迴**的方法去把他們一一取代。

```python
def solve(s1,s2):
    for i in range(len(s1)):
        if is_placeholder(s1[i]) and not is_placeholder(s2[i]):
            replace(s1,s1[i],s2[i])
            return solve(s1,s2)
        if not is_placeholder(s1[i]) and is_placeholder(s2[i]):
            replace(s2,s2[i],s1[i])
            return solve(s1,s2)
    ## until now, no more placeholder mapping issue
    for i in range(len(s1)):
        if is_placeholder(s1[i]) and is_placeholder(s2[i]):
            s1[i] = s2[i] = 'hao'
    Finally, check whether 2 strings matches 
```


## pC - Introspective Caching

Cache問題，假定有個 oracle 知道每個物件被 call 的順序，給定 cache size 及不同物件的個數，想問 cache miss 的次數有幾次。

我們能夠決定的是，就是當 cache 滿了，我們要把某個物件移掉時，要移誰？\\
一個 **Greedy** 的 criteria 是，看誰下一次最慢被用到，就先把他移掉，然後
maintain 一個 priority heap ，實際去模擬此問題的運作。

簡單來說，

```cpp
struct Node{
  int idx;//哪個物件
  int next_tid;// 下次被 call 是在第幾個時刻
  Node(int idx,int next_tid): idx(idx),next_tid(next_tid){}
  bool operator < (Node rhs) const{
    return next_tid< rhs.next_tid;
  }
};
```
實際模擬一下

```cpp
priority_queue<Node> cache;
int num_miss = 0;
int cache_size = 0;
for(int i=0;i<num_call;++i){
  if(!b_cached[queries[i]]){ // miss
    if(cache_size == c){ //full
        b_cached[cache.top().idx] = false;
        cache.pop();
      }
    else{
      ++cache_size;
    }
    b_cached[queries[i]] = true;
    ++num_miss;
  }
  cache.push(Node(queries[i],next_tids[i]));
}
```
`next_tids[i]` 表示 query[i]要 call的那個物件，下次是何時被 call。

題目還沒寫很多，但我覺得這題蠻棒的XD，Greedy 的題目也頗神奇，不知道有沒有其他 criteria 就是😎
## pD - Troll Hunt
水題！

## pE - Aspen Avenue

還沒AC...
