---
title: Codeforces 1475F - Unusual Matrix
date: 2021-01-27 21:00:33
tags: constructive algorithms
categories: ACM
---
## 题目链接
[Problem - 1475F - Codeforces](https://codeforces.com/problemset/problem/1475/F)

## 题目大意
给出仅有$0$和$1$组成的矩阵$a$和矩阵$b$，它们的大小为$n \times n$。可以对矩阵$a$的任意一行或任意一列做[逻辑异或](https://zh.wikipedia.org/wiki/%E9%80%BB%E8%BE%91%E5%BC%82%E6%88%96)运算任意次（也可以是0次），问能否将矩阵$a$变换到矩阵$b$。
<!-- more -->

## 思路
我们的大致思路是：每一行或每一列至多进行一次操作，因为对同一行或同一列做两次以上的操作是没有意义的。此外，由于矩阵大小$1 \leq n \leq 1000$，我们可以尝试进行遍历所有行和列，看看能否找到可行的操作。

首先我们对矩阵$a$的第一行进行遍历：如果元素$a_{i, j} \neq b_{i, j}$，则对其第$j$列进行操作。该操作完成后，我们确保了矩阵$a$的第一行与矩阵$b$一致，因此我们无需再对矩阵$a$中的任何一列进行操作，因为这会破坏它们第一行的一致性。同理，我们也无需对矩阵$a$中的第一行进行操作。接下来，我们直接从第二行开始遍历，即$2 \leq i \leq n$，如果对于$\forall {1 \leq j \leq n}$，均有$a_{i, j} \neq b_{i, j}$或$a_{i, j} = b_{i, j}$，则说明存在操作使得矩阵$a$变换为矩阵$b$，反之不存在。


## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

int n;

bool check(vector<vector<int>> a, const vector<vector<int>> &b) {
    for (int j = 0; j < n; j++) 
        if (a[0][j] != b[0][j])
            for (int i = 0; i < n; i++)
                a[i][j] ^= 1;

    for (int i = 1; i < n; i++) {
        bool flag = (a[i][0] == b[i][0]);

        for (int j = 1; j < n; j++)
            if ((a[i][j] == b[i][j]) != flag)
                return false;
    }

    return true;
}

void solve() {
    cin >> n;
    vector<vector<int>> a, b;

    string s;
    for (int i = 0; i < n; i++) {
        cin >> s;
        vector<int> tmp;
        for (auto &e : s)
            tmp.emplace_back(e - '0');
        a.emplace_back(tmp);
    }

    for (int i = 0; i < n; i++) {
        cin >> s;
        vector<int> tmp;
        for (auto &e : s)
            tmp.emplace_back(e - '0');
        b.emplace_back(tmp);
    }

    if (check(a, b))
        cout << "YES" << endl;
    else
        cout << "NO" << endl;
}

int main() {
    int t;
    cin >> t;
    while (t--)
        solve();

    return 0;
}
```