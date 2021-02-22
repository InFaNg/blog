---
title: 玩转二叉树
date: 2020-07-19 20:03:51
tags: [data structures, binary tree]
categories: ACM
---

## 题目
给定一棵二叉树的中序遍历和前序遍历，请你先将树做个镜面反转，再输出反转后的层序遍历的序列。所谓镜面反转，是指将所有非叶结点的左右孩子对换。这里假设键值都是互不相等的正整数。
<!-- more -->

### 输入格式
输入第一行给出一个正整数`N`（≤30），是二叉树中结点的个数。第二行给出其中序遍历序列。第三行给出其前序遍历序列。数字间以空格分隔。

### 输出格式
在一行中输出该树反转后的层序遍历的序列。数字间以1个空格分隔，行首尾不得有多余空格。

### 输入样例
> 7
> 1 2 3 4 5 6 7
> 4 1 3 2 6 5 7

### 输出样例
> 4 6 1 7 5 3 2

### 题目截图
![玩转二叉树题目](/images/wan_zhuan_er_cha_shu_problem.png)

## 分析

由前序序列的性质可知，前序序列的第一个数一定是当前二叉树的根节点. 例如，再输入样例中第一个数是 4，因此我们可知 4 是二叉树的根节点。之后再在中序遍历序列中找到 4，就可以知道 4 的左边有三个数，分别是 1、2、3，4 的右边同样有三个数分别是 5、6、7. 此外，我们还可以知道前三个数 1、2、3 按照中序排序序列的顺序为 1、3、2，后三个数5、6、7的中序排序序列顺序为6、5、7.
![玩转二叉树分析1](/images/wan_zhuan_er_cha_shu_1.png)

接下来，我们可以利用递归分别对根节点左侧的三个数与右侧的三个数建立二叉树. 下面以左侧的三个数为例，它们的前序遍历序列为 1、3、2，中序遍历序列为 1、2、3. 将这三个数看作完整的二叉树，则他们的根节点是 1，它的左子节点为空，右子节点有两个数. 再对右侧两个数分析，它们的根节点为 3，左子节点为 2，右子节点为空. 这里需要特别注意的是，**前序遍历序列为 3、2，中序遍历序列为 2、3 的两个数，只有可能为一个是父节点，一个为左子节点或右子节点，而不可能两个都为子节点，即必有一个为父节点**. 因此不难得出，前序遍历序列的第一个数 3 即为父节点，在中序遍历序列中，2 位于 3 的左边，即 2 为 3 的左子节点.
![玩转二叉树分析2](/images/wan_zhuan_er_cha_shu_2.png)

对右边的子二叉树做同样的分析，即可得出右侧的结构.
![玩转二叉树分析3](/images/wan_zhuan_er_cha_shu_3.png)

接下来，只需建立个队列，将二叉树的每个节点的数据装入队列即可。其中，由于题目要求反转，只需先装入右子节点，再装入左子节点即可.

## 代码
```cpp
#include <iostream>
#include <queue>
using namespace std;

int N, A[35], B[35];

struct TreeNode {
    int data, left_child, right_child;
} node[35];

int node_index = 1;

void create_tree(int index, int begin1, int end1, int begin2, int end2);
void print();

void create_tree(int index, int begin1, int end1, int begin2, int end2) {
    node[index].data = B[begin2];

    int father_index;
    for (father_index = begin1; father_index < end1; father_index++)
        if (A[father_index] == B[begin2])
            break;

    int left_N = father_index - begin1, right_N = end1 - father_index - 1;
    if (left_N) {
        node[index].left_child = ++node_index;
        create_tree(node[index].left_child, begin1, father_index, begin2 + 1, begin2 + 1 + left_N);
    }
    if (right_N) {
        node[index].right_child = ++node_index;
        create_tree(node[index].right_child, father_index + 1, end1, begin2 + 1 + left_N, end2);
    }
}

void print() {
    int arr[35], n = 0;
    queue<int> q;

    q.push(1);

    while (q.size()) {
        int i = q.front();
        
        if (i) {
            arr[n++] = node[i].data;
            q.push(node[i].right_child);
            q.push(node[i].left_child);
        }

        q.pop();
    }

    cout << arr[0];
    for (int i = 1; i < n; i++)
        cout << " " << arr[i];
}

int main() {
    cin >> N;

    for (int i = 0; i < N; i++)
        cin >> A[i];
    for (int i = 0; i < N; i++)
        cin >> B[i];

    create_tree(1, 0, N, 0, N);
    print();

    return 0;
}
```