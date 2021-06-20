# PEP 584 -- Add Union Operators To dict

PEP 584 -- 给dict添加合并操作符

> 原文地址：<https://www.python.org/dev/peps/pep-0584/>

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 584                                                          |
| Title:          | Add Union Operators To dict                                  |
| Author:         | Steven D'Aprano <steve at pearwood.info>, Brandt Bucher <brandt at python.org> |
| BDFL-Delegate:  | Guido van Rossum <guido at python.org>                       |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 01-Mar-2019                                                  |
| Python-Version: | 3.9                                                          |
| Post-History:   | 01-Mar-2019, 16-Oct-2019, 02-Dec-2019, 04-Feb-2020, 17-Feb-2020 |
| Resolution:     | https://mail.python.org/archives/list/python-dev@python.org/thread/6KT2KIOTYXMDCD2CCAOLOI7LUGTN6MBS |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#abstract)，摘要
- Motivation，动机
  - [`dict.update`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#dict-update)
  - [`{**d1, **d2}`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#d1-d2)
  - [`collections.ChainMap`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#collections-chainmap)
  - [`dict(d1, **d2)`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#dict-d1-d2)
- [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#rationale)，理论依据
- [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#specification)，定义
- [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#reference-implementation)，参考实现
- Major Objections，主要反对意见
  - Dict Union Is Not Commutative，字典合并不符合交换律
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#response)
  - Dict Union Will Be Inefficient，字典合并是低效的
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id1)
  - Dict Union Is Lossy，字典合并是有损失的
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id2)
  - Only One Way To Do It，只有一条路可以做到
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id3)
  - More Than One Way To Do It，有超过一条路可以做到
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id4)
  - Dict Union Makes Code Harder To Understand，字典合并使得代码更难理解
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id5)
  - What About The Full `set` API?，为什么不是完整的`set`API？
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id6)
  - What About `Mapping` And `MutableMapping`?，如何处理`Mapping`和`MutableMapping`
    - [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id7)
- Rejected Ideas，被拒绝的想法
  - Rejected Semantics，被拒绝的语义
    - [Raise](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#raise)，作为异常抛出
    - [Add The Values (As Counter Does, with `+`)](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#add-the-values-as-counter-does-with)，值相加（像Counter用`+`做的那样）
    - [Leftmost Value (First-Seen) Wins](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#leftmost-value-first-seen-wins)，让左边的值胜出
    - [Concatenate Values In A List](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#concatenate-values-in-a-list)，连接列表中的值
  - Rejected Alternatives，被拒绝的替代方案
    - [Use The Addition Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#use-the-addition-operator)，使用加法运算符
    - [Use The Left Shift Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#use-the-left-shift-operator)，使用左移运算符
    - [Use A New Left Arrow Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#use-a-new-left-arrow-operator)，使用一个新的左箭头运算符
    - Use A Method，使用一个方法
      - [Advantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#advantages)，优点
      - [Disadvantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#disadvantages)，缺点
    - Use a Function，使用一个函数
      - [Advantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id8)，优点
      - [Disadvantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id9)，缺点
- Examples，示例
  - [IPython/zmq/ipkernel.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#ipython-zmq-ipkernel-py)
  - [IPython/zmq/kernelapp.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#ipython-zmq-kernelapp-py)
  - [matplotlib/backends/backend_svg.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#matplotlib-backends-backend-svg-py)
  - [matplotlib/delaunay/triangulate.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#matplotlib-delaunay-triangulate-py)
  - [matplotlib/legend.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#matplotlib-legend-py)
  - [numpy/ma/core.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#numpy-ma-core-py)
  - [praw/internal.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#praw-internal-py)
  - [pygments/lexer.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#pygments-lexer-py)
  - [requests/sessions.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#requests-sessions-py)
  - [sphinx/domains/__init__.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sphinx-domains-init-py)
  - [sphinx/ext/doctest.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sphinx-ext-doctest-py)
  - [sphinx/ext/inheritance_diagram.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sphinx-ext-inheritance-diagram-py)
  - [sphinx/highlighting.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sphinx-highlighting-py)
  - [sphinx/quickstart.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sphinx-quickstart-py)
  - [sympy/abc.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sympy-abc-py)
  - [sympy/parsing/maxima.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sympy-parsing-maxima-py)
  - [sympy/printing/ccode.py and sympy/printing/fcode.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sympy-printing-ccode-py-and-sympy-printing-fcode-py)
  - [sympy/utilities/runtests.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#sympy-utilities-runtests-py)
- [Related Discussions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#related-discussions)，相关讨论
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id11)

摘要

This PEP proposes adding merge (`|`) and update (`|=`) operators to the built-in `dict` class.

本PEP建议在内置的`dict`类中增加合并（`|`）和更新（`|=`）操作符。

Note

注意

After this PEP was accepted, the decision was made to also implement the new operators for [several other standard library mappings](https://bugs.python.org/issue36144).

在这个PEP被接受之后，我们决定也为其他几个标准库的映射实现新的操作符。

# [Motivation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id12)

动机

The current ways to merge two dicts have several disadvantages:

目前合并两个字典的方法有几个缺点。

## [`dict.update`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id13)

`d1.update(d2)` modifies `d1` in-place. `e = d1.copy(); e.update(d2)` is not an expression and needs a temporary variable.

`d1.update(d2)`就地修改`d1`。`e = d1.copy(); e.update(d2)`不是一个表达式，需要一个临时变量。

## [`{**d1, **d2}`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id14)

Dict unpacking looks ugly and is not easily discoverable. Few people would be able to guess what it means the first time they see it, or think of it as the "obvious way" to merge two dicts.

Dict解包看起来很丑，而且不容易被发现。很少有人能在第一次看到它的时候就猜到它的意思，或者认为它是合并两个字典的 "明显方法"。

[As Guido said](https://mail.python.org/archives/list/python-ideas@python.org/message/K4IC74IXE23K4KEL7OUFK3VBC62HGGVF/):

就像Guido说的那样：

> I'm sorry for [PEP 448](https://www.python.org/dev/peps/pep-0448), but even if you know about `**d` in simpler contexts, if you were to ask a typical Python user how to combine two dicts into a new one, I doubt many people would think of `{**d1, **d2}`. I know I myself had forgotten about it when this thread started!
>
> 我为 PEP 448 感到抱歉，但即使你在更简单的上下文中知道 `**d`，如果你要问一个典型的 Python 用户如何将两个 dicts 合并成一个新的，我怀疑是否有人会想到`{**d1, **d2}`。我知道当这个主题开始的时候，我自己已经忘记了它!

`{**d1, **d2}` ignores the types of the mappings and always returns a `dict`. `type(d1)({**d1, **d2})` fails for dict subclasses such as `defaultdict` that have an incompatible `__init__` method.

`{**d1, **d2}`忽略映射的类型，并且总是返回一个`dict.type(d1)({**d1, **d2})`类型，如果包含的是dict的子类，比如是`defaultdict`这样有不兼容的`__init__`方法的子类，将会失败。

## [`collections.ChainMap`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id15)

`ChainMap` is unfortunately poorly-known and doesn't qualify as "obvious". It also resolves duplicate keys in the opposite order to that expected ("first seen wins" instead of "last seen wins"). Like dict unpacking, it is tricky to get it to honor the desired subclass. For the same reason, `type(d1)(ChainMap(d2, d1))` fails for some subclasses of dict.

不幸的是，`ChainMap`鲜为人知，不符合 "明显 "的条件。它还以与预期相反的顺序解决重复的键（"最先看到的赢 "而不是 "最后看到的赢"）。就像dict解包一样，要让它尊重所需的子类是很棘手的。出于同样的原因，`type(d1)(ChainMap(d2, d1))`对于dict的某些子类来说是失败的。

Further, ChainMaps wrap their underlying dicts, so writes to the ChainMap will modify the original dict:

此外，ChainMaps封装了它们的底层数据，所以对ChainMap的写入将修改原始数据：

```python
>>> d1 = {'spam': 1}
>>> d2 = {'eggs': 2}
>>> merged = ChainMap(d2, d1)
>>> merged['eggs'] = 999
>>> d2
{'eggs': 999}
```

## [`dict(d1, **d2)`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id16)

This "neat trick" is not well-known, and only works when `d2` is entirely string-keyed:

这个 "巧妙的技巧 "并不为人所知，而且只在`d2`完全是字符串键的情况下才有效：

```python
>>> d1 = {"spam": 1}
>>> d2 = {3665: 2}
>>> dict(d1, **d2)
Traceback (most recent call last):
  ...
TypeError: keywords must be strings
```

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id17)

理论依据

The new operators will have the same relationship to the `dict.update` method as the list concatenate (`+`) and extend (`+=`) operators have to `list.extend`. Note that this is somewhat different from the relationship that `|`/`|=` have with `set.update`; the authors have determined that allowing the in-place operator to accept a wider range of types (as `list` does) is a more useful design, and that restricting the types of the binary operator's operands (again, as `list` does) will help avoid silent errors caused by complicated implicit type casting on both sides.

新的操作符与`dict.update`方法的关系，与list concatenate (`+`)和extend (`+=`)操作符与`list.extend`的关系相同。请注意，这与`|`/`|=`与`set.update`的关系有些不同；作者认为，允许就地操作符接受更广泛的类型（就像list那样）是一个更有用的设计，而限制二进制操作符的操作数的类型（同样，就像`list`那样）将有助于避免因双方复杂的隐式类型转换而导致的沉默错误。

Key conflicts will be resolved by keeping the rightmost value. This matches the existing behavior of similar `dict` operations, where the last seen value always wins:

键的冲突将通过保留最右边的值来解决。这与现有的类似dict操作的行为相匹配，即最后看到的值总是获胜。

```python
{'a': 1, 'a': 2}
{**d, **e}
d.update(e)
d[k] = v
{k: v for x in (d, e) for (k, v) in x.items()}
```

All of the above follow the same rule. This PEP takes the position that this behavior is simple, obvious, usually the behavior we want, and should be the default behavior for dicts. This means that dict union is not commutative; in general `d | e != e | d`.

上述所有的行为都遵循同样的规则。本PEP的立场是，这种行为是简单、明显的，通常是我们想要的行为，并且应该是dict的默认行为。这意味着dict联盟不是换元的；一般来说，`d | e != e | d`。

Similarly, the *iteration order* of the key-value pairs in the dictionary will follow the same semantics as the examples above, with each newly added key (and its value) being appended to the current sequence.

同样，字典中键值对的*迭代顺序*将遵循与上面的例子相同的语义，每一个新添加的键（和它的值）都被追加到当前的序列中。

> 因为Python中字典的实现基于散列，随着字典容量的扩充很可能会触发重建机制，进而改变字典的迭代顺序，所以应该将字典视为一种无序容器，不能依赖于字典的迭代顺序，所以这里不应对字典的迭代顺序纠结。想了解原因和散列结构的，可以阅读[**Python学习笔记20：字典与集合**](https://blog.icexmoon.xyz/archives/93.html)，译者注。

# [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id18)

定义

Dict union will return a new `dict` consisting of the left operand merged with the right operand, each of which must be a `dict` (or an instance of a `dict` subclass). If a key appears in both operands, the last-seen value (i.e. that from the right-hand operand) wins:

Dict 合并运算将返回一个由左操作数和右操作数组成的新的dict，每个操作数必须是一个dict（或dict子类的一个实例）。如果一个键出现在两个操作数中，最后看到的值（即来自右侧操作数的值）获胜。

```python
>>> d = {'spam': 1, 'eggs': 2, 'cheese': 3}
>>> e = {'cheese': 'cheddar', 'aardvark': 'Ethel'}
>>> d | e
{'spam': 1, 'eggs': 2, 'cheese': 'cheddar', 'aardvark': 'Ethel'}
>>> e | d
{'cheese': 3, 'aardvark': 'Ethel', 'spam': 1, 'eggs': 2}
```

The augmented assignment version operates in-place:

增强的赋值版本在原地操作：

```python
>>> d |= e
>>> d
{'spam': 1, 'eggs': 2, 'cheese': 'cheddar', 'aardvark': 'Ethel'}
```

Augmented assignment behaves identically to the `update` method called with a single positional argument, so it also accepts anything implementing the Mapping protocol (more specifically, anything with the `keys` and `__getitem__` methods) or iterables of key-value pairs. This is analogous to `list +=` and `list.extend`, which accept any iterable, not just lists. Continued from above:

增强赋值的行为与使用单一位置参数调用的 `update` 方法相同，所以它也接受任何实现 Mapping 协议的东西（更具体地说，任何具有 `keys` 和 `__getitem__` 方法的东西）或键值对的迭代物。这类似于 `list +=` 和 `list.extend`，它们接受任何可迭代的东西，而不仅仅是列表。继续上面的内容：

```python
>>> d | [('spam', 999)]
Traceback (most recent call last):
  ...
TypeError: can only merge dict (not "list") to dict

>>> d |= [('spam', 999)]
>>> d
{'spam': 999, 'eggs': 2, 'cheese': 'cheddar', 'aardvark': 'Ethel'}
```

When new keys are added, their order matches their order within the right-hand mapping, if any exists for its type.

当新的键被添加时，它们的顺序与它们在右侧映射中的顺序一致，如果它的类型存在的话。

# [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id19)

参考实现

One of the authors has [written a C implementation](https://github.com/python/cpython/pull/12088).

其中一位作者已经[写了一个C实现](https://github.com/python/cpython/pull/12088)。

An *approximate* pure-Python implementation is:

一个*近似*的纯Python实现是：

```python
def __or__(self, other):
    if not isinstance(other, dict):
        return NotImplemented
    new = dict(self)
    new.update(other)
    return new

def __ror__(self, other):
    if not isinstance(other, dict):
        return NotImplemented
    new = dict(other)
    new.update(self)
    return new

def __ior__(self, other):
    dict.update(self, other)
    return self
```

# [Major Objections](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id20)

[主要反对意见](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id20)

## [Dict Union Is Not Commutative](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id21)

字典的合并运算不是换元的

Union is commutative, but dict union will not be (`d | e != e | d`).

合并运算是换元的，但字典合并运算不是(`d | e != e | d`)。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id22)

回复

There is precedent for non-commutative unions in Python:

在Python中，有非交换性合并运算的先例。

```python
>>> {0} | {False}
{0}
>>> {False} | {0}
{False}
```

While the results may be equal, they are distinctly different. In general, `a | b` is not the same operation as `b | a`.

虽然结果可能是相等的，但它们是明显不同的。一般来说，`a | b`与`b | a`不是同一个操作。

## [Dict Union Will Be Inefficient](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id23)

字典合并运算将是低效的

Giving a pipe operator to mappings is an invitation to writing code that doesn't scale well. Repeated dict union is inefficient: `d | e | f | g | h` creates and destroys three temporary mappings.

给映射提供一个管道操作符是对写代码的一种诱惑，它不能很好地扩展。重复的dict合并操作是低效的：`d | e | f | g | h`创建并销毁了三个临时映射。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id24)

回复

The same argument applies to sequence concatenation.

同样的论点也适用于序列连接。

Sequence concatenation grows with the total number of items in the sequences, leading to O(N**2) (quadratic) performance. Dict union is likely to involve duplicate keys, so the temporary mappings will not grow as fast.

序列连接会随着序列中项目总数的增加而增长，导致O(N**2)（平方）的性能。Dict合并操作可能涉及重复的键，所以临时映射不会增长得那么快。

Just as it is rare for people to concatenate large numbers of lists or tuples, the authors of this PEP believe that it will be rare for people to merge large numbers of dicts. `collections.Counter` is a dict subclass that supports many operators, and there are no known examples of people having performance issues due to combining large numbers of Counters. Further, a survey of the standard library by the authors found no examples of merging more than two dicts, so this is unlikely to be a performance problem in practice... "Everything is fast for small enough N".

就像人们很少连接大量的列表或图元一样，本PEP的作者认为人们很少会合并大量的dict。`collections.Counter`是一个支持许多操作符的dict子类，而且没有已知的例子表明人们会因为合并大量的Counter而产生性能问题。此外，作者对标准库的调查没有发现合并两个以上的dict的例子，所以这在实践中不太可能成为一个性能问题...... "对于足够小的N来说，一切都很快速"。

If one expects to be merging a large number of dicts where performance is an issue, it may be better to use an explicit loop and in-place merging:

如果一个人预计要合并大量的数据，而性能又是一个问题，那么使用一个显式循环和就地合并可能会更好：

```python
new = {}
for d in many_dicts:
    new |= d
```

## [Dict Union Is Lossy](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id25)

字典合并操作是有损失的

Dict union can lose data (values may disappear); no other form of union is lossy.

Dict合并操作可能会丢失数据（值可能会消失）；没有其他形式的结合操作是有损失的。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id26)

回复

It isn't clear why the first part of this argument is a problem. `dict.update()` may throw away values, but not keys; that is expected behavior, and will remain expected behavior regardless of whether it is spelled as `update()` or `|`.

`dict.update()`可以丢弃值，但不能丢弃键；这是预期的行为，而且无论它被拼成`update()`还是`|`，都是预期的行为。

Other types of union are also lossy, in the sense of not being reversible; you cannot get back the two operands given only the union. `a | b == 365`... what are `a` and `b`?

其他类型的合并操作也是有损失的，也就是不可逆的；你不能只通过合并操作来取回两个操作数。`a | b == 365`... 哪个是 `a` 哪个是 `b`？

## [Only One Way To Do It](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id27)

只有一种方法可以做到

Dict union will violate the Only One Way koan from the Zen.

Dict合并操作将违反Python之禅的 "唯一途径 "原则。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id28)

回复

There is no such koan. "Only One Way" is a calumny about Python originating long ago from the Perl community.

没有这样的原则。"只有一条路 "是对 Python 的诽谤，很久以前来自 Perl 社区。

## [More Than One Way To Do It](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id29)

不止一种方法可以做到这一点

Okay, the Zen doesn't say that there should be Only One Way To Do It. But it does have a prohibition against allowing "more than one way to do it".

好吧，Python之禅并没有说应该有唯一的方法。但它确实禁止允许 "不止一种方法"。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id30)

回复

There is no such prohibition. The "Zen of Python" merely expresses a *preference* for "only one *obvious* way":

没有这样的禁条。Python之禅 "只是表达了对 "只有一种*明显的方式 "的*偏好：

```python
There should be one-- and preferably only one --obvious way to do
it.
```

The emphasis here is that there should be an obvious way to do "it". In the case of dict update operations, there are at least two different operations that we might wish to do:

这里强调的是，应该有一个明显的方法来做 "它"。在dict更新操作的情况下，至少有两种不同的操作是我们可能希望做的：

- *Update a dict in place*: The Obvious Way is to use the `update()` method. If this proposal is accepted, the `|=` augmented assignment operator will also work, but that is a side-effect of how augmented assignments are defined. Which you choose is a matter of taste.

  *就地更新一个dict*。明显的方法是使用`update()`方法。如果这个建议被接受，`|=`增强的赋值运算符也会起作用，但这只是在增强赋值被定义后提供了一种额外途径。你选择哪一个是一个品味的问题。

- *Merge two existing dicts into a third, new dict*: This PEP proposes that the Obvious Way is to use the `|` merge operator.

  *将两个现有的数据合并成第三个新的数据*。本PEP提出，最明显的方法是使用|合并操作符。

In practice, this preference for "only one way" is frequently violated in Python. For example, every `for` loop could be re-written as a `while` loop; every `if` block could be written as an `if`/ `else` block. List, set and dict comprehensions could all be replaced by generator expressions. Lists offer no fewer than five ways to implement concatenation:

在实践中，这种对 "只有一种方式 "的偏好在Python中经常被违反。例如，每个 `for` 循环都可以被改写成 `while`循环；每个 `if`块都可以写成 `if`/`else`块。List、set和dict的表达式都可以被生成器表达式所取代。列表提供了不少于五种实现连接的方法：

- Concatenation operator: `a + b`
- In-place concatenation operator: `a += b`
- Slice assignment: `a[len(a):] = b`
- Sequence unpacking: `[*a, *b]`
- Extend method: `a.extend(b)`

We should not be too strict about rejecting useful functionality because it violates "only one way".

我们不应过于严格地拒绝有用的功能，只是因为它违反了 "只有一种方式"。

## [Dict Union Makes Code Harder To Understand](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id31)

字典合并操作使得代码更难以理解

Dict union makes it harder to tell what code means. To paraphrase the objection rather than quote anyone in specific: "If I see `spam | eggs`, I can't tell what it does unless I know what `spam` and `eggs` are".

Dict合并操作使人们更难分辨代码的含义。解释一下这个反对意见，而不是具体引用谁的话："如果我看到`spam | eggs`，我无法知道它的作用，除非我知道`spam`和`eggs`是什么"。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id32)

回复

This is very true. But it is equally true today, where the use of the `|` operator could mean any of:

这是非常正确的。但今天也同样如此，在这里，使用`|`运算符可能意味着任何：

- `int`/`bool` bitwise-or

  `int`/`bool`位或

- `set`/`frozenset` union

  `set`/`frozenset`并集

- any other overloaded operation

  任何其他被重载了的操作

Adding dict union to the set of possibilities doesn't seem to make it *harder* to understand the code. No more work is required to determine that `spam` and `eggs` are mappings than it would take to determine that they are sets, or integers. And good naming conventions will help:

将dict 合并操作添加到可能性的集合中，似乎并没有使代码更难理解。确定 `spam`和 `eggs`是映射的工作并不比确定它们是集合或整数的工作多。好的命名规则会有帮助：

```python
flags |= WRITEABLE  # Probably numeric bitwise-or.
DO_NOT_RUN = WEEKENDS | HOLIDAYS  # Probably set union.
settings = DEFAULT_SETTINGS | user_settings | workspace_settings  # Probably dict union.
```

## [What About The Full `set` API?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id33)

[关于完整的`set`API？](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id33)

dicts are "set like", and should support the full collection of set operators: `|`, `&`, `^`, and `-`.

dicts是 "类似于集合 "的，应该支持合并运算符的全部合并运算：`|`, `&`, `^`, 和 `-`.

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id34)

回应

This PEP does not take a position on whether dicts should support the full collection of set operators, and would prefer to leave that for a later PEP (one of the authors is interested in drafting such a PEP). For the benefit of any later PEP, a brief summary follows.

本PEP并不对dict是否应该支持全部的合并运算符进行表态，而是希望把这个问题留给以后的PEP（作者之一有兴趣起草这样一个PEP）。为了方便以后的PEP，下面是一个简短的总结。

Set symmetric difference (`^`) is obvious and natural. For example, given two dicts:

集合对称差分（`^`）是很明显且自然的。例如，给定两个数据：

```python
d1 = {"spam": 1, "eggs": 2}
d2 = {"ham": 3, "eggs": 4}
```

the symmetric difference `d1 ^ d2` would be `{"spam": 1, "ham": 3}`.

对称差值 `d1 ^ d2`将是 `{"spam": 1, "ham": 3}`.

Set difference (`-`) is also obvious and natural, and an earlier version of this PEP included it in the proposal. Given the dicts above, we would have `d1 - d2` be `{"spam": 1}` and `d2 - d1` be `{"ham": 3}`.

集合差分（`-`）也是很明显和自然的，本PEP的早期版本将其纳入建议。考虑到上面的数据，我们会让`d1 - d2`成为`{"spam": 1}`，`d2 - d1`为`{"ham": 3}`.

Set intersection (`&`) is a bit more problematic. While it is easy to determine the intersection of *keys* in two dicts, it is not clear what to do with the *values*. Given the two dicts above, it is obvious that the only key of `d1 & d2` must be `"eggs"`. "Last seen wins", however, has the advantage of consistency with other dict operations (and the proposed union operators).

集合相交（`&`）的问题更大一些。虽然很容易确定两个数据集的键的交集，但不清楚该如何处理这些值。鉴于上面的两个数据集，很明显，`d1 & d2`的唯一键必须是 `"eggs"`。然而，"最后看到的胜出者 "具有与其他dict操作（以及提议的合并操作）一致的优势。

## [What About `Mapping` And `MutableMapping`?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id35)

如何处理`Mapping`和`MutableMapping`？

`collections.abc.Mapping` and `collections.abc.MutableMapping` should define `|` and `|=`, so subclasses could just inherit the new operators instead of having to define them.

`collections.abc.Mapping`和`collections.abc.MutableMapping`应该定义`|`和`|=`，所以子类可以直接继承新的操作符，而不必定义它们。

### [Response](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id36)

回应

There are two primary reasons why adding the new operators to these classes would be problematic:

有两个主要的原因，将新的运算符添加到这些类中会有问题：

- Currently, neither defines a `copy` method, which would be necessary for `|` to create a new instance.

  目前，两者都没有定义`copy`方法，而那是使用`|`创建新实例的必要条件。

- Adding `|=` to `MutableMapping` (or a `copy` method to `Mapping`) would create compatibility issues for virtual subclasses.

  在`MutableMapping`中添加`|=`（或在`Mapping`中添加`copy`方法）会给虚拟子类带来兼容性问题。

# [Rejected Ideas](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id37)

被拒绝的想法

## [Rejected Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id38)

拒绝的语义

There were at least four other proposed solutions for handling conflicting keys. These alternatives are left to subclasses of dict.

至少还有其他四种处理冲突键的建议方案。这些方案留给dict的子类。

### [Raise](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id39)

作为异常抛出

It isn't clear that this behavior has many use-cases or will be often useful, but it will likely be annoying as any use of the dict union operator would have to be guarded with a `try`/`except` clause.

目前还不清楚这种行为是否有很多用处，或者是否经常有用，但它可能会很烦人，因为任何对dict合并运算符的使用都必须用`try`/`except`子句来嵌套。

### [Add The Values (As Counter Does, with `+`)](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id40)

值相加（如Counter用`+`做的那样）

Too specialised to be used as the default behavior.

太过特殊，不能作为默认行为使用。

### [Leftmost Value (First-Seen) Wins](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id41)

让左边的值（首先看到的）赢

It isn't clear that this behavior has many use-cases. In fact, one can simply reverse the order of the arguments:

这种行为是否有很多用处并不清楚。事实上，我们可以简单地扭转参数的顺序：

```python
d2 | d1  # d1 merged with d2, keeping existing values in d1
```

### [Concatenate Values In A List](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id42)

连接列表中的值

This is likely to be too specialised to be the default. It is not clear what to do if the values are already lists:

这可能太特殊了，不能作为默认情况。如果值已经是列表，不清楚该怎么做。

```python
{'a': [1, 2]} | {'a': [3, 4]}
```

Should this give `{'a': [1, 2, 3, 4]}` or `{'a': [[1, 2], [3, 4]]}`?

结果应该是 `{'a': [1, 2, 3, 4]}` 还是 `{'a': [[1, 2], [3, 4]]}`?

## [Rejected Alternatives](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id43)

被拒绝的替代方案

### [Use The Addition Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id44)

使用加法运算符

This PEP originally started life as a proposal for dict addition, using the `+` and `+=` operator. That choice proved to be exceedingly controversial, with many people having serious objections to the choice of operator. For details, see [previous versions](https://github.com/python/peps/commits/master/pep-0584.rst) of the PEP and the mailing list [discussions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#discussions).

这个PEP最初是作为一个使用 `+`和 `+=`操作符的字典加法的提案。这一选择被证明是非常有争议的，许多人对这个操作符的选择有强烈的反对意见。详情请见PEP的[先前版本](https://github.com/python/peps/commits/master/pep-0584.rst)和邮件列表[讨论](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#discussions)。

### [Use The Left Shift Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id45)

使用左移运算符

The `<<` operator didn't seem to get much support on Python-Ideas, but no major objections either. Perhaps the strongest objection was Chris Angelico's comment

在Python-Ideas上，`<<`操作符似乎没有得到多少支持，但也没有大的反对意见。也许最强烈的反对意见是Chris Angelico的评论

> The "cuteness" value of abusing the operator to indicate information flow got old shortly after C++ did it.
>
> 滥用运算符来表示信息流的 "可爱 "价值在C++做完后不久就过时了。

### [Use A New Left Arrow Operator](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id46)

使用一个新的左箭头运算符

Another suggestion was to create a new operator `<-`. Unfortunately this would be ambiguous, `d <- e` could mean `d merge e` or `d less-than minus e`.

另一个建议是创建一个新的操作符`<-`。不幸的是，这将会产生歧义，`d <- e`可能意味着`d merge e`或`d less-than minus e`。

### [Use A Method](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id47)

使用一个方法

A `dict.merged()` method would avoid the need for an operator at all. One subtlety is that it would likely need slightly different implementations when called as an unbound method versus as a bound method.

As an unbound method, the behavior could be similar to:

一个`dict.merged()`方法将完全避免对操作符的需求。一个微妙的问题是，当作为一个非绑定方法和一个绑定方法被调用时，它可能需要稍微不同的实现。

```python
def merged(cls, *mappings, **kw):
    new = cls()  # Will this work for defaultdict?
    for m in mappings:
        new.update(m)
    new.update(kw)
    return new
```

As a bound method, the behavior could be similar to:

作为一个绑定方法，其行为可能类似于：

```python
def merged(self, *mappings, **kw):
    new = self.copy()
    for m in mappings:
        new.update(m)
    new.update(kw)
    return new
```

#### [Advantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id48)

有点

- Arguably, methods are more discoverable than operators.

  可以说，方法比运算符更容易识别。

- The method could accept any number of positional and keyword arguments, avoiding the inefficiency of creating temporary dicts.

  方法可以接受任何数量的位置参数和关键字参数，避免了创建临时数据的低效率。

- Accepts sequences of `(key, value)` pairs like the `update` method.

  像`update`方法一样接受`(key, value)`对的序列。

- Being a method, it is easy to override in a subclass if you need alternative behaviors such as "first wins", "unique keys", etc.

  作为一个方法，如果你需要其他的行为，如 "first wins"、"unique keys "等，它很容易在子类中被覆盖。

#### [Disadvantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id49)

缺点

- Would likely require a new kind of method decorator which combined the behavior of regular instance methods and `classmethod`. It would need to be public (but not necessarily a builtin) for those needing to override the method. There is a [proof of concept](http://code.activestate.com/recipes/577030).

  这可能需要一种新的方法装饰器，结合常规实例方法和类方法的行为。对于那些需要覆盖该方法的人来说，它需要是公开的（但不一定是内置的）。这里有一个概念证明。

- It isn't an operator. Guido discusses [why operators are useful](https://mail.python.org/archives/list/python-ideas@python.org/message/52DLME5DKNZYFEETCTRENRNKWJ2B4DD5/). For another viewpoint, see [Nick Coghlan's blog post](https://www.curiousefficiency.org/posts/2019/03/what-does-x-equals-a-plus-b-mean.html).

  它不是一个操作符。Guido讨论了[为什么运算符是有用的](https://mail.python.org/archives/list/python-ideas@python.org/message/52DLME5DKNZYFEETCTRENRNKWJ2B4DD5/)。另一个观点，见Nick Coghlan的博文。

### [Use a Function](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id50)

使用一个函数

Instead of a method, use a new built-in function `merged()`. One possible implementation could be something like this:

用一个新的内置函数`merged()`来代替方法。一个可能的实现可以是这样的：

```python
def merged(*mappings, **kw):
    if mappings and isinstance(mappings[0], dict):
        # If the first argument is a dict, use its type.
        new = mappings[0].copy()
        mappings = mappings[1:]
    else:
        # No positional arguments, or the first argument is a
        # sequence of (key, value) pairs.
        new = dict()
    for m in mappings:
        new.update(m)
    new.update(kw)
    return new
```

An alternative might be to forgo the arbitrary keywords, and take a single keyword parameter that specifies the behavior on collisions:

另一种方法是放弃任意的关键字，而采取一个单一的关键字参数来指定碰撞的行为：

```python
def merged(*mappings, on_collision=lambda k, v1, v2: v2):
    # implementation left as an exercise to the reader
```

#### [Advantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id51)

优点：

- Most of the same advantages of the method solutions above.

  上述方法解决方案的大部分优点相同。

- Doesn't require a subclass to implement alternative behavior on collisions, just a function.

  不需要一个子类来实现碰撞时的替代行为，只需要一个函数。

#### [Disadvantages](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id52)

缺点：

- May not be important enough to be a builtin.

  可能没有重要到足以成为一个内建程序。

- Hard to override behavior if you need something like "first wins", without losing the ability to process arbitrary keyword arguments.

  如果你需要像 "first win"这样的行为，在不失去处理任意关键字参数的能力的情况下，很难重写行为。

# [Examples](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id53)

The authors of this PEP did a survey of third party libraries for dictionary merging which might be candidates for dict union.

This is a cursory list based on a subset of whatever arbitrary third-party packages happened to be installed on one of the authors' computers, and may not reflect the current state of any package. Also note that, while further (unrelated) refactoring may be possible, the rewritten version only adds usage of the new operators for an apples-to-apples comparison. It also reduces the result to an expression when it is efficient to do so.

## [IPython/zmq/ipkernel.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id54)

Before:

```python
aliases = dict(kernel_aliases)
aliases.update(shell_aliases)
```

After:

```python
aliases = kernel_aliases | shell_aliases
```

## [IPython/zmq/kernelapp.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id55)

Before:

```python
kernel_aliases = dict(base_aliases)
kernel_aliases.update({
    'ip' : 'KernelApp.ip',
    'hb' : 'KernelApp.hb_port',
    'shell' : 'KernelApp.shell_port',
    'iopub' : 'KernelApp.iopub_port',
    'stdin' : 'KernelApp.stdin_port',
    'parent': 'KernelApp.parent',
})
if sys.platform.startswith('win'):
    kernel_aliases['interrupt'] = 'KernelApp.interrupt'

kernel_flags = dict(base_flags)
kernel_flags.update({
    'no-stdout' : (
            {'KernelApp' : {'no_stdout' : True}},
            "redirect stdout to the null device"),
    'no-stderr' : (
            {'KernelApp' : {'no_stderr' : True}},
            "redirect stderr to the null device"),
})
```

After:

```python
kernel_aliases = base_aliases | {
    'ip' : 'KernelApp.ip',
    'hb' : 'KernelApp.hb_port',
    'shell' : 'KernelApp.shell_port',
    'iopub' : 'KernelApp.iopub_port',
    'stdin' : 'KernelApp.stdin_port',
    'parent': 'KernelApp.parent',
}
if sys.platform.startswith('win'):
    kernel_aliases['interrupt'] = 'KernelApp.interrupt'

kernel_flags = base_flags | {
    'no-stdout' : (
            {'KernelApp' : {'no_stdout' : True}},
            "redirect stdout to the null device"),
    'no-stderr' : (
            {'KernelApp' : {'no_stderr' : True}},
            "redirect stderr to the null device"),
}
```

## [matplotlib/backends/backend_svg.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id56)

Before:

```python
attrib = attrib.copy()
attrib.update(extra)
attrib = attrib.items()
```

After:

```python
attrib = (attrib | extra).items()
```

## [matplotlib/delaunay/triangulate.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id57)

Before:

```python
edges = {}
edges.update(dict(zip(self.triangle_nodes[border[:,0]][:,1],
             self.triangle_nodes[border[:,0]][:,2])))
edges.update(dict(zip(self.triangle_nodes[border[:,1]][:,2],
             self.triangle_nodes[border[:,1]][:,0])))
edges.update(dict(zip(self.triangle_nodes[border[:,2]][:,0],
             self.triangle_nodes[border[:,2]][:,1])))
```

Rewrite as:

```python
edges = {}
edges |= zip(self.triangle_nodes[border[:,0]][:,1],
             self.triangle_nodes[border[:,0]][:,2])
edges |= zip(self.triangle_nodes[border[:,1]][:,2],
             self.triangle_nodes[border[:,1]][:,0])
edges |= zip(self.triangle_nodes[border[:,2]][:,0],
             self.triangle_nodes[border[:,2]][:,1])
```

## [matplotlib/legend.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id58)

Before:

```python
hm = default_handler_map.copy()
hm.update(self._handler_map)
return hm
```

After:

```python
return default_handler_map | self._handler_map
```

## [numpy/ma/core.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id59)

Before:

```python
_optinfo = {}
_optinfo.update(getattr(obj, '_optinfo', {}))
_optinfo.update(getattr(obj, '_basedict', {}))
if not isinstance(obj, MaskedArray):
    _optinfo.update(getattr(obj, '__dict__', {}))
```

After:

```python
_optinfo = {}
_optinfo |= getattr(obj, '_optinfo', {})
_optinfo |= getattr(obj, '_basedict', {})
if not isinstance(obj, MaskedArray):
    _optinfo |= getattr(obj, '__dict__', {})
```

## [praw/internal.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id60)

Before:

```python
data = {'name': six.text_type(user), 'type': relationship}
data.update(kwargs)
```

After:

```python
data = {'name': six.text_type(user), 'type': relationship} | kwargs
```

## [pygments/lexer.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id61)

Before:

```python
kwargs.update(lexer.options)
lx = lexer.__class__(**kwargs)
```

After:

```python
lx = lexer.__class__(**(kwargs | lexer.options))
```

## [requests/sessions.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id62)

Before:

```python
merged_setting = dict_class(to_key_val_list(session_setting))
merged_setting.update(to_key_val_list(request_setting))
```

After:

```python
merged_setting = dict_class(to_key_val_list(session_setting)) | to_key_val_list(request_setting)
```

## [sphinx/domains/__init__.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id63)

Before:

```python
self.attrs = self.known_attrs.copy()
self.attrs.update(attrs)
```

After:

```python
self.attrs = self.known_attrs | attrs
```

## [sphinx/ext/doctest.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id64)

Before:

```python
new_opt = code[0].options.copy()
new_opt.update(example.options)
example.options = new_opt
```

After:

```python
example.options = code[0].options | example.options
```

## [sphinx/ext/inheritance_diagram.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id65)

Before:

```python
n_attrs = self.default_node_attrs.copy()
e_attrs = self.default_edge_attrs.copy()
g_attrs.update(graph_attrs)
n_attrs.update(node_attrs)
e_attrs.update(edge_attrs)
```

After:

```python
g_attrs |= graph_attrs
n_attrs = self.default_node_attrs | node_attrs
e_attrs = self.default_edge_attrs | edge_attrs
```

## [sphinx/highlighting.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id66)

Before:

```python
kwargs.update(self.formatter_args)
return self.formatter(**kwargs)
```

After:

```python
return self.formatter(**(kwargs | self.formatter_args))
```

## [sphinx/quickstart.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id67)

Before:

```python
d2 = DEFAULT_VALUE.copy()
d2.update(dict(("ext_"+ext, False) for ext in EXTENSIONS))
d2.update(d)
d = d2
```

After:

```python
d = DEFAULT_VALUE | dict(("ext_"+ext, False) for ext in EXTENSIONS) | d
```

## [sympy/abc.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id68)

Before:

```python
clash = {}
clash.update(clash1)
clash.update(clash2)
return clash1, clash2, clash
```

After:

```python
return clash1, clash2, clash1 | clash2
```

## [sympy/parsing/maxima.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id69)

Before:

```python
dct = MaximaHelpers.__dict__.copy()
dct.update(name_dict)
obj = sympify(str, locals=dct)
```

After:

```python
obj = sympify(str, locals=MaximaHelpers.__dict__|name_dict)
```

## [sympy/printing/ccode.py and sympy/printing/fcode.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id70)

Before:

```python
self.known_functions = dict(known_functions)
userfuncs = settings.get('user_functions', {})
self.known_functions.update(userfuncs)
```

After:

```python
self.known_functions = known_functions | settings.get('user_functions', {})
```

## [sympy/utilities/runtests.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id71)

Before:

```python
globs = globs.copy()
if extraglobs is not None:
    globs.update(extraglobs)
```

After:

```python
globs = globs | (extraglobs if extraglobs is not None else {})
```

The above examples show that sometimes the `|` operator leads to a clear increase in readability, reducing the number of lines of code and improving clarity. However other examples using the `|` operator lead to long, complex single expressions, possibly well over the [PEP 8](https://www.python.org/dev/peps/pep-0008) maximum line length of 80 columns. As with any other language feature, the programmer should use their own judgement about whether `|` improves their code.

# [Related Discussions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id72)

相关讨论

Mailing list threads (this is by no means an exhaustive list):

- [Dict joining using + and +=](https://mail.python.org/archives/list/python-ideas@python.org/thread/BHIJX6MHGMMD3S6D7GVTPZQL4N5V7T42/)
- [PEP: Dict addition and subtraction](https://mail.python.org/archives/list/python-ideas@python.org/thread/KLDQAPOIJEANCKYCHQZ536WHQ45I6UVW/)
- [PEP 584: Add + and += operators to the built-in dict class.](https://mail.python.org/archives/list/python-ideas@python.org/thread/W2FCSC3JDA7NUBXAVSTVCUDEGAKWWPTH/)
- [Moving PEP 584 forward (dict + and += operators)](https://mail.python.org/archives/list/python-ideas@python.org/thread/SWBLMTNQXNL3O5LN3327IYNPFIL2QSH5/)
- [PEP 584: Add Union Operators To dict](https://mail.python.org/archives/list/python-dev@python.org/thread/TTIKCDIPC2CDHX23Y57CPHDSVYOWCCER/)
- [Accepting PEP 584: Add Union Operators To dict](https://mail.python.org/archives/list/python-dev@python.org/thread/6KT2KIOTYXMDCD2CCAOLOI7LUGTN6MBS)

[Ticket on the bug tracker](https://bugs.python.org/issue36144)

Merging two dictionaries in an expression is a frequently requested feature. For example:

https://stackoverflow.com/questions/38987/how-to-merge-two-dictionaries-in-a-single-expression

https://stackoverflow.com/questions/1781571/how-to-concatenate-two-dictionaries-to-create-a-new-one-in-python

https://stackoverflow.com/questions/6005066/adding-dictionaries-together-python

Occasionally people request alternative behavior for the merge:

https://stackoverflow.com/questions/1031199/adding-dictionaries-in-python

https://stackoverflow.com/questions/877295/python-dict-add-by-valuedict-2

...including one proposal to treat dicts as [sets of keys](https://mail.python.org/archives/list/python-ideas@python.org/message/YY3KZZGEX6VEFX5QZJ33P7NTTXGPZQ7N/).

[Ian Lee's proto-PEP](https://lwn.net/Articles/635444/), and [discussion](https://lwn.net/Articles/635397/) in 2015. Further discussion took place on [Python-Ideas](https://mail.python.org/archives/list/python-ideas@python.org/thread/43OZV3MR4XLFRPCI27I7BB6HVBD25M2E/).

(Observant readers will notice that one of the authors of this PEP was more skeptical of the idea in 2015.)

Adding [a full complement of operators to dicts](https://mail.python.org/archives/list/python-ideas@python.org/thread/EKWMDJKMVOJCOROQVHJFQX7W2L55I5RA/).

[Discussion on Y-Combinator](https://news.ycombinator.com/item?id=19314646).

https://treyhunner.com/2016/02/how-to-merge-dictionaries-in-python/

https://code.tutsplus.com/tutorials/how-to-merge-two-python-dictionaries--cms-26230

In direct response to an earlier draft of this PEP, Serhiy Storchaka raised [a ticket in the bug tracker](https://bugs.python.org/issue36431) to replace the `copy(); update()` idiom with dict unpacking.

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md#id73)

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

Source: https://github.com/python/peps/blob/master/pep-0584.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。