---
title: 剑指Offer刷题日记-动态规划
date: 2020-08-20 16:47:29
tags:    
    - 剑指Offer
    - 动态规划
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``动态规划``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer10：斐波那契数列
题目：写一个函数，输入n，求斐波那契（Fibonacci）数列的第n项。

斐波那契数列的定义如下：
```
F(0) = 0
F(1) = 1
F(N) = F(N - 1) + F(N - 2), 其中 N > 1.
```

## 思路1: 直接递归，O(2^N)复杂度
自顶向下计算,存在太多的重复中间项

### show me code
```C++
int fib(int n) {
    if (n == 0) {
        return 0;
    }
    if (n == 1) {
        return 1;
    } else {
        int res;
        res = fib(n - 1) + fib(n - 2);
        return res;
    }
} 
```

## 思路2:循环实现

自下向上(计算f(2),f(3)...直到f(n)) 

### show me code

```c++
int fib(int n) {
    if (n == 0)
        return 0;
    if (n == 1)
        return 1;
    int fibN, fibN_minus1(1), fibN_minus2(0);
    for (int i = 2; i <= n; i++) {
        fibN = (fibN_minus1 + fibN_minus2) % 1000000007;
        fibN_minus2 = fibN_minus1;
        fibN_minus1 = fibN;
    }
    return fibN;
} 
```

## 思路3：带剪枝的递归

自顶向下，从f(n)开始计算

### show me code

```c++
int helper(vector<int> &memo, int n)
{
    if (n == 1 || n == 2)
        return 1;
    if (memo[n] != 0) //如果已经计算过，直接返回就好了
        return memo[n];
    memo[n] = (helper(memo, n - 1) + helper(memo, n - 2)) % 1000000007;
    return memo[n];
}

int fib(int n)
{
    if (n <= 0)
        return 0;
    vector<int> memo(n + 1, 0); // 记录已经算过的数字，用于剪枝
    return helper(memo, n);
}
```

## 测试用例

```c++
int main(int argc, char **agrv)
{
    cout << "n=2: " << fib(2) << endl;   // out:1
    cout << "n=5: " << fib(5) << endl;   // out:5
    cout << "n=45: " << fib(45) << endl; // out:134903163
}
```

# 剑指Offer10-2：青蛙跳台阶问题

题目：一只青蛙一次可以跳上1级台阶，也可以跳上2级台阶。求该青蛙跳上一个n 级的台阶总共有多少种跳法。 

答案需要取模1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1。 

## 思路：动态规划
```
n=1 f(n)=1
n=2 f(n)=2
最后一跳只有两种可能:跳一层或者跳两层,跳一层的情况即f(3-1),跳两层的情况即f(3-2)
所以,f(3)=f(1)+f(2)
n=N f(N)=f(N-1)+f(N-2)
即本题目也是一个斐波那契数列,只不过前2项不同
```

### show me code

```c++
int numWays(int n)
{
    if (n == 0)
        return 1;
    if (n == 1)
        return 1;
    if (n == 2)
        return 2;
    int f, f_minus1(2), f_minus2(1);
    for (int i = 3; i <= n; i++)
    {
        f = (f_minus1 + f_minus2) % 1000000007;
        f_minus2 = f_minus1;
        f_minus1 = f;
    }
    return f;
}
```

## 测试用例
```c++
int main(int argc, char **argv)
{
    cout << "n=2: " << numWays(2) << endl;
    cout << "n=7: " << numWays(7) << endl;
    cout << "n=0: " << numWays(0) << endl;
}
```

# 剑指Offer14-1：剪绳子

题目：给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m-1] 。 

请问k[0]*k[1]*...*k[m-1] 可能的最大乘积是多少？

例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

# 剑指Offer14-2：剪绳子

题目：给你一根长度为 n 的绳子，请把绳子剪成整数长度的 m 段（m、n都是整数，n>1并且m>1），每段绳子的长度记为 k[0],k[1]...k[m - 1] 。

请问 k[0]*k[1]*...*k[m - 1] 可能的最大乘积是多少？
例如，当绳子的长度是8时，我们把它剪成长度分别为2、3、3的三段，此时得到的最大乘积是18。

答案需要取模 1e9+7（1000000007），如计算初始结果为：1000000008，请返回 1

## 思路14_1:动态规划
定义f(n)为长度为n的绳子剪成m段乘积的最大值

自底向上计算 

### show me code

```c++
int cuttingRope(int n)
{
    //长度n<=3做特殊处理
    if (n < 2)
        return 0;
    if (n == 2)
        return 1; //1<2
    if (n == 3)
        return 2; //2<3
    // maxs[n]表示长度为n的绳子得到的结果
    vector<int> maxs(n + 1, 0);
    // n=1,2,3的时候,如果再分割的话,结果比不分割还小,所以当绳子到1,2,3长度之后就不剪了,返回的值就是1,2,3
    maxs[1] = 1;
    maxs[2] = 2;
    maxs[3] = 3;

    int max_i, i, j;
    for (i = 4; i <= n; i++)
    { //自下而上计算最大值,并存在maxs中
        max_i = 0;
        for (j = 1; j <= i / 2; j++)
        { //i/2为了减少计算次数
            int product_max = maxs[j] * maxs[i - j];
            if (product_max > max_i)
            {
                max_i = product_max;
            }
        }
        maxs[i] = max_i;
    }
    return maxs[n];
}
```
 
## 思路2: 贪婪

第二题比第一题多了大数求余步骤

当n>=5,3(n-3)>=2(n-2)

当n=4时,最大的情况是2*2=4,即可以不用剪 

### show me code

```c++
int cuttingRope(int n)
{
    if (n < 1)
        return 0;
    if (n == 2)
        return 1;
    if (n == 3)
        return 2;
    long res = 1;
    while (n > 4)
    {
        res *= 3;
        res = res % 1000000007;
        n -= 3;
    }
    return res * n % 1000000007;
}

int main(int argc, char **argv)
{
    cout << cuttingRope(8) << endl;  //期望输出18
    cout << cuttingRope(2) << endl;  //期望输出1
    cout << cuttingRope(10) << endl; //期望输出36
}
```
# 剑指Offer42：连续子数组的最大和
题目：输入一个整型数组，数组里有正数也有负数。数组中一个或连续的多个整数组成一个子数组。求所有子数组的和的最大值。要求时间复杂度为O(n)。


## 思路：动态规划！

1. 用dp[i]表示以i为nums[i]为结尾的连续子数组的最大和

2. 状态转移方程

当dp[i-1] < 0，说明不如直接用nums[i]作为dp[i]

当dp[i-1] > 0，说明nums[i] + dp[i-1]就是dp[i]

3. 返回dp数组的最大值 

### show me code

```c++
int maxSubArray(vector<int> &nums)
{
    vector<int> dp(nums.size(), nums[0]); //初始化为nums[0]
    for (int i = 1; i < nums.size(); ++i)
    {
        if (dp[i - 1] <= 0)
            dp[i] = nums[i];
        else
            dp[i] = nums[i] + dp[i - 1];
    }
    return *max_element(dp.begin(), dp.end()); // #include <algorithm>
}
```

## 测试用例

```c++
int main()
{
    vector<int> nums = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << maxSubArray(nums) << endl; //期望输出6
}
```

# 剑指Offer46：把数字翻译成字符串

题目：给定一个数字，我们按照如下规则把它翻译为字符串：0翻译成"a"，1翻译成"b"，……，11翻译成"l"，……，25翻译成"z"。

一个数字可能有多个翻译。例如12258有5种不同的翻译，它们分别是"bccfi"、"bwfi"、"bczi"、"mcfi"和"mzi"。

请编程实现一个函数用来计算一个数字有多少种不同的翻译方法。

## 思路：动态规划

这题的递推公式有点难以理解，以12258为例子

dp[i]表示长度为i的数字有几种翻译方式

1. 最后一个数字单独翻译，根据*分步乘法*（先翻译前i-1位，再翻译最后1位，而最后1位只有一种翻译方法）得到dp[i] = dp[i-1] * 1

2. 最后两个数字可以组合翻译，根据*分类加法*（最后两位组合翻译+最后两位单独翻译）得到dp[i] = dp[i-1] * 1 + dp[i-2] * 1
因此可以得到

dp[i] = dp[i-1]            10*(i-1)+i不在[10,25]的范围,不能被翻译

dp[i] = dp[i-1] + dp[i-2]  10*(i-1)+i在[10,25]的范围，能被翻译

### show me code

```c++
int translateNum(int num)
{
    string str = to_string(num);
    int len = str.length();
    // 初始化dp
    vector<int> dp(len + 1);
    dp[0] = 1;
    dp[1] = 1;

    for (int i = 2; i < len + 1; i++)
    {
        if (str[i - 2] == '1' || (str[i - 2] == '2' && str[i - 1] <= '5'))
        {
            dp[i] = dp[i - 1] + dp[i - 2];
        }
        else
        {
            dp[i] = dp[i - 1];
        }
    }
    return dp[len];
}
```

## 测试用例

```c++
int main(int argc, char **argv)
{
    cout << translateNum(12258) << endl; //5
}
```

# 剑指Offer47：礼物的最大价值
题目：在一个m×n的棋盘的每一格都放有一个礼物，每个礼物都有一定的价值（价值大于0）。

你可以从棋盘的左上角开始拿格子里的礼物，并每次向右或者向下移动一格直到到达棋盘的右下角。

给定一个棋盘及其上面的礼物，请计算你最多能拿到多少价值的礼物？


## 思路：动态规划
1. dp数组表示某一个格子的最大价值
2. 状态转移方程 
   
   dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]) + grid[i][j];

### show me code

```c++
int maxValue(vector<vector<int>> &grid)
{
    int row = grid.size();
    int col = grid[0].size();
    // 由于动态转移方程中涉及到了[i-1]的索引，因此将dp数组扩大一行一列，防止索引越界
    vector<vector<int>> dp(row + 1, vector<int>(col + 1, 0));
    // dp[i][j] 表示grid[0][0] -> grid[i-1][j-1]的最大价值
    for (int i = 1; i <= row; i++)
    {
        for (int j = 1; j <= col; j++)
        {
            dp[i][j] = max(dp[i - 1][j], dp[i][j - 1]) + grid[i - 1][j - 1];
        }
    }
    // 注意索引
    return dp[row][col];
}
```

## 测试用例

```c++
int main()
{
    vector<vector<int>> grid = {{1, 3, 1},
                                {1, 5, 1},
                                {4, 2, 1}};
    cout << maxValue(grid) << endl;
    // 12 ,路径为 1→3→5→2→1
}
```

# 剑指Offer49：丑数

题目：我们把只包含因子2、3和5的数称作丑数（Ugly Number）。求按从小到大的顺序的第n个丑数。

习惯上我们把1当做第一个丑数。 

## 思路：动态规划
1. 任意的丑数*2、*3、*5都是丑数（2、3、5都是质数）
2. 使用一个dp数组保存全部的丑数，dp[0] = 1
3. 已知一个丑数dp[n]，则dp[n+1]应满足min(dp[a]*2, dp[b]*3, dp[c]*5)，其中a、b、c是以2、3、5为因子的下标
4. 关于dp的理解：根据特点1可知，对于一个丑数数列，分别*2、*3、*5得到三个数列A、B、C都是丑数数列。因此需要把这三个数列整合成一个，整合的方法就是建立三个指针，每次比较这三个指针中那个值得数最小，然后指针后移一位

### show me code

```c++
int nthUglyNumber(int n)
{
    // dp初始化
    vector<int> dp(n, 0);
    dp[0] = 1;
    // 三个下标初始化
    int a = 0, b = 0, c = 0;
    for (int i = 1; i < n; ++i)
    {
        int n2 = dp[a] * 2;
        int n3 = dp[b] * 3;
        int n5 = dp[c] * 5;
        dp[i] = min(min(n2, n3), n5);
        if (dp[i] == n2)
            a++;
        if (dp[i] == n3)
            b++;
        if (dp[i] == n5)
            c++;
    }
    return dp[n - 1];
}
```

## 测试用例

```c++
int main()
{
    cout << nthUglyNumber(10) << endl; //12
}
```
# 剑指 Offer 60. n个骰子的点数

题目：把n个骰子扔在地上，所有骰子朝上一面的点数之和为s。输入n，打印出s的所有可能的值出现的概率。

你需要用一个浮点数数组返回答案，其中第 i 个元素代表这 n 个骰子所能掷出的点数集合中第 i 小的那个的概率。

## 思路：动态规划
1. n个骰子，朝上的面点数之和s范围是[n,6n]，因此返回数组长度为6n-n+1
2. 根据概率知识，结果s出现的概率表示为 s出现的次数/6^n
3. 动态转移方程 dp[n][s] = dp[n-1][s-k] k属于[1,6]
    举例：投3个骰子得到7的概率，可以表示为投2个骰子得到2，3，4，5，6的概率

### show me code

```c++
vector<double> dicesProbability(int n)
{
    vector<double> res(n * 6 - n + 1);
    vector<vector<int>> dp(n + 1, vector<int>(6 * n + 1, 0)); // 将全部值初始化为 0
    int row = n + 1, col = 6 * n + 1;                         //col ==> [0,6n] 共6n+1列
    // 初始化第一个骰子
    // dp[1][1]=1 代表一个骰子点数和为 1 的情况有一种
    // dp[1][2]=1 代表一个骰子点数和为 2 的情况有一种
    for (int i = 1; i <= 6; i++)
        dp[1][i] = 1; //因为只有一个骰子时，点数和为1,2,3,4,5,6的情况都各只有一种
    for (int i = 2; i < row; i++)
    { //从2颗骰子开始计算
        for (int j = i; j < col; j++)
        { //j就是点数之和s,是从i开始的，代表i个骰子点数之和的最小值为i
            for (int k = 1; k <= 6; k++)
            {
                //dp[i][j]表示 i个骰子点数之和为j的数量有多少？
                //答：当第i个骰子点数为k时，那么前i-1个骰子点数和必须等于j-k，则k+(j-k)=j;
                //把所有dp[i-1][j-k]的数量加起来(k∈[1,6])，就等于dp[i][j]的数量了
                if (j - k > 0)
                    dp[i][j] += dp[i - 1][j - k];
                else
                    break;
            }
        }
    }
    double denominator = pow(6.0, n); // 分母
    int index = 0;
    for (int k = n; k <= 6 * n; k++)
        //计算概率 放入答案
        res[index++] = dp[n][k] / denominator; //n个骰子，共可以产生6^n种
    return res;
}
```

## 测试用例

```c++
int main()
{
    vector<double> res = dicesProbability(2);
    for (auto i : res)
        cout << i << " ";
    cout << endl;
}
```

# 剑指Offer63：股票的最大利润

题目：假设把某股票的价格按照时间先后顺序存储在数组中，请问买卖交易该股票可能获得的利润是多少？

## 思路1：贪心
股票的奥义:最低点买入，最高点卖出
一轮遍历，每次更新最低值和收益

### show me code

```c++
int maxProfit(vector<int> &prices)
{
    // 没有卖出的可能
    if (prices.size() < 2)
        return 0;
    int minPrice = prices[0];
    int profit = 0;
    for (int i : prices)
    {
        minPrice = min(minPrice, i);
        profit = max(profit, i - minPrice);
    }
    return profit;
} 
```

## 思路2：动态规划
1. dp[i]表示第i天的最大利润
2. 初始化dp[o] = 0
3. 状态转移 dp[i] = max(dp[i-1] , price[i] - minPrice[0:i-1])
   第i天的最大收益 = 第i-1天的最大收益 以及 [1:i-1]天中最低价买入，当天卖出 这两种情况中较大的值  
4. 对比思路1，状态转移中dp[i]只和dp[i-1]相关，因此可以用一个变量profit表示，这样就和思路1一样的形式了 

### show me code

```c++
int maxProfit(vector<int> &prices)
{
    int day = prices.size();
    if (day < 2)
        return 0;
    vector<int> dp(day, 0);
    int minPrice = prices[0];
    for (int i = 1; i < day; ++i)
    {
        dp[i] = max(dp[i - 1], prices[i] - minPrice);
        minPrice = min(minPrice, prices[i]);
    }
    return dp[day - 1];
}
```

## 测试用例

```c++
int main()
{
    vector<int> prices1 = {7, 1, 5, 3, 6, 4};
    cout << maxProfit(prices1) << endl; // 5
    vector<int> prices2 = {7, 6, 4, 3, 1};
    cout << maxProfit(prices2) << endl; // 0
}
