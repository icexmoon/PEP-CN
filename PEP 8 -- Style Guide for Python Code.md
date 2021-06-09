# PEP 8 -- Style Guide for Python Code

PEP 8--Python编码规范

> 原文地址：<https://www.python.org/dev/peps/pep-0008/>

| PEP:          | 8                                                            |
| :------------ | ------------------------------------------------------------ |
| Title:        | Style Guide for Python Code                                  |
| Author:       | Guido van Rossum <guido at python.org>, Barry Warsaw <barry at python.org>, Nick Coghlan <ncoghlan at gmail.com> |
| Status:       | Active                                                       |
| Type:         | Process                                                      |
| Created:      | 05-Jul-2001                                                  |
| Post-History: | 05-Jul-2001, 01-Aug-2013                                     |

[TOC]



# [Introduction](https://www.python.org/dev/peps/pep-0008/#id14)

简介

This document gives coding conventions for the Python code comprising the standard library in the main Python distribution. Please see the companion informational PEP describing style guidelines for the C code in the C implementation of Python [[1\]](https://www.python.org/dev/peps/pep-0008/#id8).

本文档给出了由Python主发行版中的标准库组成的Python代码的编码规范。请参阅与之配套的信息性PEP，它描述了Python的C语言实现中C语言代码的风格指南 [1]。

This document and [PEP 257](https://www.python.org/dev/peps/pep-0257) (Docstring Conventions) were adapted from Guido's original Python Style Guide essay, with some additions from Barry's style guide [[2\]](https://www.python.org/dev/peps/pep-0008/#id9).

本文档和 PEP 257 (Docstring 惯例) 改编自 Guido 最初的 Python 风格指南文章，并从 Barry 的风格指南中增加了一些内容 [2]。

This style guide evolves over time as additional conventions are identified and past conventions are rendered obsolete by changes in the language itself.

这个风格指南随着时间的推移而发展，因为更多的约定被确认，而过去的约定由于语言本身的变化而变得过时。

Many projects have their own coding style guidelines. In the event of any conflicts, such project-specific guides take precedence for that project.

许多项目都有自己的编码风格指南。在任何冲突的情况下，这些特定项目的指南在该项目中具有优先权。

# [A Foolish Consistency is the Hobgoblin of Little Minds](https://www.python.org/dev/peps/pep-0008/#id15)

愚蠢的一致性是小脑袋的恶棍

One of Guido's key insights is that code is read much more often than it is written. The guidelines provided here are intended to improve the readability of code and make it consistent across the wide spectrum of Python code. As [PEP 20](https://www.python.org/dev/peps/pep-0020) says, "Readability counts".

Guido的一个重要见解是，代码被阅读的次数比被写的次数多得多。这里提供的指南是为了提高代码的可读性，并使其在广泛的Python代码中保持一致。正如PEP 20所说，"可读性很重要"。

A style guide is about consistency. Consistency with this style guide is important. Consistency within a project is more important. Consistency within one module or function is the most important.

风格指南是关于一致性的。与本风格指南保持一致是很重要的。在一个项目中的一致性更为重要。一个模块或函数内的一致性是最重要的。

However, know when to be inconsistent -- sometimes style guide recommendations just aren't applicable. When in doubt, use your best judgment. Look at other examples and decide what looks best. And don't hesitate to ask!

然而，要知道什么时候可以不一致 -- 有时风格指南的建议并不适用。当有疑问时，使用你的最佳判断。看看其他的例子，决定什么看起来最好。而且，不要犹豫，要大胆质疑!

In particular: do not break backwards compatibility just to comply with this PEP!

特别是：不要为了遵守这个PEP而破坏向后的兼容性。

Some other good reasons to ignore a particular guideline:

其他一些忽略某条准则的好理由。

1. When applying the guideline would make the code less readable, even for someone who is used to reading code that follows this PEP.

   当应用该准则会使代码的可读性降低时，即使是对于那些习惯于阅读遵循该PEP的代码的人来说。

2. To be consistent with surrounding code that also breaks it (maybe for historic reasons) -- although this is also an opportunity to clean up someone else's mess (in true XP style).

   为了与周围的代码保持一致，这些代码也会破坏它（也许是出于历史原因）--尽管这也是一个清理别人的烂摊子的机会（真正的XP风格）。

3. Because the code in question predates the introduction of the guideline and there is no other reason to be modifying that code.

   因为有问题的代码是在引入该准则之前的，而且没有其他理由要修改该代码。

4. When the code needs to remain compatible with older versions of Python that don't support the feature recommended by the style guide.

   当代码需要与不支持编码规范所推荐的特性的旧版本的 Python 保持兼容时。

# [Code Lay-out](https://www.python.org/dev/peps/pep-0008/#id16)

代码布局

## [Indentation](https://www.python.org/dev/peps/pep-0008/#id17)

缩进

Use 4 spaces per indentation level.

每个缩进级别使用4个空格。

Continuation lines should align wrapped elements either vertically using Python's implicit line joining inside parentheses, brackets and braces, or using a *hanging indent* [[7\]](https://www.python.org/dev/peps/pep-0008/#fn-hi). When using a hanging indent the following should be considered; there should be no arguments on the first line and further indentation should be used to clearly distinguish itself as a continuation line:

延续行应该使用Python的小括号、大括号和圆括号内的隐式连线，或者使用*悬空缩进*[7]，将包裹的元素垂直对齐。当使用悬空缩进时，应该考虑以下问题；第一行不应该有参数，应该使用进一步的缩进来明确区分自己是一个延续行。

```python
# Correct:
# 正确的书写方式：

# Aligned with opening delimiter.
# 与开头的分隔符对齐。
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# Add 4 spaces (an extra level of indentation) to distinguish arguments from the rest.
# 增加4个空格(额外的缩进级别)来区分参数和其他参数。
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# Hanging indents should add a level.
# 悬空缩进应该增加一个层次。
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

```python
# Wrong:
# 错误的书写方式

# Arguments on first line forbidden when not using vertical alignment.
# 当不使用垂直对齐时，禁止将参数放在第一行。
foo = long_function_name(var_one, var_two,
    var_three, var_four)

# Further indentation required as indentation is not distinguishable.
# 需要进一步缩进，因为缩进是无法区分的。
def long_function_name(
    var_one, var_two, var_three,
    var_four):
    print(var_one)
```

The 4-space rule is optional for continuation lines.

4个空格的规则对于延续行来说是可选的。

Optional:

可选方式：

```python
# Hanging indents *may* be indented to other than 4 spaces.
# 悬空缩进*可以*使用4个以外的空格缩进
foo = long_function_name(
  var_one, var_two,
  var_three, var_four)
```

When the conditional part of an `if`-statement is long enough to require that it be written across multiple lines, it's worth noting that the combination of a two character keyword (i.e. `if`), plus a single space, plus an opening parenthesis creates a natural 4-space indent for the subsequent lines of the multiline conditional. This can produce a visual conflict with the indented suite of code nested inside the `if`-statement, which would also naturally be indented to 4 spaces. This PEP takes no explicit position on how (or whether) to further visually distinguish such conditional lines from the nested suite inside the `if`-statement. Acceptable options in this situation include, but are not limited to:

当`if`语句的条件部分长到需要跨行书写时，值得注意的是，两个字符的关键词（即`if`），加上一个空格，再加上一个开头的小括号，为多行条件的后续行创造了一个自然的4空格缩进。这可能会与嵌套在if语句中的代码缩进产生视觉冲突，这些代码也会自然缩进到4个空格。对于如何（或是否）进一步从视觉上区分这些条件行和嵌套在`if`语句中的代码块，本PEP没有明确的立场。这种情况下可接受的选项包括，但不限于：

```python
# No extra indentation.
# 没有额外的缩进。
if (this_is_one_thing and
    that_is_another_thing):
    do_something()

# Add a comment, which will provide some distinction in editors
# 添加一个注释，这将在文本编辑器中增加一些区别
# supporting syntax highlighting.
# 支持语法高亮。 
if (this_is_one_thing and
    that_is_another_thing):
    # Since both conditions are true, we can frobnicate.
    do_something()

# Add some extra indentation on the conditional continuation line.
# 在条件延续行上增加一些额外的缩进。
if (this_is_one_thing
        and that_is_another_thing):
    do_something()
```

(Also see the discussion of whether to break before or after binary operators below.)

(也可参见下面关于在二元运算符之前或之后断开的讨论)。

The closing brace/bracket/parenthesis on multiline constructs may either line up under the first non-whitespace character of the last line of list, as in:

多行结构的结尾大括号/小括号/中括号可以排在列表的最后一行的第一个非空格字符下，如：

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
    ]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
    )
```

or it may be lined up under the first character of the line that starts the multiline construct, as in:

或者它可以排在开始多行结构的一行的第一个字符下面，如：

```python
my_list = [
    1, 2, 3,
    4, 5, 6,
]
result = some_function_that_takes_arguments(
    'a', 'b', 'c',
    'd', 'e', 'f',
)
```

## [Tabs or Spaces?](https://www.python.org/dev/peps/pep-0008/#id18)

制表符还是空格？

Spaces are the preferred indentation method.

空格是更好的缩进选择。

Tabs should be used solely to remain consistent with code that is already indented with tabs.

制表符只应该用于已经使用制表符进行缩进的已有代码，以保持风格一致。

Python 3 disallows mixing the use of tabs and spaces for indentation.

Python 3 不允许混合使用制表符和空格进行缩进。

Python 2 code indented with a mixture of tabs and spaces should be converted to using spaces exclusively.

使用制表符和空格混合缩进的Python 2代码应该被转换为只使用空格。

When invoking the Python 2 command line interpreter with the `-t` option, it issues warnings about code that illegally mixes tabs and spaces. When using `-tt` these warnings become errors. These options are highly recommended!

当用`-t`选项调用Python 2命令行解释器时，它会对非法混合使用制表符和空格的代码发出警告。当使用 `-tt` 时，这些警告会变成错误。强烈建议使用这些选项!

## [Maximum Line Length](https://www.python.org/dev/peps/pep-0008/#id19)

代码行最大长度

Limit all lines to a maximum of 79 characters.

将所有的代码行最大长度限制在79个字符。

For flowing long blocks of text with fewer structural restrictions (docstrings or comments), the line length should be limited to 72 characters.

对于结构限制较少的流动的长文本块（文档串或注释），行长应限制为72个字符。

Limiting the required editor window width makes it possible to have several files open side by side, and works well when using code review tools that present the two versions in adjacent columns.

限制所需的编辑器窗口宽度，可以让几个文件并排打开，在使用将两个版本呈现在相邻列的代码审查工具时，效果很好。

The default wrapping in most tools disrupts the visual structure of the code, making it more difficult to understand. The limits are chosen to avoid wrapping in editors with the window width set to 80, even if the tool places a marker glyph in the final column when wrapping lines. Some web based tools may not offer dynamic line wrapping at all.

大多数工具中的默认包装破坏了代码的视觉结构，使其更加难以理解。选择这些限制是为了避免在窗口宽度设置为80的编辑器中进行包装，即使工具在包装行时在最后一列放置一个标记字形。一些基于网络的工具可能根本就不提供动态换行。

Some teams strongly prefer a longer line length. For code maintained exclusively or primarily by a team that can reach agreement on this issue, it is okay to increase the line length limit up to 99 characters, provided that comments and docstrings are still wrapped at 72 characters.

有些团队强烈希望行的长度长一些。对于完全或主要由一个团队维护的代码，如果能在这个问题上达成一致，可以将行长限制增加到99个字符，但注释和文档字符串仍以72个字符包装。

The Python standard library is conservative and requires limiting lines to 79 characters (and docstrings/comments to 72).

Python 标准库是保守的，它要求将行长度限制在 79 个字符 (而 docstrings/comments 限制在 72 个字符)。

The preferred way of wrapping long lines is by using Python's implied line continuation inside parentheses, brackets and braces. Long lines can be broken over multiple lines by wrapping expressions in parentheses. These should be used in preference to using a backslash for line continuation.

包裹长行的首选方法是在小括号、大括号和大括号内使用 Python 的隐含续行功能。长行可以通过将表达式包裹在小括号中而分成多行。应该优先使用这些小括号，而不是使用反斜线来延续行。

Backslashes may still be appropriate at times. For example, long, multiple `with`-statements cannot use implicit continuation, so backslashes are acceptable:

反斜线有时仍然是合适的。例如，长而多的`with`语句不能使用隐式续行，所以反斜杠是可以接受的：

```python
with open('/path/to/some/file/you/want/to/read') as file_1, \
     open('/path/to/some/file/being/written', 'w') as file_2:
    file_2.write(file_1.read())
```

(See the previous discussion on [multiline if-statements](https://www.python.org/dev/peps/pep-0008/#multiline-if-statements) for further thoughts on the indentation of such multiline `with`-statements.)

参见前面关于多行if语句的讨论，以进一步了解这种多行`with`语句的缩进情况）。

Another such case is with `assert` statements.

另一种情况是使用`assert`语句。

Make sure to indent the continued line appropriately.

请确保适当地缩进续行。

## [Should a Line Break Before or After a Binary Operator?](https://www.python.org/dev/peps/pep-0008/#id20)

应该在二元运算符之前还是之后进行换行？

For decades the recommended style was to break after binary operators. But this can hurt readability in two ways: the operators tend to get scattered across different columns on the screen, and each operator is moved away from its operand and onto the previous line. Here, the eye has to do extra work to tell which items are added and which are subtracted:

几十年来，推荐的风格是在二元运算符之后断行。但这在两个方面会损害可读性：运算符往往分散在屏幕的不同列中，而且每个运算符都被从其操作数上移到了前一行。在这里，眼睛不得不做额外的工作来分辨哪些项目是加法，哪些是减法：

```python
# Wrong:
# 错误的风格：
# operators sit far away from their operands
# 操作数离操作符较远
income = (gross_wages +
          taxable_interest +
          (dividends - qualified_dividends) -
          ira_deduction -
          student_loan_interest)
```

To solve this readability problem, mathematicians and their publishers follow the opposite convention. Donald Knuth explains the traditional rule in his *Computers and Typesetting* series: "Although formulas within a paragraph always break after binary operations and relations, displayed formulas always break before binary operations" [[3\]](https://www.python.org/dev/peps/pep-0008/#id10).

为了解决这个可读性问题，数学家和他们的出版商遵循相反的惯例。Donald Knuth在他的《计算机与排版》系列中解释了这一传统规则。"尽管段落内的公式总是在二元运算和关系之后断开，但显示的公式总是在二元运算之前断开"[3]。

Following the tradition from mathematics usually results in more readable code:

遵循数学的传统，通常会形成可读性更强的代码：

```python
# Correct:
# 正确的风格：
# easy to match operators with operands
# 用操作符轻松地对齐了操作数
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

In Python code, it is permissible to break before or after a binary operator, as long as the convention is consistent locally. For new code Knuth's style is suggested.

在Python代码中，允许在二元运算符之前或之后断开，只要惯例在本地是一致的。对于新的代码，建议采用Knuth的风格。

## [Blank Lines](https://www.python.org/dev/peps/pep-0008/#id21)

空白行

Surround top-level function and class definitions with two blank lines.

用两行空行包围顶级函数和类的定义。

Method definitions inside a class are surrounded by a single blank line.

类中的方法定义由一个空行包围。

Extra blank lines may be used (sparingly) to separate groups of related functions. Blank lines may be omitted between a bunch of related one-liners (e.g. a set of dummy implementations).

额外少量的空行可以用来从函数组中区分功能相近的函数。在一堆相关的单行之间可以省略空行（例如，一组伪代码）。

Use blank lines in functions, sparingly, to indicate logical sections.

在函数中使用少量的空行区分不同的代码逻辑。

Python accepts the control-L (i.e. ^L) form feed character as whitespace; Many tools treat these characters as page separators, so you may use them to separate pages of related sections of your file. Note, some editors and web-based code viewers may not recognize control-L as a form feed and will show another glyph in its place.

Python 接受 control-L (即 ^L) 形式的换行字符作为空格；许多工具将这些字符视为页面分隔符，所以你可以用它们来分隔文件中相关部分的页面。注意，一些编辑器和基于网络的代码查看器可能不承认 control-L 是换页字符，而会显示另一个字形来代替它。

## [Source File Encoding](https://www.python.org/dev/peps/pep-0008/#id22)

源文件编码

Code in the core Python distribution should always use UTF-8 (or ASCII in Python 2).

核心 Python 发行版中的代码应该始终使用 UTF-8 (或 Python 2 中的 ASCII)。

Files using ASCII (in Python 2) or UTF-8 (in Python 3) should not have an encoding declaration.

使用 ASCII (在 Python 2 中) 或 UTF-8 (在 Python 3 中) 的文件不应该有编码声明。

In the standard library, non-default encodings should be used only for test purposes or when a comment or docstring needs to mention an author name that contains non-ASCII characters; otherwise, using `\x`, `\u`, `\U`, or `\N` escapes is the preferred way to include non-ASCII data in string literals.

在标准库中，非默认编码应该只用于测试目的，或者当注释或文档串需要提到包含非ASCII字符的作者姓名时；否则，使用 `\x`,` \u`, `\U`, 或 `\N` 转义是在字符串字面量中包含非ASCII数据的首选方式。

For Python 3.0 and beyond, the following policy is prescribed for the standard library (see [PEP 3131](https://www.python.org/dev/peps/pep-3131)): All identifiers in the Python standard library MUST use ASCII-only identifiers, and SHOULD use English words wherever feasible (in many cases, abbreviations and technical terms are used which aren't English). In addition, string literals and comments must also be in ASCII. The only exceptions are (a) test cases testing the non-ASCII features, and (b) names of authors. Authors whose names are not based on the Latin alphabet (latin-1, ISO/IEC 8859-1 character set) MUST provide a transliteration of their names in this character set.

对于Python 3.0及以后的版本，为标准库规定了以下政策(见PEP 3131)。Python标准库中的所有标识符必须使用纯ASCII的标识符，并且在可行的情况下应该使用英文单词(在很多情况下，使用的缩写和技术术语都不是英文)。此外，字符串字面量和注释也必须使用ASCII。唯一的例外是（a）测试非ASCII特性的测试案例，以及（b）作者的名字。名字不是基于拉丁字母（latin-1，ISO/IEC 8859-1字符集）的作者必须提供他们名字在该字符集中的音译。

Open source projects with a global audience are encouraged to adopt a similar policy.

我们鼓励有全球受众的开源项目采用类似的政策。

## [Imports](https://www.python.org/dev/peps/pep-0008/#id23)

- Imports should usually be on separate lines:

  导入语句应该使用多行代码：

  ```python
  # Correct:
  # 正确的风格
  import os
  import sys
  ```

  ```python
  # Wrong:
  # 错误的风格
  import sys, os
  ```

  It's okay to say this though:

  虽然这样做也可以：

  ```python
  # Correct:
  # 正确的风格
  from subprocess import Popen, PIPE
  ```

- Imports are always put at the top of the file, just after any module comments and docstrings, and before module globals and constants.

  导入语句总是放在文件的顶部，就在任何模块的注释和文档说明之后，在模块的全局变量和常量之前。

  Imports should be grouped in the following order:

  导入语句应按以下顺序分组：

  1. Standard library imports.

     标准库的导入。

  2. Related third party imports.

     相关的第三方导入。

  3. Local application/library specific imports.

     本地的应用程序、库的特定导入

  You should put a blank line between each group of imports.

  你应当在每组导入之间插入一个空白行。

- Absolute imports are recommended, as they are usually more readable and tend to be better behaved (or at least give better error messages) if the import system is incorrectly configured (such as when a directory inside a package ends up on `sys.path`):

  推荐使用绝对导入，因为它们通常更易读，而且如果导入系统配置不正确（比如当包内的目录最后出现在`sys.path`上时），它们往往表现得更好（或者至少给出更好的错误信息）：

  ```python
  import mypkg.sibling
  from mypkg import sibling
  from mypkg.sibling import example
  ```

  However, explicit relative imports are an acceptable alternative to absolute imports, especially when dealing with complex package layouts where using absolute imports would be unnecessarily verbose:

  然而，显式相对导入是绝对导入的一个可接受的替代方法，特别是在处理复杂的包布局时，使用绝对导入会造成不必要的冗长。

  ```python
  from . import sibling
  from .sibling import example
  ```

  Standard library code should avoid complex package layouts and always use absolute imports.

  标准库代码应该避免复杂的包布局，并始终使用绝对导入。

  Implicit relative imports should *never* be used and have been removed in Python 3.

  *永远*不要使用隐式相对导入，这种方式已经在 Python 3 中被移除。

- When importing a class from a class-containing module, it's usually okay to spell this:

  当从一个含类的模块中导入一个类时，通常可以这样拼写：

  ```python
  from myclass import MyClass
  from foo.bar.yourclass import YourClass
  ```

  If this spelling causes local name clashes, then spell them explicitly:

  如果这种拼写方式导致本地名称冲突，那么就明确地拼写：

  ```python
  import myclass
  import foo.bar.yourclass
  ```

  and use "myclass.MyClass" and "foo.bar.yourclass.YourClass".

  并且使用“myclass.MyClass”和"foo.bar.yourclass.YourClass"。

- Wildcard imports (`from <module> import *`) should be avoided, as they make it unclear which names are present in the namespace, confusing both readers and many automated tools. There is one defensible use case for a wildcard import, which is to republish an internal interface as part of a public API (for example, overwriting a pure Python implementation of an interface with the definitions from an optional accelerator module and exactly which definitions will be overwritten isn't known in advance).

  应该避免通配符导入 (`from <module> import *`)，因为它们使名空间中存在的名字不明确，使读者和许多自动化工具都感到困惑。通配符导入有一个可辩护的用例，那就是重新发布一个内部接口作为公共 API 的一部分 (例如，用一个可选的加速器模块的定义覆盖一个纯 Python 接口的实现，具体哪些定义会被覆盖并不事先知道)。

  When republishing names this way, the guidelines below regarding public and internal interfaces still apply.

  当以这种方式重新发布名称时，下面关于公共和内部接口的准则仍然适用。

## [Module Level Dunder Names](https://www.python.org/dev/peps/pep-0008/#id24)

模块级的双下划线名称

Module level "dunders" (i.e. names with two leading and two trailing underscores) such as `__all__`, `__author__`, `__version__`, etc. should be placed after the module docstring but before any import statements *except* `from __future__` imports. Python mandates that future-imports must appear in the module before any other code except docstrings:

模块级的 "dunders"(即带有两个前导和两个尾部下划线的名字)，如 `__all__`, `__author__`, `__version__`，等等，应该放在模块文档串之后，但在任何导入语句之前，*除了* `from __future__` 的导入。Python 规定未来的导入必须在模块中出现在任何其他代码之前，除了文档串：

```python
"""This is the example module.

This module does stuff.
"""

from __future__ import barry_as_FLUFL

__all__ = ['a', 'b', 'c']
__version__ = '0.1'
__author__ = 'Cardinal Biggles'

import os
import sys
```

# [String Quotes](https://www.python.org/dev/peps/pep-0008/#id25)

字符串引号

In Python, single-quoted strings and double-quoted strings are the same. This PEP does not make a recommendation for this. Pick a rule and stick to it. When a string contains single or double quote characters, however, use the other one to avoid backslashes in the string. It improves readability.

在 Python 中，单引号字符串和双引号字符串是一样的。本 PEP 并没有对此提出建议。选择一个规则并坚持下去。然而，当一个字符串包含单引号或双引号字符时，使用另一种，以避免字符串中的反斜线。它可以提高可读性。

For triple-quoted strings, always use double quote characters to be consistent with the docstring convention in [PEP 257](https://www.python.org/dev/peps/pep-0257).

对于使用三个引号的字符串，总是使用双引号字符，以便与PEP 257中的docstring约定一致。

# [Whitespace in Expressions and Statements](https://www.python.org/dev/peps/pep-0008/#id26)

表达式和语句中的空格

## [Pet Peeves](https://www.python.org/dev/peps/pep-0008/#id27)

讨厌的事

Avoid extraneous whitespace in the following situations:

在一下情况要避免多余的空格：

- Immediately inside parentheses, brackets or braces:

  紧靠中括号、大括号或小括号内：

  ```python
  # Correct:
  spam(ham[1], {eggs: 2})
  ```

  ```python
  # Wrong:
  spam( ham[ 1 ], { eggs: 2 } )
  ```

- Between a trailing comma and a following close parenthesis:

  在尾部的逗号和后面的闭合括号之间：

  ```python
  # Correct:
  foo = (0,)
  ```

  ```python
  # Wrong:
  bar = (0, )
  ```

- Immediately before a comma, semicolon, or colon:

  紧接在逗号、分号或冒号之前：

  ```python
  # Correct:
  if x == 4: print x, y; x, y = y, x
  ```

  ```python
  # Wrong:
  if x == 4 : print x , y ; x , y = y , x
  ```

- However, in a slice the colon acts like a binary operator, and should have equal amounts on either side (treating it as the operator with the lowest priority). In an extended slice, both colons must have the same amount of spacing applied. Exception: when a slice parameter is omitted, the space is omitted:

  然而，在切片中，冒号的作用就像一个二元运算符，两边应该有相等的量（把它当作优先级最低的运算符）。在一个扩展的切片中，两个冒号都必须应用相同数量的间距。例外：当一个切片参数被省略时，空格被省略了。

  ```python
  # Correct:
  ham[1:9], ham[1:9:3], ham[:9:3], ham[1::3], ham[1:9:]
  ham[lower:upper], ham[lower:upper:], ham[lower::step]
  ham[lower+offset : upper+offset]
  ham[: upper_fn(x) : step_fn(x)], ham[:: step_fn(x)]
  ham[lower + offset : upper + offset]
  ```

  ```python
  # Wrong:
  ham[lower + offset:upper + offset]
  ham[1: 9], ham[1 :9], ham[1:9 :3]
  ham[lower : : upper]
  ham[ : upper]
  ```

- Immediately before the open parenthesis that starts the argument list of a function call:

  紧挨着一个函数调用的参数列表的前括号之前：

  ```python
  # Correct:
  spam(1)
  ```

  ```python
  # Wrong:
  spam (1)
  ```

- Immediately before the open parenthesis that starts an indexing or slicing:

  紧接在开始索引或切片的前括号之前：

  ```python
  # Correct:
  dct['key'] = lst[index]
  ```

  ```python
  # Wrong:
  dct ['key'] = lst [index]
  ```

- More than one space around an assignment (or other) operator to align it with another:

  在一个赋值（或其他）运算符周围有一个以上的空格，以使其与另一个运算符对齐：

  ```python
  # Correct:
  x = 1
  y = 2
  long_variable = 3
  ```

  ```python
  # Wrong:
  x             = 1
  y             = 2
  long_variable = 3
  ```

## [Other Recommendations](https://www.python.org/dev/peps/pep-0008/#id28)

其他建议

- Avoid trailing whitespace anywhere. Because it's usually invisible, it can be confusing: e.g. a backslash followed by a space and a newline does not count as a line continuation marker. Some editors don't preserve it and many projects (like CPython itself) have pre-commit hooks that reject it.

  避免在任何地方出现拖尾的空白。因为它通常是不可见的，它可能会引起混淆：例如，一个反斜杠后面跟着一个空格和一个换行符并不算作一个续行标记。有些编辑器不保留它，许多项目（如CPython本身）有预提交钩子，会拒绝它。

- Always surround these binary operators with a single space on either side: assignment (`=`), augmented assignment (`+=`, `-=` etc.), comparisons (`==`, `<`, `>`, `!=`, `<>`, `<=`, `>=`, `in`, `not in`, `is`, `is not`), Booleans (`and`, `or`, `not`).

  在这些二元运算符的两边总是用一个空格包围：赋值（`=`），增强赋值（`+=`，`-=`等），比较（`==`，`<`，`>`，`!=`，`<>`，`<=`，`>=`，`in`，`not in`，`is`，`is not`），布尔运算（`and`，`or`，`not`）。

- If operators with different priorities are used, consider adding whitespace around the operators with the lowest priority(ies). Use your own judgment; however, never use more than one space, and always have the same amount of whitespace on both sides of a binary operator:

  如果使用了不同优先级的运算符，考虑在优先级最低的运算符周围添加空格。这取决于自己的判断；然而，永远不要使用一个以上的空格，并且在二元运算符的两边总是有相同数量的空格：

  ```python
  # Correct:
  i = i + 1
  submitted += 1
  x = x*2 - 1
  hypot2 = x*x + y*y
  c = (a+b) * (a-b)
  ```

  ```python
  # Wrong:
  i=i+1
  submitted +=1
  x = x * 2 - 1
  hypot2 = x * x + y * y
  c = (a + b) * (a - b)
  ```

- Function annotations should use the normal rules for colons and always have spaces around the `->` arrow if present. (See [Function Annotations](https://www.python.org/dev/peps/pep-0008/#function-annotations) below for more about function annotations.):

  函数注释应该使用正常的冒号规则，如果有的话，在->箭头周围总是有空格。关于函数注释的更多信息，请参见下面的函数注释）：

  ```python
  # Correct:
  def munge(input: AnyStr): ...
  def munge() -> PosInt: ...
  ```

  ```python
  # Wrong:
  def munge(input:AnyStr): ...
  def munge()->PosInt: ...
  ```

- Don't use spaces around the `=` sign when used to indicate a keyword argument, or when used to indicate a default value for an *unannotated* function parameter:

  当用于表示一个关键字参数时，或用于表示一个未注释的函数参数的默认值时，不要在=号周围使用空格：

  ```python
  # Correct:
  def complex(real, imag=0.0):
      return magic(r=real, i=imag)
  ```

  ```python
  # Wrong:
  def complex(real, imag = 0.0):
      return magic(r = real, i = imag)
  ```

  When combining an argument annotation with a default value, however, do use spaces around the `=` sign:

  然而，当把一个参数注解和一个默认值结合起来时，请在=符号周围使用空格：

  ```python
  # Correct:
  def munge(sep: AnyStr = None): ...
  def munge(input: AnyStr, sep: AnyStr = None, limit=1000): ...
  ```

  ```python
  # Wrong:
  def munge(input: AnyStr=None): ...
  def munge(input: AnyStr, limit = 1000): ...
  ```

- Compound statements (multiple statements on the same line) are generally discouraged:

  通常不鼓励使用复合语句（同一行的多个语句）：

  ```python
  # Correct:
  if foo == 'blah':
      do_blah_thing()
  do_one()
  do_two()
  do_three()
  ```

  Rather not:

  ```python
  # Wrong:
  if foo == 'blah': do_blah_thing()
  do_one(); do_two(); do_three()
  ```

- While sometimes it's okay to put an if/for/while with a small body on the same line, never do this for multi-clause statements. Also avoid folding such long lines!

  虽然有时把一个带有小主体的if/for/while放在同一行是可以的，但对于大量语句，千万不要这样做。也要避免折叠这样的长行!

  Rather not:

  ```python
  # Wrong:
  if foo == 'blah': do_blah_thing()
  for x in lst: total += x
  while t < 10: t = delay()
  ```

  Definitely not:

  ```python
  # Wrong:
  if foo == 'blah': do_blah_thing()
  else: do_non_blah_thing()
  
  try: something()
  finally: cleanup()
  
  do_one(); do_two(); do_three(long, argument,
                               list, like, this)
  
  if foo == 'blah': one(); two(); three()
  ```

# [When to Use Trailing Commas](https://www.python.org/dev/peps/pep-0008/#id29)

何时使用尾部逗号

Trailing commas are usually optional, except they are mandatory when making a tuple of one element (and in Python 2 they have semantics for the `print` statement). For clarity, it is recommended to surround the latter in (technically redundant) parentheses:

后面的逗号通常是可选的，除了在做一个元素的元组时是必须的 (在 Python 2 中，它们对打印语句有语义)。为了清楚起见，建议用（技术上多余的）小括号包围后者。

```python
# Correct:
FILES = ('setup.cfg',)
```

```python
# Wrong:
FILES = 'setup.cfg',
```

When trailing commas are redundant, they are often helpful when a version control system is used, when a list of values, arguments or imported items is expected to be extended over time. The pattern is to put each value (etc.) on a line by itself, always adding a trailing comma, and add the close parenthesis/bracket/brace on the next line. However it does not make sense to have a trailing comma on the same line as the closing delimiter (except in the above case of singleton tuples):

当尾随逗号是多余的，当使用版本控制系统时，当一个值、参数或导入项目的列表预计会随着时间的推移而扩展时，尾随逗号往往是有帮助的。其模式是将每个值（等）单独放在一行，总是加上一个尾部逗号，然后在下一行加上封闭的小括号/括号/括号。然而，将尾部逗号与闭合分隔符放在同一行中是没有意义的（除了上述只包含一个元素的元组的情况）。

```python
# Correct:
FILES = [
    'setup.cfg',
    'tox.ini',
    ]
initialize(FILES,
           error=True,
           )
```

```python
# Wrong:
FILES = ['setup.cfg', 'tox.ini',]
initialize(FILES, error=True,)
```

# [Comments](https://www.python.org/dev/peps/pep-0008/#id30)

注释

Comments that contradict the code are worse than no comments. Always make a priority of keeping the comments up-to-date when the code changes!

与代码相抵触的注释比没有注释更糟糕。当代码发生变化时，一定要优先考虑保持注释的更新。

Comments should be complete sentences. The first word should be capitalized, unless it is an identifier that begins with a lower case letter (never alter the case of identifiers!).

注释应该是完整的句子。第一个单词应该大写，除非它是一个以小写字母开头的标识符（永远不要改变标识符的大小写！）。

Block comments generally consist of one or more paragraphs built out of complete sentences, with each sentence ending in a period.

块注释通常由一个或多个完整的句子组成，每个句子以句号结束。

You should use two spaces after a sentence-ending period in multi- sentence comments, except after the final sentence.

在包含多句话的注释中，你应该在句子结束的句号后使用两个空格，但最后一句话后除外。

Ensure that your comments are clear and easily understandable to other speakers of the language you are writing in.

确保你的注释是清晰的，对于其他使用你所书写的语言的人来说容易理解。

Python coders from non-English speaking countries: please write your comments in English, unless you are 120% sure that the code will never be read by people who don't speak your language.

来自非英语国家的Python编码员：请用英语写评论，除非你有120%的把握，代码不会被不讲你的语言的人阅读。

## [Block Comments](https://www.python.org/dev/peps/pep-0008/#id31)

块注释

Block comments generally apply to some (or all) code that follows them, and are indented to the same level as that code. Each line of a block comment starts with a `#` and a single space (unless it is indented text inside the comment).

块状注释通常适用于它们后面的一些（或全部）代码，并缩进到与该代码相同的水平。块状注释的每一行都以#号和一个空格开始（除非它是注释内的缩进文本）。

Paragraphs inside a block comment are separated by a line containing a single `#`.

块状注释内的段落由包含一个#的行来分隔。

## [Inline Comments](https://www.python.org/dev/peps/pep-0008/#id32)

内联注释

Use inline comments sparingly.

尽量少用内联注释。

An inline comment is a comment on the same line as a statement. Inline comments should be separated by at least two spaces from the statement. They should start with a # and a single space.

内联注释是指与语句在同一行的注释。内联注释应该与语句至少隔开两个空格。它们应该以`#`和一个空格开始。

Inline comments are unnecessary and in fact distracting if they state the obvious. Don't do this:

内联注释是不必要的，事实上，如果它们说明了明显的问题，就会分散注意力。不要这样做。

```python
x = x + 1                 # Increment x 自增x
```

But sometimes, this is useful:

但有时，这样做是有用的：

```python
x = x + 1                 # Compensate for border 边界补偿
```

## [Documentation Strings](https://www.python.org/dev/peps/pep-0008/#id33)

文档字符串

Conventions for writing good documentation strings (a.k.a. "docstrings") are immortalized in [PEP 257](https://www.python.org/dev/peps/pep-0257).

编写好的文档字符串（又称 "docstrings"）的惯例在[PEP 257](https://www.python.org/dev/peps/pep-0257)中得到了永久性确认。

- Write docstrings for all public modules, functions, classes, and methods. Docstrings are not necessary for non-public methods, but you should have a comment that describes what the method does. This comment should appear after the `def` line.

  为所有公共模块、函数、类和方法编写文档字符串。对于非公开的方法，不需要写文档说明，但你应该有一个注释来描述该方法的作用。这个注释应该出现在 `def `行的后面。

- [PEP 257](https://www.python.org/dev/peps/pep-0257) describes good docstring conventions. Note that most importantly, the `"""` that ends a multiline docstring should be on a line by itself:

  [PEP 257](https://www.python.org/dev/peps/pep-0257)描述了良好的文档字符串惯例。请注意，最重要的是，结束多行文档字符串的`"""`应该自己在一行。

  ```python
  """Return a foobang
  
  Optional plotz says to frobnicate the bizbaz first.
  """
  ```

- For one liner docstrings, please keep the closing `"""` on the same line:

  对于单行文档字符串，请将结尾的 `"""` 放在同一行。

  ```python
  """Return an ex-parrot."""
  ```

# [Naming Conventions](https://www.python.org/dev/peps/pep-0008/#id34)

命名规则

The naming conventions of Python's library are a bit of a mess, so we'll never get this completely consistent -- nevertheless, here are the currently recommended naming standards. New modules and packages (including third party frameworks) should be written to these standards, but where an existing library has a different style, internal consistency is preferred.

Python 库的命名规则有点乱，所以我们永远不会让它完全一致 -- 尽管如此，这里是目前推荐的命名标准。新的模块和包 (包括第三方框架) 应该按照这些标准来编写，但如果现有的库有不同的风格，保持内部的一致性是首选。

## [Overriding Principle](https://www.python.org/dev/peps/pep-0008/#id35)

覆盖原则

Names that are visible to the user as public parts of the API should follow conventions that reflect usage rather than implementation.

作为API的公共部分，对用户可见的名称应该遵循反映用法而非实现的惯例。

## [Descriptive: Naming Styles](https://www.python.org/dev/peps/pep-0008/#id36)

解释：命名方式

There are a lot of different naming styles. It helps to be able to recognize what naming style is being used, independently from what they are used for.

有很多不同的命名方式。对于我们来说，能够在与它们的用途无关的基础上辨别其所使用的命名方式，这是有帮助的。

The following naming styles are commonly distinguished:

以下是命名方式的常见区别：

- `b` (single lowercase letter)

  `b`(单个小写字母)

- `B` (single uppercase letter)

  `B`（单个大写字母）

- `lowercase`

  `lowercase`（小写字母）

- `lower_case_with_underscores`

  `lower_case_with_underscores`（带下划线的小写字母）

- `UPPERCASE`

  `UPPERCASE`（大写字母）

- `UPPER_CASE_WITH_UNDERSCORES`

  `UPPER_CASE_WITH_UNDERSCORES`（带下划线的大写字母）

- `CapitalizedWords` (or CapWords, or CamelCase -- so named because of the bumpy look of its letters [[4\]](https://www.python.org/dev/peps/pep-0008/#id11)). This is also sometimes known as StudlyCaps.

  `CapitalizedWords`（或CapWords，或CamelCase--因其字母的凹凸外观而得名[4]）。这有时也被称为StudlyCaps。

  Note: When using acronyms in CapWords, capitalize all the letters of the acronym. Thus HTTPServerError is better than HttpServerError.

  注意：当在CapWords中使用首字母时，要将首字母的所有字母大写。因此HTTPServerError比HttpServerError好。

- `mixedCase` (differs from CapitalizedWords by initial lowercase character!)

  `mixedCase`（与CapitalizedWords不同的是初始小写字符！）

- `Capitalized_Words_With_Underscores` (ugly!)

  `Capitalized_Words_With_Underscores`(丑陋！)

There's also the style of using a short unique prefix to group related names together. This is not used much in Python, but it is mentioned for completeness. For example, the `os.stat()` function returns a tuple whose items traditionally have names like `st_mode`, `st_size`, `st_mtime` and so on. (This is done to emphasize the correspondence with the fields of the POSIX system call struct, which helps programmers familiar with that.)

还有一种风格是使用一个短的唯一的前缀来把相关的名字组合在一起。这在 Python 中用得不多，但为了完整起见，还是要提到它。例如，`os.stat()`函数返回一个元组，其项目传统上有`st_mode`、`st_size`、`st_mtime`等名称。(这样做是为了强调与 POSIX 系统调用结构的字段的对应关系，这有助于熟悉该结构的程序员。)

The X11 library uses a leading X for all its public functions. In Python, this style is generally deemed unnecessary because attribute and method names are prefixed with an object, and function names are prefixed with a module name.

X11 库在其所有的公共函数中都使用了前导 X。在Python中，这种风格通常被认为是不必要的，因为属性和方法名的前缀是一个对象，而函数名的前缀是一个模块名。

In addition, the following special forms using leading or trailing underscores are recognized (these can generally be combined with any case convention):

此外，还可以识别以下使用前导或尾部下划线的特殊形式（这些通常可以与任何大小写惯例相结合）。

- `_single_leading_underscore`: weak "internal use" indicator. E.g. `from M import *` does not import objects whose names start with an underscore.

  `_single_leading_underscore`：弱的 "内部使用 "指标。例如，`from M import *`不会导入名称以下划线开头的对象。

- `single_trailing_underscore_`: used by convention to avoid conflicts with Python keyword, e.g.

  `single_trailing_underscore_`：按惯例使用，以避免与Python关键字冲突，例如。

  ```python
  tkinter.Toplevel(master, class_='ClassName')
  ```

- `__double_leading_underscore`: when naming a class attribute, invokes name mangling (inside class FooBar, `__boo` becomes `_FooBar__boo`; see below).

  `__double_leading_underscore`：当命名一个类的属性时，调用名称混用（在类FooBar中，`__boo`变成`_FooBar__boo`；见下文）。

- `__double_leading_and_trailing_underscore__`: "magic" objects or attributes that live in user-controlled namespaces. E.g. `__init__`, `__import__` or `__file__`. Never invent such names; only use them as documented.

  `__double_leading_and_trailing_underscore__`。存在于用户控制的命名空间中的 "魔术 "对象或属性。例如：`__init__`，`__import__`或`__file__`。不要发明这样的名字；只使用文档中的名字。

## [Prescriptive: Naming Conventions](https://www.python.org/dev/peps/pep-0008/#id37)

约定：命名惯例

### [Names to Avoid](https://www.python.org/dev/peps/pep-0008/#id38)

应该避免的名称

Never use the characters 'l' (lowercase letter el), 'O' (uppercase letter oh), or 'I' (uppercase letter eye) as single character variable names.

不要使用字符 "l"（小写字母el）、"O"（大写字母oh）或 "I"（大写字母eye）作为单字符变量名称。

In some fonts, these characters are indistinguishable from the numerals one and zero. When tempted to use 'l', use 'L' instead.

在某些字体中，这些字符与数字1和0无法区分。当想使用'l'时，请使用'L'代替。

### [ASCII Compatibility](https://www.python.org/dev/peps/pep-0008/#id39)

ASCII 兼容性

Identifiers used in the standard library must be ASCII compatible as described in the [policy section](https://www.python.org/dev/peps/pep-3131/#policy-specification) of [PEP 3131](https://www.python.org/dev/peps/pep-3131).

在标准库中使用的标识符必须是ASCII兼容的，如PEP 3131的政策部分所述。

### [Package and Module Names](https://www.python.org/dev/peps/pep-0008/#id40)

包和模块的名称

Modules should have short, all-lowercase names. Underscores can be used in the module name if it improves readability. Python packages should also have short, all-lowercase names, although the use of underscores is discouraged.

模块应该有简短的、全小写的名字。如果能提高可读性，可以在模块名称中使用下划线。Python 包也应该有短的、全小写的名字，尽管不鼓励使用下划线。

When an extension module written in C or C++ has an accompanying Python module that provides a higher level (e.g. more object oriented) interface, the C/C++ module has a leading underscore (e.g. `_socket`).

当一个用 C 或 C++ 编写的扩展模块有一个附带的 Python 模块，它提供了一个更高级别的 (例如，更多的面向对象) 接口，C/C++ 模块有一个前导下划线 (例如 _socket)。

### [Class Names](https://www.python.org/dev/peps/pep-0008/#id41)

类名称

Class names should normally use the CapWords convention.

类的名字通常应该使用CapWords惯例。

The naming convention for functions may be used instead in cases where the interface is documented and used primarily as a callable.

在接口被记录下来并主要作为可调用的情况下，可以使用函数的命名惯例。

Note that there is a separate convention for builtin names: most builtin names are single words (or two words run together), with the CapWords convention used only for exception names and builtin constants.

请注意，对于内建程序的名称有一个单独的约定：大多数内建程序的名称是单字（或两个字并列），CapWords约定只用于异常名称和内建程序常量。

### [Type Variable Names](https://www.python.org/dev/peps/pep-0008/#id42)

类型变量名称

Names of type variables introduced in [PEP 484](https://www.python.org/dev/peps/pep-0484) should normally use CapWords preferring short names: `T`, `AnyStr`, `Num`. It is recommended to add suffixes `_co` or `_contra` to the variables used to declare covariant or contravariant behavior correspondingly:

PEP 484中引入的类型变量的名称通常应该使用CapWords，更倾向于使用短名称。建议在用于声明协变或反变行为的变量上添加后缀`_co`或`_contra`。

```python
from typing import TypeVar

VT_co = TypeVar('VT_co', covariant=True)
KT_contra = TypeVar('KT_contra', contravariant=True)
```

### [Exception Names](https://www.python.org/dev/peps/pep-0008/#id43)

异常名称

Because exceptions should be classes, the class naming convention applies here. However, you should use the suffix "Error" on your exception names (if the exception actually is an error).

因为异常应该是类，所以类的命名惯例在此适用。然而，你应该在你的异常名称上使用后缀 "Error"（如果该异常实际上是一个错误）。

### [Global Variable Names](https://www.python.org/dev/peps/pep-0008/#id44)

全局变量名称

(Let's hope that these variables are meant for use inside one module only.) The conventions are about the same as those for functions.

(我们希望这些变量只在一个模块中使用。) 这些约定与函数的约定差不多。

Modules that are designed for use via `from M import *` should use the `__all__` mechanism to prevent exporting globals, or use the older convention of prefixing such globals with an underscore (which you might want to do to indicate these globals are "module non-public").

被设计为通过`from M import *` 使用的模块应该使用` __all__` 机制来防止导出全局变量，或者使用较早的惯例，在这些全局变量前加一个下划线 (你可能想这样做来表示这些全局变量是 "模块非公共的")。

### [Function and Variable Names](https://www.python.org/dev/peps/pep-0008/#id45)

函数和局部变量名称

Function names should be lowercase, with words separated by underscores as necessary to improve readability.

函数名称应该是小写的，必要时用下划线隔开，以提高可读性。

Variable names follow the same convention as function names.

变量名遵循与函数名相同的约定。

mixedCase is allowed only in contexts where that's already the prevailing style (e.g. threading.py), to retain backwards compatibility.

混合大小写只允许在已经是主流风格的情况下使用（例如threading.py），以保持向后兼容。

### [Function and Method Arguments](https://www.python.org/dev/peps/pep-0008/#id46)

函数和方法参数

Always use `self` for the first argument to instance methods.

总是使用`self`作为实例方法的第一个参数。

Always use `cls` for the first argument to class methods.

总是使用`cls`作为类方法的第一个参数。

If a function argument's name clashes with a reserved keyword, it is generally better to append a single trailing underscore rather than use an abbreviation or spelling corruption. Thus `class_` is better than `clss`. (Perhaps better is to avoid such clashes by using a synonym.)

如果一个函数参数的名字与一个保留关键字冲突，一般来说，最好在后面加上一个下划线，而不是使用缩写或拼写错误。因此`class_`比`clss`好。也许更好的是通过使用同义词来避免这种冲突）。

### [Method Names and Instance Variables](https://www.python.org/dev/peps/pep-0008/#id47)

方法名称和实例变量

Use the function naming rules: lowercase with words separated by underscores as necessary to improve readability.

使用函数命名规则：小写字母，必要时用下划线分隔，以提高可读性。

Use one leading underscore only for non-public methods and instance variables.

只对非公共方法和实例变量使用一个前导下划线。

To avoid name clashes with subclasses, use two leading underscores to invoke Python's name mangling rules.

为了避免与子类的名称冲突，使用两个前导下划线来调用 Python 的名称混合规则。

Python mangles these names with the class name: if class Foo has an attribute named `__a`, it cannot be accessed by `Foo.__a`. (An insistent user could still gain access by calling `Foo._Foo__a`.) Generally, double leading underscores should be used only to avoid name conflicts with attributes in classes designed to be subclassed.

Python 将这些名字与类的名字混在一起：如果类 Foo 有一个名为 `__a` 的属性，它就不能被 `Foo.__a` 访问。(一个执着的用户仍然可以通过调用 `Foo._Foo__a` 获得访问权。)一般来说，双引号应该只用于避免与设计为子类的类中的属性发生名称冲突。

Note: there is some controversy about the use of __names (see below).

注意：对于`__names`的使用有一些争议（见下文）。

### [Constants](https://www.python.org/dev/peps/pep-0008/#id48)

常量

Constants are usually defined on a module level and written in all capital letters with underscores separating words. Examples include `MAX_OVERFLOW` and `TOTAL`.

常量通常是在模块层面上定义的，用大写字母书写，用下划线隔开单词。例如，`MAX_OVERFLOW`和`TOTAL`。

### [Designing for Inheritance](https://www.python.org/dev/peps/pep-0008/#id49)

和集成相关的设计

Always decide whether a class's methods and instance variables (collectively: "attributes") should be public or non-public. If in doubt, choose non-public; it's easier to make it public later than to make a public attribute non-public.

始终决定一个类的方法和实例变量（统称："属性"）应该是公共的还是非公共的。如果有疑问，请选择非公共的；以后把它变成公共的比把公共属性变成非公共的要容易。

Public attributes are those that you expect unrelated clients of your class to use, with your commitment to avoid backwards incompatible changes. Non-public attributes are those that are not intended to be used by third parties; you make no guarantees that non-public attributes won't change or even be removed.

公共属性是那些你期望你的类的无关客户使用的属性，你的承诺是避免向后不兼容的变化。非公共属性是那些不打算被第三方使用的属性；你不保证非公共属性不会改变，甚至不会被删除。

We don't use the term "private" here, since no attribute is really private in Python (without a generally unnecessary amount of work).

我们在这里不使用 "私有 "这个术语，因为在 Python 中没有任何属性是真正的私有的 (如果不做大量不必要的工作的话)。

Another category of attributes are those that are part of the "subclass API" (often called "protected" in other languages). Some classes are designed to be inherited from, either to extend or modify aspects of the class's behavior. When designing such a class, take care to make explicit decisions about which attributes are public, which are part of the subclass API, and which are truly only to be used by your base class.

另一类属性是 "子类 API "的一部分 (在其它语言中通常称为 "保护")。一些类被设计成可以被继承的，用来扩展或修改类的行为的各个方面。在设计这样的类时，要注意明确决定哪些属性是公共的，哪些是子类API的一部分，哪些是真正只能由你的基类使用的。

With this in mind, here are the Pythonic guidelines:

考虑到这一点，以下是Pythonic准则：

- Public attributes should have no leading underscores.

  公共属性不应该有前导下划线。

- If your public attribute name collides with a reserved keyword, append a single trailing underscore to your attribute name. This is preferable to an abbreviation or corrupted spelling. (However, notwithstanding this rule, 'cls' is the preferred spelling for any variable or argument which is known to be a class, especially the first argument to a class method.)

  如果你的公共属性名称与一个保留的关键字相冲突，请在你的属性名称后面加上一个单一的尾部下划线。这比缩写或破坏性的拼写更可取。(然而，尽管有这个规则，"cls "是任何已知为类的变量或参数的首选拼写，特别是类方法的第一个参数。)

  Note 1: See the argument name recommendation above for class methods.

  注1：参见上面关于类方法的参数名称建议。

- For simple public data attributes, it is best to expose just the attribute name, without complicated accessor/mutator methods. Keep in mind that Python provides an easy path to future enhancement, should you find that a simple data attribute needs to grow functional behavior. In that case, use properties to hide functional implementation behind simple data attribute access syntax.

  对于简单的公共数据属性，最好只公开属性名称，而不公开复杂的访问器/混合器方法。请记住，如果你发现一个简单的数据属性需要增加功能行为，Python 提供了一个方便的途径来实现未来的增强。在这种情况下，使用特性将功能实现隐藏在简单的数据属性访问语法的后面。

  Note 1: Properties only work on new-style classes.

  注1：特性只在新式类上工作。

  Note 2: Try to keep the functional behavior side-effect free, although side-effects such as caching are generally fine.

  注 2: 尽量保持功能行为不受副作用影响，尽管像缓存这样的副作用一般是可以的。

  Note 3: Avoid using properties for computationally expensive operations; the attribute notation makes the caller believe that access is (relatively) cheap.

  注意3：避免将特性用于计算昂贵的操作；特性符号使调用者相信访问是（相对）便宜的。

- If your class is intended to be subclassed, and you have attributes that you do not want subclasses to use, consider naming them with double leading underscores and no trailing underscores. This invokes Python's name mangling algorithm, where the name of the class is mangled into the attribute name. This helps avoid attribute name collisions should subclasses inadvertently contain attributes with the same name.

  如果你的类打算被子类化，并且你有不想让子类使用的属性，考虑用双头下划线和无尾下划线来命名它们。这将调用 Python 的名称混合算法，类的名称将被混合成属性名称。这有助于在子类无意中包含相同名称的属性时避免属性名称的冲突。

  Note 1: Note that only the simple class name is used in the mangled name, so if a subclass chooses both the same class name and attribute name, you can still get name collisions.

  注1：注意只有简单的类名被用于混杂的名称中，所以如果一个子类选择了相同的类名和属性名，你仍然可以得到名称碰撞。

  Note 2: Name mangling can make certain uses, such as debugging and `__getattr__()`, less convenient. However the name mangling algorithm is well documented and easy to perform manually.

  注2: 名字混杂会使某些使用，如调试和 `__getattr__()` ，变得不那么方便。然而，名称混杂的算法有很好的文档记录，并且很容易手动执行。

  Note 3: Not everyone likes name mangling. Try to balance the need to avoid accidental name clashes with potential use by advanced callers.

  注3: 不是每个人都喜欢名字杂乱无章。试着平衡避免意外的名字冲突的需要和高级调用者的潜在使用。

## [Public and Internal Interfaces](https://www.python.org/dev/peps/pep-0008/#id50)

公共接口和内部接口

Any backwards compatibility guarantees apply only to public interfaces. Accordingly, it is important that users be able to clearly distinguish between public and internal interfaces.

任何向后兼容的保证只适用于公共接口。因此，用户必须能够清楚地区分公共接口和内部接口。

Documented interfaces are considered public, unless the documentation explicitly declares them to be provisional or internal interfaces exempt from the usual backwards compatibility guarantees. All undocumented interfaces should be assumed to be internal.

有文档记载的接口被认为是公共的，除非文档中明确声明它们是临时的或内部的接口，不受通常的向后兼容性保证的影响。所有无文档的接口应该被认为是内部的。

To better support introspection, modules should explicitly declare the names in their public API using the `__all__` attribute. Setting `__all__` to an empty list indicates that the module has no public API.

为了更好地支持自省，模块应该使用 `__all__` 属性明确地声明其公共 API 中的名称。将 `__all__` 设置为一个空列表，表示该模块没有公共 API。

Even with `__all__` set appropriately, internal interfaces (packages, modules, classes, functions, attributes or other names) should still be prefixed with a single leading underscore.

即使适当地设置了 `__all__`，内部接口 (包、模块、类、函数、属性或其他名称) 仍然应该在前面加上一个下划线。

An interface is also considered internal if any containing namespace (package, module or class) is considered internal.

任何包含命名空间（包、模块或类）的接口也被认为是内部的。

Imported names should always be considered an implementation detail. Other modules must not rely on indirect access to such imported names unless they are an explicitly documented part of the containing module's API, such as `os.path` or a package's `__init__` module that exposes functionality from submodules.

导入的名字应该总是被认为是一个实现细节。其他模块不能依赖对这些导入名称的间接访问，除非它们是包含模块的 API 中明确记录的一部分，例如 `os.path` 或包的 `__init__` 模块，它暴露了子模块的功能。

# [Programming Recommendations](https://www.python.org/dev/peps/pep-0008/#id51)

编程建议

- Code should be written in a way that does not disadvantage other implementations of Python (PyPy, Jython, IronPython, Cython, Psyco, and such).

  代码的编写方式不应不利于Python的其他实现(PyPy, Jython, IronPython, Cython, Psyco等等)。

  For example, do not rely on CPython's efficient implementation of in-place string concatenation for statements in the form `a += b` or `a = a + b`. This optimization is fragile even in CPython (it only works for some types) and isn't present at all in implementations that don't use refcounting. In performance sensitive parts of the library, the `''.join()` form should be used instead. This will ensure that concatenation occurs in linear time across various implementations.

  例如，不要依赖CPython对 `a += b`或 `a = a + b`形式的语句进行原地字符串连接的高效实现。这种优化即使在CPython中也是脆弱的（它只对某些类型有效），而且在不使用refcounting的实现中根本不存在。在库的性能敏感部分，应该使用`''.join()`形式来代替。这将确保连接在不同的实现中以线性时间发生。

- Comparisons to singletons like None should always be done with `is` or `is not`, never the equality operators.

  对None这样的单例的比较应该总是用 `is`或 `is not`来完成，而不是用比较运算符。

  Also, beware of writing `if x` when you really mean `if x is not None` -- e.g. when testing whether a variable or argument that defaults to None was set to some other value. The other value might have a type (such as a container) that could be false in a boolean context!

  另外，当你真正的意思是`if x is not None`时，要注意写 `if x`--例如，当测试一个默认为None的变量或参数是否被设置为其他值时。其他的值可能有一个类型（如容器），在布尔语境中可能是false。

- Use `is not` operator rather than `not ... is`. While both expressions are functionally identical, the former is more readable and preferred:

  使用 `is not `操作符，而不是 `not ... is`。虽然这两个表达式在功能上是相同的，但前者更具可读性，是首选。

  ```python
  # Correct:
  if foo is not None:
  ```

  ```python
  # Wrong:
  if not foo is None:
  ```

- When implementing ordering operations with rich comparisons, it is best to implement all six operations (`__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`) rather than relying on other code to only exercise a particular comparison.

  当用丰富的比较实现排序操作时，最好是实现所有的六个操作 (`__eq__`, `__ne__`, `__lt__`, `__le__`, `__gt__`, `__ge__`)，而不是依靠其他代码只行使一个特定的比较。

  To minimize the effort involved, the `functools.total_ordering()` decorator provides a tool to generate missing comparison methods.

  为了最小化所涉及的工作，`functools.total_ordering()`装饰器提供了一个工具来生成缺少的比较方法。

  [PEP 207](https://www.python.org/dev/peps/pep-0207) indicates that reflexivity rules *are* assumed by Python. Thus, the interpreter may swap `y > x` with `x < y`, `y >= x` with `x <= y`, and may swap the arguments of `x == y` and `x != y`. The `sort()` and `min()` operations are guaranteed to use the `<` operator and the `max()` function uses the `>` operator. However, it is best to implement all six operations so that confusion doesn't arise in other contexts.

  PEP 207指出，反射性规则是由Python假定的。因此，解释器可以用`x < y`交换`y > x`，用`x <= y`交换`y >= x`，并且可以交换`x == y`和`x != y`的参数。 `sort()`和`min()`操作保证使用`<`操作符，`max()`函数使用`>`操作符。然而，最好是实现所有的六种操作，这样在其他情况下就不会出现混淆了。

- Always use a def statement instead of an assignment statement that binds a lambda expression directly to an identifier:

  始终使用def语句，而不是直接将lambda表达式与标识符绑定的赋值语句。

  ```python
  # Correct:
  def f(x): return 2*x
  ```

  ```python
  # Wrong:
  f = lambda x: 2*x
  ```

  The first form means that the name of the resulting function object is specifically 'f' instead of the generic '<lambda>'. This is more useful for tracebacks and string representations in general. The use of the assignment statement eliminates the sole benefit a lambda expression can offer over an explicit def statement (i.e. that it can be embedded inside a larger expression)

  第一种形式意味着产生的函数对象的名称是具体的 "f"，而不是通用的"<lambda>"。这对回溯和一般的字符串表示法更有用。使用赋值语句消除了lambda表达式比显式def语句所能提供的唯一好处（即它可以嵌入到一个更大的表达式中）。

- Derive exceptions from `Exception` rather than `BaseException`. Direct inheritance from `BaseException` is reserved for exceptions where catching them is almost always the wrong thing to do.

  从`Exception`而不是`BaseException`派生异常。从`BaseException`中直接继承是为那些捕捉异常几乎都是错误的事情所保留的。

  Design exception hierarchies based on the distinctions that code *catching* the exceptions is likely to need, rather than the locations where the exceptions are raised. Aim to answer the question "What went wrong?" programmatically, rather than only stating that "A problem occurred" (see [PEP 3151](https://www.python.org/dev/peps/pep-3151) for an example of this lesson being learned for the builtin exception hierarchy)

  在设计异常层次时，要基于捕捉异常的代码可能需要的区别，而不是异常发生的位置。争取以编程方式回答 "出了什么问题？"，而不是只说 "发生了一个问题"（参见PEP 3151，这是一个关于内建异常层次的例子）。

  Class naming conventions apply here, although you should add the suffix "Error" to your exception classes if the exception is an error. Non-error exceptions that are used for non-local flow control or other forms of signaling need no special suffix.

  类的命名惯例在此适用，不过如果异常是一个错误，你应该在你的异常类中添加后缀 "Error"。用于非本地流控制或其他形式的信号的非错误异常不需要特殊的后缀。

- Use exception chaining appropriately. In Python 3, "raise X from Y" should be used to indicate explicit replacement without losing the original traceback.

  适当地使用异常链。在Python 3中，"raise X from Y"应该被用来表示明确的替换，而不会丢失原始回溯。

  When deliberately replacing an inner exception (using "raise X" in Python 2 or "raise X from None" in Python 3.3+), ensure that relevant details are transferred to the new exception (such as preserving the attribute name when converting KeyError to AttributeError, or embedding the text of the original exception in the new exception message).

  当故意替换一个内部异常时 (在 Python 2 中使用 "raise X" 或在 Python 3.3+ 中使用 "raise X from None")，确保相关的细节被转移到新的异常中 (比如在将 KeyError 转换为 AttributeError 时保留属性名称，或者将原始异常的文本嵌入到新的异常消息中)。

- When raising an exception in Python 2, use `raise ValueError('message')` instead of the older form `raise ValueError, 'message'`.

  当在 Python 2 中引发一个异常时，使用 `raise ValueError('message')` 而不是老式的 `raise ValueError, 'message'`。

  The latter form is not legal Python 3 syntax.

  后一种形式不是合法的 Python 3 语法。

  The paren-using form also means that when the exception arguments are long or include string formatting, you don't need to use line continuation characters thanks to the containing parentheses.

  使用括号的形式也意味着当异常参数很长或者包括字符串格式化时，由于包含括号，你不需要使用续行字符。

- When catching exceptions, mention specific exceptions whenever possible instead of using a bare `except:` clause:

  当捕获异常时，尽可能地提到具体的异常，而不是使用赤裸裸的 except: 子句：

  ```python
  try:
      import platform_specific_module
  except ImportError:
      platform_specific_module = None
  ```

  A bare `except:` clause will catch SystemExit and KeyboardInterrupt exceptions, making it harder to interrupt a program with Control-C, and can disguise other problems. If you want to catch all exceptions that signal program errors, use `except Exception:` (bare except is equivalent to `except BaseException:`).

  光秃秃的`except:`子句会捕捉SystemExit和KeyboardInterrupt异常，使得用Control-C中断程序变得更加困难，并且可以掩盖其他问题。如果你想捕捉所有提示程序错误的异常，请使用 `except Exception:`。(裸except等同于`except BaseException:`)。

  A good rule of thumb is to limit use of bare 'except' clauses to two cases:

  一个好的经验法则是将裸 "except "条款的使用限制在两种情况下：

  1. If the exception handler will be printing out or logging the traceback; at least the user will be aware that an error has occurred.

     如果异常处理程序将打印出或记录回溯；至少用户会意识到发生了错误。

  2. If the code needs to do some cleanup work, but then lets the exception propagate upwards with `raise`. `try...finally` can be a better way to handle this case.

     如果代码需要做一些清理工作，但又让异常通过 `raise` 向上传播，那么 `try...final` 可以是处理这种情况的更好方法。

- When binding caught exceptions to a name, prefer the explicit name binding syntax added in Python 2.6:

  当把捕获的异常绑定到一个名字上时，最好使用Python 2.6中增加的显式名字绑定语法。

  ```python
  try:
      process_data()
  except Exception as exc:
      raise DataProcessingFailedError(str(exc))
  ```

  This is the only syntax supported in Python 3, and avoids the ambiguity problems associated with the older comma-based syntax.

  这是Python 3中唯一支持的语法，它避免了与旧的基于逗号的语法相关的歧义问题。

- When catching operating system errors, prefer the explicit exception hierarchy introduced in Python 3.3 over introspection of `errno` values.

  当捕捉操作系统错误时，更喜欢Python 3.3中引入的显式异常层次结构，而不是对`errno`值的内省。

- Additionally, for all try/except clauses, limit the `try` clause to the absolute minimum amount of code necessary. Again, this avoids masking bugs:

  此外，对于所有的try/except子句，将`try`子句限制在绝对必要的最小代码量。同样，这也避免了掩盖错误。

  ```python
  # Correct:
  try:
      value = collection[key]
  except KeyError:
      return key_not_found(key)
  else:
      return handle_value(value)
  ```

  ```python
  # Wrong:
  try:
      # Too broad!
      return handle_value(collection[key])
  except KeyError:
      # Will also catch KeyError raised by handle_value()
      return key_not_found(key)
  ```

- When a resource is local to a particular section of code, use a `with` statement to ensure it is cleaned up promptly and reliably after use. A try/finally statement is also acceptable.

  当一个资源是某段代码的局部变量时，使用`with`语句来确保它在使用后被及时可靠地清理掉。`try/finally`语句也是可以接受的。

- Context managers should be invoked through separate functions or methods whenever they do something other than acquire and release resources:

  除了获取和释放资源外，上下文管理程序应通过单独的函数或方法来调用。

  ```python
  # Correct:
  with conn.begin_transaction():
      do_stuff_in_transaction(conn)
  ```

  ```python
  # Wrong:
  with conn:
      do_stuff_in_transaction(conn)
  ```

  The latter example doesn't provide any information to indicate that the `__enter__` and `__exit__` methods are doing something other than closing the connection after a transaction. Being explicit is important in this case.

  后面的例子没有提供任何信息来表明`__enter__`和`__exit__`方法除了在交易后关闭连接外，还在做其他事情。在这种情况下，明确说明是很重要的。

- Be consistent in return statements. Either all return statements in a function should return an expression, or none of them should. If any return statement returns an expression, any return statements where no value is returned should explicitly state this as `return None`, and an explicit return statement should be present at the end of the function (if reachable):

  在返回语句中要保持一致。在一个函数中，要么所有的返回语句都应该返回一个表达式，要么都不应该。如果任何返回语句都返回一个表达式，任何没有返回值的返回语句都应该明确说明为返回无，并且在函数的结尾应该有一个明确的返回语句（如果可以达到的话）。

  ```python
  # Correct:
  
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
      else:
          return None
  
  def bar(x):
      if x < 0:
          return None
      return math.sqrt(x)
  ```

  ```python
  # Wrong:
  
  def foo(x):
      if x >= 0:
          return math.sqrt(x)
  
  def bar(x):
      if x < 0:
          return
      return math.sqrt(x)
  ```

- Use string methods instead of the string module.

  使用字符串方法而不是字符串模块。

  String methods are always much faster and share the same API with unicode strings. Override this rule if backwards compatibility with Pythons older than 2.0 is required.

  字符串方法总是快得多，并且与unicode字符串共享相同的API。如果需要向后兼容2.0以上的Pythons，请覆盖此规则。

- Use `''.startswith()` and `''.endswith()` instead of string slicing to check for prefixes or suffixes.

  startswith() and endswith() are cleaner and less error prone:

  使用`''.startswith()`和`''.endswith()`代替字符串切分来检查前缀或后缀。

  ```python
  # Correct:
  if foo.startswith('bar'):
  ```

  ```python
  # Wrong:
  if foo[:3] == 'bar':
  ```

- Object type comparisons should always use isinstance() instead of comparing types directly:

  对象类型比较应该总是使用`isinstance()`而不是直接比较类型。

  ```python
  # Correct:
  if isinstance(obj, int):
  ```

  ```python
  # Wrong:
  if type(obj) is type(1):
  ```

  When checking if an object is a string, keep in mind that it might be a unicode string too! In Python 2, str and unicode have a common base class, basestring, so you can do:

  当检查一个对象是否是一个字符串时，请记住它也可能是一个unicode字符串! 在Python 2中，str和unicode有一个共同的基类，basestring，所以你可以这样做：

    ```python
    if isinstance(obj, basestring):
    ```

  Note that in Python 3, `unicode` and `basestring` no longer exist (there is only `str`) and a bytes object is 	no longer a kind of string (it is a sequence of integers instead).

  注意在 Python 3 中，`unicode` 和 `basestring` 不再存在 (只有 `str`)，字节对象不再是字符串的一种 (它是一个整数序列)。

- For sequences, (strings, lists, tuples), use the fact that empty sequences are false:

  对于序列，（字符串、列表、元组），使用空序列是假的这一事实：

  ```python
  # Correct:
  if not seq:
  if seq:
  ```

  ```python
  # Wrong:
  if len(seq):
  if not len(seq):
  ```

- Don't write string literals that rely on significant trailing whitespace. Such trailing whitespace is visually indistinguishable and some editors (or more recently, reindent.py) will trim them.

  不要写那些依赖大量尾部空白的字符串字面量。这样的尾部空白在视觉上是无法区分的，一些编辑器（或最近的reindent.py）会去除它们。

- Don't compare boolean values to True or False using `==`:

  不要用==来比较布尔值是真还是假：

  ```python
  # Correct:
  if greeting:
  ```

  ```python
  # Wrong:
  if greeting == True:
  ```

  Worse:

  更糟糕的方式：

  ```python
  # Wrong:
  if greeting is True:
  ```

- Use of the flow control statements `return`/`break`/`continue` within the finally suite of a `try...finally`, where the flow control statement would jump outside the finally suite, is discouraged. This is because such statements will implicitly cancel any active exception that is propagating through the finally suite:

  不鼓励在`try...finally`的finally模块中使用流控制语句`return`/`break`/`continue`，流控制语句会跳出finally模块。这是因为这些语句将隐含地取消任何正在通过finally模块传播的活动异常。
  
  ```python
  # Wrong:
  def foo():
      try:
          1 / 0
      finally:
          return 42
  ```

## [Function Annotations](https://www.python.org/dev/peps/pep-0008/#id52)

函数注解

With the acceptance of [PEP 484](https://www.python.org/dev/peps/pep-0484), the style rules for function annotations are changing.

随着PEP 484的接受，函数注解的风格规则正在发生变化。

- In order to be forward compatible, function annotations in Python 3 code should preferably use [PEP 484](https://www.python.org/dev/peps/pep-0484) syntax. (There are some formatting recommendations for annotations in the previous section.)

  为了向前兼容，Python 3代码中的函数注解最好使用PEP 484语法。(在上一节中有一些关于注释的格式建议)。

- The experimentation with annotation styles that was recommended previously in this PEP is no longer encouraged.

  我们不再鼓励以前在本PEP中推荐的注解风格试验。

- However, outside the stdlib, experiments within the rules of [PEP 484](https://www.python.org/dev/peps/pep-0484) are now encouraged. For example, marking up a large third party library or application with [PEP 484](https://www.python.org/dev/peps/pep-0484) style type annotations, reviewing how easy it was to add those annotations, and observing whether their presence increases code understandability.

  然而，在标准库之外，现在鼓励在PEP 484的规则范围内进行实验。例如，用PEP 484风格的类型注解标记一个大型的第三方库或应用程序，审查添加这些注解有多容易，并观察它们的存在是否增加了代码的可理解性。

- The Python standard library should be conservative in adopting such annotations, but their use is allowed for new code and for big refactorings.

  Python标准库在采用这种注解时应该是保守的，但对于新代码和大的重构来说，允许使用它们。

- For code that wants to make a different use of function annotations it is recommended to put a comment of the form:

  对于想要对函数注解进行不同使用的代码，建议加上一个形式的注释：

  ```python
  # type: ignore
  ```

  near the top of the file; this tells type checker to ignore all annotations. (More fine-grained ways of disabling complaints from type checkers can be found in [PEP 484](https://www.python.org/dev/peps/pep-0484).)

  在文件顶部附近；这告诉类型检查器忽略所有注释。(在PEP 484中可以找到禁用类型检查器报警的更精细的方法)。

- Like linters, type checkers are optional, separate tools. Python interpreters by default should not issue any messages due to type checking and should not alter their behavior based on annotations.

  像linters一样，类型检查器是可选的，独立的工具。Python 解释器在默认情况下不应该因为类型检查而发出任何消息，也不应该根据注解改变它们的行为。

- Users who don't want to use type checkers are free to ignore them. However, it is expected that users of third party library packages may want to run type checkers over those packages. For this purpose [PEP 484](https://www.python.org/dev/peps/pep-0484) recommends the use of stub files: .pyi files that are read by the type checker in preference of the corresponding .py files. Stub files can be distributed with a library, or separately (with the library author's permission) through the typeshed repo [[5\]](https://www.python.org/dev/peps/pep-0008/#id12).

  不想使用类型检查器的用户可以自由地忽略它们。然而，预计第三方库包的用户可能想要在这些包上运行类型检查器。为此，PEP 484推荐使用存根文件：类型检查器优先读取的.pyi文件，而不是相应的.py文件。存根文件可以与库一起发布，也可以通过 typeshed repo [5] 单独发布（得到库作者的许可）。

- For code that needs to be backwards compatible, type annotations can be added in the form of comments. See the relevant section of [PEP 484](https://www.python.org/dev/peps/pep-0484) [[6\]](https://www.python.org/dev/peps/pep-0008/#id13).

  对于需要向后兼容的代码，可以以注释的形式添加类型注释。参见PEP 484的相关章节[6]。

## [Variable Annotations](https://www.python.org/dev/peps/pep-0008/#id53)

变量注解

[PEP 526](https://www.python.org/dev/peps/pep-0526) introduced variable annotations. The style recommendations for them are similar to those on function annotations described above:

PEP 526引入了变量注解。对它们的风格建议与上面描述的关于函数注解的建议相似。

- Annotations for module level variables, class and instance variables, and local variables should have a single space after the colon.

  模块级变量、类和实例变量以及局部变量的注解应该在冒号后面有一个空格。

- There should be no space before the colon.

  冒号前不应该有空格。

- If an assignment has a right hand side, then the equality sign should have exactly one space on both sides:

  如果一个赋值有一个右侧，那么等号的两边应该都有一个空格。

  ```python
  # Correct:
  
  code: int
  
  class Point:
      coords: Tuple[int, int]
      label: str = '<unknown>'
  ```

  ```python
  # Wrong:
  
  code:int  # No space after colon
  code : int  # Space before colon
  
  class Test:
      result: int=0  # No spaces around equality sign
  ```

- Although the [PEP 526](https://www.python.org/dev/peps/pep-0526) is accepted for Python 3.6, the variable annotation syntax is the preferred syntax for stub files on all versions of Python (see [PEP 484](https://www.python.org/dev/peps/pep-0484) for details).

  尽管 [PEP 526](https://www.python.org/dev/peps/pep-0526) 被接受用于 Python 3.6，但在所有版本的 Python 上，变量注释语法是存根文件的首选语法 (详见 [PEP 484](https://www.python.org/dev/peps/pep-0484) 。)

Footnotes

注脚

| [[7\]](https://www.python.org/dev/peps/pep-0008/#id3) | *Hanging indentation* is a type-setting style where all the lines in a paragraph are indented except the first line. In the context of Python, the term is used to describe a style where the opening parenthesis of a parenthesized statement is the last non-whitespace character of the line, with subsequent lines being indented until the closing parenthesis. |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| [7]                                                   | *悬空缩进*是一种类型设置风格，其中一个段落中的所有行都是缩进的，除了第一行。在Python的上下文中，该术语用于描述这样一种风格：括号内语句的开头括号是该行的最后一个非空格字符，随后的行被缩进，直到结束括号。 |

# [References](https://www.python.org/dev/peps/pep-0008/#id54)

参考资料

| [[1\]](https://www.python.org/dev/peps/pep-0008/#id1) | [PEP 7](https://www.python.org/dev/peps/pep-0007), Style Guide for C Code, van Rossum |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| [1]                                                   | [PEP 7](https://www.python.org/dev/peps/pep-0007), C代码的风格指南, van Rossum |
| [[2\]](https://www.python.org/dev/peps/pep-0008/#id2) | Barry's GNU Mailman style guide http://barry.warsaw.us/software/STYLEGUIDE.txt |
| [2]                                                   | Barry的GNU Mailman风格指南 http://barry.warsaw.us/software/STYLEGUIDE.txt |
| [[3\]](https://www.python.org/dev/peps/pep-0008/#id4) | Donald Knuth's *The TeXBook*, pages 195 and 196.             |
| [3]                                                   | 唐纳德-克努斯的*The TeXBook*，第195和196页。                 |
| [[4\]](https://www.python.org/dev/peps/pep-0008/#id5) | http://www.wikipedia.com/wiki/CamelCase                      |
| [4]                                                   | http://www.wikipedia.com/wiki/CamelCase                      |
| [[5\]](https://www.python.org/dev/peps/pep-0008/#id6) | Typeshed repo https://github.com/python/typeshed             |
| [5]                                                   | Typeshed repo https://github.com/python/typeshed             |
| [[6\]](https://www.python.org/dev/peps/pep-0008/#id7) | Suggested syntax for Python 2.7 and straddling code https://www.python.org/dev/peps/pep-0484/#suggested-syntax-for-python-2-7-and-straddling-code |
| [6]                                                   | 建议Python 2.7的语法和跨越代码的语法https://www.python.org/dev/peps/pep-0484/#suggested-syntax-for-python-2-7-and-straddling-code |

# [Copyright](https://www.python.org/dev/peps/pep-0008/#id55)

版权

This document has been placed in the public domain.

本文件已被置于公共领域。

Source: https://github.com/python/peps/blob/master/pep-0008.txt

> 本翻译基于DeepL机翻加本人修改审阅完成。

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。