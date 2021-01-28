---
title: Pop Sequence
date: 2020-07-17 22:03:42
tags: [implementation, data structures]
categories: ACM
---

## 题目
Given a stack which can keep ***M*** numbers at most. Push ***N*** numbers in the order of ***1, 2, 3, ..., N*** and pop randomly. You are supposed to tell if a given sequence of numbers is a possible pop sequence of the stack. For example, if ***M*** is 5 and ***N*** is 7, we can obtain 1, 2, 3, 4, 5, 6, 7 from the stack, but not 3, 2, 1, 7, 5, 6, 4.

<!-- more -->

### Input Specification
Each input file contains one test case. For each case, the first line contains 3 numbers (all no more than 1000): ***M*** (the maximum capacity of the stack), ***N*** (the length of push sequence), and ***K*** (the number of pop sequences to be checked). Then ***K*** lines follow, each contains a pop sequence of ***N*** numbers. All the numbers in a line are separated by a space.

### Output Specification
For each pop sequence, print in one line "YES" if it is indeed a possible pop sequence of the stack, or "NO" if not.

### Sample Input
> 5 7 5
> 1 2 3 4 5 6 7
> 3 2 1 7 5 6 4
> 7 6 5 4 3 2 1
> 5 6 4 3 7 2 1
> 1 7 6 5 4 3 2

### Sample Output
> YES
> NO
> NO
> YES
> NO

### 题目截图
![pop_sequence](//cdn.jsdelivr.net/gh/InFaNg/infang.github.io/images/pop_sequence.png)


### 题目大意
给定栈的容量、入栈数字的数量和测试用例数，要求对于每个用例，判断出栈顺序是否有可能与所给要求一致。

其中，入栈必须按照数字从小到大的顺序，例如，只有当数字 1~6 均已入过栈后，数字 7 才能入栈。

例如，对于题目所给的第四个用例 `5 6 4 3 7 2 1`，我们可以这么做：
1. 数字 1 入栈
2. 数字 2 入栈
3. 数字 3 入栈
4. 数字 4 入栈
5. 数字 5 入栈
6. **数字 5 出栈**
7. 数字 6 入栈
8. **数字 6 出栈**
9. **数字 4 出栈**
10. **数字 3 出栈**
11. 数字 7 入栈
12. **数字 7 出栈**
13. **数字 2 出栈**
14. **数字 1 出栈**
按照这个顺序，我们就可以得到所要求的数字序列

再如，对于第五个用例：
1. 数字 1 入栈
2. **数字 1 出栈**
3. 数字 2 入栈
4. 数字 3 入栈
5. 数字 4 入栈
6. 数字 5 入栈
7. 数字 6 入栈
此时，栈里面已经存在五个数字了，达到了所给的栈的最大容量 5，可是还是没能得到数字 7🤯，此时，我们可以认为不可能输出所要求的数字序列

## 分析
有了上面的思路，就不难用 C++ 去实现。我们可以自己去创建一个栈，当栈顶的数字不等于所要求的输出项时，就按照升序填入数字，当栈顶的数字等于所要求的输出项时，就将其弹出。当栈内存储数据超出所要求的栈的容量时，就认为题目所给的输出序列不可能完成。

然而，当栈内连一个数字也没有即当栈为空栈时，需要额外的判断才行而不能简单地使用 `s.top()` 命令，否则容易造成空指针错误。然而再加一层判断比较麻烦，我们可以在开始前，人为地往栈中加入一个数字（在下面的代码中，我加入的数字是0，当然，也可以是其他一个数例如 -1），然后在进行栈内元素个数的判断时，允许比题目所给要求的多一个即可。例如在我的代码中，while 循环的条件为 `s.size() <= M` 而不是 `s.size() < M`，即当栈内的元素个数已经为 M 时，仍允许往栈中再加入一个元素🧐

## 代码
```cpp
#include <iostream>
#include <stack>
using namespace std;

int M, N, K;

bool check(int a[]) {
    stack<int> s;
    int i = 0, num = 0;
    s.push(0);

    while(i < N) {
        while (a[i] > s.top() && s.size() <= M)
            s.push(++num);

        if (a[i++] == s.top())
            s.pop();
        else
            return false;
    }

    return 1;
}

int main() {
    cin >> M >> N >> K;

    int data[N];

    while (K--) {
        for (int i = 0; i < N; i++)
            cin >> data[i];

        if (check(data)) cout << "YES" << endl;
        else cout << "NO" << endl;
    }

    return 0;
}
```