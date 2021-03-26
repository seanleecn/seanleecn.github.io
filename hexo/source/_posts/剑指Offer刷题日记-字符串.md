---
title: 剑指Offer刷题日记-数组
date: 2020-08-18 16:24:33
tags: 
    - # 剑指Offer
    - 字符串
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``字符串``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer50：第一个只出现一次的字符

题目：在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。

## 思路：哈希表
### show me code
```C++
char firstUniqChar(string s)
{
    if (!s.length())
        return ' ';
    unordered_map<char, int> umap;
    for (char c : s)
    {
        umap[c] += 1; //语法糖
    }
    // 遍历s找到次数为1
    for (char c : s)
    {
        if (umap[c] == 1)
            return c;
    }
    return ' ';
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    cout << firstUniqChar("abaccdeff") << endl; // b
    cout << firstUniqChar("") << endl;          // " "
}
```

# 剑指Offer58（一）：翻转单词顺序

题目：输入一个英文句子，翻转句子中单词的顺序，但单词内字符的顺序不变。为简单起见，标点符号和普通字母一样处理。
例如输入字符串"I am a student. "，则输出"student. a am I"。

## 思路1：利用字符流的特性

primer p77介绍到，字符流会忽略开头的空白

### show me code
```C++
string reverseWords(string s)
{
    istringstream iss(s);
    string res, word;
    // 字符流会忽略空格得到单词
    while (iss >> word)
    {
        res = word + ' ' + res;
    }
    // res最后面会有个空格需要删除
    // 当输入为" "的时候，res为空，因此用erase为越界
    return res.substr(0, res.size() - 1);
} 
```

## 思路2：双指针

1. 两个指针从最右边开始左移
2. 遇到字符左指针移动，找到单词的开始和结束位置
3. 建立一个res来拼接单词

### show me code
```C++
string reverseWords(string s)
{
    int left = s.size() - 1;
    int right = s.size() - 1;
    string res = "";
    while (left >= 0)
    {
        // 跳过空格
        while (s[left] == ' ')
        {
            --left;
            --right;
            // 防止本层循环中s[left]越界
            if (left < 0)
                break;
        }
        // 防止下面计算s[left]越界
        if (left < 0)
            break;
        // 上面循环结束，左右指针都指向单词的最后一个字母
        while (s[left] != ' ')
        {
            --left;
            if (left < 0)
                break;
        }
        // 此时left指向单词的前一个字母,right指向单词的最后一个字母
        res += s.substr(left + 1, right - left);
        res.append(" ");
        right = left;
    }
    // 删除最后一个空格
    return res.substr(0, res.size() - 1);
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    cout << reverseWords("  hello world!  ") << endl;
    // 输出 : "world! hello"
    // 解释 : 输入字符串可以在前面或者后面包含多余的空格，但是反转后的字符不能包括。
    cout << reverseWords("the sky is blue") << endl;
    // blue is sky the
}
```

# 剑指Offer58（二）：左旋转字符串

题目：字符串的左旋转操作是把字符串前面的若干个字符转移到字符串的尾部。请定义一个函数实现字符串左旋转操作的功能。

比如，输入字符串"abcdefg"和数字2，该函数将返回左旋转两位得到的结果"cdefgab"。


## 思路1：substr一行秒杀！
### show me code
```C++
string reverseLeftWords(string s, int n)
{
    return s.substr(n) + s.substr(0, n);
} 
```

## 思路2：遍历string
### show me code
```C++
string reverseLeftWords(string s, int n)
{
    string res("");
    for (int i = n; i < s.size(); i++)
        res += s[i];
    for (int i = 0; i < n; i++)
        res += s[i];
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    cout << reverseLeftWords("lrloseumgh", 6) << endl;
    // 输出 : "umghlrlose"
}
```