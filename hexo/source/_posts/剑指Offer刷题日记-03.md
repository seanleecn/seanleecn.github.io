---
title: 剑指Offer刷题日记-03
date: 2020-08-18 16:24:33
tags: 
    - 剑指Offer
    - 数组
categories: 数据结构与算法 
toc: true
---

【剑指Offer刷题日记】系列的第一篇文章，从面试题3开始！冲冲冲！

## 面试题3：找出数组中重复的数字

题目：在一个长度为n的数组里的所有数字都在0到n-1的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，
也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。例如，如果输入长度为7的数组{2, 3, 1, 0, 2, 5, 3}， 那么对应的输出是重复的数字2或者3。

<!-- more -->

## 思路


## Show me code
```C++
#include <iostream>
#include <vector>
#include <algorithm>
#include <unordered_set>

using namespace std;

// 方法1：超时，双重遍历
// int findRepeatNumber(vector<int>& nums) {
//     int i, j;
//     int k = 0;
//     for (i = 0; i < nums.size(); i++) {
//         for (j = i + 1; j < nums.size(); j++) {
//             if (nums[i] == nums[j]) {
//                 return nums[i];
//             }
//         }
//     }
//     return -1;
// }

// 方法2：排序之后单循环
// int findRepeatNumber(vector<int>& nums){
//     sort(nums.begin(), nums.end());
//     for (int i = 0; i < nums.size();i++){
//         if (nums[i]==nums[i+1]){
//             return nums[i];
//         }
//     }
//     return -1;
// }

// 方法3：哈希表
// int findRepeatNumber(vector<int>& nums){
//     unordered_set<int> st;
//     //set的insert函数返回pair类型,second为bool变量表示是否插入成功
//     for (int i = 0; i < nums.size();i++){
//         if (st.insert(nums[i]).second == false){ // set唯一性导致插入失败
//             return nums[i];
//         }
//     }
//     return -1;
// }

//方法4：原地操作，空间复杂度O(1)
int findRepeatNumber(vector<int>& nums){
    if (!nums.size())
        return -1;
    for (int i = 0; i < nums.size(); i++){
        if (nums[i]<0 || nums[i]>nums.size()-1)
            return -1;
        while (nums[i] != i){
            //发现相同的值
            if (nums[i] == nums[nums[i]])
                return nums[i];
            //下标和值不是对应的话移动到交换元素
            swap(nums[i], nums[nums[i]]);
        }
    }
    return -1;
}

int main() {
    int data[] = {6, 2, 3, 0, 2, 5, 3};
    vector<int> vec(data, data + 7);
    int ans = findRepeatNumber(vec);
    cout << ans << endl;
    return 0;
}
```