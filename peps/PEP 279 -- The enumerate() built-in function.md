# PEP 279 -- The enumerate() built-in function

PEP 279 -- 内建函数 enumerate()

|                 |                                       |
| :-------------- | ------------------------------------- |
| PEP:            | 279                                   |
| Title:          | The enumerate() built-in function     |
| Author:         | python at rcn.com (Raymond Hettinger) |
| Status:         | Final                                 |
| Type:           | Standards Track                       |
| Created:        | 30-Jan-2002                           |
| Python-Version: | 2.3                                   |
| Post-History:   |                                       |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0279/#abstract)，概述
- [Rationale](https://www.python.org/dev/peps/pep-0279/#rationale)，理论依据
- [BDFL Pronouncements](https://www.python.org/dev/peps/pep-0279/#bdfl-pronouncements)，BDFL 公告
- [Specification for a new built-in](https://www.python.org/dev/peps/pep-0279/#specification-for-a-new-built-in)，新的内建函数的定义
- [References](https://www.python.org/dev/peps/pep-0279/#references)，参考文献
- [Copyright](https://www.python.org/dev/peps/pep-0279/#copyright)，版权声明

# [Abstract](https://www.python.org/dev/peps/pep-0279/#id8)

概述

This PEP introduces a new built-in function, `enumerate()` to simplify a commonly used looping idiom. It provides all iterable collections with the same advantage that `iteritems()` affords to dictionaries -- a compact, readable, reliable index notation.

这个PEP引入了一个新的内置函数，`enumerate()`来简化一个常用的循环习惯。它为所有可迭代的集合提供了与`iteritems()`为字典提供的相同的优势--一个紧凑、可读、可靠的索引符号。

# [Rationale](https://www.python.org/dev/peps/pep-0279/#id9)

理论依据

Python 2.2 introduced the concept of an iterable interface as proposed in [PEP 234](https://www.python.org/dev/peps/pep-0234) [[3\]](https://www.python.org/dev/peps/pep-0279/#id7). The `iter()` factory function was provided as common calling convention and deep changes were made to use iterators as a unifying theme throughout Python. The unification came in the form of establishing a common iterable interface for mappings, sequences, and file objects.

Python 2.2 引入了 PEP 234 [3] 中提出的可迭代接口的概念。`iter()` 工厂函数被提供作为通用的调用约定，并进行了深入的修改，以使迭代器成为整个 Python 的统一主题。统一的形式是为映射、序列和文件对象建立一个通用的可迭代接口。

Generators, as proposed in [PEP 255](https://www.python.org/dev/peps/pep-0255) [[1\]](https://www.python.org/dev/peps/pep-0279/#id5), were introduced as a means for making it easier to create iterators, especially ones with complex internal execution or variable states. The availability of generators makes it possible to improve on the loop counter ideas in [PEP 212](https://www.python.org/dev/peps/pep-0212) [[2\]](https://www.python.org/dev/peps/pep-0279/#id6). Those ideas provided a clean syntax for iteration with indices and values, but did not apply to all iterable objects. Also, that approach did not have the memory friendly benefit provided by generators which do not evaluate the entire sequence all at once.

在 PEP 255 [1] 中提出的生成器被引入，作为创建迭代器的一种手段，特别是具有复杂内部执行或变量状态的迭代器。生成器的出现使我们有可能改进PEP 212 [2]中的循环计数器思想。这些想法为带有索引和值的迭代提供了一种简洁的语法，但并不适用于所有的可迭代对象。而且，这种方法没有生成器所提供的内存友好的好处，生成器不会一次性地评估整个序列。

The new proposal is to add a built-in function, `enumerate()` which was made possible once iterators and generators became available. It provides all iterables with the same advantage that `iteritems()` affords to dictionaries -- a compact, readable, reliable index notation. Like `zip()`, it is expected to become a commonly used looping idiom.

新的建议是增加一个内置函数，`enumerate()`，一旦迭代器和生成器可用，它就成为可能。它为所有的迭代器提供了与`iteritems()`为字典提供的相同的优势--一个紧凑、可读、可靠的索引符号。就像`zip()`一样，它有望成为一个常用的循环习惯法。

This suggestion is designed to take advantage of the existing implementation and require little additional effort to incorporate. It is backwards compatible and requires no new keywords. The proposal will go into Python 2.3 when generators become final and are not imported from `__future__`.

这个建议的目的是利用现有的实现，不需要额外的努力来加入。它是向后兼容的，不需要新的关键字。当生成器成为最终版本并且不再从 `__future__` 中导入时，该建议将进入 Python 2.3。

# [BDFL Pronouncements](https://www.python.org/dev/peps/pep-0279/#id10)

BDFL公告

The new built-in function is ACCEPTED.

新的内置函数被接受了。

# [Specification for a new built-in](https://www.python.org/dev/peps/pep-0279/#id11)

新的内建函数的定义

```python
def enumerate(collection):
   'Generates an indexed series:  (0,coll[0]), (1,coll[1]) ...'
   i = 0
   it = iter(collection)
   while 1:
      yield (i, it.next())
      i += 1
```

Note A: [PEP 212](https://www.python.org/dev/peps/pep-0212) Loop Counter Iteration [[2\]](https://www.python.org/dev/peps/pep-0279/#id6) discussed several proposals for achieving indexing. Some of the proposals only work for lists unlike the above function which works for any generator, xrange, sequence, or iterable object. Also, those proposals were presented and evaluated in the world prior to Python 2.2 which did not include generators. As a result, the non-generator version in [PEP 212](https://www.python.org/dev/peps/pep-0212) had the disadvantage of consuming memory with a giant list of tuples. The generator version presented here is fast and light, works with all iterables, and allows users to abandon the sequence in mid-stream with no loss of computation effort.

注释A: PEP 212 循环计数器迭代 [2] 讨论了几个实现索引的建议。其中一些建议只对列表有效，而不像上面的函数对任何生成器、xrange、序列或可迭代对象有效。而且，这些建议是在 Python 2.2 之前的世界中提出和评估的，Python 2.2 不包括生成器。因此，PEP 212 中的非生成器版本有一个缺点，即用一个巨大的图元列表消耗内存。这里介绍的生成器版本是快速和轻便的，适用于所有的迭代变量，并且允许用户在中途放弃序列而不损失任何计算工作。

There are other PEPs which touch on related issues: integer iterators, integer for-loops, and one for modifying the arguments to `range` and `xrange`. The `enumerate()` proposal does not preclude the other proposals and it still meets an important need even if those are adopted -- the need to count items in any iterable. The other proposals give a means of producing an index but not the corresponding value. This is especially problematic if a sequence is given which doesn't support random access such as a file object, generator, or sequence defined with `__getitem__`.

还有其他涉及相关问题的PEP：整数迭代器、整数for循环，以及一个用于修改`range`和`xrange`的参数的PEP。`enumerate()`建议并不排除其他建议，而且即使这些建议被采纳，它仍然可以满足一个重要的需求--对任何迭代器中的项目进行计数的需求。其他建议提供了一种产生索引的方法，但没有提供相应的值。如果给出的序列不支持随机访问，如文件对象、生成器或用 `__getitem__` 定义的序列，这就特别有问题。

Note B: Almost all of the PEP reviewers welcomed the function but were divided as to whether there should be any built-ins. The main argument for a separate module was to slow the rate of language inflation. The main argument for a built-in was that the function is destined to be part of a core programming style, applicable to any object with an iterable interface. Just as `zip()` solves the problem of looping over multiple sequences, the `enumerate()` function solves the loop counter problem.

注意 B: 几乎所有的 PEP 评审员都对这个函数表示欢迎，但是对于是否应该有任何内建模块存在分歧。支持独立模块的主要观点是为了减缓语言膨胀的速度。支持内置的主要观点是，该函数注定要成为核心编程风格的一部分，适用于任何具有可迭代接口的对象。就像`zip()`解决了在多个序列上循环的问题，`enumerate()`函数解决了循环计数器的问题。

If only one built-in is allowed, then `enumerate()` is the most important general purpose tool, solving the broadest class of problems while improving program brevity, clarity and reliability.

如果只允许一个内置函数，那么`enumerate()`是最重要的通用工具，它解决了最广泛的一类问题，同时提高了程序的简洁性、清晰性和可靠性。

Note C: Various alternative names were discussed:

注C：讨论了各种替代名称：

|                 |                                                              |
| --------------- | ------------------------------------------------------------ |
| `iterindexed()` | five syllables is a mouthful                                 |
| `index()`       | nice verb but could be confused the `.index()` method        |
| `indexed()`     | widely liked however adjectives should be avoided            |
| `indexer()`     | noun did not read well in a for-loop                         |
| `count()`       | direct and explicit but often used in other contexts         |
| `itercount()`   | direct, explicit and hated by more than one person           |
| `iteritems()`   | conflicts with key:value concept for dictionaries            |
| `itemize()`     | confusing because `amap.items()` != `list(itemize(amap))`    |
| `enum()`        | pithy; less clear than enumerate; too similar to enum in other languages where it has a different meaning |

All of the names involving 'count' had the further disadvantage of implying that the count would begin from one instead of zero.

所有涉及'count'的名字都有一个缺点，那就是意味着计数将从1开始而不是从0开始。

All of the names involving 'index' clashed with usage in database languages where indexing implies a sorting operation rather than linear sequencing.

所有涉及'index'的名字都与数据库语言中的用法相冲突，在数据库语言中，索引意味着排序操作而不是线性排序。

Note D: This function was originally proposed with optional start and stop arguments. GvR pointed out that the function call `enumerate(seqn,4,6)` had an alternate, plausible interpretation as a slice that would return the fourth and fifth elements of the sequence. To avoid the ambiguity, the optional arguments were dropped even though it meant losing flexibility as a loop counter. That flexibility was most important for the common case of counting from one, as in:

注D：这个函数最初被提出来，有可选的开始和停止参数。GvR指出，函数调用`enumerate(seqn,4,6)`有另一种合理的解释，即作为一个切片，返回序列的第四和第五个元素。为了避免歧义，我们放弃了可选参数，尽管这意味着失去了作为循环计数器的灵活性。这种灵活性对于从一开始计数的常见情况来说是最重要的，比如说。

```python
for linenum, line in enumerate(source,1):  print linenum, line
```

- Comments from GvR:

  来自GvR的评论：

  `filter` and `map` should die and be subsumed into list comprehensions, not grow more variants. I'd rather introduce built-ins that do iterator algebra (e.g. the iterzip that I've often used as an example).I like the idea of having some way to iterate over a sequence and its index set in parallel. It's fine for this to be a built-in.I don't like the name "indexed"; adjectives do not make good function names. Maybe `iterindexed()`?

  `filter`和`map`应该死掉，归入列表理解，而不是增加更多的变体。我宁愿引入做迭代器代数的内建程序（例如我经常使用的`iterzip`作为例子）。我喜欢有一些方法来并行迭代一个序列和它的索引集的想法。我不喜欢 "indexed "这个名字；形容词并不能成为好的函数名称。也许`iterindexed()`？

- Comments from Ka-Ping Yee:

  余嘉平的评论：

  I'm also quite happy with everything you proposed ... and the extra built-ins (really 'indexed' in particular) are things I have wanted for a long time.

  我对你提出的所有建议也相当满意......而额外的内置功能（尤其是真正的'索引'）是我长期以来一直想要的东西。

- Comments from Neil Schemenauer:

  Neil Schemenauer的评论：

  The new built-ins sound okay. Guido may be concerned with increasing the number of built-ins too much. You might be better off selling them as part of a module. If you use a module then you can add lots of useful functions (Haskell has lots of them that we could steal).

  新的内建程序听起来不错。Guido可能会担心增加太多的内建程序的数量。你最好把它们作为模块的一部分来销售。如果你使用一个模块，那么你可以增加很多有用的函数（Haskell有很多，我们可以窃取）。

- Comments for Magnus Lie Hetland:

  对Magnus Lie Hetland的评论：

  I think indexed would be a useful and natural built-in function. I would certainly use it a lot. I like `indexed()` a lot; +1. I'm quite happy to have it make [PEP 281](https://www.python.org/dev/peps/pep-0281) obsolete. Adding a separate module for iterator utilities seems like a good idea.

  我认为indexed将是一个有用的、自然的内置函数。我当然会经常使用它。我非常喜欢indexed()；+1。我很高兴它能让PEP 281过时。为迭代器工具添加一个单独的模块似乎是个好主意。

- Comments from the Community:

  来自社区的评论：

  The response to the `enumerate()` proposal has been close to 100% favorable. Almost everyone loves the idea.

  对`enumerate()`提案的反应几乎是100%的好评。几乎所有人都喜欢这个主意。

- Author response:

  作者的回应：

  Prior to these comments, four built-ins were proposed. After the comments, `xmap`, `xfilter` and `xzip` were withdrawn. The one that remains is vital for the language and is proposed by itself. `Indexed()` is trivially easy to implement and can be documented in minutes. More importantly, it is useful in everyday programming which does not otherwise involve explicit use of generators.This proposal originally included another function `iterzip()`. That was subsequently implemented as the `izip()` function in the `itertools` module.

  在这些评论之前，有四个内建程序被提议。在这些评论之后，`xmap`、`xfilter` 和 `xzip` 被撤销了。剩下的一个对语言来说是至关重要的，因此被提出来。`Indexed()`实现起来非常容易，可以在几分钟内完成文档编写。更重要的是，它在日常编程中非常有用，否则不会明确使用生成器。这个建议最初包括另一个函数`iterzip()`，后来在`itertools`模块中被实现为`izip()`函数。

  > 这个内置函数这么有用吗？我居然一直不知道？译者注。

# [References](https://www.python.org/dev/peps/pep-0279/#id12)

| [[1\]](https://www.python.org/dev/peps/pep-0279/#id2) | [PEP 255](https://www.python.org/dev/peps/pep-0255) Simple Generators http://www.python.org/dev/peps/pep-0255/ |
| ----------------------------------------------------- | ------------------------------------------------------------ |
|                                                       |                                                              |

| [2]  | *([1](https://www.python.org/dev/peps/pep-0279/#id3), [2](https://www.python.org/dev/peps/pep-0279/#id4))* [PEP 212](https://www.python.org/dev/peps/pep-0212) Loop Counter Iteration http://www.python.org/dev/peps/pep-0212/ |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

| [[3\]](https://www.python.org/dev/peps/pep-0279/#id1) | [PEP 234](https://www.python.org/dev/peps/pep-0234) Iterators http://www.python.org/dev/peps/pep-0234/ |
| ----------------------------------------------------- | ------------------------------------------------------------ |
|                                                       |                                                              |

# [Copyright](https://www.python.org/dev/peps/pep-0279/#id13)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0279.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。