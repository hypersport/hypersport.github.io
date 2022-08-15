---
title: 三维空间点到线的距离
author: hypersport
date: 2021-12-12 12:12:12 +0800
toc: false
categories: [算法]
tags: [距离]
render_with_liquid: false
---

```cpp
#include <cmath>

struct Point
{
    float x;
    float y;
    float z;

    Point(float x_, float y_, float z_)
    {
        x = x_;
        y = y_;
        z = z_;
    }

    float Dot(const Point &p) const
    {
        return x * p.x + y * p.y + z * p.z;
    }

    Point operator-(const Point &p) const
    {
        return Point(x - p.x, y - p.y, z - p.z);
    }
};

bool IsPointInLinePlane(const Point &a, const Point &b, const Point &p)
{
    Point ba = a - b;
    Point pa = a - p;
    Point bp = p - b;
    return ba.Dot(pa) < 0 ? false : ba.Dot(bp) > 0;
}

float DistanceOfPointToLine(const Point &a, const Point &b, const Point &s)
{
    float ab = sqrt(pow((a.x - b.x), 2) + pow((a.y - b.y), 2) + pow((a.z - b.z), 2));
    float as = sqrt(pow((a.x - s.x), 2) + pow((a.y - s.y), 2) + pow((a.z - s.z), 2));
    float bs = sqrt(pow((s.x - b.x), 2) + pow((s.y - b.y), 2) + pow((s.z - b.z), 2));
    if (IsPointInLinePlane(a, b, s))
    {
        float cos_A = (pow(as, 2) + pow(ab, 2) - pow(bs, 2)) / (2 * ab * as);
        float sin_A = sqrt(1 - pow(cos_A, 2));
        return as * sin_A;
    }
    return as < bs ? as : bs;
}
```
