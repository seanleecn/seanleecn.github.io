---
title: 剑指Offer刷题日记-实现库函数
date: 2020-08-18 16:24:33
tags: 
    - 剑指Offer
    - 实现库函数
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``实现库函数``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer16：数值的整数次方  
题目：实现函数double Power(double base, int exponent)，求base的exponent次方。
不得使用库函数，同时不需要考虑大数问题。

## 思路：递归法

### show me code
```C++
double power(double x, int n)
{
    if (n == 0)
        return 1;
    double res = power(x, n >> 1); //右移一位,相当于除以2
    //判断指数是基数还是偶数,用位与代替%
    if (n & 0x1 == 1)
    {
        res = res * res * x;
    }
    else
    {
        res = res * res;
    }
    return res;
}
double myPow(double x, int n)
{
    if (x == 0)
        return 0;
    long num = n;
    if (n >= 0)
    {
        return power(x, num);
    }
    else
    {
        return power(1 / x, -num);
    }
}
```

## 思路2：二分法
设res=1,则x^n=x^n*res=(x^2)^(n/2)*res=...=(x^n)^1*res 

### show me code
```C++
double myPow(double x, int n) {
    if (x == 1 || n == 0) return 1;
    double res = 1.0;
    long num = n;//int变量取负号可能越界
    if (n < 0) {
        x = 1 / x;
        num = -num;
    }
    while (num > 0) {
        if (num % 2 == 1) res *= x;
        x *= x;
        num /= 2;
    }
    return res;
} 
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    cout << myPow(2, 10) << endl;
    cout << myPow(2.1, 3) << endl;
    cout << myPow(2, -2) << endl;
}
```
 
# 剑指Offer67：求1+2+…+n
求 1+2+...+n ，要求不能使用乘除法、for、while、if、else、switch、case等关键字及条件判断语句（A?B:C）。

## 思路1：计算内存（太秀了）
```
1+2+3+...+n
=(1+n)*n/2
=sizeof(bool a[n][n+1])/2
=sizeof(bool a[n][n+1])>>1
```

### show me code
```C++
int sumNums(int n)
{
    bool a[n][n + 1];
    return sizeof(a) >> 1;
} 
```

## 思路2：逻辑运算的短路特性 
1. A && B
2. A 为 true，则计算并返回表达式 B 的 bool 值
3. A 为 false，则直接返回 false

### show me code
```C++
int sumNums(int n)
{
    n && (n += sumNums(n - 1));
    return n;
}
```

### 测试用例
```C++
int main()
{
    cout << sumNums(3) << endl; //6
}
```