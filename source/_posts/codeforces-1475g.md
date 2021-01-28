---
title: Codeforces 1475G - Strange Beauty
date: 2021-01-27 21:42:22
tags: [dp, math, number, theory]
categories: ACM
---
## 题目链接
[Problem - 1475G - Codeforces](https://codeforces.com/problemset/problem/1475/G)

## 题目大意
给出一串长度为$n$的数列，问至少需要删除几个元素，才能使得对于任意$a_i, a_j$($i \ne j$)，均有$a_i$能被$a_j$整除或$a_j$能被$a_j$整除。
<!-- more -->

## 思路
从反面来思考问题：数列中最多有几个元素能够满足上述性质。这里使用动态规划，用`dp[i]`记录前$i$个元素中所能满足上述性质的元素个数。具体的dp表达式为($cnt(x)$表示值为$i$的元素个数)：
$$dp(x) = cnt(x) + \max \limits_{y = 1, x mod y = 0}^{x-1} dp(y)$$

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

const int MAXN = 2e5 + 5;

void solve() {
    int n, tmp;
    vector<int> cnt(MAXN), dp(MAXN);

    cin >> n;

    for (int i = 0; i < n; i++) {
        cin >> tmp;
        cnt[tmp]++;
    }
    
    for (int i = 1; i < MAXN; i++) {
        dp[i] += cnt[i];
        
        for (int j = 2 * i; j < MAXN; j += i)
            dp[j] = max(dp[j], dp[i]);
    }
    
    
    cout << n - *max_element(dp.begin(), dp.end()) << endl;
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();

    return 0;
}
```