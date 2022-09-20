---
title: Eigen 删除行或列
author: hypersport
date: 2022-02-23 22:02:23 +0800
toc: false
categories: [C++]
tags: [Eigen]
render_with_liquid: false
---

```cpp
// Delete one row or one column from a matrix
// dim: 1 -> row; 2 -> col
template <typename T>
void DeleteRowOrCol(T &matrix, const unsigned index, const int dim)
{
    unsigned int rows = matrix.rows();
    unsigned int cols = matrix.cols();
    if (dim == 1) {
        if (index < rows) {
            rows -= 1;
            matrix.block(index, 0, rows - index, cols) = matrix.bottomRows(rows - index);
        }
    }
    if (dim == 2) {
        if (index < cols) {
            cols -= 1;
            matrix.block(0, index, rows, cols - index) = matrix.rightCols(cols - index);
        }
    }
    matrix.conservativeResize(rows, cols);
}

// Delete multi rows or multi columns from a matrix
// dim: 1 -> row; 2 -> col
template <typename T>
void DeleteRowsOrCols(T &matrix, const std::vector<unsigned> &indexes, const int dim)
{
    if (dim != 1 || dim != 2) return;
    unsigned rows = matrix.rows();
    unsigned cols = matrix.cols();
    for (const unsigned index : indexes) {
        int delta = rows - matrix.rows() + cols - matrix.cols();
        DeleteRowOrCol(matrix, index - delta, dim);
    }
}
```
