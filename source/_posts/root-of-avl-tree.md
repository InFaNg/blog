---
title: Root of AVL Tree
date: 2020-08-05 15:43:26
tags: [data structures, binary tree, AVL tree]
mathjax: true
categories: ACM
---
本文将从一道要求求出 AVL树根结点的程序设计竞赛题目说起，介绍 AVL树的基本概念、平衡原理以及旋转操作，并给出相应的 C++ 代码实现.

如果对树的基本概念还不是很了解或了解不多，建议参考 [树 (数据结构)](https://zh.wikipedia.org/wiki/%E6%A0%91_(%E6%95%B0%E6%8D%AE%E7%BB%93%E6%9E%84)).

<!-- more -->

## 题目
An AVL tree is a self-balancing binary search tree. In an AVL tree, the heights of the two child subtrees of any node differ by at most one; if at any time they differ by more than one, rebalancing is done to restore this property. Figures 1-4 illustrate the rotation rules.

![root_of_avl_tree](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree.png)

Now given a sequence of insertions, you are supposed to tell the root of the resulting AVL tree.

### Input Specification
Each input file contains one test case. For each case, the first line contains a positive integer N (≤20) which is the total number of keys to be inserted. Then N distinct integer keys are given in the next line. All the numbers in a line are separated by a space.

### Output Specification
For each test case, print the root of the resulting AVL tree in one line.

### Sample Input 1:
> 5
> 88 70 61 96 120

### Sample Output 1:
> 70

### Sample Input 2:
> 7
> 88 70 61 96 120 90 65

### Sample Output 2:
> 88

## AVL树基本概念
对于一棵二叉搜索树 (binary search tree) 来说，如果它是一棵完全二叉树 (full binary tree)，那么对其进行查询、插入、删除等基本操作的最坏运行时间为$\Theta(lg\ n)$，但如果对于树上的每一个结点，如果最多只有一个结点，即构成最坏情况：整棵树事实上是一条线性链，那么对其进行基本操作的最坏运行时间则为$\Theta(n)$.
![root_of_avl_tree_1](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_1.png)

AVL树的名称取自它的两名发明者 [Georgy Adelson-Velsky](https://en.wikipedia.org/wiki/Georgy_Adelson-Velsky) 与 [Evgenii Landis](https://en.wikipedia.org/wiki/Evgenii_Landis)的名字，并于 1962 年在一篇名为 "An algorithm for the organization of information" 的论文中首次公开. 与上图所示的普通的二叉搜索树 (binary search tree) 的区别就是 AVL树是一种自平衡二叉查找树，即 AVL树在插入或删除结点时，通过一对结点指针的调整保证了对于其中的任意一个结点，其左子树的高度与右子树的高度之差的绝对值不大于 1，以此保证树的平衡，做到了对其进行基本操作的最坏运行时间也为 $Θ(lg n)$. 然而，这么做的代价也有代价：需要对每个结点引入一个额外的属性来存储其高度，并需要额外的计算量来维护结点的高度.

## AVL树旋转方法
下面将介绍 AVL树的两种旋转方法：右旋和左旋.

### 右旋
首先来看第一种二叉树结构调整的方法：右旋：
![root_of_avl_tree_2](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_2.png)

如图所示，图中 A 结点的左子树树高为 2，右子树树高为 0（事实上，右子树是不存在的. 这里我们特别规定，将不存在的树其树高记为 0）. 我们可以将 B 结点设置为 A 与 α 的父结点. 这么做使得树在保证成为二叉搜索树 (binary search tree) 的同时，其左子树的高度与右子树的高度之差的绝对值不大于 1.

然而仅仅这么做，那还是有个问题：如果图中的 B 结点有一个右结点，那该怎么办呢？对于这种情况，二叉树的调整方法是：先将 B 的右子结点挂到 A 的左子结点上，再将 A 结点挂到 B 的右子结点上. 不难证明，这么做在没有破坏二叉搜索树 (binary search tree) 性质的同时，实现了向完全二叉树 (full binary tree) 的调整.
![root_of_avl_tree_3](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_3.png)

下面使用 C++ 代码来描述AVL的右旋调整操作：
```cpp
AVLTree right_rotate(AVLTree A) {
	AVLTree B = A->Left;
	A->Left = B->Right;             // 将 B 的右子结点挂到 A 的左子结点上
	B->Right = A;                   // 将 A 结点挂到 B 的右子结点上

	A->Height = Max(GetHeight(A->Left), GetHeight(A->Right)) + 1;
	B->Height = Max(GetHeight(B->Left), A->Height) + 1;

	return B;
}
```

### 左旋
左旋操作与右旋操作的思路大致类似. 如下图所示，A 的左子树高为 0，其右子树高为2，两者树高之差的绝对值大于 1，因此我们将 B 设置为 A 与 α 的父结点. 对于 B 的左子结点 β，我们可将其挂在 A 的右子结点上.
![root_of_avl_tree_4](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_4.png)

下面使用 C++ 代码来描述AVL的左旋调整操作：
```cpp
AVLTree left_rotate(AVLTree A) {
	AVLTree B = A->Right;
	A->Right = B->Left;             // 将 A 的右子结点挂到 B 的左子结点上
	B->Left = A;                    // 将 A 结点挂到 B 的左子结点上

	A->Height = Max(GetHeight(A->Left), GetHeight(A->Right)) + 1;
	B->Height = Max(GetHeight(B->Right), A->Height) + 1;

	return B;
}
```

## AVL树的重新平衡 (Rebalancing)
当向二叉树中插入或删除一个结点时，整个二叉树可能会出现临时的其中某个结点其左右子树高度差的绝对值大于 1 的情况. 这样的情况共有四种，它们分别是：

* RR 情况 (Right Right Situation)
* LL 情况 (Left Left Situation)
* RL 情况 (Right Left Situation)
* LR 情况 (Left Right Situation)

### RR 情况 (Right Right Situation)
RR 情况 (Right Right Situation) 是指当其中的一个结点不平衡（即它的左子树与右子树树高之差的绝对值大于 1）时，**破坏平衡的结点位于被破坏平衡结点的右子树的右子树上**.

下图就比较清晰地说明了这一情况. 对于这种情况，我们只需对图中的 A 结点与 B 结点进行一次左旋调整即可. 即将 B 的左子结点挂到 A 的右子结点上，再将 A 挂到 B 的左子结点上.
![root_of_avl_tree_5](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_5.png)

### LL 情况 (Left Left Situation)
LL 情况 (Left Left Situation) 与 RR 情况 (Right Right Situation) 类似，指当其中的一个结点不平衡（即它的左子树与右子树树高之差的绝对值大于 1）时，**破坏平衡的结点位于被破坏平衡结点的左子树的左子树上**.

因此，我们只需仿照上面相应的操作，对图中的 A 结点与 B 结点进行一次右旋调整即可. 即将 B 的右子结点挂到 A 的左子结点上，再将 A 挂到 B 的右子结点上.
![root_of_avl_tree_6](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_6.png)

### RL 情况 (Right Left Situation)
RL 情况 (Right Left Situation) 如下图所示，**其破坏平衡的结点位于被破坏平衡结点的右子树的左子树上**. 对于这种情况的处理较上面介绍的两种方法又有不同，需要两次变换才能使得二叉数重新平衡 (rebalancing).

首先看第一次变换，我们将 B 与 C 两结点进行右旋，将 C 的右子结点挂到 B 的左子结点上，再将 B 挂到 C 的右子结点上.
![root_of_avl_tree_7](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_7.png)

再看第二次变换，我们将 A 与 C 两结点进行左旋，将 C 的左子结点挂到 A 的右子结点上，再将 A 挂到 C 的左子结点上.
![root_of_avl_tree_8](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_8.png)

至此，我们实现了 RL 情况 (Right Left Situation) 下二叉树的重新平衡 (rebalancing).

### LR 情况 (Left Right Situation)
再来看 LR 情况 (Left Right Situation)，**其破坏平衡的结点位于被破坏平衡结点的左子树的右子树上**，其思路大致与 RL 情况 (Right Left Situation) 下的思路一致，都是通过两次旋转的方法实现二叉树的重新平衡 (rebalancing).

第一次变换我们将 B 与 C 两节点左旋：
![root_of_avl_tree_9](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_9.png)

第二次变换我们将 A 与 C 两节点右旋：
![root_of_avl_tree_10](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/root_of_avl_tree_10.png)

## 思路
我们可以定义一个 `insert` 函数来实现向 AVL树中插入数据及重新平衡 (rebalancing)，该函数接受两个参数，分别是需要插入到的节点和需要插入的数据. 与普通的二叉搜索树类似，可使用递归的方式以实现将数据自动插入到树的最下一层. 需要特别注意的是，在 AVL树中，我们需要判断当前节点所构成的树是否存在需要重新平衡 (rebalancing) 的四种情况之一. 如果有，则需要根据前文所述的方法对其进行调整.

## 代码
了解上面的内容后，就不难写出该题目的 C++ 代码
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

typedef struct node *ptr_to_node;

struct node {
    int data;
    ptr_to_node left;
    ptr_to_node right;

    int height;
};

int get_height(ptr_to_node N) {
    if (N == NULL)
        return 0;
    if (N->left == NULL && N->right == NULL)
        return 1;
    if (N->right == NULL)
        return get_height(N->left) + 1;
    if (N->left == NULL)
        return get_height(N->right) + 1;
    return max(get_height(N->left), get_height(N->right)) + 1;
}

ptr_to_node right_rotate(ptr_to_node A) {
    ptr_to_node B = A->left;
    A->left = B->right;
    B->right = A;

    A->height = max(get_height(A->left), get_height(A->right)) + 1;
    B->height = max(get_height(B->left), A->height) + 1;

    return B;
}

ptr_to_node left_rotate(ptr_to_node A) {
    ptr_to_node B = A->right;
    A->right = B->left;
    B->left = A;

    A->height = max(get_height(A->left), get_height(A->right)) + 1;
    B->height = max(A->height, get_height(B->right)) + 1;

    return B;
}

ptr_to_node insert(ptr_to_node N, int X) {
    if (N == NULL) {
        N = (ptr_to_node)malloc(sizeof(node));
        N->data = X;
        N->left = N->right = NULL;
        N->height = 0;
    }
    else if (X < N->data) {
        N->left = insert(N->left, X);

        if (get_height(N->left) - get_height(N->right) == 2)
            if (X < N->left->data)                // LL情况 (Left Left Situation)
                N = right_rotate(N);
            else {                                // LR情况 (Left Right Situation)
                N->left = left_rotate(N->left);
                N = right_rotate(N);
            }
    }
    else if (X > N->data) {
        N->right = insert(N->right, X);

        if (get_height(N->right) - get_height(N->left) == 2)
            if (X > N->right->data)               // RR情况 (Right Right Situation)
                N = left_rotate(N);
            else {                                // RL情况 (Right Left Situation)
                N->right = right_rotate(N->right);
                N = left_rotate(N);
            }
    }

    N->height = max(get_height(N->left), get_height(N->right)) + 1;

    return N;
}

int main() {
    int N;
    ptr_to_node T = NULL;

    cin >> N;
    while (N--) {
        int num;
        cin >> num;

        T = insert(T, num);
    }

    cout << T->data << endl;

    return 0;
}
```