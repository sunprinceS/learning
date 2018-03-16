+++
date =  "2018-03-12T22:44:58+01:00"
description = "Function"
draft = false
tags = ["Programming","C++","STL"]
title =  "C++ STL Cheat Sheet - 2"
topics = ["CheatSheet"]
+++


<!--more-->

## General Initial 類
* `iota`: 等價於 `range(<start>,<end>,1)`，將 container element inplace 置換成等差數列

```cpp
//v is vector with size 10
iota(v.begin(),v.end(),0); //v becomes [0,1,2,...,9]
```


* `fill`,`fill_n`: 將 container element 置換成一定值

```cpp
fill(v.begin(),v.end(),0); // v becomes [0,0,...,0]
fill_n(v.begin(),2,-1); // v becomes [-1,-1,0,...,0]
```

* `copy`: 將[first,last) 的 element copy 到另一個 container

```cpp
copy(from_vec.begin(),from_vec.end(),std::back_inserter(to_vec)); 

//equivalent，但to_vec size 需先 resize 好，否則會SIGSEV
copy(ALL(from_vec),to_vec.begin()); 

//Another easy way if want to copy **all** elements
vector<int> to_vec = from_vec;
vector<int> to_vec(from_vec)
```
Note: 不可用於 copy 者與被 copy 者範圍重疊。

* `generate`: 


## 計算類

* `partial_sum`: 計算 [first,last) 的 prefix sum

```cpp
partial_sum(fromvec.begin(),fromvec.end(),tovec.begin()) // [0,1,3,6,...,50]

//product [0!,1!,2!,3!,...,9!]
partial_sum(ALL(from_vec),tovec.begin(),std::multiplies<int>());

//custom lambda op
partial_sum(ALL(from_vec),tovec.begin(),[](int x,int y){return foo(x,y);});
```
Note: 如果 tovec 跟 fromvec 的 `begin()` 一樣的話，就是 inplace 置換。

* accumulate: 類似於找 `partial_sum()` 的最後一個元素

```cpp
//一樣可以與 lambda 做搭配
int sum = accumulate(ALL(vec),0); // 0 為基準值
```

* `random_shuffle`: inplace shuffle elements

```cpp
srand(unsigned time(NULL)); //rng initialzation
random_shuffle(vec.begin(),vec.end());// build-in rng
random_shuffle(vec.begin(),vec.end(),[](int n){return my_rand(n)};);// custom
```
