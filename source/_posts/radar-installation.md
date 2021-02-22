---
title: Radar Installation
date: 2020-07-13 23:01:36
tags: [dp]
categories: ACM
---

## 题目
Assume the coasting is an infinite straight line. Land is in one side of coasting, sea in the other. Each small island is a point locating in the sea side. And any radar installation, locating on the coasting, can only cover d distance, so an island in the sea can be covered by a radius installation, if the distance between them is at most d.

We use Cartesian coordinate system, defining the coasting is the x-axis. The sea side is above x-axis, and the land side below. Given the position of each island in the sea, and given the distance of the coverage of the radar installation, your task is to write a program to find the minimal number of radar installations to cover all the islands. Note that the position of an island is represented by its x-y coordinates.

<!-- more -->

![rader_installation_sample](/images/rader_installation_sample.png)

### Input
The input consists of several test cases. The first line of each case contains two integers n (1<=n<=1000) and d, where n is the number of islands in the sea and d is the distance of coverage of the radar installation. This is followed by n lines each containing two integers representing the coordinate of the position of each island. Then a blank line follows to separate the cases.

The input is terminated by a line containing pair of zeros

### Output
For each test case output one line consisting of the test case number followed by the minimal number of radar installations needed. "-1" installation means no solution for that case.

### Sample Input
> 3 2
> 1 2
> -3 1
> 2 1
> 
> 1 2
> 0 2

> 0 0

### Sample Output
> Case 1: 2
> Case 2: 1

### 题目截图
![rader_installation](/images/rader_installation.png)

### 题目链接
[1328 -- Radar Installation](http://poj.org/problem?id=1328)

## 分析
对于如何使用尽可能少的雷达覆盖小岛的平面二维问题，我们的方法是求出**每个小岛对应的雷达可以监测到的范围的区间（如果存在这样的区间的话）**，从而将二位问题转化为一维问题。其次对于所求得的所有区间，判断他们两两之间是否存在交集，如果没有交集则需要再加一个雷达。然而，在一般情况下区间是无序的，因此我们可以将它们排序，方便进行之后的区间的变量。

不妨将每个区间按照它们的末端进行升序排序。对于已经排好序的 n 个区间，我们记它们的起始位置分别为$a_1, a_2, ..., a_n$，终止位置分别为$b_1, b_2, ..., a_n$，如果表达式$a_i>b_{i-1}$成立，则说明他们之间的交集不存在（或者说是空集），即我们需要再加一个雷达以进行检测。

## 代码
事实上，对于负无穷的定义，理论上使用`-numeric_limits<double>::infinity()`会更好，但很多OJ都不支持此用法，于是在本题的代码中，使用了`double temp = -1000000000`即定义一个足够小的数这种不太好的方法。

```cpp
#include <cstdio>
#include <cmath>
#include <algorithm>
using namespace std;

pair<double, double> node[1005];

bool cmp(pair<double, double> a, pair<double, double> b) {
    return a.second < b.second;
}

int main() {
    int n, k = 0;
    double d;
    bool flag;

    while (scanf("%d %lf", &n, &d) == 2 && (n || d)) {
        double x, y;
        flag = true;

        for (int i = 0; i < n; i++) {
            scanf("%lf %lf", &x, &y);

            if (!flag) continue;
            if (y > d) {
                flag = false;
                continue;
            }
            node[i].first = x - sqrt(d * d - y * y);
            node[i].second = x + sqrt(d * d - y * y);
        }

        printf("Case %d: ", ++k);
        if (!flag) {
            printf("-1\n");
            continue;
        }

        sort(node, node + n, cmp);

        int ans = 0;
        double temp = -1000000000;
        for (int i = 0; i < n; i++)
            if (temp < node[i].first) {
                ans++;
                temp = node[i].second;
            }

        printf("%d\n", ans);

    }

    return 0;
}
```