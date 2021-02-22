---
title: 'AtCoder Beginner Contest 192: D - Base n'
date: 2021-02-21 20:50:12
tags: [binary search, math]
categories: ACM
---
## 题目链接
[D - Base n](https://atcoder.jp/contests/abc192/tasks/abc192_d)
<!-- more -->

## 思路
首先考虑特殊情况，即字符串$X$的长度为$1$时，若将$X$看作是$n$($n \geq d + 1$)进制下的数，则$X$只有一种可能的取值，即十进制下的$X$本身，即在这种情况下，我们进行特判即可。

当字符串$len(X) \geq 2$时，由于题目限制了$X$不含前导`0`，我们不难得出对于每一个不同的进制$n$($n \geq d + 1$)，$X$都有一个不同的取值与之对应。因此我们需要$n = d + 1$开始判断，每次令$n := n + 1$，直到找到一种进制$n$，使得在$n$进制下有$X \gt M$成立，此时停止迭代。然而在极端条件下，例如当$X = 10$，$M = 10^{18}$时，$n$要从$n = 2$开始，直到$n = 10^{18} + 1$才停止迭代，显然会超时。于是我们考虑二分：二分的左边界为`d + 1`，右边界为$10_18$，在这一范围内寻找符合题意的$n$的最大值。

不过这么做，新的问题又出现了。考虑极端情况，$X$的值为$60$个$9$，$n = 10_18$，此时我们将$X$转换为十进制下的值时肯定会出现溢出的问题。一种思路是使用Python，避免溢出问题。还有一种巧妙的方法：由于$M$的取值范围是$1 \leq M \leq 10^{18}$，我们可以使用`C++`中的`__int128_t`来进行运算，虽然`__int128_t`也无法存储上述极端情况下这么大的数字，不过我们可以运算到一半，发现它的值比$M$的值大时，退出即可。

## 代码
`C++`代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

string x;
ll m;

bool check(ll base) {
    __int128_t res = 0;

    for (const auto &ch : x) {
        res = res * base + ch - '0';

        if (res > m)
            return false;
    }

    return true;
}

void solve() {
    cin >> x >> m;

    int d = 0;
    for (const auto &ch : x)
        d = max(d, ch - '0');

    if (x.size() == 1) {
        if (d <= m)
            cout << 1;
        else
            cout << 0;

        return;
    }

    ll l = d + 1, r = ll(1e18) + 1;
    while (l < r) {
        ll mid = (l + r) / 2;

        if (check(mid))
            l = mid + 1;
        else
            r = mid;
    }

    cout << l - 1 - d;
}

int main() {
    solve();

    return 0;
}
```

`Python`代码
```py
x = input()
m = int(input())

def calc(n_base):
    res = 0

    for e in x:
        res *= n_base
        res += int(e)

    return res

d = -1
for e in x:
    d = max(d, int(e))

if len(x) == 1:
    if d <= m:
        print(1)
    else:
        print(0)
else:
    l, r = d + 1, int(1e18) + 1

    while l < r:
        mid = int((l + r) / 2)

        if (calc(mid) <= m):
            l = mid + 1
        else:
            r = mid

    print(l - 1 - d)
```