# PEP 634 -- Structural Pattern Matching: Specification

PEP 634 -- 结构化模式匹配：规范

| PEP:            | 634                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Structural Pattern Matching: Specification                   |
| Author:         | Brandt Bucher <brandt at python.org>, Guido van Rossum <guido at python.org> |
| BDFL-Delegate:  |                                                              |
| Discussions-To: | Python-Dev <[python-dev at python.org](mailto:python-dev@python.org?subject=PEP 634)> |
| Status:         | Accepted                                                     |
| Type:           | Standards Track                                              |
| Created:        | 12-Sep-2020                                                  |
| Python-Version: | 3.10                                                         |
| Post-History:   | 22-Oct-2020, 08-Feb-2021                                     |
| Replaces:       | [622](https://www.python.org/dev/peps/pep-0622)              |
| Resolution:     | https://mail.python.org/archives/list/python-committers@python.org/message/SQC2FTLFV5A7DV7RCEAR2I2IKJKGK7W3 |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#abstract)，概述
- Syntax and Semantics，语法和语义
  - [Overview and Terminology](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#overview-and-terminology)，概述和术语
  - The Match Statement，匹配语句
    - [Match Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#match-semantics)，匹配语义
    - [Guards](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id1)，守卫
    - [Irrefutable case blocks](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#irrefutable-case-blocks)，不可辩驳的case块
  - Patterns，模式
    - [AS Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#as-patterns)，AS 模式
    - [OR Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#or-patterns)，OR 模式
    - [Literal Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#literal-patterns)，字面模式
    - [Capture Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#capture-patterns)，捕获模式
    - [Wildcard Pattern](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id3)，通配符模式
    - [Value Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#value-patterns)，值模式
    - [Group Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#group-patterns)，群组模式
    - [Sequence Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#sequence-patterns)，序列模式
    - [Mapping Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#mapping-patterns)，映射模式
    - [Class Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#class-patterns)，类模式
- [Side Effects and Undefined Behavior](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#side-effects-and-undefined-behavior)，副作用和未定义行为
- [The Standard Library](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#the-standard-library)，标准库
- [Appendix A -- Full Grammar](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#appendix-a-full-grammar)，附录 A -- 完整语法
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id5)

概述

This PEP provides the technical specification for the match statement. It replaces [PEP 622](https://www.python.org/dev/peps/pep-0622), which is hereby split in three parts:

本PEP提供了匹配语句的技术规范。它取代了[PEP 622](https://www.python.org/dev/peps/pep-0622)，后者在此被分成了三个部分。

- [PEP 634](https://www.python.org/dev/peps/pep-0634): Specification

  [PEP 634](https://www.python.org/dev/peps/pep-0634)：规范

- [PEP 635](https://www.python.org/dev/peps/pep-0635): Motivation and Rationale

  [PEP 635](https://www.python.org/dev/peps/pep-0635)：动机和原理

- [PEP 636](https://www.python.org/dev/peps/pep-0636): Tutorial

  [PEP 636](https://www.python.org/dev/peps/pep-0636)：教程

This PEP is intentionally devoid of commentary; the motivation and all explanations of our design choices are in [PEP 635](https://www.python.org/dev/peps/pep-0635). First-time readers are encouraged to start with [PEP 636](https://www.python.org/dev/peps/pep-0636), which provides a gentler introduction to the concepts, syntax and semantics of patterns.

本PEP有意不做评论；动机和对我们设计选择的所有解释都在[PEP 635](https://www.python.org/dev/peps/pep-0635)中。我们鼓励第一次阅读的读者从[PEP 636](https://www.python.org/dev/peps/pep-0636)开始，它对模式的概念、语法和语义提供了一个比较温和的介绍。

# [Syntax and Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id6)

语法和语义

See [Appendix A](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#appendix-a) for the complete grammar.

完整的语法见附录A。

## [Overview and Terminology](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id7)

概述和术语

The pattern matching process takes as input a pattern (following `case`) and a subject value (following `match`). Phrases to describe the process include "the pattern is matched with (or against) the subject value" and "we match the pattern against (or with) the subject value".

模式匹配过程需要输入一个模式（`case`之后）和一个主题值（`match`之后）。描述该过程的短语包括 "模式与（或针对）主题值进行匹配 "和 "我们将模式与（或针对）主题值进行匹配"。

> 这里关于匹配过程的描述性短语比较艰涩难读，不过都属于英文原文的咬文嚼字范畴，无需深究，译者注。

The primary outcome of pattern matching is success or failure. In case of success we may say "the pattern succeeds", "the match succeeds", or "the pattern matches the subject value".

模式匹配的主要结果是成功或失败。在成功的情况下，我们可以说 "模式成功"，"匹配成功"，或 "模式与主题值匹配"。

In many cases a pattern contains subpatterns, and success or failure is determined by the success or failure of matching those subpatterns against the value (e.g., for OR patterns) or against parts of the value (e.g., for sequence patterns). This process typically processes the subpatterns from left to right until the overall outcome is determined. E.g., an OR pattern succeeds at the first succeeding subpattern, while a sequence patterns fails at the first failing subpattern.

在许多情况下，一个模式包含子模式，成功或失败是由这些子模式与值（如OR模式）或与值的一部分（如序列模式）匹配的成功或失败决定的。这个过程通常从左到右处理子模式，直到确定总体结果。例如，一个OR模式在第一个成功的子模式处成功，而一个序列模式在第一个失败的子模式处失败。

> 从描述看，序列模式类似于“AND模式”，译者注。

A secondary outcome of pattern matching may be one or more name bindings. We may say "the pattern binds a value to a name". When subpatterns tried until the first success, only the bindings due to the successful subpattern are valid; when trying until the first failure, the bindings are merged. Several more rules, explained below, apply to these cases.

模式匹配的次要结果可能是一个或多个名称的绑定。我们可以说 "该模式将一个值与一个名字绑定"。当子模式尝试到第一个成功时，只有由于成功的子模式而产生的绑定是有效的；当尝试到第一个失败时，绑定是合并的。还有几条规则，解释如下，适用于这些情况。

## [The Match Statement](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id8)

匹配语句

Syntax:

语法：

```python
match_stmt: "match" subject_expr ':' NEWLINE INDENT case_block+ DEDENT
subject_expr:
    | star_named_expression ',' star_named_expressions?
    | named_expression
case_block: "case" patterns [guard] ':' block
guard: 'if' named_expression
```

The rules `star_named_expression`, `star_named_expressions`, `named_expression` and `block` are part of the [standard Python grammar](https://docs.python.org/3.10/reference/grammar.html).

`star_named_expressions`, `star_named_expressions`, `named_expression` 和 `block` 规则是[标准 Python 语法](https://docs.python.org/3.10/reference/grammar.html)的一部分。

The rule `patterns` is specified below.

规则的`patterns`在下面指定。

For context, `match_stmt` is a new alternative for `compound_statement`:

对于上下文，`match_stmt` 是 `compound_statement` 的一个新的替代品。

```python
compound_statement:
    | if_stmt
    ...
    | match_stmt
```

The `match` and `case` keywords are soft keywords, i.e. they are not reserved words in other grammatical contexts (including at the start of a line if there is no colon where expected). This implies that they are recognized as keywords when part of a match statement or case block only, and are allowed to be used in all other contexts as variable or argument names.

`match`和`case`关键字是软关键字，也就是说，它们在其他语法环境中不是保留字（包括在一行的开头，如果没有预期的冒号）。这意味着它们仅在作为匹配语句或case块的一部分时被识别为关键字，而在所有其他情况下允许作为变量或参数名称使用。

> “软关键字”倒是个很别致的语言特性，至少我没有在其它主流语言中见过，但考虑到极其特殊的情况下可能带来的bug，最好还是尽量避免使用`match`或`case`作为变量名，译者注。

### [Match Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id9)

匹配语义

The match statement first evaluates the subject expression. If a comma is present a tuple is constructed using the standard rules.

匹配语句首先评估主题表达式。如果出现了逗号，则使用标准规则构建一个元组。

The resulting subject value is then used to select the first case block whose patterns succeeds matching it *and* whose guard condition (if present) is "truthy". If no case blocks qualify the match statement is complete; otherwise, the block of the selected case block is executed. The usual rules for executing a block nested inside a compound statement apply (e.g. an `if` statement).

然后，产生的主题值被用来选择第一个成功地与之匹配的case块，并且其守卫条件（如果存在）是 "truthy"。如果没有case块符合条件，则匹配语句完成；否则，所选case块的块被执行。嵌套在复合语句中的语句块的执行过程遵循通常的语法规则（例如`if`语句）。

> `guard`是Python匹配模式中定义的一个附属于case块的特殊语句部分，这里翻译为守卫，因为其作用类似于case块上附加的一个额外检测用的bool表达式，关于守卫语句的详细说明见后文，译者注。

Name bindings made during a successful pattern match outlive the executed block and can be used after the match statement.

在成功的模式匹配过程中产生的名称绑定会超越已执行的块，并且可以在匹配语句之后使用。

> 这里指匹配模式运行过程产生的名称绑定其作用域等同于匹配语句所在的作用域，译者注。

During failed pattern matches, some subpatterns may succeed. For example, while matching the pattern `(0, x, 1)` with the value `[0, 1, 2]`, the subpattern `x` may succeed if the list elements are matched from left to right. The implementation may choose to either make persistent bindings for those partial matches or not. User code including a match statement should not rely on the bindings being made for a failed match, but also shouldn't assume that variables are unchanged by a failed match. This part of the behavior is left intentionally unspecified so different implementations can add optimizations, and to prevent introducing semantic restrictions that could limit the extensibility of this feature.

在失败的模式匹配中，一些子模式可能会成功。例如，在用值`[0，1，2]`匹配模式`(0，x，1)`时，如果列表元素从左到右匹配，子模式`x`可能会成功。实现可以选择为这些部分匹配做持久的绑定，也可以不做绑定。包括匹配语句的用户代码不应该依赖为失败的匹配所做的绑定，但也不应该假设变量因失败的匹配而没有变化。这部分行为是故意不被指定的，以便不同的实现可以增加优化，并防止引入语义限制，从而限制该功能的可扩展性。

The precise pattern binding rules vary per pattern type and are specified below.

精确的模式绑定规则因模式类型而异，具体规定如下。

### [Guards](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id10)

守卫

If a guard is present on a case block, once the pattern or patterns in the case block succeed, the expression in the guard is evaluated. If this raises an exception, the exception bubbles up. Otherwise, if the condition is "truthy" the case block is selected; if it is "falsy" the case block is not selected.

如果case块上有一个守卫，一旦case块中的模式成功，就会对守卫中的表达式进行评估。如果这引起了一个异常，那么这个异常就会冒出来。否则，如果条件是 "真实的"，则case块被选中；如果是 "虚假的"，则case块不被选中。

Since guards are expressions they are allowed to have side effects. Guard evaluation must proceed from the first to the last case block, one at a time, skipping case blocks whose pattern(s) don't all succeed. (I.e., even if determining whether those patterns succeed may happen out of order, guard evaluation must happen in order.) Guard evaluation must stop once a case block is selected.

由于守卫是表达式，所以允许有副作用。守卫的评估必须从第一个case块到最后一个case块，一次一个地进行，跳过模式不全的case块。(也就是说，即使确定这些模式是否成功可能会不按顺序进行，守卫评估也必须按顺序进行）。) 一旦选择了一个case块，守卫评估必须停止。

> 这里的意思是守卫表达式中的运算会随着case块匹配依次执行，直到某个case块被匹配，译者注。

### [Irrefutable case blocks](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id11)

不可辩驳的case块

A pattern is considered irrefutable if we can prove from its syntax alone that it will always succeed. In particular, capture patterns and wildcard patterns are irrefutable, and so are AS patterns whose left-hand side is irrefutable, OR patterns containing at least one irrefutable pattern, and parenthesized irrefutable patterns.

如果我们可以仅从语法上证明一个模式总是成功的，那么这个模式就被认为是不可辩驳的。特别是，捕获模式和通配符模式是不可反驳的，左手边是不可反驳的AS模式，包含至少一个不可反驳模式的OR模式，以及括号内的不可反驳模式也是如此。

A case block is considered irrefutable if it has no guard and its pattern is irrefutable.

如果一个case块没有防护，并且其模式是不可反驳的，那么它就被认为是不可反驳的。

A match statement may have at most one irrefutable case block, and it must be last.

一个匹配语句最多可以有一个不可反驳的case块，而且必须是最后一个。

## [Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id12)

模式

The top-level syntax for patterns is as follows:

模式的顶级语法如下：

```python
patterns: open_sequence_pattern | pattern
pattern: as_pattern | or_pattern
as_pattern: or_pattern 'as' capture_pattern
or_pattern: '|'.closed_pattern+
closed_pattern:
    | literal_pattern
    | capture_pattern
    | wildcard_pattern
    | value_pattern
    | group_pattern
    | sequence_pattern
    | mapping_pattern
    | class_pattern
```

### [AS Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id13)

AS 模式

Syntax:

语法：

```python
as_pattern: or_pattern 'as' capture_pattern
```

(Note: the name on the right may not be `_`.)

(注意：右边的名字不一定是`_`)。

An AS pattern matches the OR pattern on the left of the `as` keyword against the subject. If this fails, the AS pattern fails. Otherwise, the AS pattern binds the subject to the name on the right of the `as` keyword and succeeds.

AS模式与`as`关键字左边的OR模式匹配，与主题匹配。如果失败，则AS模式失败。否则，AS模式将主题与`as`关键字右边的名字绑定，并成功。

### [OR Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id14)

OR 模式

Syntax:

语法：

```python
or_pattern: '|'.closed_pattern+
```

When two or more patterns are separated by vertical bars (`|`), this is called an OR pattern. (A single closed pattern is just that.)

当两个或更多的模式被竖条（`|`）分开时，这被称为OR模式。(单一的封闭模式就是这样)。

Only the final subpattern may be irrefutable.

只有最后的子模式可以是不可反驳的。

Each subpattern must bind the same set of names.

每个子模式必须绑定相同的名称集。

> 这里的意思我也不是很明白，是指命名空间？译者注。

An OR pattern matches each of its subpatterns in turn to the subject, until one succeeds. The OR pattern is then deemed to succeed. If none of the subpatterns succeed the OR pattern fails.

一个OR模式依次与它的每个子模式匹配，直到有一个成功。然后，该OR模式被认为是成功的。如果没有一个子模式成功，则OR模式失败。

> 需要注意的是，这里的OR模式和bool表达式中的`or`是两码事，这里是用于连接多个子模式用的，而非用于连接多个bool表达式，当然作用是类似的，并非使用`or`而使用`|`或许是以示区分，译者注。

### [Literal Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id15)

字面模式

Syntax:

语法：

```python
literal_pattern:
    | signed_number
    | signed_number '+' NUMBER
    | signed_number '-' NUMBER
    | strings
    | 'None'
    | 'True'
    | 'False'
signed_number: NUMBER | '-' NUMBER
```

The rule `strings` and the token `NUMBER` are defined in the standard Python grammar.

规则`strings`和标记`NUMBER`是在标准Python语法中定义的。

Triple-quoted strings are supported. Raw strings and byte strings are supported. F-strings are not supported.

支持三引号的字符串。支持原始字符串和字节型字符串。不支持F-字符串。

The forms `signed_number '+' NUMBER` and `signed_number '-' NUMBER` are only permitted to express complex numbers; they require a real number on the left and an imaginary number on the right.

`signed_number '+' NUMBER`和`signed_number '-' NUMBER`的形式只允许用来表达复数；它们要求左边是实数，右边是虚数。

A literal pattern succeeds if the subject value compares equal to the value expressed by the literal, using the following comparisons rules:

如果主题值与字面表达的值比较相等，则字面模式成功，使用以下比较规则。

- Numbers and strings are compared using the `==` operator.

  数字和字符串使用==运算符进行比较。

- The singleton literals `None`, `True` and `False` are compared using the `is` operator.

  单个字词`None`, `True`和`False`使用`is`运算符进行比较。

> 主流语言支持的`switch...case`语句就相当于这里的字面模式匹配，译者注。

### [Capture Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id16)

捕获模式

Syntax:

语法：

```python
capture_pattern: !"_" NAME
```

The single underscore (`_`) is not a capture pattern (this is what `!"_"` expresses). It is treated as a [wildcard pattern](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id3).

单一的下划线（_）不是一个捕获模式（这就是`!"_"`所表达的意思）。它被看作是一个[通配符模式](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id3)。

A capture pattern always succeeds. It binds the subject value to the name using the scoping rules for name binding established for the walrus operator in [PEP 572](https://www.python.org/dev/peps/pep-0572). (Summary: the name becomes a local variable in the closest containing function scope unless there's an applicable `nonlocal` or `global` statement.)

一个捕获模式总是成功的。它使用[PEP 572](https://www.python.org/dev/peps/pep-0572)中为walrus操作符制定的名称绑定范围规则，将主题值与名称绑定。(摘要：除非有适用的非局部或全局语句，否则名字会成为最接近的包含函数范围的局部变量。)

In a given pattern, a given name may be bound only once. This disallows for example `case x, x: ...` but allows `case [x] | x: ...`.

在一个给定的模式中，一个给定的名字只能被绑定一次。这就不允许例如case x, x: ...，但允许case [x] | x: ....

### [Wildcard Pattern](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id17)

通配符模式

Syntax:

语法：

```python
wildcard_pattern: "_"
```

A wildcard pattern always succeeds. It binds no name.

通配符模式总是会匹配成功，它不会绑定任何名称。

### [Value Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id18)

值模式

Syntax:

语法：

```
value_pattern: attr
attr: name_or_attr '.' NAME
name_or_attr: attr | NAME
```

The dotted name in the pattern is looked up using the standard Python name resolution rules. However, when the same value pattern occurs multiple times in the same match statement, the interpreter may cache the first value found and reuse it, rather than repeat the same lookup. (To clarify, this cache is strictly tied to a given execution of a given match statement.)

模式中的带点名称是使用标准的Python名称解析规则来查找的。然而，当相同的值模式在同一个匹配语句中出现多次时，解释器可以缓存找到的第一个值并重新使用它，而不是重复相同的查找。(为了澄清，这种缓存严格地与给定的匹配语句的执行相联系)。

The pattern succeeds if the value found thus compares equal to the subject value (using the `==` operator).

如果找到的值与主题值比较相等（使用`==`运算符），则该模式成功。

### [Group Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id19)

群组模式

Syntax:

语法：

```python
group_pattern: '(' pattern ')'
```

(For the syntax of `pattern`, see Patterns above. Note that it contains no comma -- a parenthesized series of items with at least one comma is a sequence pattern, as is `()`.)

(关于`pattern`的语法，见上面列出的模式。请注意，它不包含逗号--一个至少有一个逗号的括号内的项目系列是一个序列模式，就像()一样。)

A parenthesized pattern has no additional syntax. It allows users to add parentheses around patterns to emphasize the intended grouping.

括号内的模式没有额外的语法。它允许用户在模式周围添加小括号以强调预期的分组。

> 在我的理解中，可以简单将分组模式看做是对复杂的组合模式使用`()`进行显示地优先级划分，译者注。

### [Sequence Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id20)

序列模式

Syntax:

语法：

```python
sequence_pattern:
  | '[' [maybe_sequence_pattern] ']'
  | '(' [open_sequence_pattern] ')'
open_sequence_pattern: maybe_star_pattern ',' [maybe_sequence_pattern]
maybe_sequence_pattern: ','.maybe_star_pattern+ ','?
maybe_star_pattern: star_pattern | pattern
star_pattern: '*' (capture_pattern | wildcard_pattern)
```

(Note that a single parenthesized pattern without a trailing comma is a group pattern, not a sequence pattern. However a single pattern enclosed in `[...]` is still a sequence pattern.)

(请注意，没有尾部逗号的单一括号模式是一个组模式，而不是一个序列模式。但是用`[...]`括起来的单个模式仍然是一个序列模式）。)

> 意思是`(pattern)`是一个组模式，而`[pattern]`是一个序列模式，其本质上是延续了元组和列表的字面量声明风格，比如`(11)`就不是一个元组，而`(11,)`则是一个元组，对于列表来说，无论是`[11]`还是`[11,]`，都表示一个列表，译者注。

There is no semantic difference between a sequence pattern using `[...]`, a sequence pattern using `(...)`, and an open sequence pattern.

使用`[...]`的序列模式、使用`(...)`的序列模式和一个开放的序列模式之间没有语义上的区别。

A sequence pattern may contain at most one star subpattern. The star subpattern may occur in any position. If no star subpattern is present, the sequence pattern is a fixed-length sequence pattern; otherwise it is a variable-length sequence pattern.

一个序列模式最多可以包含一个星号子模式。星号子模式可以出现在任何位置。如果没有星号子模式，该序列模式是一个固定长度的序列模式；否则它是一个可变长度的序列模式。

> 这里的“星号子模式”其实就是在Python的解包时候常用到的类似于`(x,y,*)=(1,2,3,4,5,6)`这样的语句，其中`*`起到一个“可变（长度）子序列的”效果，对应这里的“可变（长度）子模式”，译者注。

For a sequence pattern to succeed the subject must be a sequence, where being a sequence is defined as its class being one of the following:

对于一个序列模式的（匹配）成功，主体必须是一个序列，其中作为一个序列被定义为其class是以下之一。

- a class that inherits from `collections.abc.Sequence`

  一个继承自`collection.abc.Sequence`的类

- a Python class that has been registered as a `collections.abc.Sequence`

  一个已经注册为 `collections.abc.Sequence` 的 Python 类

- a builtin class that has its `Py_TPFLAGS_SEQUENCE` bit set

  一个设置了 `Py_TPFLAGS_SEQUENCE` 位的内置类。

- a class that inherits from any of the above (including classes defined *before* a parent's `Sequence` registration)

  继承自上述任何一个的类（包括在父类的`Sequence`注册之前定义的类）。

The following standard library classes will have their `Py_TPFLAGS_SEQUENCE` bit set:

以下标准库类将设置其`Py_TPFLAGS_SEQUENCE`位：

- `array.array`
- `collections.deque`
- `list`
- `memoryview`
- `range`
- `tuple`

> 事实上一些常用的标准组件，比如`tupe`、`list`等，都是使用C语言而非Python实现的，以提高程序的运行效率，所以是无法通过Python语言本身的继承关系来明确这些组件的类型的，所以才会用到`Py_TPFLAGS_SEQUENCE`bit位之类的特殊手段来标记一些内部组件的类型，译者注。

Note

注意事项

Although `str`, `bytes`, and `bytearray` are usually considered sequences, they are not included in the above list and do not match sequence patterns.

尽管`str`、`bytes`和`bytearray`通常被认为是序列，但它们不包括在上述列表中，也不匹配序列模式。

> 这或许是为了方便区分字面匹配和序列匹配，译者注。

A fixed-length sequence pattern fails if the length of the subject sequence is not equal to the number of subpatterns.

如果主体序列的长度不等于子模式的数量，则固定长度的序列模式失败。

> 这显然是利用了序列比较的一个通用的快速检测思路，译者注。

A variable-length sequence pattern fails if the length of the subject sequence is less than the number of non-star subpatterns.

如果主体序列的长度小于非星号子模式的数量，则可变长度的序列模式失败。

> 这里的意思是如果序列模式使用了星型子模式（说明是可变长度），则其中的不可变部分（非星型子模式）的数量不能超过主题的序列长度，否则肯定是无法匹配的，译者注。

The length of the subject sequence is obtained using the builtin `len()` function (i.e., via the `__len__` protocol). However, the interpreter may cache this value in a similar manner as described for value patterns.

主体序列的长度是使用内置的`len()`函数获得的（即通过`__len__`协议）。然而，解释器可以以类似于值模式的说明中描述的方式来缓存这个值。

A fixed-length sequence pattern matches the subpatterns to corresponding items of the subject sequence, from left to right. Matching stops (with a failure) as soon as a subpattern fails. If all subpatterns succeed in matching their corresponding item, the sequence pattern succeeds.

一个固定长度的序列模式将子模式与主题序列的相应项目相匹配，从左到右。一旦一个子模式失败，匹配就会停止（以失败告终）。如果所有的子图案都成功地匹配了它们的对应项，那么这个序列模式就成功了。

> 显而易见，这种匹配算法类似于序列之间的匹配，译者注。

A variable-length sequence pattern first matches the leading non-star subpatterns to the corresponding items of the subject sequence, as for a fixed-length sequence. If this succeeds, the star subpattern matches a list formed of the remaining subject items, with items removed from the end corresponding to the non-star subpatterns following the star subpattern. The remaining non-star subpatterns are then matched to the corresponding subject items, as for a fixed-length sequence.

一个可变长度的序列模式首先将领头的非星号子模式与主题序列的相应项目相匹配，就像固定长度的序列一样。如果这样做成功了，星号子模式就会与剩余的主题项目组成的列表相匹配，从最后删除的项目与星号子模式后面的非星号子序列相对应。然后，剩余的非星号子模式与相应的主题项目相匹配，如同固定长度的序列。

### [Mapping Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id21)

映射模式

Syntax:

语法：

```python
mapping_pattern: '{' [items_pattern] '}'
items_pattern: ','.key_value_pattern+ ','?
key_value_pattern:
    | (literal_pattern | value_pattern) ':' pattern
    | double_star_pattern
double_star_pattern: '**' capture_pattern
```

(Note that `**_` is disallowed by this syntax.)

(注意，这个语法不允许`**_`)。

A mapping pattern may contain at most one double star pattern, and it must be last.

一个映射模式最多可以包含一个双星模式，而且必须是最后一个。

A mapping pattern may not contain duplicate key values. (If all key patterns are literal patterns this is considered a syntax error; otherwise this is a runtime error and will raise `ValueError`.)

一个映射模式不能包含重复的键值。(如果所有的键值模式都是字面模式，这被认为是一个语法错误；否则这是一个运行时错误，将引发`ValueError`)。

For a mapping pattern to succeed the subject must be a mapping, where being a mapping is defined as its class being one of the following:

为了使映射模式成功，主题必须是一个映射，作为一个映射被定义为它的类是以下之一。

- a class that inherits from `collections.abc.Mapping`

  一个继承自`collections.abc.Mapping`的类

- a Python class that has been registered as a `collections.abc.Mapping`

  一个已经注册为 `collections.abc.Mapping`的Python类

- a builtin class that has its `Py_TPFLAGS_MAPPING` bit set

  一个设置了`Py_TPFLAGS_MAPPING`位的内置类

- a class that inherits from any of the above (including classes defined *before* a parent's `Mapping` registration)

  继承自上述任何一类的类（包括在父类的`Mapping`注册之前定义的类）。

The standard library classes `dict` and `mappingproxy` will have their `Py_TPFLAGS_MAPPING` bit set.

标准库类`dict`和`mappingproxy`将设置其`Py_TPFLAGS_MAPPING`位。

A mapping pattern succeeds if every key given in the mapping pattern is present in the subject mapping, and the pattern for each key matches the corresponding item of the subject mapping. Keys are always compared with the `==` operator. If a `'**' NAME` form is present, that name is bound to a `dict` containing remaining key-value pairs from the subject mapping.

如果映射模式中给出的每个键都出现在主题映射中，并且每个键的模式都与主题映射中的相应项目相匹配，那么映射模式就成功了。键总是用`==`操作符进行比较。如果存在 `'**'NAME`形式，该名称将被绑定到一个 `dict`上，该 `dict`包含主题映射中剩余的键值对。

If duplicate keys are detected in the mapping pattern, the pattern is considered invalid, and a `ValueError` is raised.

如果在映射模式中检测到重复的键，该模式被认为是无效的，并且会产生一个`ValueError`。

Key-value pairs are matched using the two-argument form of the subject's `get()` method. As a consequence, matched key-value pairs must already be present in the mapping, and not created on-the-fly by `__missing__` or `__getitem__`. For example, `collections.defaultdict` instances will only be matched by patterns with keys that were already present when the match statement was entered.

键值对使用主题的`get()`方法的双参数形式进行匹配。因此，匹配的键值对必须已经存在于映射中，而不是由 `__missing__` 或 `__getitem__` 即时创建。例如，`collections.defaultdict`实例将只与输入匹配语句时已经存在的键值模式匹配。

### [Class Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id22)

类模式

Syntax:

语法：

```python
class_pattern:
    | name_or_attr '(' [pattern_arguments ','?] ')'
pattern_arguments:
    | positional_patterns [',' keyword_patterns]
    | keyword_patterns
positional_patterns: ','.pattern+
keyword_patterns: ','.keyword_pattern+
keyword_pattern: NAME '=' pattern
```

A class pattern may not repeat the same keyword multiple times.

一个类模式不能多次重复相同的关键字。

If `name_or_attr` is not an instance of the builtin `type`, `TypeError` is raised.

如果`name_or_attr`不是内建的`type`的实例，将引发`TypeError`。

A class pattern fails if the subject is not an instance of `name_or_attr`. This is tested using `isinstance()`.

如果主题不是`name_or_attr`的实例，则类模式失败。这将使用`isinstance()`来测试。

If no arguments are present, the pattern succeeds if the `isinstance()` check succeeds. Otherwise:

如果没有参数存在，如果`isinstance()`检查成功，则模式成功。否则：

- If only keyword patterns are present, they are processed as follows, one by one:

  如果只有关键字模式存在，它们会被逐一处理，如下所示。

  - The keyword is looked up as an attribute on the subject.

    关键字被作为主题上的一个属性进行查找。

    - If this raises an exception other than `AttributeError`, the exception bubbles up.

      如果这引发了除 `AttributeError `以外的异常，该异常就会冒出来。

    - If this raises `AttributeError` the class pattern fails.

      如果这引起了`AttributeError`，类模式就会失败。

    - Otherwise, the subpattern associated with the keyword is matched against the attribute value. If this fails, the class pattern fails. If it succeeds, the match proceeds to the next keyword.

      否则，与关键词相关的子模式将与属性值匹配。如果失败，则类模式失败。如果它成功了，匹配继续进行到下一个关键字。

  - If all keyword patterns succeed, the class pattern as a whole succeeds.

    如果所有的关键词模式都成功，那么整个类模式就成功了。

- If any positional patterns are present, they are converted to keyword patterns (see below) and treated as additional keyword patterns, preceding the syntactic keyword patterns (if any).

  如果有任何位置模式存在，它们将被转换为关键词模式（见下文），并被视为额外的关键词模式，在句法关键词模式（如果有的话）之前。

Positional patterns are converted to keyword patterns using the `__match_args__` attribute on the class designated by `name_or_attr`, as follows:

使用`name_or_attr`指定的类上的`__match_args__`属性将位置模式转换为关键词模式，如下所示：

- For a number of built-in types (specified below), a single positional subpattern is accepted which will match the entire subject. (Keyword patterns work as for other types here.)

  对于一些内置类型（在下面指定），接受一个单一的位置子模式，它将匹配整个主题。(关键词模式在此与其他类型一样。)

- The equivalent of `getattr(cls, "__match_args__", ()))` is called.

  相当于`getattr(cls, "__match_args__", ())`被调用。

- If this raises an exception the exception bubbles up.

  如果这引发了一个异常，异常就会冒出来。

- If the returned value is not a tuple, the conversion fails and `TypeError` is raised.

  如果返回的值不是一个元组，转换就会失败，并引发 `TypeError`。

- If there are more positional patterns than the length of `__match_args__` (as obtained using `len()`), `TypeError` is raised.

  如果有更多的位置模式比`__match_args__`的长度（用`len()`获得）多，则引发`TypeError`。

- Otherwise, positional pattern `i` is converted to a keyword pattern using `__match_args__[i]` as the keyword, provided it the latter is a string; if it is not, `TypeError` is raised.

  否则，位置模式`i`被转换为关键词模式，使用`__match_args__[i]`作为关键词，只要后者是一个字符串；如果不是，则引发`TypeError`。

- For duplicate keywords, `TypeError` is raised.

  对于重复的关键词，会出现 "类型错误"。

Once the positional patterns have been converted to keyword patterns, the match proceeds as if there were only keyword patterns.

一旦位置模式被转换为关键词模式，匹配就像只有关键词模式一样进行。

As mentioned above, for the following built-in types the handling of positional subpatterns is different: `bool`, `bytearray`, `bytes`, `dict`, `float`, `frozenset`, `int`, `list`, `set`, `str`, and `tuple`.

如上所述，对于以下内置类型，位置子模式的处理是不同的：`bool`, `bytearray`, `bytes`, `dict`, `float`, `frozenset`, `int`, `list`, `set`, `str`, 和`tuple`。

This behavior is roughly equivalent to the following:

这种行为大致等同于以下情况：

```python
class C:
    __match_args__ = ("__match_self_prop__",)
    @property
    def __match_self_prop__(self):
        return self
```

# [Side Effects and Undefined Behavior](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id23)

副作用和未定义行为

The only side-effect produced explicitly by the matching process is the binding of names. However, the process relies on attribute access, instance checks, `len()`, equality and item access on the subject and some of its components. It also evaluates value patterns and the class name of class patterns. While none of those typically create any side-effects, in theory they could. This proposal intentionally leaves out any specification of what methods are called or how many times. This behavior is therefore undefined and user code should not rely on it.

匹配过程明确产生的唯一副作用是名称的绑定。然而，这个过程依赖于属性访问、实例检查、`len()`、（模式）评估以及对主题和它的一些组件的项目访问。它还评估了值模式和类模式的类名。虽然这些通常都不会产生任何副作用，但在理论上它们可以。本提案有意不对什么方法被调用或调用多少次进行任何说明。因此这种行为是未定义的，用户代码不应该依赖它。

Another undefined behavior is the binding of variables by capture patterns that are followed (in the same case block) by another pattern that fails. These may happen earlier or later depending on the implementation strategy, the only constraint being that capture variables must be set before guards that use them explicitly are evaluated. If a guard consists of an `and` clause, evaluation of the operands may even be interspersed with pattern matching, as long as left-to-right evaluation order is maintained.

另一个未定义的行为是通过捕获模式来绑定变量，而在捕获模式之后（在同一个case块中），另一个模式也会失败。这些可能会提前或延后发生，这取决于实现策略，唯一的约束是捕获变量必须在明确使用它们的守卫被评估之前被设置。如果一个守卫由and子句组成，只要保持从左到右的评估顺序，操作数的评估甚至可以与模式匹配穿插进行。

# [The Standard Library](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id24)

标准库

To facilitate the use of pattern matching, several changes will be made to the standard library:

为了促进模式匹配的使用，我们将对标准库做一些改变：

- Namedtuples and dataclasses will have auto-generated `__match_args__`.

  Namedtuples 和 dataclasses 将有自动生成的 `__match_args__`。

- For dataclasses the order of attributes in the generated `__match_args__` will be the same as the order of corresponding arguments in the generated `__init__()` method. This includes the situations where attributes are inherited from a superclass. Fields with `init=False` are excluded from `__match_args__`.

  对于dataclasses，在生成的 `__match_args__` 中属性的顺序将与生成的 `__init__()` 方法中相应参数的顺序相同。这包括属性从超类中继承的情况。`init=False`的字段被排除在`__match_args__`之外。

In addition, a systematic effort will be put into going through existing standard library classes and adding `__match_args__` where it looks beneficial.

此外，我们将系统性地检查现有的标准库类，并在它看起来有益的地方添加`__match_args__`。

# [Appendix A -- Full Grammar](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id25)

附录A -- 完整语法

Here is the full grammar for `match_stmt`. This is an additional alternative for `compound_stmt`. Remember that `match` and `case` are soft keywords, i.e. they are not reserved words in other grammatical contexts (including at the start of a line if there is no colon where expected). By convention, hard keywords use single quotes while soft keywords use double quotes.

这里是`match_stmt`的完整语法。这是 `compound_stmt`的一个额外选择。请记住，`match`和`case`是软关键字，即它们在其他语法环境中不是保留字（包括在行首，如果没有预期的冒号）。按照惯例，硬关键词使用单引号，而软关键词使用双引号。

Other notation used beyond standard EBNF:

在标准EBNF之外使用的其他符号：

- `SEP.RULE+` is shorthand for `RULE (SEP RULE)*`

  `SEP.RULE+`是`RULE (SEP RULE)*`的简写。

- `!RULE` is a negative lookahead assertion

  `！RULE`是一个负数的前瞻断言

```
match_stmt: "match" subject_expr ':' NEWLINE INDENT case_block+ DEDENT
subject_expr:
    | star_named_expression ',' [star_named_expressions]
    | named_expression
case_block: "case" patterns [guard] ':' block
guard: 'if' named_expression

patterns: open_sequence_pattern | pattern
pattern: as_pattern | or_pattern
as_pattern: or_pattern 'as' capture_pattern
or_pattern: '|'.closed_pattern+
closed_pattern:
    | literal_pattern
    | capture_pattern
    | wildcard_pattern
    | value_pattern
    | group_pattern
    | sequence_pattern
    | mapping_pattern
    | class_pattern

literal_pattern:
    | signed_number !('+' | '-')
    | signed_number '+' NUMBER
    | signed_number '-' NUMBER
    | strings
    | 'None'
    | 'True'
    | 'False'
signed_number: NUMBER | '-' NUMBER

capture_pattern: !"_" NAME !('.' | '(' | '=')

wildcard_pattern: "_"

value_pattern: attr !('.' | '(' | '=')
attr: name_or_attr '.' NAME
name_or_attr: attr | NAME

group_pattern: '(' pattern ')'

sequence_pattern:
  | '[' [maybe_sequence_pattern] ']'
  | '(' [open_sequence_pattern] ')'
open_sequence_pattern: maybe_star_pattern ',' [maybe_sequence_pattern]
maybe_sequence_pattern: ','.maybe_star_pattern+ ','?
maybe_star_pattern: star_pattern | pattern
star_pattern: '*' (capture_pattern | wildcard_pattern)

mapping_pattern: '{' [items_pattern] '}'
items_pattern: ','.key_value_pattern+ ','?
key_value_pattern:
    | (literal_pattern | value_pattern) ':' pattern
    | double_star_pattern
double_star_pattern: '**' capture_pattern

class_pattern:
    | name_or_attr '(' [pattern_arguments ','?] ')'
pattern_arguments:
    | positional_patterns [',' keyword_patterns]
    | keyword_patterns
positional_patterns: ','.pattern+
keyword_patterns: ','.keyword_pattern+
keyword_pattern: NAME '=' pattern
```

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md#id26)

版权声明

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

本文件被置于公共领域或CC0-1.0-Universal许可之下，以更许可的方式为准。

Source: https://github.com/python/peps/blob/master/pep-0634.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。