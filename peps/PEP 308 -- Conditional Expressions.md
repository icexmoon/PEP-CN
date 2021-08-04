# PEP 308 -- Conditional Expressions

PEP 308 -- 条件表达式

|               |                                     |
| :------------ | ----------------------------------- |
| PEP:          | 308                                 |
| Title:        | Conditional Expressions             |
| Author:       | Guido van Rossum, Raymond Hettinger |
| Status:       | Final                               |
| Type:         | Standards Track                     |
| Created:      | 07-Feb-2003                         |
| Post-History: | 7-Feb-2003, 11-Feb-2003             |

------

Contents

- [Adding a conditional expression](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#adding-a-conditional-expression)，添加一个条件表达式
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#references)，参考文献
- [Introduction to earlier draft of the PEP (kept for historical purposes)](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#introduction-to-earlier-draft-of-the-pep-kept-for-historical-purposes)，介绍早期的PEP（为理事目的的保留）
- [Proposal](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#proposal)，建议
- [Alternatives](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#alternatives)，替代方案
- [Summary of the Current State of the Discussion](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#summary-of-the-current-state-of-the-discussion)，对目前讨论状态的总结
- [Short-Circuit Behavior](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#short-circuit-behavior)，短路行为
- Detailed Results of Voting，投票结果详情
  - [CHOICE KEY](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#choice-key)
  - [Detail for write-in votes and their ranking](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#detail-for-write-in-votes-and-their-ranking)，写入票的细节及其排名
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#copyright)，版权声明

# [Adding a conditional expression](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id7)

添加一个条件表达式

On 9/29/2005, Guido decided to add conditional expressions in the form of "X if C else Y". [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id4)

2005年9月29日，Guido决定以 "X if C else Y "的形式添加条件表达式。[[1]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id4)

The motivating use case was the prevalence of error-prone attempts to achieve the same effect using "and" and "or". [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id5)

促成这个使用案例的原因是，使用 "and "和 "or "来实现相同效果的错误尝试非常普遍。[[2]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id5)

Previous community efforts to add a conditional expression were stymied by a lack of consensus on the best syntax. That issue was resolved by simply deferring to a BDFL best judgment call.

以前社区为增加条件表达式所做的努力，由于在最佳语法上缺乏共识而受阻。这个问题通过简单地遵从BDFL的最佳判断来解决。

The decision was validated by reviewing how the syntax fared when applied throughout the standard library (this review approximates a sampling of real-world use cases, across a variety of applications, written by a number of programmers with diverse backgrounds). [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id6)

该决定通过审查该语法在整个标准库中的应用情况得到了验证（该审查接近于真实世界的使用案例的抽样，涉及各种应用，由不同背景的程序员编写）。[[3]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id6)

The following change will be made to the grammar. (The or_test symbols is new, the others are modified.)

将对语法做如下修改。(or_test符号是新的，其他的是修改的)。

```python
test: or_test ['if' or_test 'else' test] | lambdef
or_test: and_test ('or' and_test)*
...
testlist_safe: or_test [(',' or_test)+ [',']]
...
gen_for: 'for' exprlist 'in' or_test [gen_iter]
```

The new syntax nearly introduced a minor syntactical backwards incompatibility. In previous Python versions, the following is legal:

新的语法几乎引入了一个小的语法上的向后不兼容。在以前的Python版本中，以下情况是合法的：

```python
[f for f in lambda x: x, lambda x: x**2 if f(1) == 1]
```

(I.e. a list comprehension where the sequence following 'in' is an unparenthesized series of lambdas -- or just one lambda, even.)

(即一个列表推导式，其中'in'后面的序列是一个未排序的lambdas系列--或者甚至只是一个lambda。)

In Python 3.0, the series of lambdas will have to be parenthesized, e.g.:

在Python 3.0中，一系列的lambdas将必须用括号表示，例如：

```python
[f for f in (lambda x: x, lambda x: x**2) if f(1) == 1]
```

> 这里的f，也就是生成的列表元素是匿名函数，译者注。

This is because lambda binds less tight than the if-else expression, but in this context, the lambda could already be followed by an 'if' keyword that binds less tightly still (for details, consider the grammar changes shown above).

这是因为 lambda 比 if-else 表达式结合得不那么紧密，但是在这种情况下，lambda 后面可能已经有了一个'if'关键字，它的结合仍然不那么紧密 (关于细节，请考虑上面的语法变化)。

However, in Python 2.5, a slightly different grammar is used that is more backwards compatible, but constrains the grammar of a lambda used in this position by forbidding the lambda's body to contain an unparenthesized condition expression. Examples:

然而，在 Python 2.5 中，使用了一个稍微不同的语法，它更加向后兼容，但是通过禁止 lambda 的主体包含一个非父级的条件表达式，限制了在这个位置使用 lambda 的语法。例子。

```python
[f for f in (1, lambda x: x if x >= 0 else -1)]    # OK
[f for f in 1, (lambda x: x if x >= 0 else -1)]    # OK
[f for f in 1, lambda x: (x if x >= 0 else -1)]    # OK
[f for f in 1, lambda x: x if x >= 0 else -1]      # INVALID
```

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id8)

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id1) | Pronouncement https://mail.python.org/pipermail/python-dev/2005-September/056846.html |
|                                                              |                                                              |
| [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id2) | Motivating use case: https://mail.python.org/pipermail/python-dev/2005-September/056546.html https://mail.python.org/pipermail/python-dev/2005-September/056510.html |
|                                                              |                                                              |
| [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id3) | Review in the context of real-world code fragments: https://mail.python.org/pipermail/python-dev/2005-September/056803.html |
|                                                              |                                                              |

# [Introduction to earlier draft of the PEP (kept for historical purposes)](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id9)

对PEP早期草案的介绍（为历史目的保留）

Requests for an if-then-else ("ternary") expression keep coming up on comp.lang.python. This PEP contains a concrete proposal of a fairly Pythonic syntax. This is the community's one chance: if this PEP is approved with a clear majority, it will be implemented in Python 2.4. If not, the PEP will be augmented with a summary of the reasons for rejection and the subject better not come up again. While the BDFL is co-author of this PEP, he is neither in favor nor against this proposal; it is up to the community to decide. If the community can't decide, the BDFL will reject the PEP.

comp.lang.python上不断出现对if-then-else（"三元组"）表达式的要求。本PEP包含一个相当Pythonic语法的具体建议。这是社区的一次机会：如果这个 PEP 以明显的多数通过，它将在 Python 2.4 中实现。如果没有，PEP将被添加拒绝原因的摘要，而且这个主题最好不要再出现。虽然BDFL是这个PEP的共同作者，但他既不赞成也不反对这个提议；这要由社区来决定。如果社区不能决定，BDFL将拒绝该PEP。

After unprecedented community response (very good arguments were made both pro and con) this PEP has been revised with the help of Raymond Hettinger. Without going through a complete revision history, the main changes are a different proposed syntax, an overview of proposed alternatives, the state of the current discussion, and a discussion of short-circuit behavior.

经过空前的社区反应（正反两方面都提出了非常好的论据），在Raymond Hettinger的帮助下，这个PEP已经被修订。在不回顾完整的修订历史的情况下，主要的变化是提出了不同的语法，对提议的替代方案进行了概述，目前的讨论状况，以及对短路行为进行了讨论。

Following the discussion, a vote was held. While there was an overall interest in having some form of if-then-else expressions, no one format was able to draw majority support. Accordingly, the PEP was rejected due to the lack of an overwhelming majority for change. Also, a Python design principle has been to prefer the status quo whenever there are doubts about which path to take.

讨论结束后，进行了投票。虽然大家对拥有某种形式的if-then-else表达式有总体兴趣，但没有一种格式能够得到大多数人的支持。因此，由于缺乏压倒性的多数支持，PEP被否决。另外，Python的一个设计原则是，当对采取哪种方式有疑问时，倾向于维持现状。

# [Proposal](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id10)

建议

The proposed syntax is as follows:

建议的语法如下：

```python
(if <condition>: <expression1> else: <expression2>)
```

Note that the enclosing parentheses are not optional.

请注意，括号内的内容不是可有可无的。

The resulting expression is evaluated like this:

由此产生的表达式是这样评估的：

- First, <condition> is evaluated.

  首先，<condition>被评估。

- If <condition> is true, <expression1> is evaluated and is the result of the whole thing.

  如果<condition>为真，<expression1>被评估，是整个表达式的结果。

- If <condition> is false, <expression2> is evaluated and is the result of the whole thing.

  如果<condition>为假，<expression2>被评估，是整个表达式的结果。

A natural extension of this syntax is to allow one or more 'elif' parts:

这种语法的一个自然扩展是允许一个或多个 "elif "部分：

```python
(if <cond1>: <expr1> elif <cond2>: <expr2> ... else: <exprN>)
```

This will be implemented if the proposal is accepted.

如果该提案被接受，这将被实施。

The downsides to the proposal are:

该提案的缺点是：

- the required parentheses

  需要使用括号

- confusability with statement syntax

  与语句语法的混淆

- additional semantic loading of colons

  冒号的额外语义加载

Note that at most one of <expression1> and <expression2> is evaluated. This is called a "short-circuit expression"; it is similar to the way the second operand of 'and' / 'or' is only evaluated if the first operand is true / false.

请注意，<expression1>和<expression2>中最多只有一个被评估。这被称为 "短路表达式"；它类似于'and'/'or'的第二个操作数只有在第一个操作数为真/假时才会被评估的方式。

A common way to emulate an if-then-else expression is:

模仿if-then-else表达式的一个常见方法是：

```python
<condition> and <expression1> or <expression2>
```

However, this doesn't work the same way: it returns <expression2> when <expression1> is false! See FAQ 4.16 for alternatives that work -- however, they are pretty ugly and require much more effort to understand.

然而，这不是同样的工作方式：当<expression1>为false时，它返回<expression2>! 请参阅FAQ4.16，了解能起作用的替代方法 -- 然而，它们非常难看，需要付出更多努力才能理解。

# [Alternatives](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id11)

替代方案

Holger Krekel proposed a new, minimally invasive variant:

霍尔格-克雷克尔提出了一种新的、微创的变体：

```python
<condition> and <expression1> else <expression2>
```

The concept behind it is that a nearly complete ternary operator already exists with and/or and this proposal is the least invasive change that makes it complete. Many respondants on the newsgroup found this to be the most pleasing alternative. However, a couple of respondants were able to post examples that were mentally difficult to parse. Later it was pointed out that this construct works by having the "else" change the existing meaning of "and".

它背后的概念是，一个几乎完整的三元运算符已经存在，并且/或者，这个建议是使其完整的最小的入侵性变化。新闻组中的许多回应者认为这是最令人满意的选择。然而，有几个响应者能够发布一些在精神上难以解析的例子。后来有人指出，这种结构是通过让 "else "改变 "and "的现有含义而发挥作用的。

As a result, there is increasing support for Christian Tismer's proposed variant of the same idea:

因此，越来越多的人支持Christian Tismer提出的同一想法的变体：

```python
<condition> then <expression1> else <expression2>
```

The advantages are simple visual parsing, no required parentheses, no change in the semantics of existing keywords, not as likely as the proposal to be confused with statement syntax, and does not further overload the colon. The disadvantage is the implementation costs of introducing a new keyword. However, unlike other new keywords, the word "then" seems unlikely to have been used as a name in existing programs.

其优点是视觉解析简单，不需要括号，不改变现有关键词的语义，不像提案中那样容易与语句语法相混淆，也不会进一步加重冒号的负担。缺点是引入一个新的关键字的实施成本。然而，与其他新的关键字不同，"then "这个词似乎不太可能在现有的程序中作为名称使用。

\---

Many C-derived languages use this syntax:

许多C语言的衍生语言都使用这种语法：

```c
<condition> ? <expression1> : <expression2>
```

Eric Raymond even implemented this. The BDFL rejected this for several reasons: the colon already has many uses in Python (even though it would actually not be ambiguous, because the question mark requires a matching colon); for people not used to C-derived language, it is hard to understand.

Eric Raymond甚至实现了这一点。BDFL拒绝了这一点，原因有几个：冒号在Python中已经有很多用途（尽管它实际上不会有歧义，因为问号需要一个匹配的冒号）；对于不习惯C语言衍生的人来说，它很难理解。

\---

The original version of this PEP proposed the following syntax:

本PEP的原始版本提出了以下语法：

```python
<expression1> if <condition> else <expression2>
```

The out-of-order arrangement was found to be too uncomfortable for many of participants in the discussion; especially when <expression1> is long, it's easy to miss the conditional while skimming.

在讨论中，许多参与者发现不按顺序排列的做法太不舒服了；特别是当<expression1>很长时，在略读时很容易错过条件。

> 我也同意这种观点，目前这种`exp1 if condition else exp2`的顺序实在是太反人类了，译者注。

\---

Some have suggested adding a new builtin instead of extending the syntax of the language. For example:

有些人建议增加一个新的内置函数，而不是扩展语言的语法。比如说：

```python
cond(<condition>, <expression1>, <expression2>)
```

This won't work the way a syntax extension will because both expression1 and expression2 must be evaluated before the function is called. There's no way to short-circuit the expression evaluation. It could work if 'cond' (or some other name) were made a keyword, but that has all the disadvantages of adding a new keyword, plus confusing syntax: it **looks** like a function call so a casual reader might expect both <expression1> and <expression2> to be evaluated.

这不会像语法扩展那样工作，因为表达式1和表达式2都必须在函数被调用之前被评估。没有办法绕开表达式的评估。如果把'cond'（或其他名称）作为一个关键字，它就能起作用，但这有增加一个新关键字的所有缺点，还有混乱的语法：它看起来像一个函数调用，所以不经意的读者可能会认为<expression1>和<expression2>都要被评估。

# [Summary of the Current State of the Discussion](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id12)

讨论现状总结

Groups are falling into one of three camps:

各个团体正陷入三个阵营中的一个：

1. Adopt a ternary operator built using punctuation characters:

   采用使用标点符号构建的三元运算符：

   ```python
   <condition> ? <expression1> : <expression2>
   ```

2. Adopt a ternary operator built using new or existing keywords. The leading examples are:

   采用使用新的或现有的关键字建立的三元运算符。主要的例子有：

   ```python
   <condition> then <expression1> else <expression2>
   (if <condition>: <expression1> else: <expression2>)
   ```

3. Do nothing.

   什么都不做。

The first two positions are relatively similar.

前两种立场相对类似。

Some find that any form of punctuation makes the language more cryptic. Others find that punctuation style is appropriate for expressions rather than statements and helps avoid a COBOL style: 3 plus 4 times 5.

有些人发现任何形式的标点符号都会使语言更加隐蔽。其他人则认为标点符号的风格适合于表达而不是语句，有助于避免COBOL的风格。3加4乘以5。

Adapting existing keywords attempts to improve on punctuation through explicit meaning and a more tidy appearance. The downside is some loss of the economy-of-expression provided by punctuation operators. The other downside is that it creates some degree of confusion between the two meanings and two usages of the keywords.

改编现有的关键词，试图通过明确的含义和更整洁的外观来改进标点符号。缺点是失去了一些由标点符号运算符提供的表达经济性。另一个缺点是它在关键词的两种含义和两种用法之间造成了一定程度的混淆。

Those difficulties are overcome by options which introduce new keywords which take more effort to implement.

这些困难可以通过引入新的关键字来克服，这些关键字需要花费更多的精力来实现。

The last position is doing nothing. Arguments in favor include keeping the language simple and concise; maintaining backwards compatibility; and that any every use case can already be already expressed in terms of "if" and "else". Lambda expressions are an exception as they require the conditional to be factored out into a separate function definition.

最后一种立场是什么都不做。赞成的理由包括：保持语言的简单和简洁；保持向后的兼容性；任何用例都已经可以用 "if "和 "else "来表达。Lambda表达式是一个例外，因为它需要将条件分解成一个单独的函数定义。

The arguments against doing nothing are that the other choices allow greater economy of expression and that current practices show a propensity for erroneous uses of "and", "or", or one their more complex, less visually unappealing workarounds.

反对什么都不做的理由是，其他选择允许更经济的表达方式，而且目前的做法显示了错误使用 "和"、"或 "的倾向，或者它们更复杂、更不美观的变通方法。

# [Short-Circuit Behavior](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id13)

短路行为

The principal difference between the ternary operator and the `cond()` function is that the latter provides an expression form but does not provide short-circuit evaluation.

三元运算符和`cond()`函数的主要区别是后者提供了一个表达式，但不提供短路评估。

Short-circuit evaluation is desirable on three occasions:

短路评估在三种情况下是可取的：

1. When an expression has side-effects

   当一个表达式有副作用时

2. When one or both of the expressions are resource intensive

   当一个或两个表达式都是资源密集型时

3. When the condition serves as a guard for the validity of the expression.

   当条件作为表达式有效性的保障时

```python
#  Example where all three reasons apply
data = isinstance(source, file)  ?  source.readlines()
                                 :  source.split()
```

1. `readlines()` moves the file pointer

   `readlines()`移动文件指针

2. for long sources, both alternatives take time

   对于长数据源，这两种方法都需要时间

3. `split()` is only valid for strings and `readlines()` is only valid for file objects.

   `split()`只对字符串有效，readlines()只对文件对象有效。

Supporters of a `cond()` function point out that the need for short-circuit evaluation is rare. Scanning through existing code directories, they found that if/else did not occur often; and of those only a few contained expressions that could be helped by `cond()` or a ternary operator; and that most of those had no need for short-circuit evaluation. Hence, `cond()` would suffice for most needs and would spare efforts to alter the syntax of the language.

`cond()`函数的支持者指出，对短路评估的需求是很少的。通过扫描现有的代码目录，他们发现if/else并不经常出现；其中只有少数表达式可以用`cond()`或三元操作符来帮助；而且大多数表达式不需要短路评估。因此，`cond()` 就足以满足大多数需求，并且可以省去改变语言语法的努力。

More supporting evidence comes from scans of C code bases which show that its ternary operator used very rarely (as a percentage of lines of code).

更多的支持证据来自于对C语言代码库的扫描，扫描结果显示其三元运算符的使用非常少（占代码行的百分比）。

A counterpoint to that analysis is that the availability of a ternary operator helped the programmer in every case because it spared the need to search for side-effects. Further, it would preclude errors arising from distant modifications which introduce side-effects. The latter case has become more of a reality with the advent of properties where even attribute access can be given side-effects.

对这一分析的反驳是，三元运算符的可用性在任何情况下都有助于程序员，因为它免除了寻找副作用的需要。此外，它还可以排除因引入副作用的远距离修改而产生的错误。随着属性的出现，后一种情况变得更加现实，甚至属性的访问也可以被赋予副作用。

The BDFL's position is that short-circuit behavior is essential for an if-then-else construct to be added to the language.

BDFL的立场是，短路行为对于在语言中加入if-then-else结构是至关重要的。

# [Detailed Results of Voting](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id14)

投票结果详情

```
Votes rejecting all options:  82
Votes with rank ordering:     436
                              ---
Total votes received:         518


        ACCEPT                  REJECT                  TOTAL
        ---------------------   ---------------------   -----
        Rank1   Rank2   Rank3   Rank1   Rank2   Rank3
Letter
A       51      33      19      18      20      20      161
B       45      46      21      9       24      23      168
C       94      54      29      20      20      18      235
D       71      40      31      5       28      31      206
E       7       7       10              3       5       32
F       14      19      10              7       17      67
G       7       6       10      1       2       4       30
H       20      22      17      4       10      25      98
I       16      20      9       5       5       20      75
J       6       17      5       1               10      39
K       1               6               4       13      24
L               1       2               3       3       9
M       7       3       4       2       5       11      32
N               2       3               4       2       11
O       1       6       5       1       4       9       26
P       5       3       6       1       5       7       27
Q       18      7       15      6       5       11      62
Z                                               1       1
        ---     ---     ---     ---     ---     ---     ----
Total   363     286     202     73      149     230     1303
RejectAll                       82      82      82      246
        ---     ---     ---     ---     ---     ---     ----
Total   363     286     202     155     231     312     1549
```

## [CHOICE KEY](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id15)

```
A.  x if C else y
B.  if C then x else y
C.  (if C: x else: y)
D.  C ? x : y
E.  C ? x ! y
F.  cond(C, x, y)
G.  C ?? x || y
H.  C then x else y
I.  x when C else y
J.  C ? x else y
K.  C -> x else y
L.  C -> (x, y)
M.  [x if C else y]
N.  ifelse C: x else y
O.  <if C then x else y>
P.  C and x else y
Q.  any write-in vote
```

## [Detail for write-in votes and their ranking](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id16)

写入票的细节及其排名

```
3:  Q reject y x C elsethenif
2:  Q accept (C ? x ! y)
3:  Q reject ...
3:  Q accept  ? C : x : y
3:  Q accept (x if C, y otherwise)
3:  Q reject ...
3:  Q reject NONE
1:  Q accept   select : (<c1> : <val1>; [<cx> : <valx>; ]* elseval)
2:  Q reject if C: t else: f
3:  Q accept C selects x else y
2:  Q accept iff(C, x, y)    # "if-function"
1:  Q accept (y, x)[C]
1:  Q accept          C true: x false: y
3:  Q accept          C then: x else: y
3:  Q reject
3:  Q accept (if C: x elif C2: y else: z)
3:  Q accept C -> x : y
1:  Q accept  x (if C), y
1:  Q accept if c: x else: y
3:  Q accept (c).{True:1, False:2}
2:  Q accept if c: x else: y
3:  Q accept (c).{True:1, False:2}
3:  Q accept if C: x else y
1:  Q accept  (x if C else y)
1:  Q accept ifelse(C, x, y)
2:  Q reject x or y <- C
1:  Q accept (C ? x : y) required parens
1:  Q accept  iif(C, x, y)
1:  Q accept ?(C, x, y)
1:  Q accept switch-case
2:  Q accept multi-line if/else
1:  Q accept C: x else: y
2:  Q accept (C): x else: y
3:  Q accept if C: x else: y
1:  Q accept     x if C, else y
1:  Q reject choice: c1->a; c2->b; ...; z
3:  Q accept [if C then x else y]
3:  Q reject no other choice has x as the first element
1:  Q accept (x,y) ? C
3:  Q accept x if C else y (The "else y" being optional)
1:  Q accept (C ? x , y)
1:  Q accept  any outcome (i.e form or plain rejection) from a usability study
1:  Q reject (x if C else y)
1:  Q accept  (x if C else y)
2:  Q reject   NONE
3:  Q reject   NONE
3:  Q accept  (C ? x else y)
3:  Q accept  x when C else y
2:  Q accept  (x if C else y)
2:  Q accept cond(C1, x1, C2, x2, C3, x3,...)
1:  Q accept  (if C1: x elif C2: y else: z)
1:  Q reject cond(C, :x, :y)
3:  Q accept  (C and [x] or [y])[0]
2:  Q reject
3:  Q reject
3:  Q reject all else
1:  Q reject no-change
3:  Q reject deliberately omitted as I have no interest in any other proposal
2:  Q reject (C then x else Y)
1:  Q accept       if C: x else: y
1:  Q reject (if C then x else y)
3:  Q reject C?(x, y)
```

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20308%20--%20Conditional%20Expressions.md#id17)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0308.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。