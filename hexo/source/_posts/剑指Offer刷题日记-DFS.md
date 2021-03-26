---
title: 剑指Offer刷题日记-DFS
date: 2020-08-18 16:24:33
tags: 
    - # 剑指Offer
    - DFS
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``DFS``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer12：矩阵中的路径

题目：请设计一个函数，用来判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。

路径可以从矩阵中的任意一格开始，每一步可以在矩阵中向左、右、上、下移动一格。

如果一条路径经过了矩阵的某一格，那么该路径不能再次进入该格子。

例如，在下面的3×4的矩阵中包含一条字符串“bfce”的路径（路径中的字母用加粗标出）。
```
[[ "a", "b", "c", "e" ],
 [ "s", "f", "c", "s" ],
 [ "a", "d", "e", "e" ]]
```
但矩阵中不包含字符串“abfb”的路径，因为字符串的第一个字符b占据了矩阵中的第一行第二个格子之后，路径不能再次进入这个格子。

## 思路:DFS+回溯法

### show me code
```C++
//定义一个偏移数组,用于不同方向的搜索
int dir[4][2] = {{-1, 0}, {1, 0}, {0, -1}, {0, 1}};

bool DFS(int i, int j, int index, vector<vector<char>> &board, const string &word, vector<vector<bool>> &visited)
{
    //递归终止条件
    if (index == word.size() - 1)
    {
        return word[index] == board[i][j];
    }
    if (word[index] == board[i][j])
    {
        visited[i][j] = true;
        // 4个方向都要DFS
        for (int k = 0; k < 4; k++)
        {
            // k:0->3对应左 右 下 上
            int new_i = i + dir[k][0];
            int new_j = j + dir[k][1];
            // 探索的方向需要满足以下约束条件:
            // 1.非负;
            // 2.没有越界;
            // 3.没有访问过
            if (new_i >= 0 && new_j >= 0 && new_i < board.size() &&
                new_j < board[0].size() && !visited[new_i][new_j])
            {
                if (DFS(new_i, new_j, index + 1, board, word, visited))
                    return true;
            }
        }
        visited[i][j] = false;
    }
    return false;
}

bool exist(vector<vector<char>> &board, string word)
{
    int row = board.size();
    int column = board[0].size();
    //!!!简介的二维vector初始化的方法!!!
    vector<vector<bool>> visited(row, vector<bool>(column));
    for (int i = 0; i < row; i++)
    {
        for (int j = 0; j < column; j++)
        {
            if (DFS(i, j, 0, board, word, visited))
                return true;
        }
    }
    return false;
}
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    vector<vector<char>> mat1 = {{'A', 'B', 'C', 'E'},
                                 {'S', 'F', 'C', 'S'},
                                 {'A', 'D', 'E', 'E'}};

    string word1 = "ABCCED";
    cout << exist(mat1, word1) << endl; //期望输出1
}
```

# 剑指Offer13：机器人的运动范围

题目：地上有一个m行n列的方格。一个机器人从坐标(0, 0)的格子开始移动，它每一次可以向左、右、上、下移动一格，但不能进入行坐标和列坐标的数位之和大于k的格子。

例如，当k为18时，机器人能够进入方格(35, 37)，因为3+5+3+7=18。但它不能进入方格(35, 38)，因为3+5+3+8=19。
请问该机器人能够到达多少个格子？

## 思路:二维数组双重循环遍历+DFS

### show me code
```C++
int getDigit(int num)
{
    int sum = 0;
    while (num > 0)
    {
        sum += num % 10; //个位数
        num /= 10;       //十位数
    }
    return sum;
}

void DFS(int row, int col, int m, int n, int k, vector<vector<bool>> &visited, int &count)
{
    // 数组越界
    if (row < 0 || row >= m || col < 0 || col >= n)
        return;
    // 坐标的数字和大于k
    if (getDigit(row) + getDigit(col) > k)
        return;
    // 访问过了
    if (visited[row][col] == true)
        return;

    visited[row][col] = true;
    count++;
    DFS(row + 1, col, m, n, k, visited, count); //向下搜索
    DFS(row, col + 1, m, n, k, visited, count); //向右搜索
}

int movingCount(int m, int n, int k)
{
    //!!!二维数组初始化并定义大小和初值
    vector<vector<bool>> visited(m, vector<bool>(n, false));
    int count = 0;
    DFS(0, 0, m, n, k, visited, count);
    return count;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    cout << movingCount(2, 3, 1) << endl; //期望输出3
    cout << movingCount(3, 1, 0) << endl; //期望输出1
}
```
 
# 剑指Offer29：顺时针打印矩阵

题目：输入一个矩阵，按照从外向里以顺时针的顺序依次打印出每一个数字。 
 
## 思路:按圈模拟
1. 一圈有四个顶点(T,L)(T,R)(B,R)(B,L)

2. 在一圈中的遍历顺序是:(T,L)->(T,R)->(B,R) *重点:判断L<R && T<B* (B,R)->(B,L)->(T,L)

3. 判断的作用是防止只剩一行或者一列的时候把这一行(列)重读添加
   
4. 一圈结束后,TandL++,BandR--

5. 判断所有圈都打印了:L<=R && T<=B


### show me code
```C++
vector<int> spiralOrder(vector<vector<int>> &matrix)
{
    vector<int> res;
    if (matrix.size() == 0)
    {
        return {};
    }
    int T(0), L(0), B(matrix.size() - 1), R(matrix[0].size() - 1); // 顶点坐标
    // 检测是否打印完了所有圈
    while (T <= B && L <= R)
    {
        // 左到右打印:(T,L)->(T,R)
        for (int i = L; i <= R; i++)
        {
            res.push_back(matrix[T][i]);
        }
        // 从上到下打印:(T,R)->(B,R)
        for (int i = T + 1; i <= B; i++)
        {
            res.push_back(matrix[i][R]);
        }
        // 判断是否是单行或者单列
        if (L < R && T < B)
        {
            // 从右到左打印:(B,R)->(B,L)
            for (int i = R - 1; i >= L; i--)
            {
                res.push_back(matrix[B][i]);
            }
            // 从下到上打印:(B,L)->(T,L)
            for (int i = B - 1; i > T; i--)
            {
                res.push_back(matrix[i][L]);
            }
        }
        // 一圈打印完了,顶点坐标移动
        T++;
        L++;
        B--;
        R--;
    }
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<vector<int>> mat{{1, 2, 3, 4},
                            {5, 6, 7, 8},
                            {9, 10, 11, 12}};

    vector<int> out = spiralOrder(mat);
    cout << "输出序列为: " << endl;
    for (int i = 0; i < out.size(); i++)
    {
        cout << out[i] << " ";
    }
    cout << endl;
}
```

# 剑指Offer38：字符串的排列

题目：输入一个字符串，打印出该字符串中字符的所有排列。例如输入字符串abc，
则打印出由字符a、b、c所能排列出来的所有字符串abc、acb、bac、bca、cab和cba。

## 思路:全排列问题+DFS+剪枝

第一个数字我们固定一个字符,有n个选择,第二位有n-1个选择...
直到只剩下一个选择的时候,把这一个排列push_back到vector中
当输入string存在重复的字符时,用set跳过这次选择 


### show me code
```C++
void dfs(int pos, string &res_item, vector<string> &res)
{
    // 递归终止条件:pos指针移动到了最后
    if (pos == res_item.size() - 1)
    {
        res.push_back(res_item);
        return;
    }
    unordered_set<char> uset; // 每一次递归进入dfs函数,就维护一个set
    for (int i = pos; i < res_item.size(); i++)
    {
        // 如果set中已经有这个字母了,就跳过这一次循环
        if (uset.count(res_item[i]))
        {
            continue;
        }
        uset.insert(res_item[i]);
        // 最巧妙的一步,# 剑指Offer书上也是这样处理的
        // 通过把i处的元素swap到pos的位置,来固定pos位置的元素(其实相对每次递归而言,pos就是string开头的位置)
        swap(res_item[i], res_item[pos]);
        dfs(pos + 1, res_item, res);
        // dfs返回之后,将string还原,不加这一步的话,对于输入abc的案例,输出会成为:
        // 正确输出:abc acb bac bca cba cab -> 错误输出:abc acb cab cba abc acb
        // 即上一次的修改结果会影响到下一次
        swap(res_item[i], res_item[pos]);
    }
}
vector<string> permutation(string s)
{
    if (s == "")
    {
        return {};
    }
    vector<string> res;
    string res_item = s; // 不修改传入参数
    dfs(0, res_item, res);
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    string s = "abc";
    vector<string> res = permutation(s);
    for (int i = 0; i < res.size(); i++)
    {
        cout << res[i] << " ";
    }
    cout << endl;
    // ["abc","acb","bac","bca","cab","cba"]
}
```