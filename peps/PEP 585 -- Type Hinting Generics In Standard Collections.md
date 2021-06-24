# PEP 585 -- Type Hinting Generics In Standard Collections

PEP 585 -- 标准集合中的类型提示泛型

> 原文地址：<https://www.python.org/dev/peps/pep-0585/>

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 585                                                          |
| Title:          | Type Hinting Generics In Standard Collections                |
| Author:         | Łukasz Langa <lukasz at python.org>                          |
| Discussions-To: | Typing-Sig <[typing-sig at python.org](mailto:typing-sig@python.org?subject=PEP 585)> |
| Status:         | Accepted                                                     |
| Type:           | Standards Track                                              |
| Created:        | 03-Mar-2019                                                  |
| Python-Version: | 3.9                                                          |
| Resolution:     | https://mail.python.org/archives/list/python-dev@python.org/thread/HW2NFOEMCVCTAFLBLC3V7MLM6ZNMKP42/ |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0585/#abstract)
- [Rationale and Goals](https://www.python.org/dev/peps/pep-0585/#rationale-and-goals)
- [Terminology](https://www.python.org/dev/peps/pep-0585/#terminology)
- [Backwards compatibility](https://www.python.org/dev/peps/pep-0585/#backwards-compatibility)
- Implementation
  - [Parameters to generics are available at runtime](https://www.python.org/dev/peps/pep-0585/#parameters-to-generics-are-available-at-runtime)
  - [Forward compatibility](https://www.python.org/dev/peps/pep-0585/#forward-compatibility)
- [Reference implementation](https://www.python.org/dev/peps/pep-0585/#reference-implementation)
- Rejected alternatives
  - [Do nothing](https://www.python.org/dev/peps/pep-0585/#do-nothing)
  - [Generics erasure](https://www.python.org/dev/peps/pep-0585/#generics-erasure)
  - [Disallowing instantiation of parameterized types](https://www.python.org/dev/peps/pep-0585/#disallowing-instantiation-of-parameterized-types)
  - [Making `isinstance(obj, list[str\])` perform a check ignoring generics](https://www.python.org/dev/peps/pep-0585/#making-isinstance-obj-list-str-perform-a-check-ignoring-generics)
  - [Making `isinstance(obj, list[str\])` perform a runtime type check](https://www.python.org/dev/peps/pep-0585/#making-isinstance-obj-list-str-perform-a-runtime-type-check)
  - [Naming the type `GenericType` instead of `GenericAlias`](https://www.python.org/dev/peps/pep-0585/#naming-the-type-generictype-instead-of-genericalias)
- [Note on the initial draft](https://www.python.org/dev/peps/pep-0585/#note-on-the-initial-draft)
- [Acknowledgments](https://www.python.org/dev/peps/pep-0585/#acknowledgments)
- [Copyright](https://www.python.org/dev/peps/pep-0585/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0585/#id2)

摘要

Static typing as defined by PEPs 484, 526, 544, 560, and 563 was built incrementally on top of the existing Python runtime and constrained by existing syntax and runtime behavior. This led to the existence of a duplicated collection hierarchy in the `typing` module due to generics (for example `typing.List` and the built-in `list`).

由 PEPs 484、526、544、560 和 563 定义的静态类型是在现有的 Python 运行时之上逐步建立的，并受到现有语法和运行时行为的限制。这导致了在 `typing` 模块中由于泛型（例如`typing.List`和内置`list`）而存在重复的集合层次结构。

This PEP proposes to enable support for the generics syntax in all standard collections currently available in the `typing` module.

本PEP提议在当前`typing`模块中的所有标准集合中启用对泛型语法的支持。

# [Rationale and Goals](https://www.python.org/dev/peps/pep-0585/#id3)

理由和目的

This change removes the necessity for a parallel type hierarchy in the `typing` module, making it easier for users to annotate their programs and easier for teachers to teach Python.

这一变化消除了在 `typing` 模块中建立平行类型层次的必要性，使用户更容易注解他们的程序，也使教师更容易教授Python。

# [Terminology](https://www.python.org/dev/peps/pep-0585/#id4)

术语

Generic (n.) -- a type that can be parameterized, typically a container. Also known as a *parametric type* or a *generic type*. For example: `dict`.

泛型（n.） -- 一个可以被参数化的类型，通常是一个容器。也被称为*可参数化类型*或*泛型*。例如：`dict`。

parameterized generic -- a specific instance of a generic with the expected types for container elements provided. Also known as a *parameterized type*. For example: `dict[str, int]`.

参数化泛型 -- 一个泛型的具体实例，提供容器元素的预期类型。也被称为*已参数化类型*。例如：`dict[str, int]`。

# [Backwards compatibility](https://www.python.org/dev/peps/pep-0585/#id5)

向后兼容

Tooling, including type checkers and linters, will have to be adapted to recognize standard collections as generics.

工具，包括类型检查器和锁定器，将必须进行调整，以识别标准集合为泛型。

On the source level, the newly described functionality requires Python 3.9. For use cases restricted to type annotations, Python files with the "annotations" future-import (available since Python 3.7) can parameterize standard collections, including builtins. To reiterate, that depends on the external tools understanding that this is valid.

在源代码层面，新描述的功能需要Python 3.9。对于仅限于类型注释的用例，带有 "annotations" future-import 的 Python 文件 (从 Python 3.7 开始可用) 可以对标准集合进行参数化，包括内置的。重申一下，这是否有效取决于外部工具的理解。

# [Implementation](https://www.python.org/dev/peps/pep-0585/#id6)

实现

Starting with Python 3.7, when `from __future__ import annotations` is used, function and variable annotations can parameterize standard collections directly. Example:

从Python 3.7开始，当`from __future__ import annotations`被使用时，函数和变量注释可以直接给标准集合设置参数。例子。

```python
from __future__ import annotations

def find(haystack: dict[str, list[int]]) -> int:
    ...
```

Usefulness of this syntax before [PEP 585](https://www.python.org/dev/peps/pep-0585) is limited as external tooling like Mypy does not recognize standard collections as generic. Moreover, certain features of typing like type aliases or casting require putting types outside of annotations, in runtime context. While these are relatively less common than type annotations, it's important to allow using the same type syntax in all contexts. This is why starting with Python 3.9, the following collections become generic using `__class_getitem__()` to parameterize contained types:

在PEP 585之前，这种语法的实用性是有限的，因为像Mypy这样的外部工具不承认标准集合可以作为泛型。此外，类型化的某些特征，如类型别名或类型转换，需要将类型放在注解之外，在运行时上下文中。虽然这些相对来说比类型注解要少，但允许在所有上下文中使用相同的类型语法是很重要的。这就是为什么从 Python 3.9 开始，以下集合使用 `__class_getitem__()` 参数化所包含的类型而成为泛型：

- `tuple` # typing.Tuple
- `list` # typing.List
- `dict` # typing.Dict
- `set` # typing.Set
- `frozenset` # typing.FrozenSet
- `type` # typing.Type
- `collections.deque`
- `collections.defaultdict`
- `collections.OrderedDict`
- `collections.Counter`
- `collections.ChainMap`
- `collections.abc.Awaitable`
- `collections.abc.Coroutine`
- `collections.abc.AsyncIterable`
- `collections.abc.AsyncIterator`
- `collections.abc.AsyncGenerator`
- `collections.abc.Iterable`
- `collections.abc.Iterator`
- `collections.abc.Generator`
- `collections.abc.Reversible`
- `collections.abc.Container`
- `collections.abc.Collection`
- `collections.abc.Callable`
- `collections.abc.Set` # typing.AbstractSet
- `collections.abc.MutableSet`
- `collections.abc.Mapping`
- `collections.abc.MutableMapping`
- `collections.abc.Sequence`
- `collections.abc.MutableSequence`
- `collections.abc.ByteString`
- `collections.abc.MappingView`
- `collections.abc.KeysView`
- `collections.abc.ItemsView`
- `collections.abc.ValuesView`
- `contextlib.AbstractContextManager` # typing.ContextManager
- `contextlib.AbstractAsyncContextManager` # typing.AsyncContextManager
- `re.Pattern` # typing.Pattern, typing.re.Pattern
- `re.Match` # typing.Match, typing.re.Match

Importing those from `typing` is deprecated. Due to [PEP 563](https://www.python.org/dev/peps/pep-0563) and the intention to minimize the runtime impact of typing, this deprecation will not generate DeprecationWarnings. Instead, type checkers may warn about such deprecated usage when the target version of the checked program is signalled to be Python 3.9 or newer. It's recommended to allow for those warnings to be silenced on a project-wide basis.

从 `typing` 中导入这些内容是被过时的。由于 [PEP 563](https://www.python.org/dev/peps/pep-0563)，以及将类型化的运行时影响降到最低的意图，这种废弃将不会产生DeprecationWarnings。相反，当被检查程序的目标版本被提示为 Python 3.9 或更新版本时，类型检查器可能会对这种废弃的用法发出警告。建议允许在整个项目范围内对这些警告进行沉默。

The deprecated functionality will be removed from the `typing` module in the first Python version released 5 years after the release of Python 3.9.0.

在Python 3.9.0发布5年后的第一个Python版本中，废弃的功能将从`typing`模块中删除。

## [Parameters to generics are available at runtime](https://www.python.org/dev/peps/pep-0585/#id7)

泛型的参数在运行时是可用的

Preserving the generic type at runtime enables introspection of the type which can be used for API generation or runtime type checking. Such usage is already present in the wild.

在运行时保留泛型，可以对类型进行内省，这可以用于API生成或运行时类型检查。这样的用法已经广泛出现了。

Just like with the `typing` module today, the parameterized generic types listed in the previous section all preserve their type parameters at runtime:

就像现在的 `typing` 模块一样，上一节中列出的参数化泛型都在运行时保留其类型参数。

```python
>>> list[str]
list[str]
>>> tuple[int, ...]
tuple[int, ...]
>>> ChainMap[str, list[str]]
collections.ChainMap[str, list[str]]
```

This is implemented using a thin proxy type that forwards all method calls and attribute accesses to the bare origin type with the following exceptions:

这是用一个简单代理类型实现的，它将所有的方法调用和属性访问转发到基础原类型，但有以下例外：

- the `__repr__` shows the parameterized type;

  `__repr__`显示的是参数化的类型。

- the `__origin__` attribute points at the non-parameterized generic class;

  `__origin__`属性指向非参数化的泛型类。

- the `__args__` attribute is a tuple (possibly of length 1) of generic types passed to the original `__class_getitem__`;

  `__args__` 属性是一个传递给原始 `__class_getitem__` 的泛型的元组（可能长度为 1）。

- the `__parameters__` attribute is a lazily computed tuple (possibly empty) of unique type variables found in `__args__`;

  `__parameters__`属性是一个懒惰地计算出来的在`__args__`中找到的唯一类型变量的元组（可能是空的）。

- the `__getitem__` raises an exception to disallow mistakes like `dict[str][str]`. However it allows e.g. `dict[str, T][int]` and in that case returns `dict[str, int]`.

  `__getitem__`引发了一个异常，不允许出现像`dict[str][str]`的错误。然而，它允许例如`dict[str, T][int]`，在这种情况下返回`dict[str, int]`。

This design means that it is possible to create instances of parameterized collections, like:

这种设计意味着可以创建参数化集合的实例，比如：

```python
>>> l = list[str]()
[]
>>> list is list[str]
False
>>> list == list[str]
False
>>> list[str] == list[str]
True
>>> list[str] == list[int]
False
>>> isinstance([1, 2, 3], list[str])
TypeError: isinstance() arg 2 cannot be a parameterized generic
>>> issubclass(list, list[str])
TypeError: issubclass() arg 2 cannot be a parameterized generic
>>> isinstance(list[str], types.GenericAlias)
True
```

Objects created with bare types and parameterized types are exactly the same. The generic parameters are not preserved in instances created with parameterized types, in other words generic types erase type parameters during object creation.

用基础类型和参数化类型创建的对象是完全一样的。在用参数化类型创建的实例中不保留泛型参数，换句话说，泛型在创建对象时抹去了类型参数。

One important consequence of this is that the interpreter does **not** attempt to type check operations on the collection created with a parameterized type. This provides symmetry between:

这样做的一个重要结果是，解释器不会试图对用参数化类型创建的集合进行类型检查操作。这提供了两者之间的对称性：

```python
l: list[str] = []
```

and:

```python
l = list[str]()
```

For accessing the proxy type from Python code, it will be exported from the `types` module as `GenericAlias`.

为了从Python代码中访问代理类型，它将作为 `GenericAlias` 从`types`模块中导出。

Pickling or (shallow- or deep-) copying a `GenericAlias` instance will preserve the type, origin, attributes and parameters.

序列化或（浅层或深层）拷贝一个`GenericAlias`实例将保留其类型、来源、属性和参数。

## [Forward compatibility](https://www.python.org/dev/peps/pep-0585/#id8)

向前兼容

Future standard collections must implement the same behavior.

未来的标准集合必须实现相同的行为。

# [Reference implementation](https://www.python.org/dev/peps/pep-0585/#id9)

参考实现

A proof-of-concept or prototype [implementation](https://bugs.python.org/issue39481) exists.

已经存在一个概念验证或原型[**实现**](https://bugs.python.org/issue39481)。

# [Rejected alternatives](https://www.python.org/dev/peps/pep-0585/#id10)

被拒绝的替代方案

## [Do nothing](https://www.python.org/dev/peps/pep-0585/#id11)

什么都不做

Keeping the status quo forces Python programmers to perform book-keeping of imports from the `typing` module for standard collections, making all but the simplest annotations cumbersome to maintain. The existence of parallel types is confusing to newcomers (why is there both `list` and `List`?).

保持现状迫使 Python 程序员对从标准集合的`typing`模块中导入的数据进行记账，使得除了最简单的注解之外，所有的注解都难以维护。并行类型的存在让新手感到困惑 (为什么同时存在 `list` 和 `List`？)。

The above problems also don't exist in user-built generic classes which share runtime functionality and the ability to use them as generic type annotations. Making standard collections harder to use in type hinting from user classes hindered typing adoption and usability.

上述问题也不存在于用户自建的泛型类中，这些泛型类共享运行时功能和使用它们作为泛型注解的能力。使得标准集合在用户类的类型提示中更难使用，阻碍了类型的采用和可用性。

## [Generics erasure](https://www.python.org/dev/peps/pep-0585/#id12)

泛型擦除

It would be easier to implement `__class_getitem__` on the listed standard collections in a way that doesn't preserve the generic type, in other words:

换句话说，在列出的标准集合上实现 `__class_getitem__`，以不保留泛型的方式，会更容易。

```python
>>> list[str]
<class 'list'>
>>> tuple[int, ...]
<class 'tuple'>
>>> collections.ChainMap[str, list[str]]
<class 'collections.ChainMap'>
```

This is problematic as it breaks backwards compatibility: current equivalents of those types in the `typing` module **do** preserve the generic type:

这是有问题的，因为它破坏了向后的兼容性：当前在`typing`模块中的那些类型的等价物**确实**保留了泛型类型。

```python
>>> from typing import List, Tuple, ChainMap
>>> List[str]
typing.List[str]
>>> Tuple[int, ...]
typing.Tuple[int, ...]
>>> ChainMap[str, List[str]]
typing.ChainMap[str, typing.List[str]]
```

As mentioned in the "Implementation" section, preserving the generic type at runtime enables runtime introspection of the type which can be used for API generation or runtime type checking. Such usage is already present in the wild.

正如在 "实现 "一节中提到的，在运行时保留通用类型可以实现对类型的运行时内省，这可以用于API生成或运行时类型检查。这种用法已经广泛存在。

Additionally, implementing subscripts as identity functions would make Python less friendly to beginners. Say, if a user is mistakenly passing a list type instead of a list object to a function, and that function is indexing the received object, the code would no longer raise an error.

此外，把下标作为身份函数来实现会使 Python 对初学者不那么友好。比方说，如果一个用户错误地将一个列表类型而不是一个列表对象传递给一个函数，而该函数正在对接收的对象进行索引，那么代码将不再引发错误。

Today:

现在：

```python
>>> l = list
>>> l[-1]
TypeError: 'type' object is not subscriptable
```

With `__class_getitem__` as an identity function:

用`__class_getitem__`作为身份函数：

```python
>>> l = list
>>> l[-1]
list
```

The indexing being successful here would likely end up raising an exception at a distance, confusing the user.

这里的索引成功很可能最终会在远处引发一个异常，使用户感到困惑。

> 这里是在说明如果擦除了泛型类型，可能会导致不能及时识别这种情况下的下标滥用错误，译者注。

## [Disallowing instantiation of parameterized types](https://www.python.org/dev/peps/pep-0585/#id13)

不允许参数化类型的实例化

Given that the proxy type which preserves `__origin__` and `__args__` is mostly useful for runtime introspection purposes, we might have disallowed instantiation of parameterized types.

鉴于保留 `__origin__` 和 `__args__` 的代理类型对于运行时的内省来说是非常有用的，我们可能已经禁止了参数化类型的实例化。

In fact, forbidding instantiation of parameterized types is what the `typing` module does today for types which parallel builtin collections (instantiation of other parameterized types is allowed).

事实上，禁止参数化类型的实例化就是现在`typing`模块对平行于内建集合的类型所做的事情（其他参数化类型的实例化是允许的）。

The original reason for this decision was to discourage spurious parameterization which made object creation up to two orders of magnitude slower compared to the special syntax available for those builtin collections.

这个决定的最初原因是为了阻止虚假的参数化，因为与那些内建集合的特殊语法相比，参数化使得对象的创建速度慢了两个数量级。

This rationale is not strong enough to allow the exceptional treatment of builtins. All other parameterized types can be instantiated, including parallels of collections in the standard library. Moreover, Python allows for instantiation of lists using `list()` and some builtin collections don't provide special syntax for instantiation.

这个理由不足以允许对内置集合进行特殊处理。所有其它的参数化类型都可以被实例化，包括标准库中集合的平行部分。此外，Python 允许使用 `list()` 对列表进行实例化，而一些内置集合并不提供特殊的实例化语法。

## [Making `isinstance(obj, list[str])` perform a check ignoring generics](https://www.python.org/dev/peps/pep-0585/#id14)

让 `isinstance(obj, list[str])` 执行无视泛型的检查

An earlier version of this PEP suggested treating parameterized generics like `list[str]` as equivalent to their non-parameterized variants like `list` for purposes of `isinstance()` and `issubclass()`. This would be symmetrical to how `list[str]()` creates a regular list.

本 PEP 的早期版本建议，为了 `isinstance()` 和 `issubclass()` 的目的，将 `list[str]` 这样的参数化泛型视为等同于 `list` 这样的非参数化变体。这与 `list[str]()` 创建常规列表的方式是对称的。

This design was rejected because `isinstance()` and `issubclass()` checks with parameterized generics would read like element-by-element runtime type checks. The result of those checks would be surprising, for example:

这个设计被否决了，因为使用参数化泛型的 `isinstance()` 和 `issubclass()` 检查会像逐个元素的运行时类型检查。这些检查的结果会让人吃惊，比如说。

```python
>>> isinstance([1, 2, 3], list[str])
True
```

Note the object doesn't match the provided generic type but `isinstance()` still returns `True` because it only checks whether the object is a list.

注意这个对象不符合所提供的泛型类型，但是 `isinstance()` 仍然返回 `True`，因为它只检查对象是否是一个列表。

If a library is faced with a parameterized generic and would like to perform an `isinstance()` check using the base type, that type can be retrieved using the `__origin__` attribute on the parameterized generic.

如果一个库面临着一个参数化泛型，并希望使用基本类型进行`isinstance()`检查，可以使用参数化泛型上的`__origin__`属性来检索该类型。

## [Making `isinstance(obj, list[str])` perform a runtime type check](https://www.python.org/dev/peps/pep-0585/#id15)

使`isinstance(obj, list[str])`执行运行时的类型检查

This functionality requires iterating over the collection which is a destructive operation in some of them. This functionality would have been useful, however implementing the type checker within Python that would deal with complex types, nested type checking, type variables, string forward references, and so on is out of scope for this PEP.

这个功能需要在集合上进行迭代，这在某些集合中是一个破坏性的操作。这个功能本来是很有用的，但是在 Python 中实现类型检查器，处理复杂类型、嵌套类型检查、类型变量、字符串正向引用等等，已经超出了本 PEP 的范围。

## [Naming the type `GenericType` instead of `GenericAlias`](https://www.python.org/dev/peps/pep-0585/#id16)

将类型命名为`GenericType`而不是`GenericAlias`

We considered a different name for this type, but decided `GenericAlias` is better -- these aren't real types, they are aliases for the corresponding container type with some extra metadata attached.

我们考虑过这个类型的不同名称，但决定`GenericAlias`更好 -- 这些不是真正的类型，它们是相应容器类型的别名，附加了一些额外的元数据。

# [Note on the initial draft](https://www.python.org/dev/peps/pep-0585/#id17)

关于初稿的说明

An early version of this PEP discussed matters beyond generics in standard collections. Those unrelated topics were removed for clarity.

本PEP的早期版本讨论了标准集合中泛型以外的问题。为了清晰起见，这些不相关的主题被删除了。

# [Acknowledgments](https://www.python.org/dev/peps/pep-0585/#id18)

致谢

Thank you to Guido van Rossum for his work on Python, and the implementation of this PEP specifically.

感谢Guido van Rossum在Python上所做的工作，以及对这个PEP的具体实现。

# [Copyright](https://www.python.org/dev/peps/pep-0585/#id19)

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

Source: https://github.com/python/peps/blob/master/pep-0585.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。

