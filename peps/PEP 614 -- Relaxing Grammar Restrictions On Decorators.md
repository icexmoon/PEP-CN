# PEP 614 -- Relaxing Grammar Restrictions On Decorators

PEP 614 -- 放宽对装饰器的语法限制

| PEP:            | 614                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Relaxing Grammar Restrictions On Decorators                  |
| Author:         | Brandt Bucher <brandt at python.org>                         |
| Sponsor:        | Guido van Rossum <guido at python.org>                       |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 10-Feb-2020                                                  |
| Python-Version: | 3.9                                                          |
| Post-History:   | 11-Feb-2020, 18-Feb-2020, 03-Mar-2020                        |
| Resolution:     | https://mail.python.org/archives/list/python-dev@python.org/thread/VSR66MOTCDCY7ZFH4IG7QVFI2JXQQZQ5 |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0614/#abstract)
- [Motivation](https://www.python.org/dev/peps/pep-0614/#motivation)
- Rationale
  - [Allowing Any Expression](https://www.python.org/dev/peps/pep-0614/#allowing-any-expression)
  - What Counts As An "Expression"
    - [Tuple Displays *Must* Be Parenthesized](https://www.python.org/dev/peps/pep-0614/#tuple-displays-must-be-parenthesized)
    - [Named Expressions *Need Not* Be Parenthesized](https://www.python.org/dev/peps/pep-0614/#named-expressions-need-not-be-parenthesized)
- [Specification](https://www.python.org/dev/peps/pep-0614/#specification)
- [Backwards Compatibility](https://www.python.org/dev/peps/pep-0614/#backwards-compatibility)
- [How To Teach This](https://www.python.org/dev/peps/pep-0614/#how-to-teach-this)
- [Reference Implementation](https://www.python.org/dev/peps/pep-0614/#reference-implementation)
- [Copyright](https://www.python.org/dev/peps/pep-0614/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0614/#id2)

概述

Python currently requires that all decorators consist of a dotted name, optionally followed by a single call. This PEP proposes removing these limitations and allowing decorators to be any valid expression.

目前，Python 要求所有的装饰器都由一个带点的名字组成，后面可以有一个单独的调用。本PEP建议取消这些限制，允许装饰器是任何有效的表达式。

# [Motivation](https://www.python.org/dev/peps/pep-0614/#id3)

动机

When decorators were first being introduced, [Guido described](https://mail.python.org/archives/list/python-dev@python.org/message/P3JD24UFFPZUUDANOAI6GZAPIGY4CVK7) the motivation to limit their syntax as a preference, not a technical requirement:

当装饰器第一次被引入时，[Guido描述了](https://mail.python.org/archives/list/python-dev@python.org/message/P3JD24UFFPZUUDANOAI6GZAPIGY4CVK7)限制其语法的动机是一种偏好，而不是一种技术需要。

> I have a gut feeling about this one. I'm not sure where it comes from, but I have it... So while it would be quite easy to change the syntax to `@test` in the future, I'd like to stick to with the more restricted form unless a real use case is presented where allowing `@test` would increase readability.
>
> 我对这个问题有一种直觉。我不确定它来自哪里，但我有这种感觉...... 因此，虽然将来很容易将语法改为`@test`，但我还是想坚持使用更有限制的形式，除非有真正的用例，允许`@test`会增加可读性。

While these limitations were rarely encountered in practice, [BPO issues](https://bugs.python.org/issue19660) and [mailing list posts](https://mail.python.org/archives/list/python-ideas@python.org/thread/UQOCJH3KOPBP7P3AVNS3OYBGZPR3V2WO/#CAOXYF4GV76AFJNCYSYMQTBM7CIPPH5M) have consistently surfaced over the years requesting that they be removed. The [most recent one](https://mail.python.org/archives/list/python-ideas@python.org/thread/WOWD4P323DYDIGUQVWMESDWUG6QOW4MP) (which [prompted this proposal](https://mail.python.org/archives/list/python-ideas@python.org/message/FKE7ZFGUDCU5WVOE2QTD5XGMCNCOMETV)) contained a good example of code using the `PyQt5` library that would become more readable, idiomatic, and maintainable if the existing restrictions were relaxed. Slightly modified:

虽然这些限制在实践中很少遇到，但多年来[BPO问题](https://bugs.python.org/issue19660)和[邮件列表帖子](https://mail.python.org/archives/list/python-ideas@python.org/thread/UQOCJH3KOPBP7P3AVNS3OYBGZPR3V2WO/#CAOXYF4GV76AFJNCYSYMQTBM7CIPPH5M)不断出现，要求将其删除。[最近的一个帖子](https://mail.python.org/archives/list/python-ideas@python.org/thread/WOWD4P323DYDIGUQVWMESDWUG6QOW4MP)(它[促使本建议](https://mail.python.org/archives/list/python-ideas@python.org/message/FKE7ZFGUDCU5WVOE2QTD5XGMCNCOMETV))包含了一个使用`PyQt5`库的代码的好例子，如果放宽现有的限制，将变得更加可读、习惯和可维护。略有修改：

```python
buttons = [QPushButton(f'Button {i}') for i in range(10)]

# Do stuff with the list of buttons...

@buttons[0].clicked.connect
def spam():
    ...

@buttons[1].clicked.connect
def eggs():
    ...

# Do stuff with the list of buttons...
```

Currently, these decorations must be rewritten as something like:

目前，这些装饰必须被改写成这样的东西：

```python
button_0 = buttons[0]

@button_0.clicked.connect
def spam():
    ...

button_1 = buttons[1]

@button_1.clicked.connect
def eggs():
    ...
```

Further, the current grammar is already loose enough that it's trivial to hack more complicated decorator expressions together. So rather than disallow arbitrarily complex expressions, as intended, the current restrictions only make them uglier and less efficient:

此外，目前的语法已经足够宽松了，以至于把更复杂的装饰器表达式骇在一起是很容易的。因此，目前的限制并没有像预期的那样不允许任意复杂的表达式，而是使它们变得更丑陋，效率更低：

```python
# Identity function hack:

def _(x):
    return x

@_(buttons[0].clicked.connect)
def spam():
    ...

# eval hack:

@eval("buttons[1].clicked.connect")
def eggs():
    ...
```

> 原来还可以这么曲线救国......真是眼界大开，语言的缺陷居然可以把开发者逼成这样，译者注。

# [Rationale](https://www.python.org/dev/peps/pep-0614/#id4)

理论依据

## [Allowing Any Expression](https://www.python.org/dev/peps/pep-0614/#id5)

允许任何表达式

The decision to allow *any* valid expression (and not just relaxing the current restrictions to allow, for example, subscripting) has been considered as the next logical step in the evolution of decorator grammar for quite some time. As [Guido noted](https://mail.python.org/archives/list/python-ideas@python.org/message/CAOXYF4GV76AFJNCYSYMQTBM7CIPPH5M), during yet another mailing list thread:

允许任何有效表达式的决定（而不仅仅是放宽目前的限制，例如允许下标）被认为是装饰器语法发展的下一个逻辑步骤，已经有相当一段时间了。正如Guido所指出的，在另一个邮件列表线程中：

> I don't think it's reasonable to constrain it less than it currently is but more than a general expression.
>
> 我不认为约束它比目前少，但比一般表达式多是合理的。

Special-casing the grammar to allow *some* useful cases would only complicate the current situation, and all but guarantee that the process would repeat itself sometime in the future. Further, one purpose of this grammatical change is to discourage the temptation to use hacks like the `eval` and identity-function anti-patterns shown above.

将语法特殊化以允许一些有用的情况，只会使目前的情况复杂化，而且几乎可以保证这个过程会在未来的某个时候重复。此外，这种语法变化的一个目的是阻止使用像上面所示的`eval`和identity-function反模式的诱惑。

In short: if we're removing somewhat arbitrary restrictions, we should remove *all* of them.

简而言之：如果我们要删除一些任意的限制，我们应该把它们全部删除。

## [What Counts As An "Expression"](https://www.python.org/dev/peps/pep-0614/#id6)

什么算作一个 "表达式

Throughout this document, the word "expression" is used as defined in the [Python Language Reference](https://docs.python.org/3.9/reference/expressions.html#grammar-token-expression). This can be summarized as "anything that's valid as a test in `if`, `elif`, and `while` blocks". This differs subtly from a perhaps more popular [definition](https://docs.python.org/3/glossary.html#term-expression), which can be summarized as "anything that's valid as string input to `eval`".

在本文中，"表达式 "一词是按照[Python语言参考](https://docs.python.org/3.9/reference/expressions.html#grammar-token-expression)中的定义使用的。这可以概括为 "任何在`if`、`elif`和`while`块中作为测试有效的东西"。这与可能更流行的[定义](https://docs.python.org/3/glossary.html#term-expression)有细微差别，后者可以概括为 "任何可以作为字符串输入到`eval`的东西"。

This definition of "expression" is convenient in that it fits our needs well, and reuses the allowed grammar of existing language constructs. It has two subtle differences from the other definition:

这个 "表达式 "的定义很方便，因为它很符合我们的需要，并且重用了现有语言结构的允许语法。它与其他定义有两个微妙的区别：

### [Tuple Displays *Must* Be Parenthesized](https://www.python.org/dev/peps/pep-0614/#id7)

元组显示必须用括号表示

This is based on an observation Guido made in the same email. Continued immediately from above:

这是基于Guido在同一封邮件中的观察。紧接着上面的内容：

> Though I wouldn't allow commas-- there's no way that
>
> 虽然我不允许使用逗号--但不可能有
>
> ```python
> @f, g
> def pooh(): ...
> ```
>
> can make sense.
>
> 可以说得通。

Indeed, it may even lead inexperienced readers to conclude that several decorators are being applied, as if they were stacked. Requiring parentheses here makes the (admittedly nonsensical) intent clear without imposing further restrictions and grammar complications.

事实上，它甚至可能导致没有经验的读者得出结论，认为几个装饰器正在被应用，就像它们被堆叠起来一样。在这里要求使用圆括号可以清楚地表明（公认是无意义的）意图，而不需要施加进一步的限制和语法上的复杂化。

### [Named Expressions *Need Not* Be Parenthesized](https://www.python.org/dev/peps/pep-0614/#id8)

命名的表达式不需要加括号

Here, the choice of syntax is unambiguous. [PEP 572](https://www.python.org/dev/peps/pep-0572) explains why it requires parentheses around top-level expression statements:

在这里，语法的选择是毫不含糊的。PEP 572解释了为什么它要求在顶层表达式语句周围加上括号。

> This rule is included to simplify the choice for the user between an assignment statement and an assignment expression -- there is no syntactic position where both are valid.
>
> 包括这条规则是为了简化用户在赋值语句和赋值表达式之间的选择--没有一个语法位置是两者都有效的。

Since an assignment statement is not valid here, assignment expressions should not be unnecessarily burdened with parentheses.

由于赋值语句在这里是无效的，所以赋值表达式不应该有不必要的括号负担。

# [Specification](https://www.python.org/dev/peps/pep-0614/#id9)

定义

The grammar for decorators is currently:

目前，装饰者的语法是：

```python
decorator: '@' dotted_name [ '(' [arglist] ')' ] NEWLINE
```

This PEP proposes that it be simplified to:

本PEP建议将其简化为：

```python
decorator: '@' namedexpr_test NEWLINE
```

# [Backwards Compatibility](https://www.python.org/dev/peps/pep-0614/#id10)

向后兼容

This new grammar is fully backward-compatible with the existing grammar.

这个新的语法与现有的语法完全向后兼容。

# [How To Teach This](https://www.python.org/dev/peps/pep-0614/#id11)

如何教学

Decorators can continue to be taught as they always have; the average Python programmer is likely unaware that the current restriction even exists.

装饰器可以继续像以前一样被教授；一般的Python程序员可能不知道目前的限制存在。

# [Reference Implementation](https://www.python.org/dev/peps/pep-0614/#id12)

参考实现

The author has written a [CPython implementation](https://github.com/python/cpython/pull/18570).

作者写了一个CPython的实现。

# [Copyright](https://www.python.org/dev/peps/pep-0614/#id13)

版权声明

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

本文件被置于公共领域或CC0-1.0-Universal许可之下，以更多许可为准。

Source: https://github.com/python/peps/blob/master/pep-0614.rst