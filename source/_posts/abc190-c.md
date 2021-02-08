---
title: 'AtCoder Beginner Contest 190: C - Bowls and Dishes'
date: 2021-02-08 18:21:23
tags: bitmasks
categories: ACM
---
## 题目链接
[C - Bowls and Dishes](https://atcoder.jp/contests/abc190/tasks/abc190_c)
<!-- more -->

## 题目大意
有编号为$N$的$1, 2, \dots, N$个盘子，和$1, 2, \dots, M$共$M$个条件。
当盘子$A_i$和$B_i$均有（一个或多个）球时，条件$i$被视为满足。
有$K$个编号为$1, 2, \dots, K$的人，每个人都可以放一只球在盘子$C_i$或$D_i$上。
最多有多少种条件能得到满足？

## 思路
不难发现$K$个人每个人有两种选择，所有可能的选择一共有$K!$种。对于题目给出的数据范围$1 ≤ K ≤ 16$，显然不会超时。可以选择位运算模拟每个人做出不同选择的情况，将$1$记为将球放在盘子$C_i$上，将$0$记为将球放在盘子$D_i$上。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
 
void solve() {
    int n, m, k;
    cin >> n >> m;
 
    vector<pair<int, int>> v1(m);
    for (auto &[x, y] : v1)
        cin >> x >> y;
 
    cin >> k;
    vector<pair<int, int>> v2(k);
    for (auto &[x, y] : v2)
        cin >> x >> y;
 
    int ans = 0;
    for (int i = 0; i < (1 << k); i++) {
        vector<bool> tmp(n + 1);
 
        for (int j = 0; j < k; j++)
            if (i & (1 << j))
                tmp[v2[j].first] = true;
            else
                tmp[v2[j].second] = true;
 
        int cnt = 0;
        for (const auto &[x, y] : v1)
            if (tmp[x] && tmp[y])
                cnt++;
 
        ans = max(ans, cnt);
    }
 
    cout << ans;
}
 
int main() {
    solve();
 
    return 0;
}
```