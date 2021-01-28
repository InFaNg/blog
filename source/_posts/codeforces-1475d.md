---
title: Codeforces 1475D - Cleaning the Phone
date: 2021-01-27 14:18:47
tags: [binary search, dp, greedy, number theory, sortings, two pointers]
categories: ACM
---
## 题目链接
[Problem - 1475D - Codeforces](https://codeforces.com/problemset/problem/1475/D)

## 题目大意
Polycarp的手机里有$n$个应用，第$i$个应用占用$a_i$个单元的存储空间，具有$b_i$($b_i = 1$或$b_i = 2$)的舒适度，现在Polycarp需要释放至少$m$个单元的存储空间。求Polycarp最少需要损失多少的舒适度，或输出`-1`表示不存在能够释放至少$m$个单元的存储空间的方案。
<!-- more -->

## 思路
首先考虑贪心性质：将手机里的应用分为两类，分别是舒适度为$1$和舒适度为$2$的，并将它们所占用的存储空间按从大到小的顺序进行排序。之后删除应用时，优先删除排在前面（也就是占用存储空间较大）的应用。至于到底要删除多少个舒适度为$1$的应用和多少个舒适度为$2$的应用，如果直接根据哪个应用的$\frac{存储空间}{舒适度}$更大就选择删除哪个的方法来进行选择是错误的。例如下面这组数据：
```
5 10
2 3 2 3 2
1 2 1 2 1
```
如果优先选择删除$\frac{存储空间}{舒适度}$更大的应用，显然$\frac{2}{1} \ge \frac{3}{2} $，但是这么做释放了$2 + 2 + 2 + 3 + 3 = 11$个单元的存储空间，损失了$1 + 1 + 1 + 2 + 2 = 7$的舒适度，而事实我们可以选择删除$2$个占$3$个单元存储空间的应用和$2$个占$2$个单元存储空间的应用。这么做只需要损失$2 + 2 + 1 + 1 = 6$的舒适度，显然优于前者。因此，根据$\frac{存储空间}{舒适度}$来进行判断是不合适的。

于是我们只好考虑暴力解法。直接遍历需要进行大量的求和运算，在这里我们可以先通过前缀和优化，来降低时间复杂度。之后通过二次遍历，找到舒适度损失最小的方案。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;

int n, m;
vector<int> a(2e5 + 5), b(2e5 + 5);

void solve() {
    cin >> n >> m;

    for (int i = 1; i <= n; i++)
        cin >> a[i];
    for (int i = 1; i <= n; i++)
        cin >> b[i];

    vector<int> app1, app2;
    for (int i = 1; i <= n; i++)
        if (b[i] == 1)
            app1.emplace_back(a[i]);
        else
            app2.emplace_back(a[i]);

    sort(app1.rbegin(), app1.rend());
    sort(app2.rbegin(), app2.rend());

    vector<ll> app1_prefix, app2_prefix;
    app1_prefix.emplace_back(0);
    app2_prefix.emplace_back(0);
    ll x = 0, y = 0;
    for (auto &e : app1) {
        x += e;
        app1_prefix.emplace_back(x);
    }
    for (auto &e : app2) {
        y += e;
        app2_prefix.emplace_back(y);
    }

    int ans = 2e6;
    for (int i = app1_prefix.size() - 1; i >= 0; i--) {
        if (!app2_prefix.empty() && app1_prefix[i] + app2_prefix.back() < m)
            break;

        for (int j = app2_prefix.size() - 1; j >= 0 &&
            app1_prefix[i] + app2_prefix[j] >= m; j--)
            ans = min(ans, i + 2 * j);
    }

    if (ans == 2e6)
        cout << -1 << endl;
    else
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