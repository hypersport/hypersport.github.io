---
title: Python 命名元组 namedtuple
author: hypersport
date: 2021-12-31 12:31:12 +0800
categories: [Python]
tags: [namedtuple]
render_with_liquid: false
---

### 原型
```python
collections.namedtuple(typename, field_names, *, rename=False, defaults=None, module=None)
```

其中：

**`typename`**：创建的子类类名

**`field_names`**：字符串序列，类似字典中的`key`，通过它操作命名元组。可以是`list`，如`['name', 'price']`，也可以是逗号或者空格分割的字符串，如 `'name, price'` 或者 `'name price'`。

**`*`**：`python`函数参数单独一个星号表示星号后面的所有参数都只能以关键参数的形式进行传值，不接收其他任何形式的传值。

**`rename`**：默认 `false`，如果 `rename=True`，那么无效的字段名会被替换为 `'_索引值'`。比如 `['abc', 'def', 'ghi', 'abc']` 会被转换成 `['abc', '_1', 'ghi', '_3']`，其中与关键字重名的 `'def'` 和重复的 `'abc'` 都被替换成了 `'_索引值'`。

**`defaults`**：默认 `None`，按 `field_names` 中的名字一次赋默认值。

`module`：定义类的模块名

### 举例

```python
from collections import namedtuple

Point = namedtuple('Point', ['x', 'y'])

# 通过位置参数或者关键字参数实例化
p = Point(11, y=22)

# 索引取值或命名取值
print(p[0] + p.y)

# 像普通元组一样解包
x, y = p
print(x, y)
```

### 其他方法

命名元组独有的方法都以下划线开头

```python
# _make 通过序列或者可迭代对象实例化
t = [11, 22]
print(Point._make(t))

# _asdict 返回有序字典 OrderedDict
p = Point(11, 22)
print(p._asdict())

# _replace 接收关键字参数，给指定的字段重新赋值，返回新的命名元组，但不改变原来的命名元组
p = Point(11, 22)
new_p = p._replace(x=33)

# _fields 返回字段名
print(p._fields)

# 可以用和命名元组字段同名的字典生成命名元组
d = {'x': 11, 'y': 22}
p = Point(**d)
```
