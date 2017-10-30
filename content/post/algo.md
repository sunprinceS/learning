+++
date = "2017-04-22T12:35:32+08:00"
description = "hello world"
draft = true
katex = true
tags = ["hugo","web development"]
title = "Hello World"
topics = ["misc"]

+++

你好

<!--more-->

## Code block
玩 static site 當然要有 code block 啊(？

```python
import numpy as np

A = np.random.random((10, 10))
B = np.random.random((10, 10))
print(A @ B) # python 3.5+ new syntax
```

```javascript
const a = { test: 123 };
console.log('Hello World!');
```

```cpp
#include <bits/stdc++.h>
using namespace std;

int main()
{
  cout << "Hao 123" << endl;
  return 0;
}
```

## Math
不過我也想打點 <span>$\KaTeX$</span> (竟然不能打`\LaTeX`，不過真的滿快的)

<div>
\[
  \ln x = \int_{1}^x \frac{1}{t} \, dt
\]
</div>
