---
title: 剑指Offer刷题日记-05
toc: true
date: 2020-08-20 16:47:29
tags:    
    - 剑指Offer
    - 串
categories: 数据结构与算法 
---

## 面试题5：替换空格
题目：请实现一个函数，把字符串中的每个空格替换成"%20"。例如输入“We are
happy.”， 则输出“We%20are%20happy.”。

<!-- more -->

## 思路
直接遍历，然后增删改查...

## Show me code
```C++
#include <iostream>
#include <string>
using namespace std;

string replaceSpace(string s) {
    string ss = "%20";
    for (int i = 0; i < s.length(); i++) {
        if (s[i] == ' ') {
            s.erase(i, 1);
            s.insert(i, ss);
        }
    }
    return s;
}

int main(int argc, char** agrv) {
    string s = "we are happy";
    cout << s << endl;
    cout << replaceSpace(s) << endl;
}
```