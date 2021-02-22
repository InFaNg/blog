---
title: 树的遍历
date: 2020-07-27 22:51:34
tags: [data structures, binary tree]
categories: ACM
mathjax: true
---

## 题目
给定一棵二叉树的后序遍历和中序遍历，请你输出其层序遍历的序列。这里假设键值都是互不相等的正整数。
<!-- more -->

### 输入格式
输入第一行给出一个正整数 ***N***（≤30），是二叉树中结点的个数。第二行给出其后序遍历序列。第三行给出其中序遍历序列。数字间以空格分隔。

### 输出格式
在一行中输出该树的层序遍历的序列。数字间以1个空格分隔，行首尾不得有多余空格。

### 输入样例
> 7
> 2 3 1 5 7 6 4
> 1 2 3 4 5 6 7

### 输出样例
> 4 1 6 3 5 7 2

## 分析
以题目给定输入样例为例，我们可以参考[【数据结构】玩转二叉树](/2020/07/19/wan-zhuan-er-cha-shu/)的做法. 只不过在这道题中，我们需要根据**后续遍历的最后一位**确定树的根节点，然后在中序遍历中找到树的根节点. 位于树的左边的数即为树的左子节点所包含的数，树的右边的数即为树的右子节点所包含的数. 使用递归循环往复，直到左子节点、右子节点均不含子节点为止. 

下面以图片形式给出了具体的过程
![tree_traversals1.png](/images/tree_traversals1.png)
![tree_traversals2.png](/images/tree_traversals2.png)

但是，我们真的需要定义一个结构体来储存树的每一个节点所对应的值、它的左子节点和它的右子节点吗？这么做是没有什么问题，但是还有更好的方法. **我们可以使用两个一维数组，分别用来存放左子节点和右子节点的信息，数组的下标用来表示父节点的值，数组的值对应父节点的左子节点或是右子节点**. 例如，我们可以令 `L[4] = 1` 表示 4 的左子节点是 1，令 `R[4] = 6` 表示 4 的右子节点是 6. 这么一来，不仅省去了定义结构体，还使得空间复杂度从 $T(n) = O(3n)$ 降为 $T(n) = O(2n)$.

由于需要给出层序遍历序列，因此我们可使用广度优先搜索(BFS)输出答案.

## 代码
```cpp
#include <iostream>
#include <algorithm>
#include <queue>

using namespace std;

const int maxn = 1005;
int N;
int mid[maxn], post[maxn];
int L[maxn], R[maxn];

int build(int begin1,int end1,int begin2,int end2) {
    if(begin1 > end1 || begin2 > end2)
		return 0;

    for(int i = begin1;i <= end1;i++) {
        if(mid[i] == post[end2]) {
            L[post[end2]] = build(begin1, i - 1, begin2, begin2 + i - begin1 - 1);
            R[post[end2]] = build(i + 1, end1, begin2 + i - begin1, end2 - 1);
            break;
        }
    }

    return post[end2];
}
                                
void bfs() {
    queue<int> q;
    q.push(post[N]);

    cout << post[N];

    while(!q.empty()) {
        int element = q.front();
        q.pop();

        if(L[element]) {
            cout << " " << L[element];
            q.push(L[element]);
        }

        if(R[element]) {
            cout << " " << R[element];
            q.push(R[element]);
        }
    }
}

int main()
{
    cin >> N;

    for(int i = 1;i <= N;i++)
        cin >> post[i];
    for(int i = 1;i <= N;i++)
        cin >> mid[i];

    build(1,N,1,N);

    bfs();

    return 0;
}
```