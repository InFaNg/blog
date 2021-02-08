---
title: 'AtCoder Beginner Contest 190: F - Shift and Inversions'
date: 2021-02-08 19:59:17
tags: [constructive algorithms, sortings, math]
categories: ACM
---
## 题目链接
[F - Shift and Inversions](https://atcoder.jp/contests/abc190/tasks/abc190_f)
<!-- more -->

## 思路
求数列逆序对的最佳方法为使用树状数组，其时间复杂度为$O(N \log N)$。然而如果在本题中对每一次变化都去求一次逆序对，问题的时间复杂度会变成$O(N^2 \log N)$，显然会超时。于是我们尝试找出每次变化的规律，尝试递推：当位于数列第一位的数字$a_i$被移动到数列尾部时，有$a_i + 1, a_i + 2, \dots, N - 1$共$N - a_i - 1$个比$a_i$大的数到了它的前面，即产生了$N - a_i - 1$个逆序对。此外，在变化前有$0, 1, \dots, a_i - 1$共$a_i$个比$a_i$小的数在它后面，构成了$a_i$个逆序对，现在这些逆序对随着这次移动被消除。

综上，每次移动的操作会产生$N - a_i - 1$个逆序对，消除$a_i$个逆序对。因此我们可以对输入的内容使用树状数组求逆序对，之后对其进行递推，时间复杂度为$O(N \log N + N) = O(N \log N)$。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;
using ll = long long;
 
int n;
int num[int(3e5) + 5];
 
inline int lowbit(int x) {
    return x & (-x);
}
 
void add(int x, int k) {
    for (; k <= n; k += lowbit(k))
        num[k] += x;
}
 
int sum(int x) {
    int ans = 0;
 
    for (; x; x -= lowbit(x))
        ans += num[x];
 
    return ans;
}
 
ll calc(const vector<int> &v) {
    vector<pair<int, int>> tmp(n + 1);
 
    for (int i = 1; i <= n; i++) {
        tmp[i].first = v[i];
        tmp[i].second = i;
    }
 
    sort(tmp.begin(), tmp.end());
 
    vector<int> rank(n + 1);
 
    for (int i = 1; i <= n; i++)
        rank[tmp[i].second] = i;
 
    ll ans = 0;
 
    for (int i = n; i >= 1; i--) {
        ans += sum(rank[i] - 1);
 
        add(1, rank[i]);
    }
 
    return ans;
}
 
void solve() {
    cin >> n;
 
    vector<int> v(n + 1);
 
    for (int i = 1; i <= n; i++)
        cin >> v[i];
 
    ll ans = calc(v);
 
    cout << ans << '\n';
 
    for (int i = 1; i < n; i++)
        cout << (ans = (ans + n - 2 * v[i] - 1)) << '\n';
}
 
int main() {
    solve();
 
    return 0;
}
```