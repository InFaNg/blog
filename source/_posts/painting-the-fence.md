---
title: Painting the Fence
date: 2020-08-20 21:17:54
tags: [brute force]
categories: ACM
mathjax: true
---
本文将介绍 Codeforces 上一道暴力求解法的题目，并介绍如何使用前缀优化的方法来降低时间复杂度.
<!-- more -->

## 题目
[Problem - 1132C - Codeforces](https://codeforces.com/problemset/problem/1132/C)

## 分析
如果单纯地只考虑暴力，我们可以使用一个数组来储存篱笆的状态，数组的下标对应篱笆的编号，数组的值对应共有几名油漆工可以粉刷数组下标对应的篱笆. 然后讨论去除所有去除任意两名不同的油漆工的情况，再对数组进行一次遍历，去除数组对应的值为零的篱笆，剩下的篱笆就是可以被粉刷的篱笆. 通过这种方法，虽然完成了题目所要求的问题，但其时间复杂度过高. 假设给出 $q$ 名油漆工，$n$ 个篱笆，其时间复杂度为
$$
T(q, n) = \Theta(\cfrac{q*(q-1)}{2}*n)
$$

上面算法最主要的问题是我们关心了数组的具体的值，事实上，我们只关心最终该篱笆是否能被粉刷，至于其到底是能由一名油漆工还是多名来负责完成无关紧要. 在对去除任意两名不同的油漆工的讨论里，需要用到两次遍历，我们可以在第一次遍历中维护一个数组，该数组负责记录那些只有一名油漆工负责的篱笆的数量，然后在第二次的遍历中，我们去除那些仅由一名油漆工负责且该油漆工未被雇佣的篱笆，求得的结果即为能够粉刷的篱笆数量.

对于这一记录只有一名油漆工负责的篱笆的数组，我们可以引入一个概念：前缀. 即从第一个篱笆到该数组下标的篱笆为止，该数组下标所对应的值记录了共有多少个篱笆仅由一名油漆工负责. 伪代码如下：
```
for i = 1 to q - 1               // 第一次遍历油漆工
    for j = left[i] to right[i]  // left[i], right[i] 分别表示第 i 个油漆工粉刷篱笆的起始位置与终止位置
        count[j]--               // count[i] 表示第 i 个篱笆能有几位油漆工负责

    for j = 1 to n
        if count[j] == 1
            pre[j] = pre[j - 1] + 1 // pre[j] 表示从第 1 个篱笆到第 j 个篱笆，共有多少个篱笆仅有一名油漆工负责
        else
            pre[j] = pre[j - 1]

        if count[j] != 0
            sum++                   // 表示当前共有多少篱笆可被粉刷

    for j = i + 1 to q              // 第二次遍历油漆工
        ans = max(ans, sum - (pre[right[j]] - pre[left[j] - 1]))

    for j = left[i] to right[i]
        count[j]++                  // 恢复第一次遍历所去除的油漆工
```

其时间复杂度为：
$$
\begin{split}
T(q, n) &= \Theta(\sum_{i = 1}^{q - 1}({n + \sum_{j = i + 1}^q{1}})) \\\\
        &= \Theta((q - 1)\*n + \cfrac{q\*(q-1)}{2})
\end{split}
$$

## 代码
具体的 C++ 实现代码如下：
```cpp
#include <iostream>
#include <algorithm>
using namespace std;

int n, q, sum, ans;
int l[5005], r[5005], cnt[5005], pre[5005];

int main() {
    cin >> n >> q;

    for (int i = 1; i <= q; i++) {
        cin >> l[i] >> r[i];

        for (int j = l[i]; j <= r[i]; j++)
            cnt[j]++;
    }

    for (int i = 1; i <= q - 1; i++) {
        for (int j = l[i]; j <= r[i]; j++)
            cnt[j]--;

        sum = 0;

        for (int j = 1; j <= n; j++) {
            pre[j] = cnt[j] == 1 ? pre[j - 1] + 1 : pre[j - 1];

            if (cnt[j])
                sum++;
        }

        for (int j = i + 1; j <= q; j++)
            ans = max(ans, sum - pre[r[j]] + pre[l[j] - 1]);

        for (int j = l[i]; j <= r[i]; j++)
            cnt[j]++;
    }

    cout << ans << endl;

    return 0;
}
```