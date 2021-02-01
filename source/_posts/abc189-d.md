---
title: 'AtCoder Beginner Contest 189: D - Logical Expression'
date: 2021-01-31 21:07:37
tags: [dp, bitmasks]
categories: ACM
---
## 题目链接
[D - Logical Expression](https://atcoder.jp/contests/abc189/tasks/abc189_d)
<!-- more -->

## 思路
如果$S_i$为`AND`，此时对于$y_i=y_{i-1} \land x_i$，要使得其值为`True`的充要条件为$y_{i-1}$为`True`且$x_i$为`True`。如果$S_i$为`OR`，此时对于$y_i=y_{i-1} \lor x_i$，要使得其值为`True`的充要条件为$y_{i-1}$与$x_i$至少有一个为`True`。

因此以$dp_i$表示能够使$y_i$为`True`的变量组$(x_0,\ldots,x_i)$的数量，我们可以写出状态转移表达式：
$$
dp_i = \\
\begin{cases}
1, & i = 0 \\\\
dp_{i - 1} & \text{if $S_i$ is AND} \\\\
dp_{i - 1} + 2^i & \text{if $S_i$ is OR} \\\\
\end{cases}
$$

上面对于$S_i$为`OR`的讨论中，$dp_{i - 1}$对应$x_i$为`False`且$y_{i-1}$为`True`的情况，而$2^i$对应了$x_i$为`True`，$y_{i-1}$为`True`的共$2^i - 1$种情况以及$x_i$为`True`，$y_{i-1}$为`False`的$1$种情况，其和为$2^i$。合起来恰好等价于$y_{i-1}$与$x_i$至少有一个为True。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

int n;
vector<string> v(65);
vector<long long> dp(65, 1LL);

void solve() {
    cin >> n;
    
    for (int i = 1; i <= n; i++)
        cin >> v[i];
        
    for (int i = 1; i <= n; i++) {
        if (v[i] == "AND")
            dp[i] = dp[i - 1];
        else
            dp[i] = dp[i - 1] + (1LL << i);
    }
    
    cout << dp[n] << endl;
}

int main() {
    solve();
    
    return 0;
}
```