---
title: Codeforces 1475B - New Year's Number
date: 2021-01-27 13:23:41
tags: [brute force, dp, math]
categories: ACM
---
## 题目链接
[Problem - 1475B - Codeforces](https://codeforces.com/problemset/problem/1475/B)

## 题目大意
给出一个整数$n$($1 \leq n \leq 10^6$)，判断其是否能表示为$2020$和$2021$的和的形式。
<!-- more -->

## 思路
如果有$n = x \cdot 2020 + y \cdot 2021$，则必有$(x + y) \cdot 2020 \le n \le (x + y) \cdot 2021$。因此，我们可以根据是否有$\lfloor \frac{n}{2020} \rfloor \cdot 2020 \le n \le \lfloor \frac{n}{2020} \rfloor \cdot 2021$来判断其是否能由$2020$和$2021$的和来进行表示。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

void solve() {
    int n;
    cin >> n;
    if (n >= (n / 2020) * 2020 && n <= (n / 2020) * 2021)
        puts("YES");
    else
        puts("NO");
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();
    return 0;
}
```