---
title: Codeforces 1475C - Ball in Berland
date: 2021-01-27 13:35:20
tags: [combinatorics, dfs and similar, graphs, hashing, math]
categories: ACM
---
## 题目链接
[Problem - 1475C - Codeforces](https://codeforces.com/problemset/problem/1475/C)

## 题目大意
毕业典礼上的舞会由若干对男女组成。已知共有$a$名男生和$b$名女生想要参加以及$k$对男女（不存在完全相同的两对）。要求在这几对男女中选出两对，使得没有人同时出现在两对之中，共有多少种取法？
<!-- more -->

## 思路
可以先假设所有的取法都不冲突，一共有$C_k^i$种，再减去有一名男生同时出现在两对之中和有一名女生同时出现在两对之中的取法（即不符合题意的种类）。由于题目规定了给出的$k$对男女不存在完全相同的两对，因此我们无需担心重复减去某一对的问题。可以用两个map分别为`mpa`和`mpb`来记录第`i`个男生和第`i`个女生在给出的$k$对男女中出现的次数，然后遍历两个map，减去重复出现的取法即可得到答案。
使用数学公式可表示为：
$$C_k^2 - \sum_{i = 1}^a C_{mpa[i]}^2 - \sum_{i = 1}^b C_{mpb[i]}^2$$

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const int MAXN = 2e5 + 5;

ll cn2(ll x) {
    if (x == 2)
        return 1;
    else if (x <= 1)
        return 0;
    return x * (x - 1) / 2;
}

void solve() {
    int a, b, k, ai[MAXN], bi[MAXN];
    cin >> a >> b >> k;
    for (int i = 1; i <= k; i++)
        cin >> ai[i];
    for (int i = 1; i <= k; i++)
        cin >> bi[i];

    map<int, int> mpa, mpb;

    for (int i = 1; i <= k; i++) {
        mpa[ai[i]]++;
        mpb[bi[i]]++;
    }

    ll ans = cn2(k);
    for (int i = 1; i <= a; i++)
        ans -= cn2(mpa[i]);

    for (int i = 1; i <= b; i++)
        ans -= cn2(mpb[i]);

    cout << ans << endl;
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```