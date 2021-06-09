# PEP 20 -- The Zen of Python

Python之禅

> 原文地址：<https://www.python.org/dev/peps/pep-0020/>

| PEP:          | 20                                   |
| :------------ | ------------------------------------ |
| Title:        | The Zen of Python                    |
| Author:       | tim.peters at gmail.com (Tim Peters) |
| Status:       | Active                               |
| Type:         | Informational                        |
| Created:      | 19-Aug-2004                          |
| Post-History: | 22-Aug-2004                          |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0020/#abstract)
- [The Zen of Python](https://www.python.org/dev/peps/pep-0020/#the-zen-of-python)
- [Easter Egg](https://www.python.org/dev/peps/pep-0020/#easter-egg)
- [References](https://www.python.org/dev/peps/pep-0020/#references)
- [Copyright](https://www.python.org/dev/peps/pep-0020/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0020/#id1)

概览

Long time Pythoneer Tim Peters succinctly channels the BDFL's guiding principles for Python's design into 20 aphorisms, only 19 of which have been written down.

长期从事Pythoneer工作的Tim Peters将BDFL对Python设计的指导原则简洁地归纳为20句箴言，其中只有19句被写下来。

# [The Zen of Python](https://www.python.org/dev/peps/pep-0020/#id2)

Python之禅

```
Beautiful is better than ugly.
优美胜于丑陋（Python以编写优美的代码为目标）
Explicit is better than implicit.
明了胜于晦涩（优美的代码应当是明了的，命名规范，风格相似）
Simple is better than complex.
简洁胜于复杂（优美的代码应当是简洁的，不要有复杂的内部实现） 
Complex is better than complicated.
复杂胜于凌乱（如果复杂不可避免，那代码间也不能有难懂的关系，要保持接口简洁）
Flat is better than nested.
扁平胜于嵌套（优美的代码应当是扁平的，不能有太多的嵌套）
Sparse is better than dense.
间隔胜于紧凑（优美的代码有适当的间隔，不要奢望一行代码解决问题）
Readability counts.
可读性很重要（优美的代码是可读的）
Special cases aren't special enough to break the rules.
Although practicality beats purity.
即便假借特例的实用性之名，也不可违背这些规则（这些规则至高无上）
Errors should never pass silently.
Unless explicitly silenced.
不要悄无声息地屏蔽异常，除非你确定需要这样做（精准地捕获异常，不写except:pass风格的代码）
In the face of ambiguity, refuse the temptation to guess.
面对模棱两可的问题，不要随意揣测。
There should be one-- and preferably only one --obvious way to do it.
Although that way may not be obvious at first unless you're Dutch.
应该有一个--最好是只有一个--明显的方法作为解决方案。
尽管这个方法一开始可能并不容易找到，因为你不是荷兰人（Python之父是荷兰人）。
Now is better than never.
Although never is often better than *right* now.
现在做总比不做好。
尽管永远不做往往比*立即做*要好（意指不加思索蒙头蛮干）。
If the implementation is hard to explain, it's a bad idea.
If the implementation is easy to explain, it may be a good idea.
如果实现是难以解释的，那就是一个不好的方案。
如果实现很容易解释，那它可能是一个好方案。
Namespaces are one honking great idea -- let's do more of those!
命名空间是一个伟大的想法 -- 让我们做更多这样的事情吧。
```

# [Easter Egg](https://www.python.org/dev/peps/pep-0020/#id3)

彩蛋

```
>>> import this
```

# [References](https://www.python.org/dev/peps/pep-0020/#id4)

- Originally posted to [comp.lang.python/python-list@python.org](mailto:comp.lang.python/python-list@python.org) under a thread called "The Way of Python" https://groups.google.com/d/msg/comp.lang.python/B_VxeTBClM0/L8W9KlsiriUJ

# [Copyright](https://www.python.org/dev/peps/pep-0020/#id5)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0020.txt

> 参考资料：
>
> - [**PEP 20 -- The Zen of Python（Python之禅）**](https://blog.csdn.net/u014044812/article/details/94994391)
> - [**Python 之禅 (可能是目前知乎上最好的版本)**](https://zhuanlan.zhihu.com/p/40950546)

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。