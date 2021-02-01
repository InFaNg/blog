---
title: 'AtCoder Beginner Contest 189: F - Sugoroku2'
date: 2021-01-31 22:50:03
tags: [dp]
categories: ACM
---
## 题目链接
[F - Sugoroku2](https://atcoder.jp/contests/abc189/tasks/abc189_f)
<!-- more -->

## 思路
对于这一类求期望的题目，一般是使用[概率DP](https://oi-wiki.org/dp/probability/)加上倒推的方式。但这一道题目还有回到零点这一特殊的情况，需要一些额外的讨论，才能求得最终解。

首先我们不考虑根本走不到终点这一特殊情况，即假设能够走到终点的可能性始终存在。
假设从$i$走到终点所需要的步数为$f(i)$，则可由下式表示为：
$$
f(i)=
\begin{cases}
    f(0) & \text{,$i$ $\in$ {$A_i$, $\ldots$, $A_K$},}  \\\\
    0 & , i \ge N, \\\\
    \dfrac{1}{M}(f(i+1)+\ldots+f(i+M))+1 & \text{,其他.}
\end{cases}
$$

设$f(0)$为$x$，可得等式
$$x = a * x + b$$
即
$$x = \frac{b}{1 - a}$$

因此，从`N - 1`点到`0`点进行dp，期间维护当前点的前`M`项`f(0)`的系数和常数项系数即可。

最后对于不可能走到终点的情况进行特判。易证如果无法走到终点，$a$的系数为1。但由于double型运算精度问题，需使用`abs(1 - a) < 1e-6`进行判断。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m, k;
double a, b;
set<int> st;

void solve() {
    cin >> n >> m >> k;
    
    for (int i = 0, tmp; i < k; i++) {
        cin >> tmp;
        
        st.insert(tmp);
    }
    
    vector<pair<double, double>> dp(n + 1);
    pair<double, double> sum;
    for (int i = n - 1; i >= 0; i--) {
        if (st.count(i))
            dp[i] = make_pair(1, 0);
        else {
            dp[i].first = sum.first / m;
            dp[i].second = sum.second / m + 1;
        }
        sum.first += dp[i].first;
        sum.second += dp[i].second;
        
        if (i + m < n) {
            sum.first -= dp[i + m].first;
            sum.second -= dp[i + m].second;
        }
    }
    
    double a = dp[0].first;
    double b = dp[0].second;
    
    if (abs(1 - a) < 1e-6)
        cout << -1 << endl;
    else
        cout << setiosflags(ios::fixed) << setprecision(4) << b / (1 - a) << endl;
}

int main() {
    solve();
    
    return 0;
}
```