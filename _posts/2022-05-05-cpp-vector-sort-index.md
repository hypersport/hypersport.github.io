---
title: C++ 得到 vector 排序后的索引
author: hypersport
date: 2022-05-05 21:22:23 +0800
toc: false
categories: [C++]
tags: [vector]
render_with_liquid: false
---

直接看例子：

```cpp
#include <vector>
#include <numeric>
#include <iostream>
#include <algorithm>

using namespace std;

int main()
{
    vector<int> test = {1, 2, 3, 4, 5, 9, 8, 7, 6, 0};
    vector<int> index(test.size());
    iota(index.begin(), index.end(), 0);
    sort(index.begin(), index.end(), [&test](int i1, int i2) { return test[i1] < test[i2]; });

    for (const auto &i : index)
        cout << i << " ";

    return 0;
}
```

输出结果
```cpp
9 0 1 2 3 4 8 7 6 5 
```
