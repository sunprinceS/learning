+++
date =  "2017-09-19T08:32:38+01:00"
description = "Warmup / Greedy Algorithms / Dynamic Programming"
tags = ["Data Structure","Kattis","KTH"]
title =  "Problem Solving and Programming- hw2"
topics = ["Online-Judge"]
+++

<a href="https://kth.kattis.com/problemgroups/624" target="_blank">題目連結</a>

<!--more-->

## pA - Turbo

有一種叫 `TurboSort` 的排序演算法，執行方式是在奇數次迴圈時，將目前尚未選過的最小 element 一路跟隔壁的 element swap 移到左邊(也是他應該在的位置)，在偶數次迴圈時，則是移大的 element 到右邊。\\
今天想問在每次 iteration 中，每次需要與幾個 element swap 才可以到達目的地 (Note: element <span>$i \in [1,N]$</span> 且不重複)。\\
Well... 一個 <span>$\mathcal{O}(n^2)$</span> 的演算法，所以基本上有個 <span>$\mathcal{O}(n \log n)$</span> 的方法差不多就符合題目要求惹。

簡單來說，以奇數次迴圈 (<span>$2i - 1$</span>)為例，想問的就是現在有幾個在<span>$[\,i,\,\text{position of } i \,]$</span> 中的元素 <span>$> i$</span> ，而我們希望這件事可以在 <span>$\mathcal{O}(\log n)$</span>查到，剛好可以用上最近學過的 Binary Indexed Tree。

bit[i] 所記的東西為 **原先在 <span>$i$</span> 這個位置的 element** 是否已經排到正確的地方了，\\
所以 <span>$\texttt{PrefixSum}(\,\text{position of }i\,)$</span> 就是在 i 元素左方還有幾個 element 還沒排到正確位置 (也就是那些還需要與他 swap 的 element)。因為我們是依序 pick 最小、最大、次小...的元素來 swap，所以只要還不在正確位置的那些元素，勢必都需要 swap，而交換好了之後，則 update bit[position of i] 為 0 (i.e <span>$\delta = -1$</span>)。

```cpp
FenwickTree shift(N);
//一開始都不在正確位置
for(size_t i=0;i<=n;++i){
  shift.add(i,1);
}
int left = 1, right = N;
for(size_t i=1;i<=N;++i){
  if( i & 0x1){ //odd
    shift.add(idx2loc[left],-1);
    cout << shift.prefix_sum(idx2loc[left]) << endl;
    left++;
  }
  else{
    shift.add(idx2loc[right],-1);
    cout << shift.interval_sum(idx2loc[right]+1,N) << endl;
    right--;
  }
}
```

## pB - Movie Collection

今天有一列光碟共 <span>$n$</span> 片，每次拿完一片看完後，會重新放到最上方，想去 maintain 對於編號<span>$i$</span> 的光碟，在他上面的光碟數有多少。

如果實際去模擬這個運作，假設有 <span>$m$</span> 次拿光碟的動作，複雜度為
<span>$\mathcal{O}(mn)$</span>，顯然大概是要我們找一個 <span>$\mathcal{O}(m \log n)$</span> 的方法，再一次想到 Binary Indexed Tree。

讓我們先跳脫來想一想，假定今天有個 <span>$m+n$</span> 個 slot 的光碟架，同樣做一樣的操作，(但光碟不會 **往下移動** 了！)，bit[i] 所記的東西為現在在 <span>$i$</span> 位置( buttom 為 <span>$0$</span> ,top 為 <span>$n-1$</span>) 的 element，光碟是否還存在，所以 <span>$\texttt{PrefixSum}(i)$</span>。即是從 <span>$i$</span> 以降架上的光碟有幾片， <span>$n$</span> 減他即是題目所求。我覺得這題蠻好的XD

```cpp
FenwickTree cnt(m+r+1);
top_loc = m;

for(size_t i=1;i<=m;++i){
  pos[i] = m-i;
  cnt.add(m-i,1);
}

for(size_t i=0;i<r;++i){
  cin >> q;
  cout << m - cnt.prefix_sum(pos[q]) -1  << ' ';
  cnt.add(pos[q],-1);
  pos[q] = top_loc++;
  cnt.add(pos[q],1);
}
```

## pC - Kitten on a Tree
水題！

## pD - Binary Search Tree
依序插入元素到 BST 中，想問每次插入的元素，其深度為多少。\\
就跟 `delete` 元素一樣，插入分兩種情況，一個是原 bst 中比你大的 node <span>$i$</span> (但他的前一個 <span>$j$</span> 小於你)，下面已經
有兩個 child ，或是不是。不是的話很簡單，直接接在 <span>$i$</span> 的左 child， 深度為
<span>$\text{depth}[i]+1$</span> 。有兩個 child 的話，則是要接在 <span>$j$</span> 的右 child (<span>$j$</span> 一定是 leaf)，深度為<span>$\text{depth}[j]+1$</span> 。\\
直接用 c++ 的 map ，對 BST 夠熟的話，應該不困難！

```cpp
map<int,int> bst;
bst[n] = 0;
cout << 0 << endl;

for(size_t i=1;i<N;++i){
  cin >> n;
  it = bst.lower_bound(n);
  if(it == bst.begin()){
    depth = bst.begin() -> second + 1;
  }
  else if(it == bst.end()){
    it--;
    depth = (*it).second + 1;
  }
  else{
    it_backup = it;
    it--;
    depth = max(it->second,it_backup->second) + 1;
  }
  cnt += depth;
  bst[n] = depth;
}
```

## pE - All just a dream
寫起來很煩... 有空在寫解答 :p
