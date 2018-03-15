+++
date =  "2018-03-13T20:44:58+01:00"
description = "資料結構 - Heap/ BST/ Hash Table"
tags = ["Programming"]
title =  "C++ STL Cheat Sheet - 3"
topics = ["CheatSheet"]
+++

用途在為了保證 container **存在某些性質**時使用。

<!--more-->


## MAX Binary Heap - Priority Queue

### 性質

* **top() 恆為當前資料中最大者， query 複雜度 <span>$O(1)$</span>**
* push/pop 複雜度為 <span>$O(\log N)$</span>

### 型態宣告

```cpp
<class T, class Container = vector<T>, less<typename Container::value_type> > pq;
//正常使用
priority_queue<int> pq; // 
priority_queue<pair<int,int> > pq;

//e.g 若想改為 Min Binary Heap
priority_queue<int,vector<int>,greater<int> > pq;
priority_queue<pair<int,int>,vector<pair<int,int> >,greater<pair<int,int> > > pq;
```

## Initialization

```cpp
priority_queue<int> pq;
priority_queue<int> pq(vec.begin(),vec.end()); //copy [start,end)
```

Note: 直接在 constructor 做這件事，背後他會去 call `make_heap`，複雜度為 <span>$O(N)$</span>，\\
如果依序 push element 進來，複雜度為 <span>$)(N \log N)$</span>。

## Binary Search Tree - set

## 性質
* container 中的 element 恆 follow 某個 order (default 是 ascending)
* element 為 unique ([cf] `multi_set` element 可 repeat)

### 宣告

```
set<int> s;
set<int> s(other_set);  //copy from other set
set<int> s(ALL(other)); // can be other containers (like vector, set...) from [start, end)

//Descending
set<int,greater<int> >s;

//Define custom order
struct mycomp {
  bool operator() (const int& lhs, const int& rhs) const
  {return lhs>rhs;} // descending
};
set<int,mycomp> s;
```

### 計數/查找: <span>$O(\log N)$</span>

```cpp
set<int>::iterator it = s.find(123); // return iterator，找不到回傳 s.end()
int b_exist = s.count(123); //either 0 or 1;

//**lower_bound, upper_bound**
// 注意：若今天查找的值，不在 set 裡頭，此兩者等價
// Assume s = [1,2,3,5,6]
set<int>:: iterator l_it = s.lower_bound(5); //*l_it = 5, 若all element < val ，回傳 s.end()
set<int>:: iterator u_it = s.upper_bound(5); //*u_it = 6,               <=  
```
Note: `lower_bound()` 回傳的為 dereference 後 <span>$\geq$</span> val 者，\\
而 `upper_bound()` 回傳的為 dereference 後 <span>$>$</span> val 者 ([cf] `multi_set` 的圖解)

### 插入/刪除: <span>$O(\log N)$</span>

```cpp
s.insert(123);

s.erase(123);
s.erase(it); //*it = 123, O(1)
s.erase(s.begin(),s.end()); //O(interval width)
```

Note:

  * 可以想見 `erase(val)` 實際上是去 call `find()` 拿到 iterator 後，再去 erase
  * 在 container 中的元素是不能被更改的 ([cf] map 可改 val，不能改 key)
  * 元素保證 unique ，若重複插入，`insert()` 不會插入 (關於其 return 值請查 cppreference)

### MISC

* access `begin()`, `end()` 是 <span>$O(1)$</span> ，網路上某些參考資料有誤。
* 基本上是下方 map 的一個 **key = val** 之特例。
