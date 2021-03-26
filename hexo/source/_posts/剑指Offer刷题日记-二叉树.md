---
title: 剑指Offer刷题日记-二叉树
date: 2020-08-19 16:47:29
tags:    
    - 剑指Offer
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

### show me code

```c++
int main(int argc, char **argv)
{
    vector<int> pre_vec = {3, 9, 20, 15, 7};
    vector<int> in_vec = {9, 3, 15, 20, 7};
    TreeNode *tree = buildTree(pre_vec, in_vec);
    printTree(tree);
}
```
# 剑指Offer8：二叉树的下一个结点

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

## 思路：
中序遍历:左递归->打印->右递归
存在3种情况:
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


### show me code

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

# 剑指Offer26：树的子结构
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

### show me code

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

# 剑指Offer27：二叉树的镜像
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

### show me code

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

# 剑指Offer28：对称的二叉树

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

### show me code

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

# 剑指Offer32（一）：不分行从上往下打印二叉树

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

### show me code

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

### show me code

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

# 剑指Offer32（三）：之字形打印二叉树

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


### show me code

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

# 剑指Offer33：二叉搜索树的后序遍历序列

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

### show me code

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

# 剑指Offer34：二叉树中和为某一值的路径

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
### show me code

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

