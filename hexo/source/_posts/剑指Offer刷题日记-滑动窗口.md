---
title: 剑指Offer刷题日记-滑动窗口
date: 2020-08-18 16:24:33
tags: 
    - 剑指Offer
    - 滑动窗口
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``滑动窗口``相关的题目## 思路及解答。

文章会给出## 思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->



# 剑指Offer48：最长不含重复字符的子字符串
题目：请从字符串中找出一个最长的不包含重复字符的子字符串，计算该最长子字符串的长度。
假设字符串中只包含从'a'到'z'的字符。


## 思路：滑动窗口
1. 初始化两个指针left 和 right，都指向字符串最左边 
2. right 指针遍历字符串，并将所指的字符添加到hash表
3. 每添加一个元素进hash，检查是否存在相同元素。若存在，left指针右移，直到hash表中没有重复元素
4. res更新当前滑动窗口中的字符串长度

### show me code
```C++
int lengthOfLongestSubstring(string s)
{
    unordered_map<char, int> hash;
    int res = 0;
    for (int left = 0, right = 0; right < s.size(); right++)
    {
        hash[s[right]]++;
        // 检查重复元素并消除重复元素
        while (hash[s[right]] > 1)
        {
            hash[s[left]]--;
            ++left;
        }
        res = max(res, right - left + 1);
    }
    return res;
}
```
### 测试用例
```C++
int main()
{
    cout << lengthOfLongestSubstring("abcabcbb") << endl; // 3
    cout << lengthOfLongestSubstring("bbbbb") << endl;    // 1
}
```

# 剑指Offer57（一）：和为s的两个数字
题目：输入一个递增排序的数组和一个数字s，在数组中查找两个数，使得它们的和正好是s。如果有多对数字的和等于s，输出任意一对即可。 



## 思路：
首先，双循环会超时，且题目给出了是递增数列，所以采用双指针
建立两个指针，分别指向数组的头和尾
### show me code
```C++
vector<int> twoSum(vector<int> &nums, int target)
{
    vector<int> res(2);
    int i = 0;
    int j = nums.size() - 1;
    // 注意这个判断条件
    while (i < j)
    {
        if (nums[i] + nums[j] > target)
        {
            --j;
        }
        else if (nums[i] + nums[j] < target)
        {
            ++i;
        }
        else
        {
            res[0] = nums[i];
            res[1] = nums[j];
            break;
        }
    }
    return res;
}
```
### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> test1 = {10, 26, 30, 31, 47, 60};
    for (auto i : twoSum(test1, 40))
    {
        cout << i << " ";
    }
    cout << endl;
    // 10 30
}
```

# 剑指Offer57（二）：为s的连续正数序列
题目：输入一个正数s，打印出所有和为s的连续正数序列（至少含有两个数）。
序列内的数字由小到大排列，不同序列按照首个数字从小到大排列。

## 思路：滑动窗口

### show me code
```C++
vector<vector<int>> findContinuousSequence(int target)
{
    vector<vector<int>> res;
    vector<int> ans;
    // i为窗口的左端点，j为窗口的右端点，sum为窗口内元素之和
    int i = 1;
    int j = 2;
    int sum = 3;
    while (i < j)
    {
        if (sum == target)
        {
            for (int k = i; k <= j; k++)
            {
                ans.push_back(k);
            }
            res.push_back(ans);
            ans.clear();
        }
        // 元素和太小，j右移一位
        if (sum < target)
        {
            ++j;
            sum += j;
        }
        // 元素和太大，i右移一位
        else
        {
            sum -= i;
            ++i;
        }
    }
    return res;
}
```

### 测试用例
```C++
int main(int arg, char **argv)
{
    vector<vector<int>> out1 = findContinuousSequence(9);
    // [ [ 2, 3, 4 ], [ 4, 5 ] ]
    for (auto vec : out1)
    {
        for (auto i : vec)
        {
            cout << i << " ";
        }
        cout << endl;
    }
    cout << endl;
    vector<vector<int>> out2 = findContinuousSequence(15);
    // [[1,2,3,4,5],[4,5,6],[7,8]]
    for (auto vec : out2)
    {
        for (auto i : vec)
        {
            cout << i << " ";
        }
        cout << endl;
    }
}
```