+++
date =  "2018-03-10T20:44:58+01:00"
description = "資料結構 - Pair & Vector"
tags = ["Programming"]
title =  "C++ STL Cheat Sheet - 1"
topics = ["CheatSheet"]
+++

這個系列主要會整理一些自己刷 judge 常使用到的 STL 用法，cppreference 好是好，但就是有點太詳細了XD，首篇介紹的 vetor 和 pair，絕對是我最常用到的 STL 資料結構 XD !

<!--more-->


## Pair

### 賦值

```cpp
pair<int,int> p = {1,2};
pair<int,int> p = make_pair(1,2);
```

Note: 

1. 比大小時，根據 lexicographical order
2. first,second 是 member ，不用加括號


## Vector

### 初始化

```cpp
vector<int> vec; //empty vec
vector<int> vec(3); //[0,0,0], int default coonstructor
vector<int> vec = {1,2,3}; //實際上做了 int array 的型別轉換
vector<int> vec(3,123); //[123,123,123]
vector<int> vec(ALL(other)); //can be other containers (like vector, set...), copy from [start,end)
vector<int> vec(other_vec);//copy from other vec
```

### 賦值

```cpp
int from_arr = [1,2,3,4,5];
vector<int> to_vec;
to_vec.assign(3,123); //to_vec: [123,123,123]
to_vec.assign(from_vec.begin(),from_vec.end());
to_vec.assign(from_arr,from_arr+3); //to_vec: [1,2,3]
```

#### 應用: 2 維 vector

```cpp
//Method 1
vector<vector<int> > vec2d(num_row);
for(auto& row: vec2d) row = vector<int>(num_col,123); // 注意一定要是 reference

//Method 1'
vector<vector<int> > vec2d(num_row,vector<int>(num_col,123));

//Method 2
vector<int> row(num_col,123);
vector<vector<int> > vec2d;
vec2d.assign(num_row,row);
```


### 存取元素

```cpp
int a = vec.at(i);
int a = vec[i];
```

Note: `at()` 會做邊界檢查，出現越界的 idx 時，會丟出例外，而不是直接 SIGSEV 。

### 空間操作

```cpp
// 保留空間 W/ size 不變
vec.reserve(123); //capacity = 123, vec.size() is the same

// 伸縮 size
vec.resize(100,123);
//如果原 size < 100，則[size+1,100)會填入 123
//如果原 size > 100，則只保留[0,100)，其他 destroy
```

Note: capacity 只能增加不能縮小，另外其會隨著 size 的更動而動態調整。

### Iterate
```
//Method 1: array 法
//Method 2:
for(vector<int>::iterator it = vec.begin(); it!= vec.end(); ++i){
  foo(*it); // * is de-reference
}
//Method 3: 見 好用 function 的 traverse
```

Note: 

  * `rbegin()`, `rend()` 用法一樣， 概念上 `rend()`  是指到 `begin()` 的前一個。
  * 如果 vec 是 const，則要用 `const_iterator` or array 法，如以下

```cpp
for(vector<int>::const_iterator it = vec.begin(); it!= vec.end(); ++i){
  foo(*it); // * is de-reference, but can not modify the value
}
```

### 加入/移除 元素 : <span>$O(N)$</span>

```cpp
vec.insert(<pos iterator>,123);
//會加在 pos 前面，故若為 begin()，則 123 變第一個，若為 end()，則 123 變最後一個
// 記法是新的vector中，pos 位置為方才插入的 element

vec.erase(vec.begin()+2);// remove idx=2 的元素
vec.erase(vec.begin(),vec.end()-1); //remove [0,end-1) 中的元素

vec.clear();//destroy all elements, size = 0
```

Note: `erase()`的複雜度跟元素位置有關係(因需要移動其後的element)，而 `pop_front()`則保證是 <span>$O(1)$</span>


### MISC
* `size()`,`back()`,`front()`是 method，不是member，要記得加括號。
* `vector<bool>` 用在單純紀錄或 query 定長度的 flag 就好，因為其會做空間優化，所以無法回傳 element 的 reference (因為實際上他不是存 bool var ，而是僅僅一個 bit )，所以有些 STL method 會 fail (但反正簡單的 task 還是可以用)；另一個缺點是其運算效率比一般的 type 差得多。
