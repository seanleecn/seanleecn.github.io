---
title: 剑指Offer刷题日记-栈和队列
date: 2020-08-18 16:24:33
tags: 
    - 剑指Offer
    - 栈和队列
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``栈和队列``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

# 剑指Offer9：用两个栈实现一个队列
题目：用两个栈实现一个队列。队列的声明如下，请实现它的两个函数 appendTail 和 deleteHead ，分别完成在队列尾部插入整数和在队列头部删除整数的功能。

若队列中没有元素，deleteHead 操作返回 -1 

### show me code
```C++
class CQueue
{
public:
    CQueue() {}

    void appendTail(int value) { s1.push(value); }

    int deleteHead()
    {
        //如果s2是空的,将s1的元素pop之后push进s2,并弹出s2栈顶元素
        if (s2.empty())
        {
            if (s1.empty())
            {
                return -1; //此时队列为空
            }
            else
            {
                while (!s1.empty())
                {
                    int &elemnet = s1.top(); // top返回栈顶元素的引用
                    s1.pop();
                    s2.push(elemnet);
                }
            }
        }
        int &top = s2.top();
        s2.pop();
        return top;
    }

private:
    stack<int> s1; //对s1入栈操作,即入队列
    stack<int> s2; //对s2出栈操作,即出队列
};
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    CQueue *obj = new CQueue();
    obj->appendTail(1);
    obj->appendTail(2);
    obj->appendTail(3);
    int param_1 = obj->deleteHead();
    cout << param_1 << endl;
    obj->appendTail(4);
    int param_2 = obj->deleteHead();
    cout << param_2 << endl;
    delete obj;
}
```
 
# 剑指Offer30:包含min函数的栈
定义栈的数据结构，请在该类型中实现一个能够得到栈的最小元素的 min 函数在该栈中，
调用 min、push 及 pop 的时间复杂度都是 O(1)。
 
## 思路:
1. 只用单个变量记录最小元素,当最小元素出栈后无法再找到最小的元素
2. 需要用到辅助栈,辅助栈用来保存插入元素后这一时刻,原栈中的最小元素
3. 即原栈有元素入栈时,只有是最小元素时才能进入辅助栈,否则辅助栈中的栈顶元素重复入栈

### show me code
```C++
class MinStack
{
public:
    * initialize your data structure here. 
    stack<int> stk, stk_min;
    MinStack()
    {
    }

    void push(int x)
    {
        stk.push(x); // 原栈
        // 辅助栈入栈流程
        if (stk_min.empty())
        {
            stk_min.push(x);
        }
        else if (x >= stk_min.top())
        {
            stk_min.push(stk_min.top());
        }
        else
        {
            stk_min.push(x);
        }
    }

    void pop()
    {
        stk.pop();
        stk_min.pop();
    }

    int top()
    {
        return stk.top();
    }

    int min()
    {
        return stk_min.top();
    }
};
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    MinStack *obj = new MinStack();
    obj->push(3);
    obj->push(4);
    cout << obj->top() << endl;
    cout << obj->min() << endl;
}
```

# 剑指Offer31:栈的压入、弹出序列
输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。
假设压入栈的所有数字均不相等。

例如，序列 {1,2,3,4,5} 是某栈的压栈序列，
序列 {4,5,3,2,1} 是该压栈序列对应的一个弹出序列，
但 {4,3,5,1,2} 就不可能是该压栈序列的弹出序列。
 
> 题意理解:入栈顺序不意味着要一次全入完,可以部分入栈再出栈再入栈
## 思路:一个元素入栈后,检查栈顶的元素,如果满足弹出队列,出栈;否则继续插入 

### show me code
```C++
bool validateStackSequences(vector<int> &pushed, vector<int> &popped)
{
    stack<int> stk;
    int pop_idx = 0;
    for (int push_idx = 0; push_idx < pushed.size(); push_idx++)
    {
        stk.push(pushed[push_idx]);
        while (!stk.empty() && popped[pop_idx] == stk.top())
        { // 出错点2:stk需要非空才能pop
            stk.pop();
            if (pop_idx == popped.size() - 1)
            { // 出错点1:防止pop_idx数组越界
                break;
            }
            pop_idx++;
        }
    }
    return stk.empty();
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> push = {1, 0};
    vector<int> pop1 = {1, 0};
    vector<int> pop2 = {4, 3, 5, 1, 2};
    validateStackSequences(push, pop1) ? cout << "true" << endl : cout << "false" << endl;
    validateStackSequences(push, pop2) ? cout << "true" << endl : cout << "false" << endl;
}
```

# 剑指Offer59（一）：滑动窗口的最大值
题目：给定一个数组和滑动窗口的大小，请找出所有滑动窗口里的最大值。
 
## 思路1：双端队列元素下标
如何保证队列中的front是最大?生活中我们使用末位淘汰制度，这里也是一个道理

新来的元素从max_index先前检查，当新来元素比尾部的大，就把当前尾部的元素弹出，这样队列中头部的元素一定是最大的
由于是一个滑动窗口，因此当窗口离开最大元素的位置时，要弹出头部元素

### show me code
```C++
vector<int> maxSlidingWindow(vector<int> &nums, int k)
{
    if (nums.empty())
        return {};

    vector<int> res;
    deque<int> max_index; //保存的是最大值的下标

    // 队列初始化，队列初始话之后，front是最大的元素的下标
    for (int i = 0; i < k; ++i)
    {
        // 新来的比back大，说明back不可能是窗口中的最大值，pop_back
        while (!max_index.empty() && nums[i] >= nums[max_index.back()])
            max_index.pop_back();
        // 新来的进入队列
        max_index.push_back(i);
    }
    res.push_back(nums[max_index.front()]);

    // 初始化之后，窗口从k开始滑动
    for (int i = k; i < nums.size(); i++)
    {
        //
        // 新来的比back大，back肯定不是最大值，pop_back
        while (!max_index.empty() && nums[i] >= nums[max_index.back()])
            max_index.pop_back();
        // 当窗口离开front位置时，front已经没用了，因此要pop_front
        if (!max_index.empty() && max_index.front() <= (i - k))
            max_index.pop_front();
        // 新来的进入队列
        max_index.push_back(i);
        res.push_back(nums[max_index.front()]);
    }
    return res;
}
```

### 测试用例
```C++
int main()
{
    vector<int> in = {1, 3, -1, -3, 5, 3, 6, 7};
    // 由于输入参数为vector的左值引用，因此不能直接拿表达式{}传参数进去
    vector<int> out = maxSlidingWindow(in, 3);
    // 输出:[ 3, 3, 5, 5, 6, 7 ]
    for (int i : out)
        cout << i << ' ';
    cout << endl;
}
```

# 剑指Offer59（二）：队列的最大值
请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。
若队列为空，pop_front 和 max_value 需要返回 -1

## 思路：
这题和上一题很像，datal队列维护全部的数据，max双端队列用末位淘汰法维护最大的元素

### show me code
```C++
class MaxQueue
{
private:
    queue<int> data;
    deque<int> max;

public:
    MaxQueue()
    {
    }

    int max_value()
    {
        if (max.empty())
            return -1;
        return max.front();
    }

    void push_back(int value)
    {
        data.push(value);
        // 从max队列的尾端开始，进行末位淘汰制度
        while (!max.empty() && value >= max.back())
            max.pop_back();
        max.push_back(value);
    }

    int pop_front()
    {
        if (data.empty())
            return -1;
        int front = data.front();
        // 最大元素弹出的话，max队列也要更新
        if (front == max.front())
            max.pop_front();
        data.pop();
        return front;
    }
};
```

### 测试用例
```C++
int main()
{
    MaxQueue *obj = new MaxQueue();
    cout << obj->max_value() << endl; // -1
    obj->push_back(1);
    obj->push_back(2);
    cout << obj->pop_front() << endl; //2
    cout << obj->max_value() << endl; //1
}
```