---
title: 剑指Offer刷题日记-二叉树
date: 2020-08-19 16:47:29
tags:    
    - # 剑指Offer
    - 二叉树
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``二叉树``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

如果要在本地调试，需要先包含下面的头文件。

```c++

#ifndef MY_TREE_NODE
#define MY_TREE_NODE

#include <iostream>

using namespace std;

struct TreeNode
{
    int val;
    TreeNode *left;
    TreeNode *right;
    TreeNode(int x) : val(x), left(NULL), right(NULL) {}
};

void printTree(TreeNode *pNode)
{
    if (pNode != NULL)
    {
        cout << "node: " << pNode->val << endl;
        if (pNode->left != NULL)
            cout << "left: " << pNode->left->val << " ";
        else
            cout << "left: NULL ";
        if (pNode->right != NULL)
            cout << "right: " << pNode->right->val << endl;
        else
            cout << "right: NULL" << endl;
    }
    if (pNode->left != NULL)
    {
        printTree(pNode->left);
    }
    if (pNode->right != NULL)
    {
        printTree(pNode->right);
    }
}

#endif
```

<!-- more -->

# 剑指Offer7：重建二叉树
题目：输入某二叉树的前序遍历和中序遍历的结果，请重建该二叉树。

假设输入的前序遍历和中序遍历的结果中都不含重复的数字。
```
例如，给出
前序遍历 preorder = [3,9,20,15,7]
中序遍历 inorder = [9,3,15,20,7]
返回如下的二叉树：
    3
   / \
  9  20
    /  \
   15   7 
```


### show me code

```c++
TreeNode *buildTree(vector<int> &preorder, vector<int> &inorder)
{
    if (preorder.size() == 0 || inorder.size() == 0)
        return NULL;
    vector<int> preorder_left, preorder_right;
    vector<int> inorder_left, inorder_right;
    TreeNode *root = new TreeNode(preorder[0]);

    //根据前序遍历的第一个节点(根节点)将中序遍历分为两棵子树
    int pos; //根节点在中序遍历中的位置
    for (pos = 0; pos < inorder.size(); pos++)
    {
        if (inorder[pos] == preorder[0])
            break;
        inorder_left.push_back(inorder[pos]);
    }
    for (pos = pos + 1; pos < inorder.size(); pos++)
        inorder_right.push_back(inorder[pos]);

    //把前序遍历分成左右两棵子树
    for (int i = 1; i < preorder.size(); i++)
    {
        if (i <= inorder_left.size())
            preorder_left.push_back(preorder[i]);
        else
            preorder_right.push_back(preorder[i]);
    }
    root->left = buildTree(preorder_left, inorder_left);
    root->right = buildTree(preorder_right, inorder_right);
    return root;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    vector<int> pre_vec = {3, 9, 20, 15, 7};
    vector<int> in_vec = {9, 3, 15, 20, 7};
    TreeNode *tree = buildTree(pre_vec, in_vec);
    printTree(tree);
}
```
#  剑指Offer8：二叉树的下一个结点

题目：给定一棵二叉树和其中的一个结点，如何找出中序遍历顺序的下一个节点？

树中的结点除了有两个分别指向左右子结点的指针以外，还有一个指向父结点的指针 


```c++
struct BinaryTreeNode
{
    int value;
    BinaryTreeNode *left;
    BinaryTreeNode *right;
    BinaryTreeNode *parent;
    BinaryTreeNode(int x) : value(x), left(NULL), right(NULL), parent(NULL) {}
};

void connectTree(BinaryTreeNode *parent, BinaryTreeNode *left, BinaryTreeNode *right)
{
    parent->left = left;
    parent->right = right;
    left->parent = parent;
    right->parent = parent;
}
```

## 思路：中序遍历

左递归->打印->右递归存在3种情况:

1. 打印该节点之后,存在右子节点,进入右递归,接下来打印的是右递归子树的最左节点
2. 打印该节点之后,不存在右子节点,且该节点是父节点的左节点,接下来打印这个节点的父节点(用入栈出栈理解递归)
3. 打印该节点之后,不存在右子节点,且该节点是父节点的右节点,需要回溯父节点,直到找到一个左子节点,返回这个左子节点的父节点 

### show me code

```c++
BinaryTreeNode *getNext(BinaryTreeNode *node)
{
    if (node == NULL)
        return NULL;
    BinaryTreeNode *res = NULL; // 返回值
    // 情况1
    if (node->right != NULL)
    {
        BinaryTreeNode *rightTree = node->right; //该节点的右子树
        // 找最左
        while (rightTree->left != NULL)
        {
            rightTree = rightTree->left;
        }
        res = rightTree;
    }
    else
    {
        BinaryTreeNode *current = node;        //当前节点
        BinaryTreeNode *parent = node->parent; //该节点的父节点
        while (parent != NULL)
        {
            // 情况2
            if (parent->left == current)
            {
                res = parent;
                break;
            }
            // 情况3
            current = parent;
            parent = parent->parent;
            if (parent != NULL)
            {
                break;
            }
            if (current == parent->left && parent != NULL)
            {
                res = current->parent;
                break;
            }
        }
    }
    return res;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    //     3
    //    / \
    //   9  20
    //     /  \
    //    15   7
    // 中序遍历[9,3,15,20,7 ]
    BinaryTreeNode *node3 = new BinaryTreeNode(3);
    BinaryTreeNode *node9 = new BinaryTreeNode(9);
    BinaryTreeNode *node20 = new BinaryTreeNode(20);
    BinaryTreeNode *node15 = new BinaryTreeNode(15);
    BinaryTreeNode *node7 = new BinaryTreeNode(7);
    connectTree(node3, node9, node20);
    connectTree(node20, node15, node7);
    BinaryTreeNode *next = NULL;
    next = getNext(node9);
    cout << next->value << endl;
    next = getNext(node3);
    cout << next->value << endl;
    next = getNext(node15);
    cout << next->value << endl;
    next = getNext(node20);
    cout << next->value << endl;
    next = getNext(node7);
    if (next == NULL)
    {
        cout << "NULL" << endl;
    }
    else
    {
        cout << next->value << endl;
    }
}
```

#  剑指Offer26：树的子结构
题目：输入两棵二叉树A和B，判断B是不是A的子结构。(约定空树不是任意一个树的子结构)
B是A的子结构， 即 A中有出现和B相同的结构和节点值。

## 思路:
1. 对Ａ遍历，找到A中和Ｂ的根节点value相同的节点RA
2. 判断以RA为根节点的子树是否和B相同

### show me code

```c++
bool isBinA(TreeNode *A, TreeNode *B);

// 在isSubStructure函数中对A树*先序遍历*，同时对每个A树的结点和Ｂ树的*根节点*进行isBinA查找
bool isSubStructure(TreeNode *A, TreeNode *B)
{
    bool res = false;
    if (A == NULL || B == NULL)
    {
        return false;
    }
    if (A->val == B->val)
    {
        res = isBinA(A, B); //如果A,B的根节点就相同,进入isBinA函数检查是否有相同的结构
    }
    // 如果根节点不满足相同结构,就递归左右子树
    if (!res)
    {
        res = isSubStructure(A->left, B);
    }
    if (!res)
    {
        res = isSubStructure(A->right, B);
    }
    return res;
}
// 找B是否在A中存在，且使用递归的形式，递归的重点是当B为空
bool isBinA(TreeNode *A, TreeNode *B)
{
    if (B == NULL)
    {
        return true; //B为空,说明此时B递归到了叶子节点
    }
    if (A == NULL)
    {
        return false;
    }
    if (A->val != B->val)
    {
        return false;
    }
    // 当上面的三个if都不满足的时候，说明此时A.val == B.val，可以递归
    return isBinA(A->left, B->left) && isBinA(A->right, B->right);
}
```

### 测试用例

```c++
int main(int argc, char **agrv)
{
    // A树
    TreeNode *TNode3 = new TreeNode(3);
    TreeNode *TNode4 = new TreeNode(4);
    TreeNode *TNode5 = new TreeNode(5);
    TreeNode *TNode1 = new TreeNode(1);
    TreeNode *TNode2 = new TreeNode(2);
    TNode3->left = TNode4;
    TNode3->right = TNode5;
    TNode4->left = TNode1;
    TNode4->right = TNode2;
    // B树
    TreeNode *TNodeB4 = new TreeNode(4);
    TreeNode *TNodeB1 = new TreeNode(1);
    TNodeB4->left = TNodeB1;

    if (isSubStructure(TNode3, TNodeB4))
    {
        cout << "True" << endl;
    }
}
```

#  剑指Offer27：二叉树的镜像
题目：请完成一个函数，输入一个二叉树，该函数输出它的镜像。

## 思路:前序遍历,当遍历到的节点*存在子节点*时,交换子节点

### show me code

```c++

TreeNode *mirrorTree(TreeNode *root)
{
    if (root != NULL)
    {
        swap(root->left, root->right); //这里用的是STL里面的swap函数,自己写的当左右节点有一个是空的时候不会处理
    }
    else
    {
        return NULL;
    }
    mirrorTree(root->left);
    mirrorTree(root->right);
    return root;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *node4 = new TreeNode(4);
    TreeNode *node2 = new TreeNode(2);
    TreeNode *node7 = new TreeNode(7);
    TreeNode *node1 = new TreeNode(1);
    TreeNode *node3 = new TreeNode(3);
    TreeNode *node6 = new TreeNode(6);
    TreeNode *node9 = new TreeNode(9);
    node4->left = node2;
    node4->right = node7;
    node2->left = node1;
    node2->right = node3;
    node7->left = node6;
    node7->right = node9;
    cout << "镜像前：" << endl;
    printTree(node4);
    cout << "镜像后:" << endl;
    TreeNode *mirror = mirrorTree(node4);
    printTree(mirror);
}
```

#  剑指Offer28：对称的二叉树

题目：请实现一个函数，用来判断一棵二叉树是不是对称的。如果一棵二叉树和它的镜像一样，那么它是对称的。

## 思路:自顶向下递归,比较左右一对节点是否相同
递归终止条件:
1. 左右同时没有子节点,对称
2. 有左无右或者有右无左,不对称
3. 左右值不相同,不对称 


### show me code

```c++
bool judge(TreeNode *left, TreeNode *right)
{
    if (!left && !right)
        return true;
    if (!left || !right)
        return false;
    if (left->val != right->val)
        return false;
    // 对称条件: 左子树的左=右子树的右 且 左子树的右=右子树的左
    return judge(left->left, right->right) && judge(left->right, right->left);
}

bool isSymmetric(TreeNode *root)
{
    if (!root)
    {
        return true; // 空树也是对称的
    }
    return judge(root->left, root->right);
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *root = new TreeNode(1);
    TreeNode *l2 = new TreeNode(2);
    TreeNode *r2 = new TreeNode(2);
    TreeNode *l3 = new TreeNode(3);
    TreeNode *r3 = new TreeNode(3);
    root->left = l2;
    root->right = r2;
    l2->right = l3;
    r2->right = r3;
    isSymmetric(root) ? cout << "对称的" << endl : cout << "不对称" << endl;
}
```

#  剑指Offer32（一）：不分行从上往下打印二叉树

题目：从上往下打印出二叉树的每个结点，同一层的结点按照从左到右的顺序打印

## 思路:层序遍历
二叉树的前/中/后遍历可以递归实现,即使用栈结构

层序遍历无法递归,用队列实现


### show me code

```c++
vector<int> levelOrder(TreeNode *root)
{
    if (root == NULL)
    {
        return {};
    }
    deque<TreeNode *> deq; // 队列中存的是*节点*
    vector<int> res;
    deq.push_back(root);
    while (!deq.empty())
    {
        TreeNode *node = deq.front();
        res.push_back(node->val); // 插入队列头元素
        // 子节点入队列
        if (node->left)
        {
            deq.push_back(node->left);
        }
        if (node->right)
        {
            deq.push_back(node->right);
        }
        deq.pop_front();
    }
    return res;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *node3 = new TreeNode(3);
    TreeNode *node9 = new TreeNode(9);
    TreeNode *node20 = new TreeNode(20);
    TreeNode *node15 = new TreeNode(15);
    TreeNode *node7 = new TreeNode(7);
    node3->left = node9;
    node3->right = node20;
    node20->left = node15;
    node20->right = node7;
    vector<int> res = levelOrder(node3);
    for (int i = 0; i < res.size(); i++)
    {
        cout << res[i] << " ";
    }
    cout << endl;
}
```

# 剑指Offer32（二）：分行从上到下打印二叉树

题目：从上到下按层打印二叉树，同一层的结点按从左到右的顺序打印，每一层打印到一行。

## 思路:和32_1相比,只需要添加两个变量

一个记录当前层等待输出的个数cur,另一个记录下一层的个数next

### show me code

```c++
vector<vector<int>> levelOrder(TreeNode *root)
{
    if (root == NULL)
    {
        return {};
    }
    deque<TreeNode *> deq;
    vector<vector<int>> res;
    vector<int> res_level; // 每一层的值
    deq.push_back(root);
    int cur = 1;
    int next = 0;
    while (!deq.empty())
    {
        TreeNode *node = deq.front();
        res_level.push_back(node->val);
        // 子节点入队列
        if (node->left)
        {
            deq.push_back(node->left);
            next++;
        }
        if (node->right)
        {
            deq.push_back(node->right);
            next++;
        }
        // node出队列
        deq.pop_front();
        cur--;
        // 当cur为0,即当前层的都放到vector中了
        if (cur == 0)
        {
            res.push_back(res_level);
            res_level.clear(); // 清空当前层
            cur = next;
            next = 0;
        }
    }
    return res;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *node3 = new TreeNode(3);
    TreeNode *node9 = new TreeNode(9);
    TreeNode *node20 = new TreeNode(20);
    TreeNode *node15 = new TreeNode(15);
    TreeNode *node7 = new TreeNode(7);
    node3->left = node9;
    node3->right = node20;
    node20->left = node15;
    node20->right = node7;
    vector<vector<int>> res = levelOrder(node3);
    for (int i = 0; i < res.size(); i++)
    {
        for (int j = 0; j < res[i].size(); j++)
        {
            cout << res[i][j] << " ";
        }
        cout << endl;
    }
    cout << endl;
}
```

#  剑指Offer32（三）：之字形打印二叉树

题目：请实现一个函数按照之字形顺序打印二叉树

即第一行按照从左到右的顺序打印，第二层按照从右到左的顺序打印，第三行再按照从左到右的顺序打印，其他行以此类推。

## 思路1:利用双向队列

1. 在32_2基础上,添加奇偶标志位
2. 奇数层,从左至右输出,所以前取后放
3. 偶数层,从右至左输出,所以后取前放


### show me code

```c++
vector<vector<int>> levelOrder(TreeNode *root)
{
    if (root == NULL)
    {
        return {};
    }
    deque<TreeNode *> deq;
    vector<vector<int>> res;
    vector<int> res_level; // 每一层的值
    bool isOdd = true;     // 奇偶标志位
    TreeNode *node;
    deq.push_back(root);
    int cur = 1;
    int next = 0;
    while (!deq.empty())
    {
        // 奇数层,从左至右输出,所以前取后放
        if (isOdd)
        {
            // 前取
            node = deq.front();
            res_level.push_back(node->val);
            deq.pop_front();
            cur--;
            // 后放
            if (node->left)
            {
                deq.push_back(node->left);
                next++;
            }
            if (node->right)
            {
                deq.push_back(node->right);
                next++;
            }
        }
        // 偶数层,从右至左输出,所以后取前放
        else
        {
            // 后取
            node = deq.back();
            res_level.push_back(node->val);
            deq.pop_back();
            cur--;
            // 前放
            if (node->right)
            {
                deq.push_front(node->right);
                next++;
            }
            if (node->left)
            {
                deq.push_front(node->left);
                next++;
            }
        }
        // 当cur为0,即当前层的都放到vector中了
        if (cur == 0)
        {
            res.push_back(res_level);
            res_level.clear(); // 清空当前层
            isOdd = !isOdd;    // 切换奇偶
            cur = next;
            next = 0;
        }
    }
    return res;
}
```


### 测试用例

```c++
int main(int argc, char **argv)
{
    // TreeNode* node3 = new TreeNode(3);
    // TreeNode* node9 = new TreeNode(9);
    // TreeNode* node20 = new TreeNode(20);
    // TreeNode* node15 = new TreeNode(15);
    // TreeNode* node7 = new TreeNode(7);
    // node3->left = node9;
    // node3->right = node20;
    // node20->left = node15;
    // node20->right = node7;
    // vector<vector<int>> res = levelOrder(node3);
    TreeNode *node1 = new TreeNode(1);
    TreeNode *node2 = new TreeNode(2);
    node1->left = node2;
    vector<vector<int>> res = levelOrder(node1);
    for (int i = 0; i < res.size(); i++)
    {
        for (int j = 0; j < res[i].size(); j++)
        {
            cout << res[i][j] << " ";
        }
        cout << endl;
    }
}
```

#  剑指Offer33：二叉搜索树的后序遍历序列

题目：输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。
如果是则返回true，否则返回false。假设输入的数组的任意两个数字都互不相同。

## 思路:
BST的后序遍历满足两个条件:
1. 左子树<根<右子树
2. 最后一个数字是根节点
3. 
找到左右子树的序列,并*递归*检查左右子树是不是后序遍历序列
递归终止条件:序列的左右断点重合,即无法再分出子树

### show me code

```c++
bool recursion(vector<int> &vec, int left, int right);

bool verifyPostorder(vector<int> &postorder)
{
    return recursion(postorder, 0, postorder.size() - 1);
}

bool recursion(vector<int> &vec, int left, int right)
{
    // 递归终止条件
    if (left >= right)
        return true;
    int root = vec[right]; // 根节点
    // 根据左子树<根节点,找左子树[left,divide-1]
    int divide = left; // 设置一个divide变量可以避免超时
    for (; divide < right; divide++)
    {
        if (vec[divide] > root)
            break;
    }
    // 检查右子树[divide,right]>根节点
    for (int j = divide; j < right; j++)
    { /这里不是j < right-1*
        if (vec[j] < root)
            return false;
    }
    // 上面已经把遍历序列分成:左子树[left,divide-1];右子树[divide,right-1]
    // 左右子树都要满足,因此用&&连接
    return recursion(vec, left, divide - 1) && recursion(vec, divide, right - 1);
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    // 测试1输出false
    vector<int> in1 = {1, 6, 3, 2, 5};
    if (verifyPostorder(in1))
    {
        cout << "true" << endl;
    }
    else
    {
        cout << "false" << endl;
    }
    // 测试2输出false
    vector<int> in2 = {1, 2, 5, 10, 6, 9, 4, 3};
    if (verifyPostorder(in2))
    {
        cout << "true" << endl;
    }
    else
    {
        cout << "false" << endl;
    }
}
```

#  剑指Offer34：二叉树中和为某一值的路径

题目：输入一棵二叉树和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。
从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。

## 思路:前序遍历+回溯
当遍历到叶子节点后,检查当前路径的长度
一层递归结束后,需要弹出path末端的节点
递归终点:到左右子节点为空 

### show me code

```c++
void recursion(TreeNode* root, int& sum, vector<vector<int>>& res, vector<int>& path, int& p_sum) {
    path.push_back(root->val);
    // 到叶子节点,检查当前路径长度
    if (root->left == NULL && root->right == NULL) {
        for (int i = 0;i < path.size();i++) {
            p_sum += path[i];
        }
        if (p_sum == sum) {
            res.push_back(path);
        }
        p_sum = 0;
    }
    if (root->left)
        recursion(root->left, sum, res, path, p_sum);
    if (root->right)
        recursion(root->right, sum, res, path, p_sum);
    path.pop_back();// 返回父节点的时候要从数组中pop末端节点
}

vector<vector<int>> pathSum(TreeNode* root, int sum) {
    if (root == NULL) {
        return {};
    }
    vector<vector<int>> res;
    vector<int> path;
    int p_sum = 0;
    recursion(root, sum, res, path, p_sum);
    return res;
}
```

### 测试用例
```c++
int main(int argc, char** argv) {
    TreeNode* n5 = new TreeNode(5);
    TreeNode* n4 = new TreeNode(4);
    TreeNode* n8 = new TreeNode(8);
    TreeNode* n11 = new TreeNode(11);
    TreeNode* n13 = new TreeNode(13);
    TreeNode* n4_ = new TreeNode(4);
    TreeNode* n7 = new TreeNode(7);
    TreeNode* n2 = new TreeNode(2);
    TreeNode* n5_ = new TreeNode(5);
    TreeNode* n1 = new TreeNode(1);
    n5->left = n4;
    n5->right = n8;
    n4->left = n11;
    n8->left = n13;
    n8->right = n4_;
    n11->left = n7;
    n11->right = n2;
    n4_->left = n5_;
    n4_->right = n1;
    vector<vector<int>> res = pathSum(n5, 22);
    for (int i = 0;i < res.size();i++) {
        for (int j = 0;j < res[i].size();j++) {
            cout << res[i][j] << " ";
        }
        cout << endl;
    }
}
```

# 剑指Offer36：二叉搜索树与双向链表

题目：输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求
不能创建任何新的结点，只能调整树中结点指针的指向。

## 思路:

1. 本题目需要返回的是循环双向队列
2。 BST的中序遍历就是递增序列
3. 先定义head和tail指针,head指向左递归的终点,tail随着递归的回溯向后移动
4. 最后将头尾连接起来,实现循环

### show me code

```c++
Node *head, *tail = NULL;

void dfs(Node *root)
{
    if (!root)
        return;
    dfs(root->left);
    // head指针在左递归尽头就确定不变了
    // tail指针只有当第一次左递归到头的时候是NULL
    // tail用作一个判断,tail非空表示已经发生回溯,root指向tail的后一个数字
    if (!tail)
    {
        head = root; // head指向BST的左递归尽头
    }
    else
    {
        // root已经回溯到tail后一个数字
        tail->right = root;
        root->left = tail;
    }
    tail = root; // 左递归到头之后tail就不是NULL了
    dfs(root->right);
}

Node *treeToDoublyList(Node *root)
{
    if (!root)
        return NULL;
    dfs(root);
    head->left = tail;
    tail->right = head;
    return head;
}
```
### 测试用例

```c++
int main(int argc, char **argv)
{
    Node *n1 = new Node(1);
    Node *n3 = new Node(3);
    Node *n2 = new Node(2, n1, n3);
    Node *n5 = new Node(5);
    Node *n4 = new Node(4, n2, n5);
    Node *list = treeToDoublyList(n4);
    cout << list->val << " left: " << list->left->val << " right: " << list->right->val << endl;
}
```

# 剑指Offer37. 序列化二叉树

题目：请实现两个函数，分别用来序列化和反序列化二叉树。
说明: 不要使用类的成员 / 全局 / 静态变量来存储状态，你的序列化和反序列化算法应该是无状态的。 

## 思路:
序列化的方式和书上不一样,书上是前序遍历,LeetCode上面是层序遍历。其实根据题目意思,只要反序列化后能和之前一样,就没问题
关于层序遍历,可以先看LeetCode102,要用到BFS广度优先遍历
注意：这里末尾输出会包含很多null,即叶子节点的子节点，但是只要能够反序列化成原始的状态,就能通过测试 
### show me code

```c++
string serialize(TreeNode *root)
{
    if (!root)
        return "";
    ostringstream res;
    queue<TreeNode *> bfs;
    bfs.push(root);
    while (!bfs.empty())
    {
        TreeNode *node = bfs.front();
        bfs.pop();
        if (node != NULL)
        {
            bfs.push(node->left);
            bfs.push(node->right);
            res << node->val << ',';
        }
        else
        {
            res << "null,";
        }
    }
    return res.str();
}

TreeNode *deserialize(string data)
{
    if (data.empty())
    {
        return NULL;
    }
    istringstream node_steam(data); // 从data中读取string数据,会自动分割
    string node;
    vector<TreeNode *> res; // 用vector存储节点,方便索引
    while (getline(
        node_steam, node,
        ','))
    { // 这里要用到getline指定分隔符,如果是空格分隔符,直接用node_stream
        // >> node就好了
        if (node == "null")
        { // 这里不是"null,"分隔符会自动去除
            res.push_back(NULL);
        }
        else
        {
            res.push_back(new TreeNode(stoi(node)));
        }
    }
    // 下面要将vector中的指针连起来
    // 很巧妙的做法,用两个指针i和j
    // i是vector的索引指针,一次循环移动一次
    // j是左右指针的索引,由于序列化的顺序是:a(根)-a左(b)-a右(c)-b(左)-b(右)...
    // 因此j可以用来索引左右节点
    int j = 1; // 跳过根节点
    for (int i = 0; j < res.size();
         i++)
    { // 因为j比i增长的快,因此用j做判断条件可以防止数组越界
        if (!res[i])
        {
            continue;
        }
        res[i]->left = res[j++]; // 先res[j]再j++
        if (j < res.size())
        { // 防止越界
            res[i]->right = res[j++];
        }
    }
    return res[0];
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *n1 = new TreeNode(1);
    TreeNode *n2 = new TreeNode(2);
    TreeNode *n3 = new TreeNode(3);
    TreeNode *n4 = new TreeNode(4);
    TreeNode *n5 = new TreeNode(5);
    n1->left = n2;
    n1->right = n3;
    n3->left = n4;
    n3->right = n5;
    cout << "原树: " << endl;
    printTree(n1);
    string out = serialize(n1);
    cout << "序列化后:" << out << '\n'
         << "反序列化后:" << endl;
    printTree(deserialize(out));
}
```


# 剑指Offer54：二叉搜索树的第k个结点
题目：给定一棵二叉搜索树，请找出其中的第k大的结点。

## 思路：
二叉搜索树的中序遍历就是一个递增的数组
这道题目需要构建一个递减的数组找到第k大的数字，因此采用右-中-左的顺序
### show me code

```c++
int count = 0, res = 0; // 全局变量

void helper(TreeNode *node, int k)
{
    if (!node)
        return;
    helper(node->right, k); //右
    ++count;                //统计递归次数
    if (count == k)
    {
        res = node->val;
        return;
    }
    helper(node->left, k); //左
}

int kthLargest(TreeNode *root, int k)
{
    helper(root, k);
    return res;
}
```
### 测试用例

```c++
int main()
{
    TreeNode *root = new TreeNode(3);
    TreeNode *node1 = new TreeNode(1);
    TreeNode *node4 = new TreeNode(4);
    TreeNode *node2 = new TreeNode(2);
    root->left = node1;
    root->right = node4;
    node1->right = node2;
    printTree(root);
    cout << kthLargest(root, 1) << endl;
}
```

# 剑指Offer55（一）：二叉树的深度

题目：输入一棵二叉树的根结点，求该树的深度。
从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。

## 思路1：层序遍历
和剑指32一样的，对二叉树做一个层序遍历
不同的是用到了两个队列，一个记录当前层节点，另一个记录下一层节点

### show me code

```c++ 
int maxDepth(TreeNode *root)
{
    if (!root)
        return 0;
    queue<TreeNode *> cur;
    cur.push(root);
    int cnt = 0;
    // 第一层循环：按照层数添加节点到cur队列
    while (!cur.empty())
    {
        queue<TreeNode *> next; // next必须在这里，保证每一层都清空
        // 第二层循环：把cur队列中的节点的子节点添加到next
        // 因为queue不支持迭代器遍历，这里用pop函数
        while (!cur.empty())
        {
            // 把子节点存到next
            if (cur.front()->left)
                next.push(cur.front()->left);
            if (cur.front()->right)
                next.push(cur.front()->right);
            cur.pop();
        }
        // 处理下一层节点
        cur = next;
        ++cnt;
    }
    return cnt;
} 
```

## 思路2：DFS后序遍历
找到左子树以及右子树的深度，两者中大的值+1(root)返回
基础的后序遍历返回值是void，这里返回的是深度值，可以理解为函数调用的次数
### show me code

```c++
int maxDepth(TreeNode *root)
{
    if (!root)
        return 0;
    return max(maxDepth(root->left), maxDepth(root->right)) + 1;
}
```

### 测试用例

```c++
int main(int argc, char **argv)
{
    TreeNode *n3 = new TreeNode(3);
    TreeNode *n9 = new TreeNode(9);
    TreeNode *n20 = new TreeNode(20);
    TreeNode *n15 = new TreeNode(15);
    TreeNode *n7 = new TreeNode(7);
    n3->left = n9;
    n3->right = n20;
    n20->left = n15;
    n20->right = n7;
    cout << maxDepth(n3) << endl; //输出3
}
```

# 剑指Offer55（二）：平衡二叉树
题目：输入一棵二叉树的根结点，判断该树是不是平衡二叉树。
如果某二叉树中任意结点的左右子树的深度相差不超过1，那么它就是一棵平衡二叉树。

## 思路1：
用55_1_maxDepth的思路，分别找出左子树和右子树的深度
判断是不是两者差<=1

### show me code

```c++
// depeth函数*递归*找到当前节点的深度
int depth(TreeNode *node)
{
    if (!node)
        return 0;
    return max(depth(node->left), depth(node->right)) + 1;
}
// depth函数也是*递归*确保每个节点都是平衡的
bool isBalanced(TreeNode *root)
{
    if (!root)
        return true;
    bool res = abs(depth(root->left) - depth(root->right)) <= 1;
    return res && isBalanced(root->left) && isBalanced(root->right);
}
```
## 思路2：后序遍历+剪枝
对二叉树做一个后序遍历，从底向上返回深度，当子树不平衡的话就剪枝直接向上返回

### show me code

```c++
int dfs(TreeNode *root)
{
    if (!root)
        return 0;
    int left = dfs(root->left);
    if (left == -1)
        return -1;
    int right = dfs(root->right);
    if (right == -1)
        return -1;
    return abs(left - right) < 2 ? max(left, right) + 1 : -1;
}
bool isBalanced(TreeNode *root)
{
    return dfs(root) != -1;
}
```

### 测试用例

```c++
int main(int argc, char **agrv)
{
    TreeNode *n3 = new TreeNode(3);
    TreeNode *n9 = new TreeNode(9);
    TreeNode *n20 = new TreeNode(20);
    TreeNode *n15 = new TreeNode(15);
    TreeNode *n7 = new TreeNode(7);
    n3->left = n9;
    n3->right = n20;
    n20->left = n15;
    n20->right = n7;

    if (isBalanced(n3))
        cout << "true" << endl;
    else
        cout << "false" << endl;
}
```

# 剑指Offer68-1：二叉搜索树的最近公共祖先
题目：给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。

## 思路1：两次遍历找到路径
1. 对于输入的两个节点，两次遍历得到从根节点到两个节点的路径
2. 由于是二叉搜索树，因此可以通过比对大小确定路径
3. 最后输出两个路径上共同的点

 ### show me code

```c++
vector<TreeNode *> getPath(TreeNode *root, TreeNode *target)
{
    vector<TreeNode *> path;
    TreeNode *node = root;
    while (node != target)
    {
        path.push_back(node);
        if (target->val < node->val)
            node = node->left;
        else
            node = node->right;
    }
    path.push_back(node);
    return path;
}
TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q)
{
    vector<TreeNode *> path_p = getPath(root, p);
    vector<TreeNode *> path_q = getPath(root, q);
    TreeNode *ancestor;
    for (int i = 0; i < path_p.size() && i < path_q.size(); ++i)
    {
        if (path_p[i] == path_q[i])
            ancestor = path_p[i];
        else
            break;
    }
    return ancestor;
} 
```

## 思路2：一次遍历
1. p,q都在root左子树，root遍历至左节点
2. p,q都在root右子树，root遍历至右节点
3. p,q分别在root的左右两边，说明找到了祖先节点
### show me code

```c++
TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q)
{
    while (root)
    {
        if (p->val < root->val && q->val < root->val)
            root = root->left;
        else if (p->val > root->val && q->val > root->val)
            root = root->right;
        else
            break;
    }
    return root;
}
```

## 思路3：递归
### show me code

```c++
TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q)
{
    if (p->val < root->val && q->val < root->val)
        return lowestCommonAncestor(root->left, p, q);
    if (p->val > root->val && q->val > root->val)
        return lowestCommonAncestor(root->right, p, q);
    return root;
}
```
### 测试用例

```c++
int main()
{
    TreeNode *n6 = new TreeNode(6);
    TreeNode *n2 = new TreeNode(2);
    TreeNode *n8 = new TreeNode(8);
    TreeNode *n0 = new TreeNode(0);
    TreeNode *n4 = new TreeNode(4);
    TreeNode *n7 = new TreeNode(7);
    TreeNode *n9 = new TreeNode(9);
    TreeNode *n3 = new TreeNode(3);
    TreeNode *n5 = new TreeNode(5);
    n6->left = n2;
    n6->right = n8;
    n2->left = n0;
    n2->right = n4;
    n8->left = n7;
    n8->right = n9;
    n4->left = n3;
    n4->right = n5;
    cout << lowestCommonAncestor(n6, n2, n4)->val << endl; // 2
}
```

# 剑指Offer68-2：二叉树的最近公共祖先
题目：给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。

## 思路：递归
1. 在root的左子树递归查找p,q
2. 在root的右子树递归查找p,q
3. p,q的分布存在四种情况
    1. 左右都不在，返回nullptr
    2. 左边和右边都存在，返回root
    3. 只在左边找到了，返回root->left
    4. 只在右边找到了，返回root->right
### show me code

```c++
TreeNode *lowestCommonAncestor(TreeNode *root, TreeNode *p, TreeNode *q)
{
    if (!root || !p || !q || p == root || q == root)
        return root;
    TreeNode *left = lowestCommonAncestor(root->left, p, q);
    TreeNode *right = lowestCommonAncestor(root->right, p, q);
    if (!left && !right)
        return nullptr;
    if (left && !right)
        return left;
    if (!left && right)
        return right;
    else
        return root;
}
```

### 测试用例

```c++
int main()
{
    TreeNode *n3 = new TreeNode(3);
    TreeNode *n5 = new TreeNode(5);
    TreeNode *n1 = new TreeNode(1);
    TreeNode *n6 = new TreeNode(6);
    TreeNode *n2 = new TreeNode(2);
    TreeNode *n0 = new TreeNode(0);
    TreeNode *n8 = new TreeNode(8);
    TreeNode *n7 = new TreeNode(7);
    TreeNode *n4 = new TreeNode(4);
    n3->left = n5;
    n3->right = n1;
    n5->left = n6;
    n5->right = n2;
    n1->left = n0;
    n1->right = n8;
    n2->left = n7;
    n2->right = n4;
    cout << lowestCommonAncestor(n3, n5, n4)->val << endl; // 5
}
```

