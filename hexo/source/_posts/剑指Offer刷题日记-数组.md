---
title: 剑指Offer刷题日记-数组
date: 2020-08-16 16:24:33
tags: 
    - 剑指Offer
    - 数组
categories: 数据结构与算法 
---

【剑指Offer刷题日记】的第一篇文章，从数组相关的面试题3开始！冲冲冲！

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``数组``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

 
# 剑指Offer3：数组中重复的数字
题目：在一个长度为n的数组里的所有数字都在0到n-1的范围内。

数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。

请找出数组中任意一个重复的数字。

## 思路1：双重遍历（超时） 

### show me code
```C++
int findRepeatNumber(vector<int>& nums) {
    int i, j;
    int k = 0;
    for (i = 0; i < nums.size(); i++) {
        for (j = i + 1; j < nums.size(); j++) {
            if (nums[i] == nums[j]) {
                return nums[i];
            }
        }
    }
    return -1;
}
```

## 思路2：排序之后单循环 

### show me code
```C++
int findRepeatNumber(vector<int>& nums){
    sort(nums.begin(), nums.end());
    for (int i = 0; i < nums.size();i++){
        if (nums[i]==nums[i+1]){
            return nums[i];
        }
    }
    return -1;
}
```

## 思路3：哈希表 

### show me code
```C++
int findRepeatNumber(vector<int>& nums){
    unordered_set<int> st;
    //set的insert函数返回pair类型,second为bool变量表示是否插入成功
    for (int i = 0; i < nums.size();i++){
        if (st.insert(nums[i]).second == false){ // set唯一性导致插入失败
            return nums[i];
        }
    }
    return -1;
} 
```

## 思路4：原地操作，空间复杂度O(1) 
### show me code
```C++
int findRepeatNumber(vector<int> &nums)
{
    if (!nums.size())
        return -1;
    for (int i = 0; i < nums.size(); i++)
    {
        if (nums[i] < 0 || nums[i] > nums.size() - 1)
            return -1;
        while (nums[i] != i)
        {
            //发现相同的值
            if (nums[i] == nums[nums[i]])
                return nums[i];
            //下标和值不是对应的话移动到交换元素
            swap(nums[i], nums[nums[i]]);
        }
    }
    return -1;
}
```

### 测试用例
```C++
int main()
{
    vector<int> vec = {2, 3, 1, 0, 2, 5, 3};
    int ans = findRepeatNumber(vec);
    cout << ans << endl; // 2或者3
}
```

# 剑指Offer4：二维数组中的查找

题目：在一个二维数组中，每一行都按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。

请完成一个函数，输入这样的一个二维数组和一个整数，判断数组中是否含有该整数。 

## 思路：将矩阵旋转45°
由于矩阵从左到右递增，从上到下递增，旋转之后从顶点向下看就是一颗二叉搜索树

### show me code
```C++
bool findNumberIn2DArray(vector<vector<int>> &matrix, int target)
{
    int i = matrix.size() - 1, j = 0;
    while (i >= 0 && j < matrix[0].size())
    {
        if (matrix[i][j] > target)
            i--;
        else if (matrix[i][j] < target)
            j++;
        else
            return true;
    }
    return false;
}
```

### 测试用例
```C++
int main()
{
    vector<vector<int>> vec_mat = {{1, 4, 7, 11, 15},
                                   {2, 5, 8, 12, 19},
                                   {3, 6, 9, 16, 22},
                                   {10, 13, 14, 17, 24},
                                   {18, 21, 23, 26, 30}};
    findNumberIn2DArray(vec_mat, 5) ? cout << "true" << endl : cout << "false" << endl;  // true
    findNumberIn2DArray(vec_mat, 20) ? cout << "true" << endl : cout << "false" << endl; //false
}
```

# 剑指Offer5：替换空格
题目：请实现一个函数，把字符串中的每个空格替换成"%20"。
例如输入“We are happy.”， 则输出“We%20are%20happy.”。 

## 思路：直接遍历，然后增删改查 

### show me code
```C++
string replaceSpace(string s)
{
    string ss = "%20";
    for (int i = 0; i < s.length(); i++)
    {
        if (s[i] == ' ')
        {
            s.erase(i, 1);
            s.insert(i, ss);
        }
    }
    return s;
}
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    string s = "we are happy";
    cout << s << endl;
    cout << replaceSpace(s) << endl;
}
```

# 剑指Offer11：旋转数组的最小数字
题目：把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。
输入一个递增排序的数组的一个旋转，输出旋转数组的最小元素。例如数组
{3, 4, 5, 1, 2}为{1, 2, 3, 4, 5}的一个旋转，该数组的最小值为1。

## 思路1:遍历数组,找到最小的元素,这没有使用到旋转数组的性质

### show me code
```C++
int minArray(vector<int>& numbers) {
    vector<int>::const_iterator iter;
    int min = numbers[0];
    for (iter = numbers.begin(); iter != numbers.end(); iter++) {
        if (min > *iter) {
            min = *iter;
        }
    }
    return min;
} 
```

## 思路2:原数组可以分成两个排序好的数组

1. 这两个数组的分界点右边的元素就是我们要找的元素，如 44|122
2. 双指针法从两端搜索,每次找数组的中间元素,直到两个指针重合
3. 注意边界情况：begin mid end指向的元素都相同 

### show me code
```C++
int minArray(vector<int> &numbers)
{
    // 定义三个指针
    int begin = 0;
    int end = numbers.size() - 1;
    //二分法套路
    while (begin < end)
    {
        int mid = begin + (end - begin) / 2; //防止数组溢出
        if (numbers[mid] < numbers[end])
        {
            end = mid;
            // mid比右边小,可能是最小值,因此缩小后的区间需要包含mid
        }
        else if (numbers[mid] > numbers[end])
        {
            begin = mid + 1;
            // 这里很关键!!!
            // 因为mid比end大,所以mid肯定不是最小的元素,缩小区间的时候可以跳过mid元素到mid+1
        }
        else
        {
            end--; //此时mid和end相同,左移end指针
        }
    }
    return numbers[begin];
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> v1 = {3, 4, 5, 1, 2};
    vector<int> v2 = {2, 2, 2, 0, 1};

    cout << minArray(v1) << endl; //期望输出1
    cout << minArray(v2) << endl; //期望输出0
}
```

# 剑指Offer17. 打印从1到最大的n位数
题目：输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数 999。

## 思路：字符串模拟数字加法 

当n很大的时候，打印出来的数字会超过INT_MAX

先用string形式保存数,然后转为数组push_back进vector
- int转chat char c = int  i + '0'
- char转int int  i = char c - '0'

### show me code
```C++
vector<int> res;
void string2number(string s)
{
    bool isBeginZero = true;
    string temp = "";
    for (int i = 0; i < s.length(); i++)
    {
        if (isBeginZero && s[i] != '0')
        { //找到了第一个不是起始0的数字
            isBeginZero = false;
        }
        if (!isBeginZero)
        {
            temp = temp + s[i]; //字符串重载了+号运算符,返回的是字符串连接后
        }
    }
    if (temp != "")
    {                              //输入是"",stoi函数会报错
        res.push_back(stoi(temp)); //C++11的string转int函数
    }
}

void recursive(string &s, int len, int pos)
{
    if (len == pos)
    {
        string2number(s);
        return;
    }
    for (int i = 0; i <= 9; i++)
    {
        s[pos] = i + '0';
        recursive(s, s.length(), pos + 1);
    }
}

vector<int> printNumbers(int n)
{
    string s(n, '0'); //构造一个长为n,每个元素都是'0'的字符串
    recursive(s, s.length(), 0);
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> out = printNumbers(2);
    for (int i = 0; i < out.size(); i++)
    {
        cout << out[i] << " ";
    }
    cout << endl;
}
```

# 剑指Offer21：调整数组顺序使奇数位于偶数前面
题目：输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。

## 思路1:最简单的方法,建立一个偶数数组,浪费空间

### show me code
```C++
vector<int> exchange(vector<int> &nums) {
    vector<int> res;
    vector<int> res_even;  //偶数
    int i, j;
    for (i = 0; i < nums.size(); i++) {
        if (nums[i] % 2 == 1) {
            res.push_back(nums[i]);
        } else {
            res_even.push_back(nums[i]);
        }
    }
    for (j = 0; j < res_even.size(); j++) {
        res.push_back(res_even[j]);
    }
    return res;
} 
```

## 思路2:双指针,当左指针为偶,右指针为奇,交换两个数字

### show me code
```C++
vector<int> exchange(vector<int> &nums)
{
    int left = 0;
    int right = nums.size() - 1;
    while (left < right)
    {
        if (nums[left] % 2 == 1)
        {
            left++;
            continue;
        }
        if (nums[right] % 2 == 0)
        {
            right--;
            continue;
        }
        swap(nums[left], nums[right]);
        left++;
        right--;
    }
    return nums;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> nums = {1, 2, 3, 4};
    vector<int> res = exchange(nums);
    for (int i :res)
    {
        cout << i << " ";
    }
    cout << endl;// 1 3 2 4
}
```

# 剑指Offer39：数组中出现次数超过一半的数字
题目：数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。
例如输入一个长度为9的数组{1, 2, 3, 2, 2, 2, 5, 4, 2}。
由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。 

## 思路1: 对数组排序,最多的数组一定是中位数
时间复杂度: O(nlogn) 
### show me code
```C++
 int majorityElement(vector<int>& nums) {
    sort(nums.begin(), nums.end());
    return nums[nums.size() / 2];
} 
```


## 思路2: 哈希表统计次数
### show me code
```C++
 int majorityElement(vector<int>& nums) {
    int res;
    if (!nums.size()) {
        res = 0;
    }
    unordered_map<int, int> umap;
    for (int i = 0; i < nums.size(); i++) {
        umap[nums[i]]++;
        if (umap[nums[i]] > nums.size() / 2) {
            res = nums[i];
            break;
        }
    }
    return res;
} 
```

## 思路3: 摩尔投票法
时间效率最好
诸侯纷争,轮流当王,最多的元素一定是最后的王
遍历一遍数组,同元素+1,不同元素-1 

### show me code
```C++
int majorityElement(vector<int> &nums)
{
    int res = 0;
    int cnt = 0;
    for (int i = 0; i < nums.size(); i++)
    {
        if (cnt == 0)
        {
            res = nums[i];
            cnt = 1;
        }
        else
        {
            if (res == nums[i])
            {
                cnt += 1;
            }
            else
            {
                cnt -= 1;
            }
        }
    }
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> in = {1, 2, 3, 2, 2, 2, 5, 4, 2};
    cout << majorityElement(in) << endl; // 输出2
}
```

# 剑指Offer40：最小的k个数
题目：输入n个整数，找出其中最小的k个数。例如输入4、5、1、6、2、7、3、8
这8个数字，则最小的4个数字是1、2、3、4。 

## 思路1: 排序

### show me code
```C++
vector<int> getLeastNumbers(vector<int>& arr, int k) {
    sort(arr.begin(), arr.end());
    vector<int> res(arr.begin(), arr.begin() + k);
    return res;
} 
```

## 思路2: 最大堆解法
这种方法适合处理大量数据 

### show me code
```C++
vector<int> getLeastNumbers(vector<int>& arr, int k) {
    if (!arr.size() || !k) {
        return {};
    }
    vector<int> res;
    priority_queue<int> que_less;  // 优先队列默认把插入的元素从大到小(less)排序
    // 前k个元素填充队列
    for (int i = 0; i < k; i++) {
        que_less.push(arr[i]);
    }
    // k之后的元素,如果比队列中的最大元素小,就弹出队列中最大元素
    for (int j = k; j < arr.size(); j++) {
        // 优先队列默认是用less函数排序,即第一个元素最大
        if (arr[j] < que_less.top()) {
            que_less.pop();
            que_less.push(arr[j]);
        }
    }
    while (!que_less.empty()) {
        res.push_back(que_less.top());
        que_less.pop();
    }
    return res;
}
```

## 思路3: 快速选择
1. 类似快排,依靠partition函数,将数组分为"比pivot小"和"比pivot大"两部分,并返回pivot在数组的下标pos)
2.与快排不同的是,不需要递归处理两侧的部分,而是选择一部分递归
3. 当pos = index 返回左半边的数组
4. 当pos > index 递归左半部分,直到pos = k
5. 当pos < index 还需要在右边再找k-pos个元素 

### show me code
```C++
vector<int> res; // 递归调用的话,全局变量可以减少参数
// 参考的是大话数据结构p418里面的写法
int partition(vector<int> &arr, int left, int right)
{
    int pivot = arr[left]; // 用左指针的元素做pivot(也可以用rand生成随机数)
    while (left < right)
    {
        // 下面等号不能少,否则超时
        while (left < right && arr[right] > pivot)
        {
            right -= 1;
        }
        swap(arr[right], arr[left]);
        while (left < right && arr[left] < pivot)
        {
            left += 1;
        }
        swap(arr[left], arr[right]);
    }
    return left;
}

// 下面传进来的各个参数,都是数组中的下标,即从0开始
vector<int> quickSelect(vector<int> &arr, int left, int right, int index)
{
    int pos = partition(arr, left, right);
    if (pos == index)
    {
        // 这里要拷贝到pos处的元素,因为pos(即index)指的是第k小元素的下标
        for (int i = 0; i <= pos; i++)
        {
            res.push_back(arr[i]);
        }
        return res;
    }
    return pos > index ? quickSelect(arr, left, pos - 1, index)
                       : quickSelect(arr, pos + 1, right, index);
}

vector<int> getLeastNumbers(vector<int> &arr, int k)
{
    if (!arr.size() || !k)
    {
        return {};
    }
    // !!!传k-1进去,因为第k个变量的下标是k-1
    return quickSelect(arr, 0, arr.size() - 1, k - 1);
}
```

### 测试用例
```C++
int main(int agrc, char **argv)
{
    vector<int> in1 = {3, 2, 1};
    vector<int> out1 = getLeastNumbers(in1, 2);
    for (auto i : out1)
    {
        cout << i << " ";
    }
    cout << endl;
    // 1 2
}
```

# 剑指Offer41：数据流中的中位数
题目：如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么
中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，
那么中位数就是所有数值排序之后中间两个数的平均值。 

## 思路:建立两个优先队列,一个为最大堆,一个为最小堆
向这两个优先队列插入元素,保证:
1.最大堆的所有元素 比 最小堆中的所有元素 小
2.两个优先队列的数目差距最大为1 

### show me code
```C++
priority_queue<int, vector<int>, less<int>> maxHeap;
priority_queue<int, vector<int>, greater<int>> minHeap;

void addNum(int num)
{
    // 两个堆元素相同,先插入最大堆,然后把最大堆的栈顶元素移到最小堆中
    // 这样就保证了最大堆的所有元素都比最小堆的元素小(因为最大堆中最大的被移到最小堆了)
    if (maxHeap.size() == minHeap.size())
    {
        maxHeap.push(num);
        minHeap.push(maxHeap.top());
        maxHeap.pop();
    }
    // 两个堆个数不同(即相差1),方法同上
    else
    {
        minHeap.push(num);
        maxHeap.push(minHeap.top());
        minHeap.pop();
    }
}

double findMedian()
{
    // 分奇偶讨论
    if (maxHeap.size() == minHeap.size())
    {
        return (maxHeap.top() + minHeap.top()) / 2.0; // 必须要用/2.0
    }
    else
    {
        return minHeap.top();
    }
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    addNum(1);
    addNum(2);
    cout << findMedian() << endl;
    addNum(3);
    cout << findMedian() << endl;
}
```

# 剑指Offer44：数字序列中某一位的数字
题目：数字以0123456789101112131415…的格式序列化到一个字符序列中。
在这个序列中，第5位（从0开始计数）是5，第13位是1，第19位是4，等等。
请写一个函数求任意位对应的数字。 

### show me code
```C++
int findNthDigit(int n)
{
    if (n < 0)
        return -1;
    if (n == 0)
        return 0;

    int digit = 1;       //几位数
    long digitBegin = 1; //该位数的开始
    long digitLen = 9;   //该位所有数字共有多长
    while (true)
    {
        if (n <= digitLen) //说明在这一位中
            break;
        // 不在这一位，进入递推公式
        n -= digitLen;
        ++digit;
        digitBegin *= 10;
        digitLen = digit * digitBegin * 9;
    }
    // 找到具体是哪个数字,很重要的注意索引是从0开始，所以要(n-1)
    int digitNums = (n - 1) / digit;        // 有几个 某位数
    int digitRes = (n - 1) % digit;         // 余数
    long finalNum = digitBegin + digitNums; // 找到这个数字了
    // 之后就是根据余数确定哪一位的问题,这里用转为string的方式
    string s_finalNum = to_string(finalNum);
    return int(s_finalNum[digitRes] - '0');
}
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    cout << "n=3: " << findNthDigit(3) << endl;       // out:3
    cout << "n=11: " << findNthDigit(11) << endl;     // out:0
    cout << "n=1001: " << findNthDigit(1001) << endl; // out:7
}
```

# 剑指Offer45：把数组排成最小的数
题目：输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼
接出的所有数字中最小的一个。例如输入数组{3, 32, 321}，则打印出这3个数
字能排成的最小数字321323。

## 思路：
1. 按照最高位从小到大排序
2. 最高位相同的话，比较ab和ba哪个大

### show me code
```C++
string minNumber(vector<int> &nums)
{
    if (nums.empty())
        return to_string(0);
    vector<string> strs;
    string res;
    for (int i : nums)
    {
        strs.push_back(to_string(i));
    }
    // sort结合lambda表达式自定义排序
    sort(strs.begin(), strs.end(), [](const string &s1, const string &s2) {
        // 1.根据最高位判断位置
        if (s1[0] != s2[0])
            return s1[0] < s2[0];
        // 2.最高位相同的话判断ab还是ba
        return s1 + s2 < s2 + s1;
    });
    for (string i : strs)
    {
        res += i;
    }
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> vec1 = {10, 2};
    cout << minNumber(vec1) << endl; //102
    vector<int> vec2 = {3, 30, 34, 5, 9};
    cout << minNumber(vec2) << endl; //3033459
}
```

# 剑指Offer53（一）：数字在排序数组中出现的次数
题目：统计一个数字在排序数组中出现的次数。

## 思路1:遍历数组

### show me code
```C++
int search(vector<int> &nums, int target) {
    int cnt = 0;
    for (int i : nums) {
        if (i == target)
            ++cnt;
    }
    return cnt;
}
```

## 思路2:哈希表

### show me code
```C++
int search(vector<int> &nums, int target) {
    unordered_map<int, int> map;
    for (int i : nums) {
        ++map[i];
    }
    int res = map.find(target)->second;
    return res;
}
```

## 思路3
由于是一个排序的数列，可以采用二分法快速搜索
二分的目的是找到target区间的左右端点，最后返回r-l+1就是个数

### show me code
```C++
int search(vector<int> &nums, int target)
{
    int L, R;
    // 找right
    int left = 0, right = nums.size() - 1;
    while (left <= right)
    {
        int mid = (left + right) / 2;
        // <= 是为了确定右边界
        // 当mid等于target时，因为不确定后面还有没有target，所以同样需要左边收缩范围
        if (nums[mid] <= target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    R = right;
    // 找left
    left = 0, right = nums.size() - 1;
    while (left <= right)
    {
        int mid = (left + right) / 2;
        // < 是为了确定左边界
        // 因为就算当mid等于target的时候，因为不确定左边还有没有target，所以同样需要收缩右边界
        if (nums[mid] < target)
            left = mid + 1;
        else
            right = mid - 1;
    }
    L = left;
    return R - L + 1;
}
```

### 测试用例
```C++
int main()
{
    vector<int> vec1 = {5, 7, 7, 8, 8, 10};
    cout << search(vec1, 8) << endl; // 输出2
}
```

# 剑指Offer53（二）：0到n-1中缺失的数字
题目：一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0到n-1之内。
在范围0到n-1的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

## 思路：二分法
长度为n-1且递增的数组，应该满足nums[i] = i
需要用二分法找到不满足这个条件的数字

### show me code
```C++
int missingNumber(vector<int> &nums)
{
    int L = 0, R = nums.size() - 1;
    while (L <= R)
    {
        int m = (L + R) / 2;
        if (nums[m] == m)
            L = m + 1;
        else
            R = m - 1;
    }
    // 离开while循环之后，L指向缺少数字的位置
    return L;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    vector<int> vec = {0, 1, 2, 3, 4, 5, 6, 7, 9};
    cout << missingNumber(vec) << endl; //输出8
}
```

# 剑指Offer61：扑克牌的顺子
题目：从扑克牌中随机抽5张牌，判断是不是一个顺子，即这5张牌是不是连续的。
2～10为数字本身，A为1，J为11，Q为12，K为13，而大、小王为 0 ，可以看成任意数字。A 不能视为 14。

## 思路：排序+遍历检查
用0的个数来补充相邻的距离

### show me code
```C++
bool isStraight(vector<int> &nums)
{
    int jocker_num = 0;
    sort(nums.begin(), nums.end());
    for (int i = 0; i < 5; ++i)
    {
        if (nums[i] == 0)
        {
            ++jocker_num;
            continue;
        }
        // 防止越界
        if (i + 1 < 5)
        {
            // 出现相同的非零元素
            if (nums[i] == nums[i + 1])
                return false;
            int dis = nums[i + 1] - nums[i];
            while (dis > 1)
            {
                if (jocker_num == 0)
                    return false;
                --jocker_num;
                --dis;
            }
        }
    }
    return true;
}
```

### 测试用例
```C++
int main()
{
    vector<int> test1 = {0, 0, 1, 2, 5};
    cout << isStraight(test1) << endl; //1
}
```

# 剑指Offer62：圆圈中最后剩下的数字
题目：0, 1, …, n-1这n个数字排成一个圆圈，从数字0开始每次从这个圆圈里删除第m个数字。
求出这个圆圈里剩下的最后一个数字。

## 思路1：模拟环形链表，该方法超时 
### show me code
```C++
 int lastRemaining(int n, int m)
{
    // 链表初始化
    list<int> nums;
    for (int num = 0; num < n; ++num)
        nums.push_back(num);
    list<int>::iterator cur = nums.begin();
    while (nums.size() > 1)
    {
        // 找到要删除的节点
        for (int i = 1; i < m; ++i)
        {
            cur++;
            // 模拟环形
            if (cur == nums.end())
                cur = nums.begin();
        }
        list<int>::iterator next = ++cur;
        if (next == nums.end())
            next = nums.begin();
        --cur;
        nums.erase(cur);
        //第二次循环的开始
        cur = next;
    }
    return *(cur);
} 
```

## 思路2：数学问题约瑟夫环
https://leetcode-cn.com/problems/yuan-quan-zhong-zui-hou-sheng-xia-de-shu-zi-lcof/solution/huan-ge-jiao-du-ju-li-jie-jue-yue-se-fu-huan-by-as/

### show me code
```C++
int lastRemaining(int n, int m)
{
    int pos = 0; // 最终活下来那个人的初始位置
    for (int i = 2; i <= n; i++)
    {
        pos = (pos + m) % i; // 每次循环右移
    }
    return pos;
}
```

### 测试用例
```C++
int main()
{
    cout << lastRemaining(5, 3) << endl;   // 3
    cout << lastRemaining(10, 17) << endl; // 2
}
```
 
# 剑指Offer66：构建乘积数组
给定一个数组 A[0,1,…,n-1]，请构建一个数组 B[0,1,…,n-1]，其中 B[i] 的值是数组 A 中除了下标 i 以外的元素的积, 
即 B[i]=A[0]×A[1]×…×A[i-1]×A[i+1]×…×A[n-1]。

不能使用除法。

## 思路1：O(N^2)遍历
超时不能通过

### show me code
```C++ 
vector<int> constructArr(vector<int> &a)
{
    vector<int> res(a.size(), 1);
    for (int i = 0; i < a.size(); ++i)
    {
        for (int j = 0; j < a.size(); ++j)
        {
            if (i != j)
                res[i] *= a[j];
        }
    }
    return res;
}
```

## 思路2：两次遍历
```
b0 = 1  * a1 * a2 * a3
b1 = a0 * 1  * a2 * a3
b2 = a0 * a1 * 1  * a3
b3 = a0 * a1 * a2 * 1 
```
沿着对角线1把计算B的过程分为两部分：左下 和 右上


### show me code
```C++
vector<int> constructArr(vector<int> &a)
{
    int size = a.size();
    vector<int> b(size, 1);
    // 一次遍历得到左下
    for (int i = 1; i < size; ++i)
    {
        b[i] = b[i - 1] * a[i - 1];
    }
    // 二次遍历得到右上
    int RU = 1; // 右上角乘积
    for (int i = size - 2; i >= 0; --i)
    {
        RU = RU * a[i + 1];
        b[i] = b[i] * RU;
    }
    return b;
}
```

### 测试用例
```c++
int main()
{
    vector<int> in = {1, 2, 3, 4, 5};
    for (auto i : constructArr(in))
    {
        cout << i << ' ';
    }
    cout << endl;
    // [120,60,40,30,24]
}
```