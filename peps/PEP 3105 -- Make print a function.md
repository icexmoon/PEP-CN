# PEP 3105 -- Make print a function

PEP 3105 -- 将print设置为一个函数

> 原文地址：<https://www.python.org/dev/peps/pep-3105/>

| PEP:            | 3105                               |
| :-------------- | ---------------------------------- |
| Title:          | Make print a function              |
| Author:         | Georg Brandl <georg at python.org> |
| Status:         | Final                              |
| Type:           | Standards Track                    |
| Created:        | 19-Nov-2006                        |
| Python-Version: | 3.0                                |
| Post-History:   |                                    |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#abstract)，概述
- [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#rationale)，目的
- [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#specification)，定义
- [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#backwards-compatibility)，向后兼容
- [Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#implementation)，实现
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#references)，参考资料
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id7)

概述

The title says it all -- this PEP proposes a new `print()` builtin that replaces the `print` statement and suggests a specific signature for the new function.

就像标题说的那样 -- 本PEP提出了一个新的`print()`内置函数，以取代`print`语句，并为新函数提出了具体的签名。

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id8)

目的

The `print` statement has long appeared on lists of dubious language features that are to be removed in Python 3000, such as Guido's "Python Regrets" presentation [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id4). As such, the objective of this PEP is not new, though it might become much disputed among Python developers.

`print`语句早已出现在Python 3000中要删除的可疑的语言特性列表中，例如Guido的 "Python的遗憾 "报告 [1]。因此，这个PEP的目标并不新鲜，尽管它在Python开发者中可能会有很多争议。

The following arguments for a `print()` function are distilled from a python-3000 message by Guido himself [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id5):

以下关于`print()`函数的论点是由Guido本人从python-3000消息中提炼出来的[[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id5)：

- `print` is the only application-level functionality that has a statement dedicated to it. Within Python's world, syntax is generally used as a last resort, when something *can't* be done without help from the compiler. Print doesn't qualify for such an exception.

  `print`是唯一的应用级功能，有一个专门的声明。在Python的世界里，语法通常是作为最后的手段使用的——当某件事情没有编译器的帮助就无法完成时。打印没有资格成为例外。

- At some point in application development one quite often feels the need to replace `print` output by something more sophisticated, like logging calls or calls into some other I/O library. With a `print()` function, this is a straightforward string replacement, today it is a mess adding all those parentheses and possibly converting `>>stream` style syntax.

  在应用程序开发的某个阶段，人们经常觉得需要用更复杂的东西来代替`print`输出，比如日志调用或调用其他I/O库。在`print()`函数中，这是一个直接的字符串替换，而在今天，加入所有的括号和可能转换为`>>stream`风格的语法是一团糟。

- Having special syntax for `print` puts up a much larger barrier for evolution, e.g. a hypothetical new `printf()` function is not too far fetched when it will coexist with a `print()` function.

  为`print`设置特殊的语法为进化设置了更大的障碍，例如，假设一个新的`printf()`函数与`print()`函数共存时，（这种情况）并非遥不可及。

- There's no easy way to convert `print` statements into another call if one needs a different separator, not spaces, or none at all. Also, there's no easy way *at all* to conveniently print objects with some other separator than a space.

  如果需要一个不同的分隔符，而不是空格，或者根本没有分隔符，就没有简单的方法将`print`语句转换为另一个调用。同时，也*完全*没有简单的方法方便地打印带有其他分隔符而不是空格的对象。

- If `print()` is a function, it would be much easier to replace it within one module (just `def print(*args):...`) or even throughout a program (e.g. by putting a different function in `__builtin__.print`). As it is, one can do this by writing a class with a `write()` method and assigning that to `sys.stdout` -- that's not bad, but definitely a much larger conceptual leap, and it works at a different level than print.

  如果`print()`是一个函数，那么在一个模块内（只需`def print(*args):...`）或甚至在整个程序中（例如在`__builtin__.print`中放入一个不同的函数）替换它就容易多了。现在，人们可以通过编写一个带有`write()`方法的类，并将其分配给`sys.stdout`来实现这一目的 -- 这还不错，但绝对是一个更大的概念飞跃，而且它的工作层次与print不同。

# [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id9)

定义

The signature for `print()`, taken from various mailings and recently posted on the python-3000 list [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id6) is:

`print()`的签名，取自各种邮件，最近发布在python-3000列表[3]上的签名是：

> Python社区是通过各种“邮件”来推动PEP提案的，译者注。

```python
def print(*args, sep=' ', end='\n', file=None)
```

A call like:

一个可能的调用示例：

```python
print(a, b, c, file=sys.stderr)
```

will be equivalent to today's:

将等同于现在这样的用法：

> 这里的现在指提案发表的2000年初时，当时 Python2 是主流，译者注。

```python
print >>sys.stderr, a, b, c
```

while the optional `sep` and `end` arguments specify what is printed between and after the arguments, respectively.

其中可选的`sep`和`end`参数分别指定参数之间和参数之后的打印内容。

The `softspace` feature (a semi-secret attribute on files currently used to tell print whether to insert a space before the first item) will be removed. Therefore, there will not be a direct translation for today's:

`softspace`功能（目前用于告诉print是否在第一项前插入空格的文件上的一个半秘密属性）将被删除。因此，将不会有如今（下面示例）的直接翻译：

```
print "a",
print
```

which will not print a space between the `"a"` and the newline.

这样就不会在 "a "和新行之间打印空格。

# [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id10)

向后兼容

The changes proposed in this PEP will render most of today's `print` statements invalid. Only those which incidentally feature parentheses around all of their arguments will continue to be valid Python syntax in version 3.0, and of those, only the ones printing a single parenthesized value will continue to do the same thing. For example, in 2.x:

本PEP中提出的修改将使今天的大多数打印语句失效。只有那些在所有参数周围都有小括号的语句在3.0版本中仍然是有效的Python语法，其中只有那些打印单个小括号值的语句会继续做同样的事情。例如，在 2.x:

```python
>>> print ("Hello")
Hello
>>> print ("Hello", "world")
('Hello', 'world')
```

whereas in 3.0:

如果在Python 3中：

```shell
>>> print ("Hello")
Hello
>>> print ("Hello", "world")
Hello world
```

Luckily, as it is a statement in Python 2, `print` can be detected and replaced reliably and non-ambiguously by an automated tool, so there should be no major porting problems (provided someone writes the mentioned tool).

幸运的是，由于它是Python 2中的一个语句，`print`可以被一个自动工具可靠而不含糊地检测和替换，所以应该不会有大的移植问题(只要有人写出上述工具)。

# [Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id11)

实现

The proposed changes were implemented in the Python 3000 branch in the Subversion revisions 53685 to 53704. Most of the legacy code in the library has been converted too, but it is an ongoing effort to catch every print statement that may be left in the distribution.

建议的修改已经在Subversion的53685到53704版本的Python 3000分支中实现。库中的大部分遗留代码也已被转换，但要抓住可能留在发行版中的每一条打印语句，还需要不断努力。

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id12)

参考资料

| [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id1) | http://legacy.python.org/doc/essays/ppt/regrets/PythonRegrets.pdf |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |
| [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id2) | Replacement for print in Python 3.0 (Guido van Rossum) https://mail.python.org/pipermail/python-dev/2005-September/056154.html |
|                                                              |                                                              |
| [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id3) | print() parameters in py3k (Guido van Rossum) https://mail.python.org/pipermail/python-3000/2006-November/004485.html |
|                                                              |                                                              |

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203105%20--%20Make%20print%20a%20function.md/#id13)

版权声明

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-3105.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。