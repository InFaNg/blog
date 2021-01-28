---
title: Codeforces 1475A - Odd Divisor
date: 2021-01-27 11:09:59
tags: [bitmasks, math, number theory]
categories: ACM
---
## 题目链接
[Problem - 1475A - Codeforces](https://codeforces.com/problemset/problem/1475/A)

## 题目大意
给出一个整数$n$($2 \le n \le 10^{14}$)，判断$n$能否被一个大于1的奇数整除。
<!-- more -->

## 思路
首先考虑下算法的时间复杂度。如果对输入的每个整数进行质因子分解，如果能找到奇数的质因子则输出`YES`，反之输出`NO`，这么做的话其时间复杂度为$\mathcal{O}(\log n)$。显然对于题目中的测试用例数量$t$($1 \le t \le 10^4$)会出现超时。我们可以换下思路：怎么样的数只有偶数的质因子，且没有奇数的质因子。不难发现，如果一个数是$2$的$n$次幂，则符合上述条件，输出`NO`即可，反之输出`YES`。

接下来问题就转换成如何判断一个数是否是$2$的$n$次幂了。一个可行的思路是将所有的$x$($x = 2^n$且$2 \le x \le 2^{14}$)放入一个set中，之后判断set中是否存在输入的数字即可。不过还有一个更简单的思路：判断一个数以二进制表示时是否只有一个$1$，且这个数不为$1$本身。如果是，则这个数不存在奇数的质因子。由于题目规定了输入的$n \ge 2$，因此无需对输入的数是否为$1$进行特判，直接根据其二进制表示时是否只有一个1来进行判断即可。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

void solve() {
    ll n;
    cin >> n;
    
    if (__builtin_popcountll(n) == 1)
        cout << "NO" << endl;
    else
        cout << "YES" << endl;
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();
    
    return 0;
}
```