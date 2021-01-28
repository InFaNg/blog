---
title: 布尔表达式
date: 2020-07-11 21:58:03
tags: [递归, implementation]
categories: ACM
---

## 题目

The objective of the program you are going to produce is to evaluate boolean expressions as the one shown next:
Expression: ( V \| V ) & F & ( F \| V )

where V is for True, and F is for False. The expressions may include the following operators: ! for not , & for and, \| for or , the use of parenthesis for operations grouping is also allowed.

To perform the evaluation of an expression, it will be considered the priority of the operators, the not having the highest, and the or the lowest. The program must yield V or F , as the result for each expression in the input file.

<!-- more -->

### Input
The expressions are of a variable length, although will never exceed 100 symbols. Symbols may be separated by any number of spaces or no spaces at all, therefore, the total length of an expression, as a number of characters, is unknown.

The number of expressions in the input file is variable and will never be greater than 20. Each expression is presented in a new line, as shown below.

### Output
For each test expression, print "Expression " followed by its sequence number, ": ", and the resulting value of the corresponding test expression. Separate the output for consecutive test expressions with a new line.

Use the same format as that shown in the sample output shown below.

### Sample Input
> ( V \| V ) & F & ( F\| V)<br />
> !V \| V & V & !F & (F \| V ) & (!F \| F \| !V & V)<br />
> (F&F\|V\|!V&!F&!(F\|F&V))

### Sample Output
> Expression 1: F<br />
> Expression 2: V<br />
> Expression 3: V

### 题目链接
[Boolean Expressions](http://poj.org/problem?id=2106)

## 分析
根据题意，在运算的优先级中：括号的优先级最大，其次为三种运算符，三种运算符（与、或、非）的优先级为 **非 > 与 > 或**。由于一个表达式中可能含有多个括号，括号内部又有可能含有括号，于是就可以想到使用递归来处理括号。

因此，我们的思路大致是：**第一层处理或逻辑，第二场处理与逻辑，第三层处理非逻辑（注意：与表达式优先级恰好相反）**。针对输入表达式，我们可以去除字符串中所有的空格，然后将其存入一个字符数组，并使用一个 int 型变量来记录当前所处理到的字符位置。

我们可以设计三个函数，分别对应处理以上的三个逻辑。**对于或逻辑函数，只关心或逻辑，不关心其余部分，可以将或逻辑所连接的左右部分看作“最小单元”，具体细节交给与逻辑进行处理。同理，对于与逻辑函数，只关心与逻辑，不关心其余部分，可以将与逻辑所连接的左右两部分看作“最小单元”，具体细节交给非逻辑进行处理。而对于非逻辑函数，除了要根据非符号的个数判断是否为逆的同时，还要检测是否存在括号，如果存在括号，则将其交给或函数进行处理。**

再来探讨有关三个逻辑函数设计的具体细节，**对于或函数，只要其中的一个“最小单元”为真，则函数为真。而对于与函数，只有所有的“最小单元”均为真时，整个函数才为真。**

了解完这些细节之后，就不难写出该题目的代码。

## 代码
```cpp
#include <iostream>
#include <vector>
using namespace std;

vector<char> cmd;
int cmdIndex = 0;       // 当前处理到的字符的位置

// 由题意可知，优先级中或大于与大于非，因此第一层判断或，第二层判断与，第三层判断非

// 判断或逻辑的函数
char expression_value() {
    char result = term_value();

    while (true) {
        if (cmd[cmdIndex] == '|') {
            cmdIndex++;
            char next_result = term_value();

            // 只要其中的一个“最小单元”为真，则函数为真
            result = (result == 'V') ? 'V' : next_result;
        }
        else break;
    }

    return result;
}

// 判断与逻辑的函数
char term_value() {
    char result = factor_value();

    while (true) {
        if (cmd[cmdIndex] == '&') {
            cmdIndex++;
            char next_result = factor_value();

        // 只有所有的“最小单元”均为真时，整个函数才为真
        result = (result == 'F') ? 'F' : next_result;
        }
        else break;
    }

    return result;
}

// 判断非逻辑的函数
char factor_value() {
    char result;
    int count = 0;      // 记录非逻辑的次数，用于之后的判断

    while (cmd[cmdIndex] == '!') {
        count++;
        cmdIndex++;
    }

    if (cmd[cmdIndex] == '(') {
        cmdIndex++;
        result = expression_value();
        cmdIndex++;     // 顺便读取右括号
    }
    else {
        result = cmd[cmdIndex];
        cmdIndex++;
    }

    if (count % 2)
        return (result == 'V') ? 'F' : 'V';

    return result;
}

int main() {
    string a;
    int t = 0;

    while (getline(cin, a)) {
        cmdIndex = 0;
        cmd.clear();

        // 去掉输入的逻辑表达式中的空格，并将其存入名为 cmd 的 vector 中
        for (int i = 0; i < a.length(); i++) {
            if (a[i] != ' ')
                cmd.push_back(a[i]);
        }

        printf("Expression %d: %c\n", ++t, expression_value());
    }

    return 0;
}
```