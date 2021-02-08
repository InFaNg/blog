---
title: 'AtCoder Beginner Contest 190: D - Staircase Sequences'
date: 2021-02-08 18:37:41
tags: [math, number]
categories: ACM
---
## 题目链接
[D - Staircase Sequences](https://atcoder.jp/contests/abc190/tasks/abc190_d)
<!-- more -->

## 题目大意
输入一个$N$，问有几种公差为$1$且和为$N$的等差数列。

## 思路
对于一个正整数$N$，总可以将他表示成$a_{i-k} + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k}$($k \ge 0$)的形式，即
$$
\begin{align}
N & = a_{i-k} + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k} \\\\
& = (2k + 1) * a_i \\\\
\end{align}
$$

不难发现，每有一个$N$的奇数因子，就有一对$k$和$a_i$满足上述等式。
其中，若$a_{i-k} > 0$，上式又可表示成
$$
\begin{align}
N & = a_{i-k} + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k} \\\\
& = -(a_{i-k}-1) + \dots + 0 + (a_{i-k}-1) + a_{i-k} + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k} \\\\
\end{align}
$$

反之，若$a_{i-k} \le 0$，则可表示成
$$
\begin{align}
N & = a_{i-k} + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k} \\\\
& = (-a_{i-k}+1) + \dots + a_{i-1} + a_i + a_{i+1} + \dots + a_{i+k} \\\\
\end{align}
$$
也就是说，对于每一个$N$的奇数因子，都有两个符合题意的等差数列与之对应，我们只需找出所有$N$的奇数因子，将其乘以$2$即为答案。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
void solve() {
    ll n;
    cin >> n;
 
    int ans = 0;
 
    for (ll i = 1; i * i <= n; i++)
        if (n % i == 0) {
            if (i % 2 == 1)
                ans += 2;
            if (n / i % 2 == 1 && n / i != i)
                ans += 2;
        }
 
    cout << ans;
}
 
int main() {
    solve();
 
    return 0;
}
```