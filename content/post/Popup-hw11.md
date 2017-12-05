+++
date =  "2017-12-05T09:56:56+01:00"
description = "Computational Geometry"
tags = ["Programming","Computational Geometry","Kattis","KTH"]
title =  "Problem Solving and Programming- hw11"
topics = ["Online-Judge"]
+++

<a href="https://kth.kattis.com/problemgroups/641" target="_blank">題目連結</a>

<!--more-->

## pA - White Water Rafting

給一個簡單內多邊形和外多邊形，求中間圍起來的部份，寬度最小為多少。\\
枚舉每個邊的組合，**求兩線段之距離**，取最小者，複雜度為 <span>$\mathcal{O}(n^2)$</span>。


## pB - Colliding Traffic

在平面上，有 <span>$n$</span> 個點，各自以定速朝定方向前進 (i.e 射線)，當兩個點間距
<span>$< r$</span> 時，定義他們 collide ，想問第一個 collide 發生在什麼時候。
枚舉每條射線的組合，先看距離是否已小於 <span>$r$</span> ，如果是就輸出 <span>$0$</span>。
不是的話，由兩條射線的參數方程相減得到其距離的參數方程，

<div>
\[
\vec{d}_{i,j} = (\vec{s}_j - \vec{s}_i) + (\vec{v}_j - \vec{v}_i) t \quad (\, \vec{s} \; \text{為起始位置}\,)
\]
</div>

找是否存在 <span>$t > 0$</span> 滿足 <span>$|\vec{d}_{i,j}| \leq r$</span>，然後從所有射線組合中，找出最小的 <span>$t$</span>，複雜度為 <span>$\mathcal{O}(n^2)$</span>。

```cpp
double get_collision_t(Obj i,Obj j,double r_sq){
  double a,b,c;
  a = i.vel.distance_square(j.vel);
  b = (j.pos - i.pos).dot(j.vel - i.vel);
  c = i.pos.distance_square(j.pos) - r_sq;

  double D = b*b - a*c;
  if(isz(D)){
    return (-b/a);
  }
  else if(D<0){
    return -100;
  }
  else{
    if(b+sqrt(D) > 0)
      return ((-b+sqrt(D))/a);
    else
      return ((-b-sqrt(D))/a);
  }
}
```


## pC - Pesky Mosquitoes

圖上散落了一些點，給定一個定直徑 <span>$d$</span> 的圓，想找出最多可以蓋住幾個點。\\
觀察到若想蓋住最多點的話，至少有兩個點會在圓的邊上(否則總是可以再移動，
看能否再蓋住更多點)。

枚舉所有點的組合，找出過那兩點 <span>$A, B$</span> 且直徑為 <span>$d$</span> 的圓，再來看剩下的點是
否在此圓裡面，複雜度為 <span>$\mathcal{O}(n^3)$</span>。\\
而找圓心的話，算是高中數學的範疇，應該很簡單。但注意到這樣定義的圓會有兩個，方便起見我們統一
一個方向，法向量指向 <span>$\overrightarrow{AB}$</span> 的右邊 (如此算 <span>$\overrightarrow{BA}$</span> 時，就會得到另一個圓了)。

## pD - Unusual Darts

吃了兩個 WA ，真難玩...

## pE - Meltdown

我看我是沒理解題意...
