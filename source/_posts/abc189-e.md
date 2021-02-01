---
title: 'AtCoder Beginner Contest 189: E - Rotate and Flip'
date: 2021-01-31 21:48:57
tags: dp
categories: ACM
---
## 题目链接
[E - Rotate and Flip](https://atcoder.jp/contests/abc189/tasks/abc189_e)
<!-- more -->

## 思路
如果将原坐标表示为$(x, y)$，则下列操作所起到的作用分别为：
* 顺时针旋转$90$度：$(x, y) \rightarrow (y, -x)$
* 逆时针旋转$90$度：$(x, y) \rightarrow (-y, x)$
* 关于$x = p$对称：$(x, y) \rightarrow (2p - x, y)$
* 关于$y = p$对称：$(x, y) \rightarrow (x, 2p - y)$

然而，对于每次查询，其时间复杂度均为$\scr{O}$($N$)，显然会超时。事实上我们可以使用变量来记录第$i$次操作后新坐标相对于最初的坐标的影响。观察上面四项操作，发现对于坐标的改变主要有以下五点：
* $x$与$y$互换
* $x$改变符号
* $y$改变符号
* $x$加上$2p$
* $y$加上$2p$

因此，我们可以用五个数组来记录第$i$次操作后坐标的变化，分别名为`swap`, `a`, `c`, `b`, `d`。
若此时`swap`为假，则新坐标可表示为
$$(ax + b, cy + d)$$
否则，新坐标为
$$(ay + b, cx + d)$$

相应操作的具体变换步骤如下：
| 操作             | $swap$对应变换           | $a$对应变换        | $c$对应变换        | $b$对应变换               | $d$对应变换              |
| ---------------- | ------------------------ | ------------------ | ------------------ | ------------------------- | ------------------------ |
| 顺时针旋转$90$度 | $swap_i = !swap_{i - 1}$ | $a_i = a_{i - 1}$  | $c_i = -c_{i - 1}$ | $b_i = d_{i - 1}$         | $d_i = -b_{i - 1}$       |
| 逆时针旋转$90$度 | $swap_i = !swap_{i - 1}$ | $a_i = -a_{i - 1}$ | $c_i = c_{i - 1}$  | $b_i = -d_{i - 1}$        | $d_i = b_{i - 1}$        |
| 关于$x = p$对称  | $swap_i = swap_{i - 1}$  | $a_i = -a_{i - 1}$ | $c_i = c_{i - 1}$  | $b_i = 2 * p - b_{i - 1}$ | $d_i = d_{i - 1}$        |
| 关于$y = p$对称  | $swap_i = swap_{i - 1}$  | $a_i = a_{i - 1}$  | $c_i = -c_{i - 1}$ | $b_i = b_{i - 1}$         | $d_i = 2 * p -d_{i - 1}$ |

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
 
void solve() {
    int n, m, q, op, p, x, y;
 
    cin >> n;
 
    vector<pair<int, int>> v(n + 1);
    
    for (int i = 1; i <= n; i++)
        cin >> v[i].first >> v[i].second;
 
    cin >> m;
 
    vector<bool> swap(m + 1);
    vector<int> a(m + 1, 1), c(m + 1, 1);
    vector<long long> b(m + 1, 0LL), d(m + 1, 0LL);
 
    for (int i = 1; i <= m; i++) {
        cin >> op;
        if (op == 3 || op == 4)
            cin >> p;
 
        if (op == 1 || op == 2) {
            swap[i] = !swap[i - 1];
 
            a[i] = c[i - 1];
            c[i] = a[i - 1];
            b[i] = d[i - 1];
            d[i] = b[i - 1];
 
            if (op == 1) {
                c[i] *= -1;
                d[i] *= -1;
            }
            else {
                a[i] *= -1;
                b[i] *= -1;
            }
        }
        else {
            swap[i] = swap[i - 1];
 
            a[i] = a[i - 1];
            b[i] = b[i - 1];
            c[i] = c[i - 1];
            d[i] = d[i - 1];
 
            if (op == 3) {
                a[i] *= -1;
                b[i] = 2 * p - b[i - 1];
            }
            else {
                c[i] *= -1;
                d[i] = 2 * p - d[i - 1];
            }
        }
    }
 
    cin >> q;
    while (q--) {
        cin >> x >> y;
 
        if (!swap[x])
            printf("%lld %lld\n", a[x] * v[y].first + b[x], c[x] * v[y].second + d[x]);
        else
            printf("%lld %lld\n", a[x] * v[y].second + b[x], c[x] * v[y].first + d[x]);
    }
}
 
int main() {
    solve();
 
    return 0;
}
```