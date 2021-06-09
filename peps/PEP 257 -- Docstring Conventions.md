# PEP 257 -- Docstring Conventions

文档字符串约定

> 原文地址：<https://www.python.org/dev/peps/pep-0257/>

| PEP:            | 257                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Docstring Conventions                                        |
| Author:         | David Goodger <goodger at python.org>, Guido van Rossum <guido at python.org> |
| Discussions-To: | [doc-sig at python.org](mailto:doc-sig@python.org?subject=PEP 257) |
| Status:         | Active                                                       |
| Type:           | Informational                                                |
| Created:        | 29-May-2001                                                  |
| Post-History:   | 13-Jun-2001                                                  |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0257/#abstract)
- [Rationale](https://www.python.org/dev/peps/pep-0257/#rationale)
- Specification
  - [What is a Docstring?](https://www.python.org/dev/peps/pep-0257/#what-is-a-docstring)
  - [One-line Docstrings](https://www.python.org/dev/peps/pep-0257/#one-line-docstrings)
  - [Multi-line Docstrings](https://www.python.org/dev/peps/pep-0257/#multi-line-docstrings)
  - [Handling Docstring Indentation](https://www.python.org/dev/peps/pep-0257/#handling-docstring-indentation)
- [References and Footnotes](https://www.python.org/dev/peps/pep-0257/#references-and-footnotes)
- [Copyright](https://www.python.org/dev/peps/pep-0257/#copyright)
- [Acknowledgements](https://www.python.org/dev/peps/pep-0257/#acknowledgements)

# [Abstract](https://www.python.org/dev/peps/pep-0257/#id12)

概述

This PEP documents the semantics and conventions associated with Python docstrings.

这个 PEP 记录了与 Python 文档字符串相关的语义和约定。

# [Rationale](https://www.python.org/dev/peps/pep-0257/#id13)

The aim of this PEP is to standardize the high-level structure of docstrings: what they should contain, and how to say it (without touching on any markup syntax within docstrings). The PEP contains conventions, not laws or syntax.

本PEP的目的是对文档串的高层结构进行标准化：它们应该包含什么，以及如何表达（不涉及文档串中的任何标记语法）。本PEP包含惯例，而不是规则或语法。

> "A universal convention supplies all of maintainability, clarity, consistency, and a foundation for good programming habits too. What it doesn't do is insist that you follow it against your will. That's Python!"
>
> "一个通用的惯例提供了所有的可维护性、清晰性、一致性，也是良好的编程习惯的基础。它所做的不是坚持让你违背自己的意愿去遵循它。这就是 Python！"
>
> —Tim Peters on comp.lang.python, 2001-06-16

If you violate these conventions, the worst you'll get is some dirty looks. But some software (such as the [Docutils](http://docutils.sourceforge.net/) [[3\]](https://www.python.org/dev/peps/pep-0257/#id6) docstring processing system [[1\]](https://www.python.org/dev/peps/pep-0257/#id4) [[2\]](https://www.python.org/dev/peps/pep-0257/#id5)) will be aware of the conventions, so following them will get you the best results.

如果你违反了这些惯例，你最坏的结果就是被人看不起。但有些软件（如Docutils[3]文档串处理系统[1][2]）会意识到这些约定，所以遵守这些约定会让你得到最好的结果。

# [Specification](https://www.python.org/dev/peps/pep-0257/#id14)

格式

## [What is a Docstring?](https://www.python.org/dev/peps/pep-0257/#id15)

什么是文档字符串？

A docstring is a string literal that occurs as the first statement in a module, function, class, or method definition. Such a docstring becomes the `__doc__` special attribute of that object.

文档字符串是一个字符串字面量，作为模块、函数、类或方法定义中的第一个语句出现。这样的文档字符串成为该对象的特殊属性` __doc__`。

All modules should normally have docstrings, and all functions and classes exported by a module should also have docstrings. Public methods (including the `__init__` constructor) should also have docstrings. A package may be documented in the module docstring of the `__init__.py` file in the package directory.

所有的模块通常都应该有文档字符串，所有由模块导出的函数和类也应该有文档字符串。公共方法（包括 `__init__`构造函数）也应该有文档字符串。一个包可以在包目录下的 `__init__.py` 文件的模块的文档字符串中进行记录。

String literals occurring elsewhere in Python code may also act as documentation. They are not recognized by the Python bytecode compiler and are not accessible as runtime object attributes (i.e. not assigned to `__doc__`), but two types of extra docstrings may be extracted by software tools:

在 Python 代码中其他地方出现的字符串字面量也可以当作说明文档。只是它们不会被Python字节码编译器识别，也不能作为运行时对象属性访问 (即不分配给`__doc__`)，但有两种类型的额外文档字符串可以被软件工具提取出来：

1. String literals occurring immediately after a simple assignment at the top level of a module, class, or `__init__` method are called "attribute docstrings".

   紧随模块、类或`__init__`方法顶层的简单赋值之后出现的字符串被称为 "属性文档字符串"。

2. String literals occurring immediately after another docstring are called "additional docstrings".

   紧接在另一个文档字符串之后出现的字符串字面量被称为 "附加文档字符串"。

Please see [PEP 258](https://www.python.org/dev/peps/pep-0258), "Docutils Design Specification" [[2\]](https://www.python.org/dev/peps/pep-0257/#id5), for a detailed description of attribute and additional docstrings.

请参阅 [PEP 258](https://www.python.org/dev/peps/pep-0258), "Docutils Design Specification" [[2\]](https://www.python.org/dev/peps/pep-0257/#id5), 以了解对属性和附加文档字符串的详细描述。

For consistency, always use `"""triple double quotes"""` around docstrings. Use `r"""raw triple double quotes"""` if you use any backslashes in your docstrings. For Unicode docstrings, use `u"""Unicode triple-quoted strings"""`.

为了保持一致性，在文档串周围总是使用`"""三重双引号""`。如果你在文档串中使用任何反斜线，请使用`r"""原始三重双引号""`。对于Unicode文件串，使用`u"""Unicode三引号字符串"""。

There are two forms of docstrings: one-liners and multi-line docstrings.

有两种形式的文档字符串：单行文档字符串和多行文档字符串。

## [One-line Docstrings](https://www.python.org/dev/peps/pep-0257/#id16)

当行文档字符串

One-liners are for really obvious cases. They should really fit on one line. For example:

单行线是用于确实明显的情况的，它们应该确实地适合于一行，比如说：

```python
def kos_root():
    """Return the pathname of the KOS root directory."""
    global _kos_root
    if _kos_root: return _kos_root
    ...
```

Notes:

注意：

- Triple quotes are used even though the string fits on one line. This makes it easy to later expand it.

  即使字符串适合使用单行，也要使用三重双引号，这样做方便以后扩展。

- The closing quotes are on the same line as the opening quotes. This looks better for one-liners.

  最后的引号与开头的引号在同一行，这样更美观。

- There's no blank line either before or after the docstring.

  在文档串的前后都没有空行。

- The docstring is a phrase ending in a period. It prescribes the function or method's effect as a command ("Do this", "Return that"), not as a description; e.g. don't write "Returns the pathname ...".

  文档串是一个以句号结尾的短语。它说明了函数和方法作为一个命令的使用效果（"做这个"，"返回那个"），而不是描述具体的实现逻辑；例如，不要写 "返回路径名..."。

- The one-line docstring should NOT be a "signature" reiterating the function/method parameters (which can be obtained by introspection). Don't do:

  单行文档串不应该是重申函数/方法参数的 "签名"（可以通过自省获得）。不要这样做。

  ```python
  def function(a, b):
      """function(a, b) -> list"""
  ```

  This type of docstring is only appropriate for C functions (such as built-ins), where introspection is not possible. However, the nature of the *return value* cannot be determined by introspection, so it should be mentioned. The preferred form for such a docstring would be something like:

  这种类型的文档串只适合于C语言的函数（比如内置函数），因为在这里不可能进行内省。然而，返回值的性质不能通过自省来确定，所以应该被提及。这种文档串的首选形式是这样的：

  ```
  def function(a, b):
      """Do X and return a list."""
  ```

  (Of course "Do X" should be replaced by a useful description!)

  （当然，“Do X”应该被更有用的说明文字取代）

## [Multi-line Docstrings](https://www.python.org/dev/peps/pep-0257/#id17)

多行文档字符串

Multi-line docstrings consist of a summary line just like a one-line docstring, followed by a blank line, followed by a more elaborate description. The summary line may be used by automatic indexing tools; it is important that it fits on one line and is separated from the rest of the docstring by a blank line. The summary line may be on the same line as the opening quotes or on the next line. The entire docstring is indented the same as the quotes at its first line (see example below).

多行文档串包括一个摘要行，就像单行文档串一样，后面是一个空行，然后是一个更详细的描述。摘要行可以被自动索引工具使用；重要的是，它要适合于一行，并且用空行与文档串的其他部分分开。摘要行可以和开头的引号在同一行，也可以在下一行。整个文档串的缩进程度与第一行的引号相同（见下面的例子）。

Insert a blank line after all docstrings (one-line or multi-line) that document a class -- generally speaking, the class's methods are separated from each other by a single blank line, and the docstring needs to be offset from the first method by a blank line.

在记录一个类的所有文档串（单行或多行）之后插入一个空行 -- 一般来说，类的方法之间是用一个空行隔开的，文档串需要从第一个方法处偏移一个空行。

The docstring of a script (a stand-alone program) should be usable as its "usage" message, printed when the script is invoked with incorrect or missing arguments (or perhaps with a "-h" option, for "help"). Such a docstring should document the script's function and command line syntax, environment variables, and files. Usage messages can be fairly elaborate (several screens full) and should be sufficient for a new user to use the command properly, as well as a complete quick reference to all options and arguments for the sophisticated user.

脚本（一个独立的程序）的文档串应该可以作为它的 "使用 "信息，在调用脚本时，如果参数不正确或缺失，就会打印出来（或者用"-h "选项，表示帮助）。这样的文档串应该记录脚本的功能和命令行语法、环境变量和文件。使用信息可以是相当详细的（有几个屏幕之多），应该足以让新用户正确地使用该命令，同时也可以为老练的用户提供所有选项和参数的完整快速参考。

The docstring for a module should generally list the classes, exceptions and functions (and any other objects) that are exported by the module, with a one-line summary of each. (These summaries generally give less detail than the summary line in the object's docstring.) The docstring for a package (i.e., the docstring of the package's `__init__.py` module) should also list the modules and subpackages exported by the package.

一个模块的文档串一般应列出该模块导出的类、异常和函数（以及任何其他对象），并对每个对象进行单行总结。(这些摘要通常比对象的文档串中的摘要行要少。) 包的 docstring (即包的 `__init__.py` 模块的 docstring) 也应该列出该包导出的模块和子包。

The docstring for a function or method should summarize its behavior and document its arguments, return value(s), side effects, exceptions raised, and restrictions on when it can be called (all if applicable). Optional arguments should be indicated. It should be documented whether keyword arguments are part of the interface.

一个函数或方法的文档串应该总结它的行为，并记录它的参数、返回值、副作用、引发的异常以及对它可以被调用的限制（如果适用的话）。可选的参数应该被指出。应该记录下关键字参数是否是接口的一部分。

The docstring for a class should summarize its behavior and list the public methods and instance variables. If the class is intended to be subclassed, and has an additional interface for subclasses, this interface should be listed separately (in the docstring). The class constructor should be documented in the docstring for its `__init__` method. Individual methods should be documented by their own docstring.

一个类的文档串应该总结其行为，并列出公共方法和实例变量。如果该类打算被子类化，并且有一个额外的接口供子类使用，这个接口应该被单独列出（在文档串中）。类的构造函数的说明文档应该被记录在它的 `__init__` 方法的文档字符串中。单独方法的说明文档应该由它们自己的文档串来记录。

If a class subclasses another class and its behavior is mostly inherited from that class, its docstring should mention this and summarize the differences. Use the verb "override" to indicate that a subclass method replaces a superclass method and does not call the superclass method; use the verb "extend" to indicate that a subclass method calls the superclass method (in addition to its own behavior).

如果一个类对另一个类进行了子类化，并且它的行为大部分是从该类继承而来，那么它的文档串应该提到这一点，并总结出其中的差异。使用动词 "override"来表示一个子类方法取代了超类方法，并且不调用超类方法；使用动词 "extend"来表示一个子类方法调用超类方法（除了它自己的行为之外）。

*Do not* use the Emacs convention of mentioning the arguments of functions or methods in upper case in running text. Python is case sensitive and the argument names can be used for keyword arguments, so the docstring should document the correct argument names. It is best to list each argument on a separate line. For example:

*不要*使用 Emacs 的惯例，在运行文本中以大写字母提及函数或方法的参数。Python 是区分大小写的，参数名可以用于关键字参数，所以 docstring 应该记录正确的参数名。最好将每个参数单独列在一行。比如说：

```
def complex(real=0.0, imag=0.0):
    """Form a complex number.

    Keyword arguments:
    real -- the real part (default 0.0)
    imag -- the imaginary part (default 0.0)
    """
    if imag == 0.0 and real == 0.0:
        return complex_zero
    ...
```

Unless the entire docstring fits on a line, place the closing quotes on a line by themselves. This way, Emacs' `fill-paragraph` command can be used on it.

除非整个文档串适合放在一行中，否则应将结尾的引号单独放在一行中。这样，Emacs的`fill-paragraph`命令就可以用在它上面。

## [Handling Docstring Indentation](https://www.python.org/dev/peps/pep-0257/#id18)

处理文件串缩进

Docstring processing tools will strip a uniform amount of indentation from the second and further lines of the docstring, equal to the minimum indentation of all non-blank lines after the first line. Any indentation in the first line of the docstring (i.e., up to the first newline) is insignificant and removed. Relative indentation of later lines in the docstring is retained. Blank lines should be removed from the beginning and end of the docstring.

文档串处理工具将从文档串的第二行和更多行中使用统一的缩进量，相当于第一行之后所有非空行的最小缩进量。文档串第一行的任何缩进（即到第一个换行为止）都是不重要的，并被删除。文档串中后面几行的相对缩进被保留。文档串的开头和结尾的空行应被删除。

Since code is much more precise than words, here is an implementation of the algorithm:

由于代码比文字要精确得多，这里有一个算法的实现：

```python
def trim(docstring):
    if not docstring:
        return ''
    # Convert tabs to spaces (following the normal Python rules)
    # and split into a list of lines:
    lines = docstring.expandtabs().splitlines()
    # Determine minimum indentation (first line doesn't count):
    indent = sys.maxint
    for line in lines[1:]:
        stripped = line.lstrip()
        if stripped:
            indent = min(indent, len(line) - len(stripped))
    # Remove indentation (first line is special):
    trimmed = [lines[0].strip()]
    if indent < sys.maxint:
        for line in lines[1:]:
            trimmed.append(line[indent:].rstrip())
    # Strip off trailing and leading blank lines:
    while trimmed and not trimmed[-1]:
        trimmed.pop()
    while trimmed and not trimmed[0]:
        trimmed.pop(0)
    # Return a single string:
    return '\n'.join(trimmed)
```

The docstring in this example contains two newline characters and is therefore 3 lines long. The first and last lines are blank:

这个例子中的文档串包含两个换行符，因此有3行长。第一行和最后一行是空白：

```
def foo():
    """
    This is the second line of the docstring.
    """
```

To illustrate:

举例说明：

```
>>> print repr(foo.__doc__)
'\n    This is the second line of the docstring.\n    '
>>> foo.__doc__.splitlines()
['', '    This is the second line of the docstring.', '    ']
>>> trim(foo.__doc__)
'This is the second line of the docstring.'
```

Once trimmed, these docstrings are equivalent:

一旦经过修剪，这些文件串是等价的：

```
def foo():
    """A multi-line
    docstring.
    """

def bar():
    """
    A multi-line
    docstring.
    """
```

# [References and Footnotes](https://www.python.org/dev/peps/pep-0257/#id19)

| [[1\]](https://www.python.org/dev/peps/pep-0257/#id1)  | [PEP 256](https://www.python.org/dev/peps/pep-0256), Docstring Processing System Framework, Goodger (http://www.python.org/dev/peps/pep-0256/) |
| ------------------------------------------------------ | ------------------------------------------------------------ |
|                                                        | PEP 256, Docstring处理系统框架, Goodger (http://www.python.org/dev/peps/pep-0256/) |
| [2]                                                    | *([1](https://www.python.org/dev/peps/pep-0257/#id2), [2](https://www.python.org/dev/peps/pep-0257/#id3))* [PEP 258](https://www.python.org/dev/peps/pep-0258), Docutils Design Specification, Goodger (http://www.python.org/dev/peps/pep-0258/) |
|                                                        | (1, 2) PEP 258, Docutils设计规范, Goodger (http://www.python.org/dev/peps/pep-0258/) |
| [[3\]](https://www.python.org/dev/peps/pep-0257/#id7)  | http://docutils.sourceforge.net/                             |
|                                                        |                                                              |
| [[4\]](https://www.python.org/dev/peps/pep-0257/#id9)  | http://www.python.org/dev/peps/pep-0008/                     |
|                                                        |                                                              |
| [[5\]](https://www.python.org/dev/peps/pep-0257/#id11) | http://www.python.org/sigs/doc-sig/                          |
|                                                        |                                                              |

# [Copyright](https://www.python.org/dev/peps/pep-0257/#id20)

This document has been placed in the public domain.

# [Acknowledgements](https://www.python.org/dev/peps/pep-0257/#id21)

The "Specification" text comes mostly verbatim from the [Python Style Guide](http://www.python.org/dev/peps/pep-0008/) [[4\]](https://www.python.org/dev/peps/pep-0257/#id8) essay by Guido van Rossum.

This document borrows ideas from the archives of the Python [Doc-SIG](http://www.python.org/sigs/doc-sig/) [[5\]](https://www.python.org/dev/peps/pep-0257/#id10). Thanks to all members past and present.

Source: https://github.com/python/peps/blob/master/pep-0257.txt

> 参考资料：
>
> - [**PEP 0257 -- Docstring 约定**](https://mp.weixin.qq.com/s/jG9FaT-3m59FN33D2jwzGg)

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。

