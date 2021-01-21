---
title: 剑指Offer刷题日记-04
date: 2020-08-19 16:47:29
tags:    
    - 剑指Offer
    - 数组
categories: 数据结构与算法 
---

## 面试题4：二维数组中的查找
题目：在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。

<!-- more -->

## 思路


## Show me code
```C++
#include <iostream>
#include <vector>
using namespace std;

bool findNumberIn2DArray(vector<vector<int>>& matrix, int target) {
    int i = matrix.size() - 1, j = 0;
    while (i >= 0 && j < matrix[0].size()) {
        if (matrix[i][j] > target)
            i--;
        else if (matrix[i][j] < target)
            j++;
        else
            return true;
    }
    return false;
}

int main() {
    vector<vector<int>> vec_mat;
    // 测试矩阵
    //  1   2   8
    //  2   4   9
    //  4   7   10
    //  6   8   11
    int mat[][4] = {{1, 2, 8}, {2, 4, 9}, {4, 7, 10}, {6, 8, 11}};
    vec_mat.resize(4);
    for (int i = 0; i < 4; i++) {
        vec_mat[i].resize(3);
    }
    for (int j = 0; j < 4; j++) {
        for (int k = 0; k < 3; k++) {
            vec_mat[j][k] = mat[j][k];
        }
    }
    int target = 9;
    cout << findNumberIn2DArray(vec_mat, target) << endl;
}
```