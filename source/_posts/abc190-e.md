---
title: 'AtCoder Beginner Contest 190: E - Magical Ornament'
date: 2021-02-08 18:56:43
tags: [bitbits, dp, graphs]
categories: ACM
---
## 题目链接
[E - Magical Ornament](https://atcoder.jp/contests/abc190/tasks/abc190_e)
<!-- more -->

## 思路
可以把题目给出的能够相邻的数字对看成图中的边，使用BFS分别找出$C_1, C_2, \dots, C_K$它们两两连接所需要的最小代价。
接下来使用包含当前状态和最后一次操作的二维数组，从前往后进行动态规划。具体表达式如下：
$$dp\[bit\]\[last\] = min(dp\[pre\\_bit\]\[pre\\_last\])$$
其中`bit`包含$k$个二进制的`0`或`1`，表示当前已经选择了的数字，`last`表示上一次选择的数字。`pre_bit`为将`bit`中的任意一个`1`置为`0`，其余数组均不变的二进制数，`pre_last`则为`pre_bit`中任意一个值为`1`的位所表示的数字。

## 代码
```cpp
#include <bits/stdc++.h>
using namespace std;

const int INF = int(1e6);

void solve() {
    int n, m, k;
    cin >> n >> m;

    vector<vector<int>> adj(n + 1);

    for (int i = 0, a, b; i < m; i++) {
        cin >> a >> b;

        adj[a].emplace_back(b);
        adj[b].emplace_back(a);
    }

    cin >> k;
    vector<int> arr(k + 1);

    for (int i = 1; i <= k; i++)
        cin >> arr[i];

    vector<vector<int>> bfs_result(k + 1, vector<int>(k + 1, INF));
    for (int i = 1; i <= k; i++) {
        vector<int> dist(n + 1, INF);
        dist[arr[i]] = 0;

        queue<int> q;
        q.push(arr[i]);

        while (!q.empty()) {
            int e = q.front();
            q.pop();

            for (const auto next : adj[e])
                if (dist[next] > dist[e] + 1) {
                    dist[next] = dist[e] + 1;

                    q.push(next);
                }
        }

        for (int j = 1; j <= k; j++)
            bfs_result[i][j] = dist[arr[j]];
    }

    vector<vector<int>> dp(1 << k, vector<int>(k, INF));
    for (int i = 0; i < k; i++)
        dp[1 << i][i] = 1;

    for (int bit = 1; bit < (1 << k); bit++)
        for (int i = 0; i < k; i++)
            if (bit & (1 << i)) {
                int pre_bit = bit ^ (1 << i);

                for (int j = 0; j < k; j++)
                    if (pre_bit & (1 << j))
                        dp[bit][i] = min(dp[bit][i], dp[pre_bit][j] + bfs_result[j + 1][i + 1]);
            }

    int ans = *min_element(dp.back().begin(), dp.back().end());

    if (ans == INF)
        ans = -1;

    cout << ans;
}

int main() {
    solve();

    return 0;
}
```