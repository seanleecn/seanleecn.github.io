---
title: 剑指Offer刷题日记-位运算
date: 2020-08-23 16:24:33
tags: 
    - 剑指Offer
    - 位运算
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``位运算``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer15：二进制中1的个数
题目：请实现一个函数，输入一个整数，输出该数二进制表示中1的个数。
例如把9表示成二进制是1001，有2位是1。因此如果输入9，该函数输出2。

### show me code
```C++
int hammingWeight(uint32_t n)
{
    int count = 0;
    while (n != 0)
    {
        if (n & 1)
            count += 1;
        n >>= 1;
    }
    return count;
}
```

### show me code
```C++
int main(int argc, char **agrv)
{
    cout << hammingWeight(11) << endl;         //输入00000000000000000000000000001011,期望输出3
    cout << hammingWeight(4294967293) << endl; //输入11111111111111111111111111111101,期望输出31
}
```

# 剑指Offer56（一）：数组中数字出现的次数
题目：一个整型数组里除了两个数字之外，其他的数字都出现了两次。
请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。



## 思路：异或运算
首先，拿hashmap统计次数的方法不行，不满足空间复杂度的要求
异或运算的性质：
1. 任何数 x 与 0 异或，结果为 x（恒等律）
2. 任何数与自身异或，结果为 0（归零律）
3. 异或运算还满足交换律与结合律
   
以输入的测试vector为例子
```
1 ^ 2 ^ 10 ^ 4 ^ 1 ^ 4 ^ 3 ^ 3 = 2 ^ 10 = 0010 ^ 1010 = 1000
```
计算结果是*两个只出现一次的数*的二进制形式
为什么会出现1000这个结果？
原因是，当数组进行异或运算之后，最后的结果只和*两个只出现一次的数*有关，其他的数字都被归零了
如果这个数组里面的数字都出现了两次的，那么最后输出的结果一定是0
在测试案例中，因为存在2和10，导致在输出结果的1*右到左第四位*没有归零(1000)
下面是关键
我们把数组分成两个数组，分组依据是这个数的二进制形式在*右到左第四位*是0还是1
出现两次的数字*右到左第四位*一定相同，这样分别在这两个数组中进行异或运算，就能得到出现一次的数字

### show me code
```C++
vector<int> singleNumbers(vector<int> &nums)
{
    int exor = 0; //异或结果
    // 得到两个只出现一次的数字的异或结果
    for (int i : nums)
    {
        exor = exor ^ i;
    }
    // 找到从右到左第一个1的位置
    int index_one = 1; // 二进制形式是0001
    while (!(index_one & exor))
    {
        index_one <<= 1;
    }
    vector<int> res(2, 0); // 有两个0的数组
    // 根据index_one对数组进行分组处理
    for (int i : nums)
    {
        if (i & index_one)
        {
            res[0] = res[0] ^ i;
        }
        else
        {
            res[1] = res[1] ^ i;
        }
    }
    return res;
}
```

### show me code
```C++
int main()
{
    vector<int> vec = {1, 2, 10, 4, 1, 4, 3, 3};
    for (int i : singleNumbers(vec))
    {
        cout << i << " ";
    }
    cout << endl;
    // 输出：[2,10] 或 [10,2]
}
```

# 剑指Offer56（一）：数组中数字出现的次数 II
题目：在一个数组 nums 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字

## 思路1：hashmap

### show me code
```C++ 
int singleNumber(vector<int> &nums)
{
    unordered_map<int, int> hashmap;
    for(int i : nums)
    {
        hashmap[i]++;
    }
    unordered_map<int, int>::const_iterator itr;
    for (itr = hashmap.begin(); itr != hashmap.end();itr++)
    {
        if (itr->second == 1)
            return itr->first;
    }
    return 0;
} 
```
 
## 思路2：位运算
把数组中每个数字的二进制形式的每一位都加起来，这样出现三次的数字每一位能够被3整除
当某一位不能整除，说明这个出现一次的数字在这里是1

### show me code
```C++
int singleNumber(vector<int> &nums)
{
    vector<int> bits(32, 0); //题干中给出int最大到2^31,所以需要一个32位的二进制
    // 每个数字转2进制并累加起来
    for (int num : nums)
    {
        for (int index = 31; index >= 0; index--)
        {
            bits[index] += (num & 1);
            num >>= 1;
        }
    }
    int res = 0;
    // 找出哪一位不能被3整除
    for (int i = 31; i >= 0; --i)
    {
        if (bits[i] % 3 != 0)
        {
            res += pow(2, (31 - i));
        }
    }
    return res;
}
```

### 测试用例
```c++
int main()
{
    vector<int> vec = {9, 1, 7, 9, 7, 9, 7};
    cout << singleNumber(vec) << endl; //1
}
```

# 剑指Offer65：不用加减乘除做加法
题目：写一个函数，求两个整数之和，要求在函数体内不得使用＋、－、×、÷四则运算符号。

## 思路：位运算
十进制的加法步骤(5+17)：
1. 不进位加法 12
2. 计算进位，且进位的值是10
3. 上面两步相加 22

用二进制运算步骤(101+10001)
1. 不进位加法 10100
2. 计算进位，且进位的值为10(二进制)
3. 上面两步相加 10110(22)

转换到位运算上
1. 不进位加法 == 异或运算
2. 进位 == (a & b) << 1
3. 两步相加 ==递归实现，直到没有进位

### show me code
```C++
int add(int a, int b)
{
    // 递归终止条件:进位为0
    if (b == 0)
    {
        return a;
    }
    int sum = a ^ b;
    int carry = (unsigned int)(a & b) << 1;
    return add(sum, carry);
}
```

### 测试用例
```c++
int main()
{
    cout << add(1, 2) << endl;
}
```
