# PEP 202 – List Comprehensions  
PEP 202 – 列表推导式

> 原文地址：https://peps.python.org/pep-0202/

Author:  作者：

Barry Warsaw <barry at python.org>

Status:  状态：

Final  最终

Type:  类型：

Standards Track  标准路径

Created:  创建：

13-Jul-2000  2000 年 7 月 13 日

Python-Version:  Python 版本：

2.0

Post-History:  历史记录：

---

Table of Contents  目录

-   [Introduction](#introduction)
-   [The Proposed Solution](#the-proposed-solution)
-   [Rationale](#rationale)
-   [Examples](#examples)
-   [Reference Implementation](#reference-implementation)
-   [BDFL Pronouncements](#bdfl-pronouncements)
-   [References](#references)

## [Introduction  介绍](#introduction)

This PEP describes a proposed syntactical extension to Python, list comprehensions.  
这个 PEP 描述了 Python 的一种提议的语法扩展，即列表推导式。

## [The Proposed Solution  提议的解决方案](#the-proposed-solution)

It is proposed to allow conditional construction of list literals using for and if clauses. They would nest in the same way for loops and if statements nest now.  
提议允许使用 for 和 if 子句来条件性地构建列表字面量。它们将像现在的 for 循环和 if 语句一样嵌套。

## [Rationale  理由](#rationale)

List comprehensions provide a more concise way to create lists in situations where `map()` and `filter()` and/or nested loops would currently be used.  
列表推导式提供了一种更简洁的方式来创建列表，在当前使用 `map()` 和 `filter()` 以及/或嵌套循环的情况下。

## [Examples  示例](#examples)

```python
>>> print [i for i in range(10)]
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]

>>> print [i for i in range(20) if i%2 == 0]
[0, 2, 4, 6, 8, 10, 12, 14, 16, 18]

>>> nums = [1, 2, 3, 4]
>>> fruit = ["Apples", "Peaches", "Pears", "Bananas"]
>>> print [(i, f) for i in nums for f in fruit]
[(1, 'Apples'), (1, 'Peaches'), (1, 'Pears'), (1, 'Bananas'),
 (2, 'Apples'), (2, 'Peaches'), (2, 'Pears'), (2, 'Bananas'),
 (3, 'Apples'), (3, 'Peaches'), (3, 'Pears'), (3, 'Bananas'),
 (4, 'Apples'), (4, 'Peaches'), (4, 'Pears'), (4, 'Bananas')]
>>> print [(i, f) for i in nums for f in fruit if f[0] == "P"]
[(1, 'Peaches'), (1, 'Pears'),
 (2, 'Peaches'), (2, 'Pears'),
 (3, 'Peaches'), (3, 'Pears'),
 (4, 'Peaches'), (4, 'Pears')]
>>> print [(i, f) for i in nums for f in fruit if f[0] == "P" if i%2 == 1]
[(1, 'Peaches'), (1, 'Pears'), (3, 'Peaches'), (3, 'Pears')]
>>> print [i for i in zip(nums, fruit) if i[0]%2==0]
[(2, 'Peaches'), (4, 'Bananas')]
```

## [Reference Implementation  参考实现](#reference-implementation)
List comprehensions become part of the Python language with release 2.0, documented in [\[1\]](#id2).  
列表推导式随着 2.0 版本的发布成为 Python 语言的一部分，并在 \[1\] 中进行了说明。

## [BDFL Pronouncements  BDFL 声明](#bdfl-pronouncements)

-   The syntax proposed above is the Right One.  
    上述提议的语法是正确的。
-   The form `[x, y for ...]` is disallowed; one is required to write `[(x, y) for ...]`.  
    形式 `[x, y for ...]` 是不允许的；必须写成 `[(x, y) for ...]` 。
-   The form `[... for x... for y...]` nests, with the last index varying fastest, just like nested for loops.  
    形式 `[... for x... for y...]` 是嵌套的，最后一个索引变化最快，就像嵌套的 for 循环一样。

## [References  参考文献](#references)

---

Source: [https://github.com/python/peps/blob/main/peps/pep-0202.rst](https://github.com/python/peps/blob/main/peps/pep-0202.rst)  
来源：https://github.com/python/peps/blob/main/peps/pep-0202.rst

Last modified: [2025-02-01 08:55:40 GMT](https://github.com/python/peps/commits/main/peps/pep-0202.rst)  
最后修改时间：2025-02-01 08:55:40 GMT