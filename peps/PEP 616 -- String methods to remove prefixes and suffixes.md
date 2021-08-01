# PEP 616 -- String methods to remove prefixes and suffixes

移除前缀和后缀的字符串方法

|                 |                                                 |
| :-------------- | ----------------------------------------------- |
| PEP:            | 616                                             |
| Title:          | String methods to remove prefixes and suffixes  |
| Author:         | Dennis Sweeney <sweeney.dennis650 at gmail.com> |
| Sponsor:        | Eric V. Smith <eric at trueblade.com>           |
| Status:         | Accepted                                        |
| Type:           | Standards Track                                 |
| Created:        | 19-Mar-2020                                     |
| Python-Version: | 3.9                                             |
| Post-History:   | 20-Mar-2020                                     |



------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#abstract)，概述
- [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#rationale)，动机
- [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#specification)，定义
- Motivating examples from the Python standard library，来自Python标准库的优化示例
  - [find_recursionlimit.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#find-recursionlimit-py)
  - [deccheck.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#deccheck-py)
  - [cookiejar.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#cookiejar-py)
  - [test_i18n.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#test-i18n-py)
- Rejected Ideas，被拒绝的想法
  - [Expand the lstrip and rstrip APIs](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#expand-the-lstrip-and-rstrip-apis)，扩展lstrip和rstrip的API
  - [Remove multiple copies of a prefix](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#remove-multiple-copies-of-a-prefix)，删除一个前缀的多个副本
  - [Raising an exception when not found](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#raising-an-exception-when-not-found)，未找到时引发异常
  - [Accepting a tuple of affixes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#accepting-a-tuple-of-affixes)，接受一个词缀的元组
  - [Alternative Method Names](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#alternative-method-names)，替代性方法名
- [How to Teach This](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#how-to-teach-this)，如何教学
- [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#reference-implementation)
- [History of Major revisions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#history-of-major-revisions)
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#references)
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#copyright)

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id15)

概述

This is a proposal to add two new methods, `removeprefix()` and `removesuffix()`, to the APIs of Python's various string objects. These methods would remove a prefix or suffix (respectively) from a string, if present, and would be added to Unicode `str` objects, binary `bytes` and `bytearray` objects, and `collections.UserString`.

这是一个建议，在Python的各种字符串对象的API中增加两个新方法，`removeprefix()`和`removesuffix()`。这些方法将从一个字符串中移除前缀或后缀(如果存在的话)，并将被添加到Unicode `str`对象，二进制的`bytes`和`bytearray`对象，以及`collections.UserString`。

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id16)

动机

There have been repeated issues on Python-Ideas [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#pyid) [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id10), Python-Dev [[4\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id11) [[5\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id12) [[6\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id13) [[7\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id14), the Bug Tracker, and StackOverflow [[8\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#confusion), related to user confusion about the existing `str.lstrip` and `str.rstrip` methods. These users are typically expecting the behavior of `removeprefix` and `removesuffix`, but they are surprised that the parameter for `lstrip` is interpreted as a set of characters, not a substring. This repeated issue is evidence that these methods are useful. The new methods allow a cleaner redirection of users to the desired behavior.

在Python-Ideas[[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#pyid) [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id10)、Python-Dev [[4\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id11) [[5\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id12) [[6\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id13) [[7\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id14)、Bug Tracker和StackOverflow[8]上反复出现的问题，与用户对现有`str.lstrip`和`str.rstrip`方法的困惑有关。这些用户通常期待着`removeprefix`和`removesuffix`的行为，但他们惊讶于`lstrip`的参数被解释为一组字符，而不是一个子串。这个重复的问题证明了这些方法是有用的。新的方法可以更干净地将用户重定向到所需的行为。

As another testimonial for the usefulness of these methods, several users on Python-Ideas [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#pyid) reported frequently including similar functions in their code for productivity. The implementation often contained subtle mistakes regarding the handling of the empty string, so a well-tested built-in method would be useful.

作为这些方法有用的另一个证明，Python-Ideas[[2]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#pyid)上的几个用户报告说，为了提高生产力，他们的代码中经常包括类似的函数。在处理空字符串方面，其实现经常包含一些微妙的错误，所以一个经过良好测试的内置方法将是非常有用的。

The existing solutions for creating the desired behavior are to either implement the methods as in the [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#specification) below, or to use regular expressions as in the expression `re.sub('^' + re.escape(prefix), '', s)`, which is less discoverable, requires a module import, and results in less readable code.

创建所需行为的现有解决方案是，要么像下面的规范那样实现这些方法，要么使用正则表达式，如表达式re.sub('^' + re.escape(prefix), '', s），这种方法不容易被发现，需要导入模块，而且代码的可读性较差。

# [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id17)

定义

The builtin `str` class will gain two new methods which will behave as follows when `type(self) is type(prefix) is type(suffix) is str`:

内建的`str`类将获得两个新的方法，当`type(self) is type(prefix) is type(suffix) is str`时，它们的行为如下。

```python
def removeprefix(self: str, prefix: str, /) -> str:
    if self.startswith(prefix):
        return self[len(prefix):]
    else:
        return self[:]

def removesuffix(self: str, suffix: str, /) -> str:
    # suffix='' should not call self[:-0].
    if suffix and self.endswith(suffix):
        return self[:-len(suffix)]
    else:
        return self[:]
```

When the arguments are instances of `str` subclasses, the methods should behave as though those arguments were first coerced to base `str` objects, and the return value should always be a base `str`.

当参数是`str`子类的实例时，这些方法的行为就像这些参数首先被胁迫为基础`str`对象一样，并且返回值应该总是一个基础`str`。

Methods with the corresponding semantics will be added to the builtin `bytes` and `bytearray` objects. If `b` is either a `bytes` or `bytearray` object, then `b.removeprefix()` and `b.removesuffix()` will accept any bytes-like object as an argument. The two methods will also be added to `collections.UserString`, with similar behavior.

具有相应语义的方法将被添加到内置的 `bytes` 和 `bytearray` 对象中。如果`b`是`bytes`或`bytearray`对象，那么`b.removeprefix()`和`b.removesuffix()`将接受任何byte类对象作为参数。这两个方法也将被添加到`collections.UserString`中，其行为类似。

# [Motivating examples from the Python standard library](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id18)

来自Python标准库的优化示例

The examples below demonstrate how the proposed methods can make code one or more of the following:

下面的例子展示了所提出的方法如何使代码成为以下一种或多种：

1. Less fragile:

   不那么脆弱：

   The code will not depend on the user to count the length of a literal.

   代码将不依赖于用户来计算字面的长度。

2. More performant:

   更具性能：

   The code does not require a call to the Python built-in `len` function nor to the more expensive `str.replace()` method.

   代码不需要调用Python内置的`len`函数或更昂贵的`str.replace()`方法。

3. More descriptive:

   更具描述性：

   The methods give a higher-level API for code readability as opposed to the traditional method of string slicing.

   相对于传统的字符串切片方法，这些方法给出了一个更高层次的API，以保证代码的可读性。

## [find_recursionlimit.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id19)

- Current:

  现在：

  ```python
  if test_func_name.startswith("test_"):
      print(test_func_name[5:])
  else:
      print(test_func_name)
  ```

- Improved:

  改进后：
  
  ```python
  print(test_func_name.removeprefix("test_"))
  ```

## [deccheck.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id20)

This is an interesting case because the author chose to use the `str.replace` method in a situation where only a prefix was intended to be removed.

这是一个有趣的案例，因为作者选择在只打算删除前缀的情况下使用str.replace方法。

- Current:

  现在：

  ```python
  if funcname.startswith("context."):
      self.funcname = funcname.replace("context.", "")
      self.contextfunc = True
  else:
      self.funcname = funcname
      self.contextfunc = False
  ```

- Improved:

  改进后

  ```python
  if funcname.startswith("context."):
      self.funcname = funcname.removeprefix("context.")
      self.contextfunc = True
  else:
      self.funcname = funcname
      self.contextfunc = False
  ```

- Arguably further improved:

  更进一步的改进：
  
  ```python
  self.contextfunc = funcname.startswith("context.")
  self.funcname = funcname.removeprefix("context.")
  ```

## [cookiejar.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id21)

- Current:

  现在：

  ```python
  def strip_quotes(text):
      if text.startswith('"'):
          text = text[1:] 
      if text.endswith('"'):
          text = text[:-1]
      return text
  ```

- Improved:

  改进后：
  
  ```python
  def strip_quotes(text):
      return text.removeprefix('"').removesuffix('"')
  ```

## [test_i18n.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id22)

- Current:

  现在：

  ```python
  creationDate = header['POT-Creation-Date']
  
  # peel off the escaped newline at the end of string
  if creationDate.endswith('\\n'):
      creationDate = creationDate[:-len('\\n')]
  ```

- Improved:

  改进后：
  
  ```python
  creationDate = header['POT-Creation-Date'].removesuffix('\\n')
  ```

There were many other such examples in the stdlib.

在标准库中还有许多类似的例子。

# [Rejected Ideas](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id23)

被拒绝的想法

## [Expand the lstrip and rstrip APIs](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id24)

扩展lstrip和rstrip的API

Because `lstrip` takes a string as its argument, it could be viewed as taking an iterable of length-1 strings. The API could, therefore, be generalized to accept any iterable of strings, which would be successively removed as prefixes. While this behavior would be consistent, it would not be obvious for users to have to call `'foobar'.lstrip(('foo',))` for the common use case of a single prefix.

因为`lstrip`需要一个字符串作为它的参数，它可以被看作是接受一个长度为1的字符串的迭代。因此，该API可以被泛化为接受任何字符串的迭代，这些字符串将作为前缀被连续地移除。虽然这种行为是一致的，但对于用户来说，如果为了一个前缀的常见使用情况而调用`'foobar'.lstrip(('foo',))`，那就不太明显。

> 这里的意思是将`lstrip`方法扩展为可以接受一个字符串组成的序列的参数，译者注。

## [Remove multiple copies of a prefix](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id25)

删除一个前缀的多个副本

This is the behavior that would be consistent with the aforementioned expansion of the `lstrip`/`rstrip` API -- repeatedly applying the function until the argument is unchanged. This behavior is attainable from the proposed behavior via by the following:

这是与前面提到的`lstrip`/`rstrip` API的扩展相一致的行为--重复应用函数直到参数不变。这种行为可以通过以下方式从提议的行为中实现。

```python
>>> s = 'Foo' * 100 + 'Bar'
>>> prefix = 'Foo'
>>> while s.startswith(prefix): s = s.removeprefix(prefix)
>>> s
'Bar'
```

## [Raising an exception when not found](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id26)

未找到时引发异常

There was a suggestion that `s.removeprefix(pre)` should raise an exception if `not s.startswith(pre)`. However, this does not match with the behavior and feel of other string methods. There could be `required=False` keyword added, but this violates the KISS principle.

有人建议`s.removeprefix(pre)`如果`not s.startswith(pre)`，应该引发一个异常。然而，这与其他字符串方法的行为和感觉不一致。可以添加required=False关键字，但这违反了KISS原则。

## [Accepting a tuple of affixes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id27)

接受一个词缀的元组

It could be convenient to write the `test_concurrent_futures.py` example above as `name.removesuffix(('Mixin', 'Tests', 'Test'))`, so there was a suggestion that the new methods be able to take a tuple of strings as an argument, similar to the `startswith()` API. Within the tuple, only the first matching affix would be removed. This was rejected on the following grounds:

将上面的`test_concurrent_futures.py`例子写成`name.removesuffix(('Mixin', 'Tests', 'Test'))`可能很方便，所以有人建议新方法能够接受一个字符串的元组作为参数，类似于`startswith()` API。在这个元组中，只有第一个匹配的词缀会被删除。这被拒绝了，理由如下。

- This behavior can be surprising or visually confusing, especially when one prefix is empty or is a substring of another prefix, as in `'FooBar'.removeprefix(('', 'Foo')) == 'Foo'` or `'FooBar text'.removeprefix(('Foo', 'FooBar ')) == 'Bar text'`.

  这种行为可能会令人惊讶或视觉混乱，特别是当一个前缀是空的或者是另一个前缀的子串时，如`'FooBar'.removeprefix(('', 'Foo')) == 'Foo'`或`'FooBar text'.removeprefix(('Foo', 'FooBar ')) == 'Bar text'`。

- The API for `str.replace()` only accepts a single pair of replacement strings, but has stood the test of time by refusing the temptation to guess in the face of ambiguous multiple replacements.

  `str.replace()`的API只接受一对替换字符串，但它经受住了时间的考验，在面对模糊的多重替换时拒绝了猜测的诱惑。

- There may be a compelling use case for such a feature in the future, but generalization before the basic feature sees real-world use would be easy to get permanently wrong.

  在未来，这样的功能可能会有一个令人信服的使用案例，但在基本功能看到现实世界的使用之前，泛化会很容易出现永久性的错误。

## [Alternative Method Names](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id28)

替代性方法名

Several alternatives method names have been proposed. Some are listed below, along with commentary for why they should be rejected in favor of `removeprefix` (the same arguments hold for `removesuffix`).

已经提出了几个替代的方法名称。下面列出了一些，并附有评论，说明为什么要拒绝这些方法而支持 `removeprefix`（同样的论点也适用于 `removesuffix`）。

- `ltrim`, `trimprefix`, etc.:

  "Trim" does in other languages (e.g. JavaScript, Java, Go, PHP) what `strip` methods do in Python.

  "Trim "在其他语言（如JavaScript、Java、Go、PHP）中的作用与Python中的`strip`方法相同。

- `lstrip(string=...)`

  This would avoid adding a new method, but for different behavior, it's better to have two different methods than one method with a keyword argument that selects the behavior.

  这将避免增加一个新的方法，但对于不同的行为，有两个不同的方法比一个方法有一个选择行为的关键字参数要好。

- `remove_prefix`:

  All of the other methods of the string API, e.g. `str.startswith()`, use `lowercase` rather than `lower_case_with_underscores`.

  字符串API的所有其他方法，例如`str.startswith()`，都使用`lowercase`，而不是`lower_case_with_underscores`。

- `removeleft`, `leftremove`, or `lremove`:

  The explicitness of "prefix" is preferred.

  倾向于 "前缀 "的明确性。

- `cutprefix`, `deleteprefix`, `withoutprefix`, `dropprefix`, etc.:

  Many of these might have been acceptable, but "remove" is unambiguous and matches how one would describe the "remove the prefix" behavior in English.

  其中许多可能是可以接受的，但 "remove "是没有歧义的，并且符合人们对英语中 "remove the prefix "行为的描述。

- `stripprefix`:

  Users may benefit from remembering that "strip" means working with sets of characters, while other methods work with substrings, so re-using "strip" here should be avoided.
  
  用户可能会受益于记住 "strip "意味着处理字符集，而其他方法是处理子串，所以应该避免在这里重复使用 "strip"。

# [How to Teach This](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id29)

如何教学

Among the uses for the `partition()`, `startswith()`, and `split()` string methods or the `enumerate()` or `zip()` built-in functions, a common theme is that if a beginner finds themselves manually indexing or slicing a string, then they should consider whether there is a higher-level method that better communicates *what* the code should do rather than merely *how* the code should do it. The proposed `removeprefix()` and `removesuffix()` methods expand the high-level string "toolbox" and further allow for this sort of skepticism toward manual slicing.

在`partition()`、`startwith()`和`split()`字符串方法或`enumerate()`或`zip()`内置函数的使用中，一个共同的主题是，如果初学者发现自己手动索引或切分一个字符串，那么他们应该考虑是否有一个更高级的方法可以更好地传达代码应该做什么，而不仅仅是代码应该如何做。拟议的`removeprefix()`和`removeesuffix()`方法扩展了高层字符串 "工具箱"，并进一步允许这种对手动切片的怀疑态度。

The main opportunity for user confusion will be the conflation of `lstrip`/`rstrip` with `removeprefix`/`removesuffix`. It may therefore be helpful to emphasize (as the documentation will) the following differences between the methods:

用户混淆的主要机会是将`lstrip`/`rstrip`与`removeprefix`/`removesuffix`混淆。因此，强调（就像文档中所说的那样）这些方法之间的以下区别可能会有帮助。

- (l/r)strip:

  - The argument is interpreted as a character set.

    该参数被解释为一个字符集合。

  - The characters are repeatedly removed from the appropriate end of the string.

    字符被重复地从字符串的适当末端移除。

- remove(prefix/suffix):

  - The argument is interpreted as an unbroken substring.

    该参数被解释为一个不间断的子串。
  
  - Only at most one copy of the prefix/suffix is removed.
  
    最多只删除一个前缀/后缀的副本。

# [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id30)

See the pull request on GitHub [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#pr).

# [History of Major revisions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id31)

- Version 3: Remove tuple behavior.
- Version 2: Changed name to `removeprefix`/`removesuffix`; added support for tuples as arguments
- Version 1: Initial draft with `cutprefix`/`cutsuffix`

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id32)

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id9) | GitHub pull request with implementation (https://github.com/python/cpython/pull/18939) |
|                                                              |                                                              |
| [2]                                                          | *([1](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id1), [2](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id8))* [Python-Ideas] "New explicit methods to trim strings" (https://mail.python.org/archives/list/python-ideas@python.org/thread/RJARZSUKCXRJIP42Z2YBBAEN5XA7KEC3/) |
|                                                              |                                                              |
| [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id2) | "Re: [Python-ideas] adding a trim convenience function" (https://mail.python.org/archives/list/python-ideas@python.org/thread/SJ7CKPZSKB5RWT7H3YNXOJUQ7QLD2R3X/#C2W5T7RCFSHU5XI72HG53A6R3J3SN4MV) |
|                                                              |                                                              |
| [[4\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id3) | "Re: [Python-Dev] strip behavior provides inconsistent results with certain strings" (https://mail.python.org/archives/list/python-ideas@python.org/thread/XYFQMFPUV6FR2N5BGYWPBVMZ5BE5PJ6C/#XYFQMFPUV6FR2N5BGYWPBVMZ5BE5PJ6C) |
|                                                              |                                                              |
| [[5\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id4) | [Python-Dev] "correction of a bug" (https://mail.python.org/archives/list/python-dev@python.org/thread/AOZ7RFQTQLCZCTVNKESZI67PB3PSS72X/#AOZ7RFQTQLCZCTVNKESZI67PB3PSS72X) |
|                                                              |                                                              |
| [[6\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id5) | [Python-Dev] "str.lstrip bug?" (https://mail.python.org/archives/list/python-dev@python.org/thread/OJDKRIESKGTQFNLX6KZSGKU57UXNZYAN/#CYZUFFJ2Q5ZZKMJIQBZVZR4NSLK5ZPIH) |
|                                                              |                                                              |
| [[7\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id6) | [Python-Dev] "strip behavior provides inconsistent results with certain strings" (https://mail.python.org/archives/list/python-dev@python.org/thread/ZWRGCGANHGVDPP44VQKRIYOYX7LNVDVG/#ZWRGCGANHGVDPP44VQKRIYOYX7LNVDVG) |
|                                                              |                                                              |
| [[8\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id7) | Comment listing Bug Tracker and StackOverflow issues (https://mail.python.org/archives/list/python-ideas@python.org/message/GRGAFIII3AX22K3N3KT7RB4DPBY3LPVG/) |
|                                                              |                                                              |

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20616%20--%20String%20methods%20to%20remove%20prefixes%20and%20suffixes.md#id33)

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

Source: https://github.com/python/peps/blob/master/pep-0616.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。