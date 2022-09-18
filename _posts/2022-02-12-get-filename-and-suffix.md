---
title: Linux 获取文件名和后缀
author: hypersport
date: 2022-02-12 22:02:12 +0800
categories: [Linux]
tags: [文件名]
toc: false
render_with_liquid: false
---

### 举例说明

``filename="this.is.a.sample.txt"``

| 命令             | 结果                                                |
|:----------------|:----------------------------------------------------|
| ${filename%*.}  | this.is.a.sample (从右向左删除第一个点右边的，非贪婪模式) |
| ${filename%%*.} | this             (从右向左删除最后一个点右边的，贪婪模式) |
| ${filenam#*.}   | is.a.sample.txt  (从左向右删除第一个点左边的，非贪婪模式) |
| ${filenam##*.}  | txt              (从左向右删除最后一个点左边的，贪婪模式) |
