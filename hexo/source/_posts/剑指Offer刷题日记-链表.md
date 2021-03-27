---
title: 剑指Offer刷题日记-链表
date: 2020-08-18 16:24:33
tags: 
    - 剑指Offer
    - 链表
categories: 数据结构与算法 
---

记录[剑指Offer](https://leetcode-cn.com/problemset/lcof/)中``链表``相关的题目思路及解答。

文章会给出思路和代码，同时为了方便本地调试，还会提供相应的测试用例。

<!-- more -->

如果要在本地调试，需要先包含下面的头文件。

```c++
// 本头文件定义了剑指Offer中链表题常用的链表结构体和链表构造函数
#ifndef MYLISTNODE
#define MYLISTNODE

#include <iostream>

using namespace std;

struct ListNode
{
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};

void printList(ListNode *head)
{
    while (head != NULL)
    {
        cout << head->val << "->";
        head = head->next;
    }
    cout << "NULL" << endl;
}
#endif
```

 # 剑指Offer6：从尾到头打印链表
题目：输入一个链表的头节点，从尾到头反过来返回每个节点的值（用数组返回）。

## 思路1：使用栈先入后出的特性，实现反向打印

### show me code
```C++
 vector<int> reversePrint(ListNode *head)
{
    stack<int> stk;
    vector<int> vec;
    //入栈
    while (head != nullptr)
    {
        stk.push(head->val);
        head = head->next;
    }
    //出栈
    while (!stk.empty())
    {
        vec.push_back(stk.top()); //栈顶元素进数组
        stk.pop();                //出栈
    }
    return vec;
} 
```

## 思路2：递归的方法，递归本质就是一个栈结构，但是其时间空间复杂度较差

### show me code
```C++
vector<int> vec_recursive;
vector<int> reversePrint(ListNode *head)
{
    if (head == nullptr)
        return vec_recursive;
    reversePrint(head->next);
    vec_recursive.push_back(head->val);
    return vec_recursive;
}
```


### 测试用例
```C++
int main(int argc, char **argv)
{
    //输入[1,3,2]
    ListNode *node1 = new ListNode(1);
    ListNode *node2 = new ListNode(2);
    ListNode *node3 = new ListNode(3);
    node1->next = node3;
    node3->next = node2;
    cout << "before: ";
    printList(node1);
    cout << "after(recursive): ";
    vector<int> revList = reversePrint(node1);
    vector<int>::iterator iter;
    for (iter = revList.begin(); iter != revList.end(); iter++)
    {
        cout << *iter << " ";
    }
    cout << endl;
}
```

# 剑指Offer18：删除链表的节点
题目：给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
返回删除后的链表的头节点。
注意：此题对比原题有改动 

## 思路1:单指针扫描法
 
 
### show me code
```C++
ListNode* deleteNode(ListNode* head, int val) {
    //第一个元素就是要删除的
    if (head->val == val) {
        return head->next;
    }
    ListNode* node = head;//保存头指针,不然while之后找不到头指针
    while (head->next->val != val) {
        head = head->next;
    }
    head->next = head->next->next;
    return node;
} 
```

## 思路2:递归删除法

### show me code
```C++
ListNode *deleteNode(ListNode *head, int val)
{
    if (head->val == val)
    {
        return head->next;
    }
    head->next = deleteNode(head->next, val);
    return head;
}
```


### 测试用例
```C++
int main(int argc, char **argv)
{
    ListNode *node4 = new ListNode(4);
    ListNode *node5 = new ListNode(5);
    ListNode *node1 = new ListNode(1);
    ListNode *node9 = new ListNode(9);
    node4->next = node5;
    node5->next = node1;
    node1->next = node9;
    cout << "原始链表: ";
    printList(node4);
    cout << "删除: ";
    printList(deleteNode(node4, 1));
}
```

# 剑指Offer22：链表中倒数第k个结点
题目：输入一个链表，输出该链表中倒数第k个结点。为了符合大多数人的习惯，
本题从1开始计数，即链表的尾结点是倒数第1个结点。例如一个链表有6个结点，
从头结点开始它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个结点是
值为4的结点。 

## 思路1:由于是单向链表,不能回溯.因此两次遍历
第一次遍历确定链表长度,第二次遍历次数为长度-k 


### show me code
```C++
ListNode* getKthFromEnd(ListNode* head, int k) {
    int length = 0;
    ListNode* list = head;  //保存头指针
    while (head != NULL) {
        head = head->next;
        length++;
    }
    int cnt = 1;
    while (list != NULL) {
        if (cnt == length - k + 1) {
            break;
        }
        list = list->next;
        cnt++;
    }
    return list;
} 
```

## 思路2:快慢指针,一个指针先走k步,保证双指针的距离为k
当先走的指针指向NULL时,后走的指针指向倒数k个结点 

### show me code
```C++
ListNode *getKthFromEnd(ListNode *head, int k)
{
    if (head == NULL | k == 0)
        return NULL;
    ListNode *slow = head;
    ListNode *quick = head;

    int cnt_k = 0;
    while (cnt_k < k)
    {
        quick = quick->next;
        cnt_k++;
    }
    while (quick != NULL)
    {
        slow = slow->next;
        quick = quick->next;
    }
    return slow;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    ListNode *node1 = new ListNode(1);
    ListNode *node2 = new ListNode(2);
    ListNode *node3 = new ListNode(3);
    ListNode *node4 = new ListNode(4);
    ListNode *node5 = new ListNode(5);
    node1->next = node2;
    node2->next = node3;
    node3->next = node4;
    node4->next = node5;
    cout << "原始链表: ";
    printList(node1);
    cout << "倒数第k个结点:";
    ListNode *endk = getKthFromEnd(node1, 1);
    printList(endk);
}
```
 
# 剑指Offer24：反转链表
题目：定义一个函数，输入一个链表的头结点，反转该链表并输出反转后链表的头结点。

## 思路1:# 剑指Offer书上,定义三个指针

### show me code
```C++
 ListNode* reverseList(ListNode* head) {
    ListNode* res = NULL;  //返回值
    ListNode* cur = head;
    ListNode* pre = NULL;
    ListNode* next = NULL;
    while (cur != NULL) {
        next = cur->next;  //把cur->next存起来,下一步就要断开链表了
        if (next == NULL) {
            res = cur;
        }
        cur->next = pre;  //反转
        pre = cur;        //右移一次
        cur = next;       //右移一次
    }
    return res;
} 
```
## 思路2:递归
https://leetcode-cn.com/problems/fan-zhuan-lian-biao-lcof/solution/ru-guo-ni-kan-wan-ping-lun-he-ti-jie-huan-you-wen-/

### show me code
```C++
ListNode *reverseList(ListNode *head)
{
    // 递归的终止条件
    if (head == NULL | head->next == NULL)
    {
        return head;
    }
    ListNode *cur = reverseList(head->next);
    head->next->next = head;
    head->next = NULL;
    return cur;
}
```


### 测试用例
```C++
int main(int argc, char **argv)
{
    ListNode *node1 = new ListNode(1);
    ListNode *node2 = new ListNode(2);
    ListNode *node3 = new ListNode(3);
    ListNode *node4 = new ListNode(4);
    ListNode *node5 = new ListNode(5);
    node1->next = node2;
    node2->next = node3;
    node3->next = node4;
    node4->next = node5;
    cout << "原始链表: ";
    printList(node1);
    cout << "反转链表: ";
    printList(reverseList(node1));
}
```

# 剑指Offer25：合并两个排序的链表
题目：输入两个递增排序的链表，合并这两个链表并使新链表中的结点仍然是按照递增排序的。

## 思路:迭代
由于一开始合并链表中无头节点,需要建立一个辅助结点dum
由于是有序链表,当一个链表遍历完了,只要把剩下的链表接到后面就好 

### show me code
```C++
ListNode *mergeTwoLists(ListNode *l1, ListNode *l2)
{
    if (l1 == NULL || l2 == NULL)
    {
        return l1 == NULL ? l2 : l1;
    }
    ListNode *dum = new ListNode(-1);
    ListNode *cur = dum;
    while (l1 != NULL && l2 != NULL)
    {
        if (l1->val >= l2->val)
        {
            cur->next = l2;
            l2 = l2->next;
        }
        else
        {
            cur->next = l1;
            l1 = l1->next;
        }
        cur = cur->next;
    }
    cur->next = l1 == NULL ? l2 : l1; // 把剩下的接上去
    return dum->next;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    ListNode *n11 = new ListNode(1);
    ListNode *n12 = new ListNode(2);
    ListNode *n14 = new ListNode(4);
    ListNode *n21 = new ListNode(1);
    ListNode *n23 = new ListNode(3);
    ListNode *n24 = new ListNode(4);
    n11->next = n12;
    n12->next = n14;
    n21->next = n23;
    n23->next = n24;
    cout << "链表1: ";
    printList(n11);
    cout << "链表2: ";
    printList(n21);
    cout << "合并后:";
    printList(mergeTwoLists(n11, n21));
}
```

# 剑指Offer35. 复杂链表的复制
请实现 copyRandomList 函数，复制一个复杂链表。

在复杂链表中，每个节点除了有一个 next 指针指向下一个节点，还有一个 random 指针指向链表中的任意节点或者 null。

### show me code
```C++
class Node
{
public:
    int val;
    Node *next;
    Node *random;

    Node(int _val)
    {
        val = _val;
        next = NULL;
        random = NULL;
    }
};
```
 
## 思路1:
random输出的是到head的距离

第一轮遍历,先根据next把新链表连接起来,并建一个哈希表

再根据哈希表,确定新链表random的连接关系 

### show me code
```C++
Node* copyRandomList(Node* head) {
    if (head == NULL)
        return NULL;
    unordered_map<Node*, Node*> match;  //哈希表
    Node* pos = head;                   // 原链表的索引指针
    Node* res_head = new Node(head->val);
    Node* res_pos = res_head;  // 新链表的索引指针
    // 第一轮遍历先根据next建好链表
    while (pos) {
        match[pos] = res_pos;  // 哈希表中存新旧链表的索引指针
        if (!pos->next) {
            res_pos->next = NULL;
        } else {
            res_pos->next = new Node(pos->next->val);
        }
        pos = pos->next;
        res_pos = res_pos->next;
    }
    // 第一轮遍历结束,pos和res_pos指向链表末端,需要重新指向头部
    pos = head;
    res_pos = res_head;
    // 第二轮遍历,根据哈希表,连接random
    while (pos) {
        res_pos->random = match[pos->random];
        res_pos = res_pos->next;
        pos = pos->next;
    }
    return res_head;
} 
```
 
## 思路2:
1. 复制节点,复制节点插入在原始节点后面如:a->a'->b->b'
2. 复制random指针,复制后的random指向原random的next
3. 链表的偶数项就是复制后的链表 
4. 
### show me code
```C++
void copyNode(Node *head);
void copyRandom(Node *head);
Node *getCopy(Node *head);

Node *copyRandomList(Node *head)
{
    if (!head)
        return NULL;
    copyNode(head);
    copyRandom(head);
    return getCopy(head);
}
// 1.复制节点,复制节点插入在原始节点后面如:a->a'->b->b'
void copyNode(Node *head)
{
    Node *pos = head;
    while (pos)
    {
        Node *copy = new Node(pos->val);
        // 链表插入节点,先接上,再断开
        copy->next = pos->next;
        pos->next = copy;
        // 插入节点后,pos指针移动两个单位
        pos = pos->next->next;
    }
}
// 2.复制random指针,复制后的random指向原random的next
void copyRandom(Node *head)
{
    Node *pos = head;
    while (pos)
    {
        // 如果a->random存在,那么a'->random就是a->random->next
        if (pos->random)
        {
            pos->next->random = pos->random->next;
        }
        else
        {
            pos->next->random = NULL;
        }
        pos = pos->next->next;
    }
}
// 3.链表的偶数项就是复制后的链表
Node *getCopy(Node *head)
{
    Node *pos = head;
    Node *copy = pos->next;
    Node *res = copy;
    while (pos)
    {
        // 下面这里的指针移动要注意画图理解,关注当pos移动到NULL之后的操作
        pos->next = copy->next;
        pos = pos->next;
        // 下面很巧妙,可以用a->a'->b->b'->NULL理画图理解
        if (pos)
        {
            copy->next = pos->next;
        }
        copy = copy->next;
    }
    return res;
}
```

### 测试用例
```C++
int main(int argc, char **argv)
{
    Node *head = new Node(7);
    Node *two = new Node(13);
    Node *three = new Node(11);
    Node *four = new Node(10);
    Node *five = new Node(1);
    head->next = two;
    two->next = three;
    three->next = four;
    four->next = five;
    five->next = NULL;

    head->random = NULL;
    two->random = head;
    three->random = five;
    four->random = three;
    five->random = head;

    // 本题目不写输出函数,因为输出部分其实就涉及到了复制的思路
    // 直接在调试里面看

    Node *res = copyRandomList(head);
}
```

# 剑指Offer52：两个链表的第一个公共结点
题目：输入两个链表，找出它们的第一个公共结点。

## 思路1:哈希表

### show me code
```C++
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
    if (!headA || !headB)
        return nullptr;
    unordered_map<ListNode *, int> nodes;
    ListNode *n1 = headA;
    ListNode *n2 = headB;
    while (n1) {
        ++nodes[n1];
        n1 = n1->next;
    }
    // 遍历一次链表2，当哈希表中有相同元素返回
    while (n2) {
        if (nodes[n2])   
            return n2;
        n2 = n2->next;
    }
    return nullptr;
}
```

## 思路2
1. headA的长度可以表示为 A+same
2. headB的长度可以表示为 B+same
3. 因此采用两个指针分别遍历 A+same+B 和 B+same+A
4. 最后停下来的地方就是重复的节点 

### show me code
```C++
ListNode *getIntersectionNode(ListNode *headA, ListNode *headB)
{
    if (!headA || !headB)
        return nullptr;
    ListNode *ptrA = headA, *ptrB = headB;
    while (ptrA != ptrB)
    {
        if (ptrA)
            ptrA = ptrA->next;
        else
            // headA走到头，从headB开始走
            ptrA = headB;
        if (ptrB)
            ptrB = ptrB->next;
        else
            ptrB = headA;
    }
    return ptrA;
}
```

### 测试用例
```C++
int main(int argc, char **agrv)
{
    // 这道题目的输入要注意，不是两条链表，可以理解是一个分叉链表
    // 各自独立的节点
    ListNode *headA = new ListNode(4);
    ListNode *a1 = new ListNode(1);
    ListNode *headB = new ListNode(5);
    ListNode *b0 = new ListNode(0);
    ListNode *b1 = new ListNode(1);
    // 下面是重复的节点
    ListNode *n8 = new ListNode(8);
    ListNode *n4 = new ListNode(4);
    ListNode *n5 = new ListNode(5);
    // 链接重复部分
    n8->next = n4;
    n4->next = n5;

    headA->next = a1;
    a1->next = n8;
    printList(headA);
    headB->next = b0;
    b0->next = b1;
    b1->next = n8;
    printList(headB);

    cout << getIntersectionNode(headA, headB)->val << endl;
}
```
