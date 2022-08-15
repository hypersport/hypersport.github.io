---
title: 无向图中的所有连通分量
author: hypersport
date: 2021-12-23 12:23:34 +0800
toc: false
categories: [算法]
tags: [无向图, 连通分量]
render_with_liquid: false
---

```cpp
#include <map>
#include <list>
#include <vector>

using namespace std;

int merge(vector<int> parent, int x)
{
    if (parent[x] == x)
    {
        return x;
    }
    return merge(parent, parent[x]);
}

vector<vector<int>> connected_components(int n, vector<pair<int, int>> &edges)
{
    vector<int> parent;
    vector<vector<int>> result;
    for (int i = 0; i < n; i++)
    {
        parent.push_back(i);
    }
    for (const auto &x : edges)
    {
        parent[merge(parent, x.first)] = merge(parent, x.second);
    }
    for (int i = 0; i < n; i++)
    {
        parent[i] = merge(parent, parent[i]);
    }
    map<int, list<int>> m;
    for (int i = 0; i < n; i++)
    {
        m[parent[i]].push_back(i);
    }
    for (auto it = m.begin(); it != m.end(); it++)
    {
        list<int> l = it->second;
        vector<int> temp;
        for (const auto &x : l)
        {
            temp.push_back(x);
        }
        result.push_back(temp);
    }
    return result;
}
```
