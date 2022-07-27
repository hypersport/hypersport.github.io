---
title: C++ 将指针 delete 后要置为 null
author: hypersport
date: 2021-11-23 11:23:45 +0800
toc: false
categories: [C++]
tags: [指针]
render_with_liquid: false
---

直接看例子：

```cpp
#include <iostream>

using namespace std;

int main()
{
    int* p = new int(9);
    cout << "Before Delete P:" << endl;
    cout << "The Pointer Address Is: " << p << endl;
    cout << "The Value That P Point Is: " << *p << endl;
    cout << "=================================" << endl;

    delete p;
    cout << "After Delete P:" << endl;
    cout << "The Pointer Address Is: " << p << endl;
    cout << "The Value That P Point Is: " << *p << endl;
    cout << "=================================" << endl;

    cout << "Create A New Poniter:" << endl;
    int* newP = new int(8);
    cout << "The New Pointer Address Is: " << newP << endl;
    cout << "The Value That newp Point Is: " << *newP << endl;
    cout << "=================================" << endl;

    cout << "Assian A New Value To P:" << endl;
    *p = 10;
    cout << "The Value That P Point Is: " << *p << endl;
    cout << "The Value That newp Point Is: " << *newP << endl;

    return 0;
}
```

输出结果
```cpp
Before Delete P:
The Pointer Address Is: 0x7faa08705bf0
The Value That P Point Is: 9
==========================================
After Delete P:
The Pointer Address Is: 0x7faa08705bf0        // delete 后指针依然指向原来的地址
The Value That P Point Is: 9                  // 这里的值与编译器有关，有的是 0 或者随机值
==========================================
Create A New Poniter:
The New Pointer Address Is: 0x7faa08705bf0    // 新创建的指针和之前被 delete 的指针指向同一个地址
The Value That newp Point Is: 8
==========================================
Assian A New Value To P:
The Value That P Point Is: 10                // 修改被 delete 的指针指向的值
The Value That newP Point Is: 10             // 也会修改新创建的指针指向的值
```

从上面可以看出，delete 指针只是会释放指针指向的空间，但是不会删除指针本身。当紧接着创建一个新指针的时候，编译器会把刚刚回收的空间给新的指针，这样新旧指针就指向同一个地址，进而修改那个已经 *被删掉* 的指针 P 指向的值的时候也会改变新指针指向的值。

所以，**在 delete 一个指针后一定要手动置为 NULL** 。
