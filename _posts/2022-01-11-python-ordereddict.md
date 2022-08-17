---
title: Python 有序字典 OrderedDict
author: hypersport
date: 2022-01-11 22:22:22 +0800
toc: false
categories: [Python]
tags: [OrderedDict]
render_with_liquid: false
---

```python
from collections import OrderedDict

d = OrderedDict()
d['foo'] = 1
d['bar'] = 2
d['spam'] = 3
d['grok'] = 4
# Outputs "foo 1", "bar 2", "spam 3", "grok 4"
for key in d:
    print(key, d[key])
 
# 要构建一个需要序列化或编码成其他格式的映射的时候，OrderedDict 是非常有用
import json
json.dumps(d) # '{"foo": 1, "bar": 2, "spam": 3, "grok": 4}'
```

**注意：** `OrderedDict` 内部维护着一个根据键插入顺序排序的双向链表。每次当一个新的元素插入进来的时候，它会被放到链表的尾部。**对于一个已经存在的键的重复赋值不会改变键的顺序**。需要注意的是，**一个 `OrderedDict` 的大小是一个普通字典的两倍，因为它内部维护着另外一个链表**，所以使用（尤其是数据太大）时有必要考虑内存消耗的问题。