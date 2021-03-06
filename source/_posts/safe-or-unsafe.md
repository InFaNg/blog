---
title: 哈夫曼编码
date: 2020-08-10 13:25:25
tags: [data structures, binary tree, Huffman coding]
categories: ACM
---
本文将从一道要求判断是否能用给定大小的位来表示给出字符串的题目说起，来介绍哈夫曼编码 (Huffman coding) 的来历、其算法的设计以及有关前缀码部分的证明，在文章的最后将给出这道题目的 C++ 代码实现.

<!-- more -->

## 题目

### Problem Description
> Javac++ 一天在看计算机的书籍的时候，看到了一个有趣的东西！每一串字符都可以被编码成一些数字来储存信息，但是不同的编码方式得到的储存空间是不一样的！并且当储存空间大于一定的值的时候是不安全的！所以Javac++ 就想是否有一种方式是可以得到字符编码最小的空间值！显然这是可以的，因为书上有这一块内容--哈夫曼编码(Huffman coding)；一个字母的权值等于该字母在字符串中出现的频率。所以Javac++ 想让你帮忙，给你安全数值和一串字符串，并让你判断这个字符串是否是安全的？

### Input
> 输入有多组case，首先是一个数字n表示有n组数据，然后每一组数据是有一个数值m(integer)，和一串字符串没有空格只有包含小写字母组成！

### Output
> 如果字符串的编码值小于等于给定的值则输出yes，否则输出no。

### Sample Input
> 2
> 12
> helloworld
> 66
> ithinkyoucandoit

### Sample Output
> no
> yes

### 题目链接
[Safe Or Unsafe](http://acm.hdu.edu.cn/showproblem.php?pid=2527)

## 哈夫曼编码 (Huffman coding) 的基本概念
首先我们来看一下什么是编码. 在计算机中，所有的文本都是以二进制数的形式来进行存储. 但是，对于一个给定的字符，到底应该对应哪个二进制数呢？对于这个问题，我们往往使用一套固定的编码系统，这套编码系统规定了字符与码字之间一一对应的关系. 常见的编码系统有 ASCII, Unicode 等，下面将以 ASCII 为例，大致说明编码的原理：

例如，我们想要在计算机中储存 `gooooogle` 这个英文单词，在 ASCII 中规定了字符与码字之间的关系：

| 字符 | 码字 |
| :--: | :--: |
| e | 01100101 |
| g | 01100111 |
| l | 01101100 |
| o | 01101111 |

因此，我们可以将 `gooooogle` 转为二进制的 ASCII 码 `01100111_01101111_01101111_01101111_01101111_01101111_01100111_01101100_01100101`**（这里为浏览方便起见，在每个字符之间添加了下划线，在实际的存储过程中是没有这一下划线加以区分的）**. ASCII 中的字符均使用 8 个码字进行表示，因此在读取时，计算机将每次读入八位码字并将其转为相应的字符，确保了存储与读取时数据的一致性.

可以看出，使用ASCII码的情况下计算机使用了 72 位的空间来存储 `gooooogle` 这个单词. 然而有没有更好的编码方法来存储这个单词，使其占用的空间更少呢？在这个例子中，总共只出现了 4 个不同的字符，因此在这种情况下我们无需使用 8 位码字来表示一个字符，事实上，用 2 位码字就够了. 因此我们可以自己定义一套如下的编码系统：

| 字符 | 码字 |
| :--: | :--: |
| e | 00 |
| g | 01 |
| l | 10 |
| o | 11 |

因此，`gooooogle` 的编码就变成了 `01_11_11_11_11_11_01_10_00`，这套编码只要用 18 位的空间就能存储 `gooooogle` 这一单词了.

上面的方法都是用了`定长编码`来储存字符，即使用固定长度的码字来表示某一字符. 例如在 ASCII 中，我们使用 8 位码字来表示字符，在后面我们自己的编码系统中，我们使用 2 位码字
来表示字符. 然而除了`定长编码`外，还有`变长编码`，即使用长度不同的码字来表示不同的字符，一般地，我们都对出现频次较高的字符使用较短长度的码字进行表示，对出现频次较低的字符使用较长长度的码字进行表示以节省空间. 例如，我们可以使用如下编码系统：

| 字符 | 码字 |
| :--: | :--: |
| e | 000 |
| g | 01 |
| l | 001 |
| o | 1 |

因此，我们可以使用编码 `01_1_1_1_1_1_01_001_000` 来表示单词 `gooooogle`，事实上，我们至少需要 15 位的空间来存储 `gooooogle` 这一单词. 上面所演示的对其进行编码的方法就是哈夫曼编码 (Huffman coding).

值得注意的是，要想使用变长编码清晰地、没有二义性地表示一个字符，我们必需保证使用的编码是一种`前缀码`，即**对于编码系统中的每一个字符，都找不到另外一个字符，使得前者的码字是后者的前缀**. 对于不是前缀码的编码系统，例如：

| 字符 | 码字 |
| :--: | :--: |
| e | 1 |
| g | 0 |
| l | 10 |
| o | 11 |

对于使用这套系统的编码，例如 `1011`，我们无法确定其表示的是 `egee`, `lo`, `lee` 还是 `ego`，前缀码保证了对于给定的一串编码，都有唯一确定的字符串与之对应.

## 如何构造哈夫曼编码 (Huffman coding)
构造哈夫曼编码，本质上就是构造长度最短的前缀码. 仍以单词 `gooooogle` 为例，我们首先统计每个字符出现的次数.

| 字符 | 出现次数 |
| :--: | :--: |
| e | 1 |
| g | 2 |
| l | 1 |
| o | 5 |

统计完后，**我们将按字符出现次数构造一个最小优先队列，并对队列中字符出现次数最小的两个结点进行合并，合并操作一共进行 `n - 1` 次**. 完成上面的操作后，我们就将相当于是创建了一棵二叉树，如下图所示：

![safe_or_unsafe_1](/images/safe_or_unsafe_1.png)
![safe_or_unsafe_2](/images/safe_or_unsafe_2.png)
![safe_or_unsafe_3](/images/safe_or_unsafe_3.png)

建立完二叉树后，就不难得到字符所对应的码字：从根结点开始，自上而下地去寻找字符，其中 0 表示左子结点、1 表示右子结点. 例如，`e` 位于根结点的左子结点的左子结点的右子结点，那么它的码字就是 `001`. 由于在我们构造的二叉树中，字符都位于二叉树的叶子结点上，所以我们所构造的码字就是一种前缀码，证明如下：

我们用反证法来证明： 假设我们构造的码字不是一种前缀码，那么必然存在一个字符，它的码字为编码系统中另一个字符码字的前缀，在二叉树中，它就必然是另一个字符的父结点，即它在二叉树中的结点就不是叶子结点. 这一结论与命题相矛盾，故原命题成立.

## 题目分析
在上面的分析中我们不难得出规律：对于一个字符，其码字的长度等于该字符所对应的结点在二叉树中的深度. 然而，在这道题目中，我们根本不关心二叉树的数据结构，我们也无需建立二叉树的数据结构. 事实上，**对于二叉树中的任意一个非根结点，其深度总等于其与其它结点的合并次数**. 因此，我们可以在合并最小优先队列中的两个数值最小的结点时，就将它们的和加到答案中，并在最小优先队列中删去这两项，然后在最小优先队列中加入一个结点，该结点的值为删去的两个结点的数值之和. 伪代码描述如下：
GET_LENGTH()
```
ans = 0

while minimum_priority_queue.size() != 1
    first = minimum_priority_queue.top()
    minimum_priority_queue.pop()
    second = minimum_priority_queue.top()
    minimum_priority_queue.pop()

    ans = ans + first + second

    minimum_priority_queue.insert(first + second)

return ans
```

## 代码
C++ 实现的代码如下：
```cpp
#include <iostream>
#include <queue>
#include <vector>
#include <cstring>
using namespace std;

int n, m, ans, count[26];
char s[1000];

int main() {
    while (cin >> n)
        while (n--) {
            cin >> m >> s;

            memset(count, 0, sizeof(count));

            for (int i = 0; i < strlen(s); i++)
                count[s[i] - 'a']++;

            priority_queue< int, vector<int>, greater<int> > q;

            for (int i = 0; i < 26; i++)
                q.push(count[i]);

            if (q.size() == 1)
                ans = strlen(s);
            else {
                ans = 0;

                while (q.size() != 1) {
                    int a = q.top();
                    q.pop();
                    int b = q.top();
                    q.pop();

                    ans += (a + b);

                    q.push(a + b);
                }
            }

            if (ans > m)
                cout << "no" << endl;
            else
                cout << "yes" << endl;
        }

        return 0;
}
```