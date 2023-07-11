---
title: Windows 编程宏被系统宏替换
author: hypersport
date: 2022-06-06 22:06:06 +0800
categories: [Windows]
tags: [macro]
render_with_liquid: false
---

### 问题描述

自己写的或者使用第三方库里的一些方法名与 *Windows* 中的一些系统宏同名而被替换，导致编译错误

### 原因分析

以 *GetObject* 为例，*GetObject* 方法在 *wingdi.h* 中被定义为 *GetObjectW* 或 *GetObjectA*：

```cpp
#ifdef UNICODE
#define GetObject  GetObjectW
#else
#define GetObject  GetObjectA
#endif // !UNICODE
```
{: file='wingdi.h'}

包含该头文件或者 *windows.h* 或者 *framework.h* 的时候会自动包含

### 解决方法

1. 将自己写的或者第三方库包含该方法的头文件放在 *wingdi.h* 或 *windows.h* 或 *framework.h* 之前

2. 使用该方法的时候先将宏压栈取消定义，执行完毕再重新出栈恢复定义

```cpp
#ifdef GetObject
#pragma push_macro("GetObject")
#undef GetObject
	auto outcome = client_.GetObject(request);
#pragma pop_macro("GetObject")
#else
	auto outcome = client_.GetObject(request);
#endif // GetObject
```
