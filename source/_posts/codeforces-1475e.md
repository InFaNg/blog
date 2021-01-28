---
title: Codeforces 1475E - Advertising Agency
date: 2021-01-27 15:14:48
tags: [combinatorics, implementation, math, sortings]
categories: ACM
---
## 题目链接
[Problem - 1475E - Codeforces](https://codeforces.com/problemset/problem/1475/E)

## 题目大意
给出两个整数$n$和$k$($1 \le k \le n \le 1000$)，之后给出$n$个整数$a_1, a_2, \ldots a_n$。选出其中的$k$个整数使它们的和最大。最多有几种选法(对$10^9 + 7$求模)？如果不存在任何一种选法，输出`-1`。
<!-- more -->

## 思路
问题的关键就在于假设大小为$x$的整数一共有$y$个，此时已经选择了$m$个数，还需要选择$k - m$个数，我们需要求的就是$C_y^{k - m}$。我们可以使用一张map来记录每个数字出现的次数，之后根据map的键从大到小进行遍历，如果此时map的值为小于$k$,就令$k := k - it\-\>second$，否则就计算$C_k^{it\-\>second}$并退出循环。如果循环结束了还没找到答案，就输出`-1`。

接下来的重点就是求$C_k^{it\-\>second}$，由于该数值较大，且题目要求给出模意义下的答案，在这里需要使用乘法逆元、杨辉三角或利用等式$\binom n k = \binom {n-1} {k} + \binom {n-1} {k-1}$结合动态规划的方法来计算组合数。这里我们选择乘法逆元的方法，如果对于乘法逆元还不熟悉，可以参考[乘法逆元 - OI Wiki](https://oi-wiki.org/math/inverse/)。如果使用快速幂法求乘法逆元，答案可表示为：
$$C_k^{(it\-\>second)} \pmod {1e9 + 7} = \prod_{i = 1}^{k - (it\-\>second)} i^{1e9 + 7 - 2} \pmod {1e9 + 7}$$ 

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

const int MOD = 1e9 + 7;
int n, k, tmp;

int binpow(long long a, int b) {
  int ans = 1;
  a = a % MOD;
  for (; b; b >>= 1) {
    if (b & 1)
        ans = (a * ans) % MOD;
    a = (a * a) % MOD;
  }
  return ans;
}

ll c(int n, int m) {
    if (n == m)
        return 1;

    ll ans = 1;

    for (int i = m + 1; i <= n; i++)
        ans = ans * i % MOD;
    for (int i = 1; i <= n - m; i++)
        ans = ans * binpow(i, MOD - 2) % MOD;

    return ans;
}

void solve() {
    map<int, int> mp;

    cin >> n >> k;
    for (int i = 0; i < n; i++) {
        cin >> tmp;
        mp[tmp]++;
    }

    for (auto it = mp.rbegin(); it != mp.rend(); it++) {
        if (it->second < k)
            k -= it->second;

        else {
            cout << c(it->second, k) << endl;
            return;
        }
    }

    cout << -1 << endl;
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();

    return 0;
}
```