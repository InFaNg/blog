---
title: Igor In the Museum
date: 2020-08-20 14:35:06
tags: [graphs, DFS]
categories: ACM
mathjax: true
---
本文将介绍 Codeforces 上一道图论的题目，并使用 Depth-First Search (DFS, 深度优先搜索) 算法进行解决.
<!-- more -->

## 题目
Igor is in the museum and he wants to see as many pictures as possible.

Museum can be represented as a rectangular field of n × m cells. Each cell is either empty or impassable. Empty cells are marked with '.', impassable cells are marked with '*'. Every two adjacent cells of different types (one empty and one impassable) are divided by a wall containing one picture.

At the beginning Igor is in some empty cell. At every moment he can move to any empty cell that share a side with the current one.

For several starting positions you should calculate the maximum number of pictures that Igor can see. Igor is able to see the picture only if he is in the cell adjacent to the wall with this picture. Igor have a lot of time, so he will examine every picture he can see.

### Input
First line of the input contains three integers $n$, $m$ and $k (3 ≤ n, m ≤ 1000, 1 ≤ k ≤ min(n·m, 100 000))$ — the museum dimensions and the number of starting positions to process.

Each of the next $n$ lines contains $m$ symbols '.', '*' — the description of the museum. It is guaranteed that all border cells are impassable, so Igor can't go out from the museum.

Each of the last $k$ lines contains two integers $x$ and $y (1 ≤ x ≤ n, 1 ≤ y ≤ m)$ — the row and the column of one of Igor's starting positions respectively. Rows are numbered from top to bottom, columns — from left to right. It is guaranteed that all starting positions are empty cells.

### Output
Print $k$ integers — the maximum number of pictures, that Igor can see if he starts in corresponding position.

### 题目链接
[Problem - 598D - Codeforces](https://codeforces.com/problemset/problem/598/D)

## 分析
最初我的想法很简单，只需判断对于给定的起始位置，其上下左右四个方向是墙、空地还是超出边界的情况. 如果是墙，则答案 + 1，如果是空地，就递归进行搜索，如果是超出了边界，那就什么也不做. 具体代码如下：
```cpp
#include <iostream>
#include <cstring>
using namespace std;

const int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1};
int n, m, k, x, y, ans;
char matrix[1005][1005];
bool vis[1005][1005];

void dfs(int x, int y) {
    vis[x][y] = true;

    for (int i = 0; i < 4; i++) {
        int xx = x + dir[i][0];
        int yy = y + dir[i][1];

        if (vis[xx][yy] || matrix[xx][yy] == 0) continue;
        if (matrix[xx][yy] == '.') dfs(xx, yy);
        else ans++;
    }
}

int main() {
    cin >> n >> m >> k;

    for (int i = 1; i <= n; i++)
        for (int j = 1; j <= m; j++)
            cin >> matrix[i][j];

    for (int i = 0; i < k; i++) {
        memset(vis, false, sizeof(vis));
        ans = 0;

        cin >> x >> y;

        dfs(x, y);

        cout << ans << endl;
    }

    return 0;
}
```

这么做好像也没有什么问题，但是在 Codeforces 的中会出现测试数据超时的情况，后来我又改用了 BFS 算法，结果也是超时. 看来，搜索部分的算法已经比较难再进行优化了，于是我们换一个思路，能否通过缓存搜索结果的方式来加快程序运行速度呢？显然是可以的. 我们可以将由墙围城的面积看作是一块封闭空间，题目中的 Igor 无论从封闭空间中的哪一块区域开始，他所能看到的画的数量都是相等的. 因此，我们很容易想到缓存每一块封闭空间内所能看到的画的数量. 这样，当我们查询一块封闭空间内已缓存的格子时，时间复杂度仅为 $\Theta(1)$.

如下图所示，图中 `*` 代表该位置有墙，数字则代表从这里开始最多可以看到几幅画.
![igor_in_the_museum](/images/igor_in_the_museum.png)

## 代码
最终 AC 的代码如下：
```cpp
#include <cstdio>

char s[1005][1005];
int v[1005][1005], ans[1000500], pos;
const int dir[4][2] = {0, 1, 1, 0, -1, 0, 0, -1};

void dfs(int x, int y) {
    v[x][y] = pos;

    for (int i = 0; i < 4; i++) {
        int xx = x + dir[i][0];
        int yy = y + dir[i][1];

        if (v[xx][yy] || s[xx][yy] == 0) continue;

        if (s[xx][yy] == '.') dfs(xx, yy);
        else ans[pos]++;
    }
}

int main() {
    int n, m, k;
    scanf("%d %d %d", &n, &m, &k);

    for (int i = 1; i <= n; i++)
        scanf("%s", s[i] + 1);

    for (int i = 0; i < k; i++) {
        int x, y;

        scanf("%d %d", &x, &y);

        if (!v[x][y]) {
            pos++;
            dfs(x, y);
        }

        printf("%d\n", ans[v[x][y]]);
    }

    return 0;
}
```