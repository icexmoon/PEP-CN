# PEP 635 -- Structural Pattern Matching: Motivation and Rationale

PEP 635 -- 结构化模式匹配：动机和原理

> 原文地址：<https://www.python.org/dev/peps/pep-0635/>

| PEP:            | 635                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Structural Pattern Matching: Motivation and Rationale        |
| Author:         | Tobias Kohn <kohnt at tobiaskohn.ch>, Guido van Rossum <guido at python.org> |
| BDFL-Delegate:  |                                                              |
| Discussions-To: | Python-Dev <[python-dev at python.org](mailto:python-dev@python.org?subject=PEP 635)> |
| Status:         | Final                                                        |
| Type:           | Informational                                                |
| Created:        | 12-Sep-2020                                                  |
| Python-Version: | 3.10                                                         |
| Post-History:   | 22-Oct-2020, 08-Feb-2021                                     |
| Resolution:     | https://mail.python.org/archives/list/python-committers@python.org/message/SQC2FTLFV5A7DV7RCEAR2I2IKJKGK7W3 |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#abstract)，概述
- Motivation，动机
  - [Pattern Matching and OO](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#pattern-matching-and-oo)，模式匹配和OO
  - [Patterns and Functional Style](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#patterns-and-functional-style)，模式和函数式风格
- Rationale，理论依据
  - [Overview and Terminology](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#overview-and-terminology)，概述和术语
  - The Match Statement，匹配语句
    - [Match Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#match-semantics)，匹配语句
    - [Guards](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#guards)，守卫
  - Patterns，模式
    - [AS Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#as-patterns)，AS模式
    - [OR Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#or-patterns)，OR模式
    - [Literal Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#literal-patterns)，字面模式
    - [Capture Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#capture-patterns)，捕获模式
    - [Wildcard Pattern](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id3)，通配符模式
    - [Value Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#value-patterns)，值模式
    - [Group Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#group-patterns)，群组模式
    - [Sequence Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#sequence-patterns)，序列模式
    - [Mapping Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#mapping-patterns)，映射模式
    - [Class Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#class-patterns)，类模式
- [History and Context](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#history-and-context)，历史和背景
- [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#backwards-compatibility)，向后兼容
- [Security Implications](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#security-implications)，安全方面的影响
- [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#reference-implementation)，参考实现
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#references)，参考文献
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id9)

概述

This PEP provides the motivation and rationale for [PEP 634](https://www.python.org/dev/peps/pep-0634) ("Structural Pattern Matching: Specification"). First-time readers are encouraged to start with [PEP 636](https://www.python.org/dev/peps/pep-0636), which provides a gentler introduction to the concepts, syntax and semantics of patterns.

本PEP提供了[PEP 634](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md)（"结构模式匹配：规范"）的动机和原理。我们鼓励第一次阅读的读者从[PEP 636](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md)开始，它对模式的概念、语法和语义做了较温和的介绍。

> PEP 634和PEP 636的翻译工作已完成，使用上边的链接访问即可，译者注。

# [Motivation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id10)

动机

(Structural) pattern matching syntax is found in many languages, from Haskell, Erlang and Scala to Elixir and Ruby. (A proposal for JavaScript is also under consideration.)

(结构性）模式匹配语法在许多语言中都可以找到，从Haskell、Erlang和Scala到Elixir和Ruby。(JavaScript的相应提案也在考虑之中)。

Python already supports a limited form of this through sequence unpacking assignments, which the new proposal leverages.

Python已经通过序列解包赋值支持了一种有限的形式，新的提案就是利用了这一点。

Several other common Python idioms are also relevant:

其他几个常见的Python习语也是相关的：

- The `if ... elif ... elif ... else` idiom is often used to find out the type or shape of an object in an ad-hoc fashion, using one or more checks like `isinstance(x, cls)`, `hasattr(x, "attr")`, `len(x) == n` or `"key" in x` as guards to select an applicable block. The block can then assume `x` supports the interface checked by the guard. For example:

  `if ... elif ... elif ... else`习语经常被用来以一种特别的方式找出对象的类型或形状，使用一个或多个检查，如`isinstance(x, cls)`、`hasattr(x, "attr")`、`len(x) == n`或`"key" in x`作为守卫来选择一个适用块。然后该块可以假设`x`支持由守卫检查的接口。比如说：

  ```python
  if isinstance(x, tuple) and len(x) == 2:
      host, port = x
      mode = "http"
  elif isinstance(x, tuple) and len(x) == 3:
      host, port, mode = x
  # Etc.
  ```

  Code like this is more elegantly rendered using `match`:

  像这样的代码使用`match`会更优雅：

  ```python
  match x:
      case host, port:
          mode = "http"
      case host, port, mode:
          pass
      # Etc.
  ```

- AST traversal code often looks for nodes matching a given pattern, for example the code to detect a node of the shape "A + B * C" might look like this:

  AST遍历代码经常寻找与给定模式相匹配的节点，例如，检测 "A + B * C "形状的节点的代码可能看起来像这样：

  ```python
  if (isinstance(node, BinOp) and node.op == "+"
          and isinstance(node.right, BinOp) and node.right.op == "*"):
      a, b, c = node.left, node.right.left, node.right.right
      # Handle a + b*c
  ```

  Using `match` this becomes more readable:

  使用 `match`后这段代码将更具可读性：

  ```python
  match node:
      case BinOp("+", a, BinOp("*", b, c)):
          # Handle a + b*c
  ```

We believe that adding pattern matching to Python will enable Python users to write cleaner, more readable code for examples like those above, and many others.

我们相信在 Python 中加入模式匹配将使 Python 用户能够为上面的例子和其他许多例子编写更干净、更可读的代码。

For a more academic discussion to this proposal, see [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id6).

关于这个提议的更多学术讨论，请看 [1]。

## [Pattern Matching and OO](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id11)

模式匹配和OO

Pattern matching is complimentary to the object-oriented paradigm. Using OO and inheritance we can easily define a method on a base class that defines default behavior for a specific operation on that class, and we can override this default behavior in subclasses. We can also use the Visitor pattern to separate actions from data.

模式匹配是对面向对象范式的补充。使用OO和继承，我们可以很容易地在基类上定义一个方法，为该类的特定操作定义默认行为，我们可以在子类中覆盖这个默认行为。我们还可以使用访问器模式将操作与数据分开。

But this is not sufficient for all situations. For example, a code generator may consume an AST, and have many operations where the generated code needs to vary based not just on the class of a node, but also on the value of some class attributes, like the `BinOp` example above. The Visitor pattern is insufficiently flexible for this: it can only select based on the class.

但这并不能满足所有的情况。例如，一个代码生成器可能会消耗一个AST，并且有许多操作，生成的代码不仅需要根据节点的类来变化，还需要根据一些类属性的值来变化，比如上面的`BinOp`例子。访问器模式对此不够灵活：它只能根据类进行选择。

For a complete example, see https://github.com/gvanrossum/patma/blob/master/examples/expr.py#L231

关于一个完整的例子，见https://github.com/gvanrossum/patma/blob/master/examples/expr.py#L231

Like the Visitor pattern, pattern matching allows for a strict separation of concerns: specific actions or data processing is independent of the class hierarchy or manipulated objects. When dealing with predefined or even built-in classes, in particular, it is often impossible to add further methods to the individual classes. Pattern matching not only relieves the programmer or class designer from the burden of the boilerplate code needed for the Visitor pattern, but is also flexible enough to directly work with built-in types. It naturally distinguishes between sequences of different lengths, which might all share the same class despite obviously differing structures. Moreover, pattern matching automatically takes inheritance into account: a class *D* inheriting from *C* will be handled by a pattern that targets *C* by default.

像访问器模式一样，模式匹配允许严格的分离关注点：具体的动作或数据处理独立于类的层次结构或被操作的对象。特别是在处理预定义的甚至是内置的类时，往往不可能给各个类增加更多的方法。模式匹配不仅将程序员或类的设计者从访问器模式所需的模板代码的负担中解脱出来，而且还足够灵活，可以直接处理内置类型。它自然地区分了不同长度的序列，尽管结构明显不同，但它们可能都共享同一个类。此外，模式匹配还自动考虑到了继承性：一个继承自C的类D将被一个针对C的模式默认处理。

Object oriented programming is geared towards single-dispatch: it is a single instance (or the type thereof) that determines which method is to be called. This leads to a somewhat artificial situation in case of binary operators where both objects might play an equal role in deciding which implementation to use (Python addresses this through the use of reversed binary methods). Pattern matching is structurally better suited to handle such situations of multi-dispatch, where the action to be taken depends on the types of several objects to equal parts.

面向对象的编程是面向单派发的：它是一个单一的实例（或其类型）来决定哪个方法要被调用。这导致在二元运算符的情况下出现了某种人为的情况，在这种情况下，两个对象在决定使用哪种实现时可能发挥同样的作用（Python通过使用反转的二元方法来解决这个问题）。模式匹配在结构上更适合于处理这种多重分配的情况，在这种情况下，要采取的行动取决于几个对象的类型，而且是相等的部分。

> 这里讨论了OO中的二元操作符问题，即A+B和B+A可能因为A和B是不同的类导致不同的运算结果，在Python中这是由开发者决定的，`+`运算符的重载也区分了左结合和右结合，而如果在模式匹配的情形下，这种定义将更清晰，即执行那种运算取决于A和B的类型及出现顺序，将不会有歧义，译者注。

## [Patterns and Functional Style](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id12)

模式和函数式（编程）风格

Many Python applications and libraries are not written in a consistent OO style -- unlike Java, Python encourages defining functions at the top-level of a module, and for simple data structures, tuples (or named tuples or lists) and dictionaries are often used exclusively or mixed with classes or data classes.

许多 Python 应用程序和库不是以一致的 OO 风格编写的 -- 与 Java 不同，Python 鼓励在模块的顶层定义函数，对于简单的数据结构，元组（或具名元组或列表）和字典经常被完全使用或与类或数据类混合使用。

Pattern matching is particularly suitable for picking apart such data structures. As an extreme example, it's easy to write code that picks a JSON data structure using `match`:

模式匹配特别适合于扒开这种数据结构。作为一个极端的例子，很容易写出使用match挑选JSON数据结构的代码：

```python
match json_pet:
    case {"type": "cat", "name": name, "pattern": pattern}:
        return Cat(name, pattern)
    case {"type": "dog", "name": name, "breed": breed}:
        return Dog(name, breed)
    case _:
        raise ValueError("Not a suitable pet")
```

Functional programming generally prefers a declarative style with a focus on relationships in data. Side effects are avoided whenever possible. Pattern matching thus naturally fits and highly supports functional programming style.

函数式编程一般倾向于声明式的风格，重点关注数据中的关系。只要有可能，就会避免副作用。因此，模式匹配自然适合并高度支持函数式编程风格。

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id13)

理论依据

This section provides the rationale for individual design decisions. It takes the place of "Rejected ideas" in the standard PEP format. It is organized in sections corresponding to the specification ([PEP 634](https://www.python.org/dev/peps/pep-0634)).

这一部分提供了个别设计决定的理由。它取代了标准PEP格式中的 "被拒绝的想法"。它被组织在与规范（[PEP 634](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md)）相对应的章节中。

## [Overview and Terminology](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id14)

概述和术语

Much of the power of pattern matching comes from the nesting of subpatterns. That the success of a pattern match depends directly on the success of subpattern is thus a cornerstone of the design. However, although a pattern like `P(Q(), R())` succeeds only if both subpatterns `Q()` and `R()` succeed (i.e. the success of pattern `P` depends on `Q` and `R`), the pattern `P` is checked first. If `P` fails, neither `Q()` nor `R()` will be tried (this is a direct consequence of the fact that if `P` fails, there are no subjects to match against `Q()` and `R()` in the first place).

模式匹配的大部分力量来自于子模式的嵌套。因此，模式匹配的成功直接取决于子模式的成功，这是设计的一个基石。然而，尽管像`P(Q(), R())`这样的模式只有在子模式`Q()`和`R()`都成功时才会成功（即模式`P`的成功取决于`Q`和`R`），但模式`P`首先被检查。如果`P`失败了，`Q()`和`R()`都不会被尝试（这是一个直接的结果，如果`P`失败了，首先就没有可以与`Q()`和`R()`匹配的主题）。

Also note that patterns bind names to values rather than performing an assignment. This reflects the fact that patterns aim to not have side effects, which also means that Capture or AS patterns cannot assign a value to an attribute or subscript. We thus consistently use the term 'bind' instead of 'assign' to emphasise this subtle difference between traditional assignments and name binding in patterns.

还要注意的是，模式将名字与值绑定，而不是进行赋值。这反映了一个事实，即模式的目的是不产生副作用，这也意味着捕获或AS模式不能给属性或下标分配一个值。因此，我们一贯使用术语 "绑定 "而不是 "赋值 "来强调传统赋值和模式中的名称绑定之间的这种微妙差别。

## [The Match Statement](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id15)

匹配语句

The match statement evaluates an expression to produce a subject, finds the first pattern that matches the subject, and executes the associated block of code. Syntactically, the match statement thus takes an expression and a sequence of case clauses, where each case clause comprises a pattern and a block of code.

匹配语句评估一个表达式以产生一个主题，找到第一个与该主题相匹配的模式，并执行相关的代码块。从语法上讲，匹配语句需要一个表达式和一连串的case子句，其中每个case子句包括一个模式和一个代码块。

Since case clauses comprise a block of code, they adhere to the existing indentation scheme with the syntactic structure of `<keyword> ...: <(indented) block>`, which resembles a compound statement. The keyword `case` reflects its widespread use in pattern matching languages, ignoring those languages that use other syntactic means such as a symbol like `|`, because it would not fit established Python structures. The syntax of patterns following the keyword is discussed below.

由于case子句包括一个代码块，它们遵循现有的缩进方案，其语法结构为`<keyword> ...: <(indented) block>`，类似于一个复合语句。关键词的情况反映了它在模式匹配语言中的广泛使用，忽略了那些使用其他语法手段的语言，例如像`|`这样的符号，因为它不适合既定的Python结构。下面将讨论关键词后面的模式的语法。

Given that the case clauses follow the structure of a compound statement, the match statement itself naturally becomes a compound statement itself as well, following the same syntactic structure. This naturally leads to `match <expr>: <case_clause>+`. Note that the match statement determines a quasi-scope in which the evaluated subject is kept alive (although not in a local variable), similar to how a with statement might keep a resource alive during execution of its block. Furthermore, control flows from the match statement to a case clause and then leaves the block of the match statement. The block of the match statement thus has both syntactic and semantic meaning.

鉴于 case 子句遵循复合语句的结构，匹配语句本身自然也成为复合语句本身，遵循同样的语法结构。这自然导致了匹配`<expr>: <case_clause>+`。请注意，匹配语句确定了一个准范围，在这个范围内，被评估的主题被保持活力（尽管不是在一个局部变量中），类似于with语句在其块的执行过程中保持资源活力的方式。此外，控制从匹配语句流向case子句，然后离开匹配语句的块。因此，匹配语句的块具有语法和语义上的双重意义。

Various suggestions have sought to eliminate or avoid the naturally arising "double indentation" of a case clause's code block. Unfortunately, all such proposals of *flat indentation schemes* come at the expense of violating Python's established structural paradigm, leading to additional syntactic rules:

各种建议都试图消除或避免case子句的代码块自然产生的 "双缩进"。不幸的是，所有这些平缓缩进方案的建议都是以违反Python的既定结构范式为代价的，从而导致了额外的语法规则：

- *Unindented case clauses.* The idea is to align case clauses with the `match`, i.e.:

  *无缩进的 case 子句。*我们的想法是将case子句与之匹配，即：

  ```python
  match expression:
  case pattern_1:
      ...
  case pattern_2:
      ...
  ```

  This may look awkward to the eye of a Python programmer, because everywhere else a colon is followed by an indent. The `match` would neither follow the syntactic scheme of simple nor composite statements but rather establish a category of its own.

  在Python程序员的眼里，这可能看起来很别扭，因为在其他地方，冒号后面都有缩进。`match`既不会遵循简单语句也不会遵循复合语句的语法方案，而是建立一个自己的类别。

- *Putting the expression on a separate line after "match".* The idea is to use the expression yielding the subject as a statement to avoid the singularity of `match` having no actual block despite the colons:

  *将表达式放在 "匹配 "之后的单独一行。*这个想法是把产生主题的表达式作为一个语句来使用，以避免尽管有冒号，但`match`没有实际块的奇异现象。

  ```python
  match:
      expression
  case pattern_1:
      ...
  case pattern_2:
      ...
  ```

  This was ultimately rejected because the first block would be another novelty in Python's grammar: a block whose only content is a single expression rather than a sequence of statements. Attempts to amend this issue by adding or repurposing yet another keyword along the lines of `match: return expression` did not yield any satisfactory solution.

  这最终被拒绝了，因为第一个块将是 Python 语法中的另一个新事物：一个块的唯一内容是一个表达式，而不是一个语句序列。试图通过添加或重新使用另一个类似于 `match: return expression` 的关键字来修正这个问题，但没有得到任何满意的解决方案。

Although flat indentation would save some horizontal space, the cost of increased complexity or unusual rules is too high. It would also complicate life for simple-minded code editors. Finally, the horizontal space issue can be alleviated by allowing "half-indent" (i.e. two spaces instead of four) for match statements (though we do not recommend this).

尽管平面缩进可以节省一些水平空间，但增加复杂性或不寻常规则的代价太高了。这也会使头脑简单的代码编辑者的生活变得复杂。最后，水平空间问题可以通过允许匹配语句的 "半缩进"（即两个空格而不是四个空格）来缓解（尽管我们不推荐这样做）。

In sample programs using `match`, written as part of the development of this PEP, a noticeable improvement in code brevity is observed, more than making up for the additional indentation level.

在作为本PEP开发的一部分而编写的使用`match`的示例程序中，可以观察到代码简洁度的明显改善，这比额外的缩进程度要好。

*Statement vs. Expression.* Some suggestions centered around the idea of making `match` an expression rather than a statement. However, this would fit poorly with Python's statement-oriented nature and lead to unusually long and complex expressions and the need to invent new syntactic constructs or break well established syntactic rules. An obvious consequence of `match` as an expression would be that case clauses could no longer have arbitrary blocks of code attached, but only a single expression. Overall, the strong limitations could in no way offset the slight simplification in some special use cases.

*语句与表达式。*一些建议集中在使匹配成为一个表达式而不是一个语句的想法上。然而，这与 Python 面向语句的特性不相称，而且会导致异常冗长和复杂的表达式，并需要发明新的语法结构或打破既定的语法规则。匹配作为一种表达式的一个明显的后果是，case子句不能再有任意的代码块附加，而只能是一个单一的表达式。总的来说，强烈的限制绝不可能抵消某些特殊用例中的轻微简化。

*Hard vs. Soft Keyword.* There were options to make match a hard keyword, or choose a different keyword. Although using a hard keyword would simplify life for simple-minded syntax highlighters, we decided not to use hard keyword for several reasons:

*硬关键字与软关键字。*有一些选项可以使匹配成为硬关键字，或者选择一个不同的关键字。尽管使用硬关键字可以简化头脑简单的语法高亮器的工作，但我们决定不使用硬关键字，原因有几个：

> 怪不得输入完`match`后不会立即高亮，需要输入完整的`match subject:`后才会高亮语法，译者注。

- Most importantly, the new parser doesn't require us to do this. Unlike with `async` that caused hardships with being a soft keyword for few releases, here we can make `match` a permanent soft keyword.

  最重要的是，新的解析器不需要我们这样做。与`async`不同的是，在几个版本中，`async`是一个软关键字，造成了困难，在这里，我们可以把`match`变成一个永久的软关键字。

- `match` is so commonly used in existing code, that it would break almost every existing program and will put a burden to fix code on many people who may not even benefit from the new syntax.

  `match`在现有的代码中非常常用，它几乎会破坏所有现有的程序，并且会给许多人带来修复代码的负担，而他们甚至可能不会从新的语法中受益。

- It is hard to find an alternative keyword that would not be commonly used in existing programs as an identifier, and would still clearly reflect the meaning of the statement.

  很难找到一个替代的关键词，它不会在现有的程序中作为标识符被普遍使用，并且仍然能够清楚地反映语句的含义。

**Use "as" or "|" instead of "case" for case clauses.** The pattern matching proposed here is a combination of multi-branch control flow (in line with `switch` in Algol-derived languages or `cond` in Lisp) and object-deconstruction as found in functional languages. While the proposed keyword `case` highlights the multi-branch aspect, alternative keywords such as `as` would equally be possible, highlighting the deconstruction aspect. `as` or `with`, for instance, also have the advantage of already being keywords in Python. However, since `case` as a keyword can only occur as a leading keyword inside a `match` statement, it is easy for a parser to distinguish between its use as a keyword or as a variable.

**使用 "as "或"|"而不是 "case "来表示case语句。**这里提出的模式匹配是多分支控制流（与Algol衍生语言中的`switch`或Lisp中的`cond`一致）和函数式语言中的对象解构的结合。虽然建议的关键字`case`突出了多分支的方面，但其他的关键字如`as`也同样可以，突出了解构的方面。例如，`as`或`with`也有一个优点，就是已经是Python的关键字。然而，由于`case`作为一个关键字只能在匹配语句中作为前导关键字出现，解析器很容易区分它是作为一个关键字还是作为一个变量使用。

Other variants would use a symbol like `|` or `=>`, or go entirely without special marker.

其他变体会使用像 `|` 或 `=>` 这样的符号，或者完全没有特殊标记。

Since Python is a statement-oriented language in the tradition of Algol, and as each composite statement starts with an identifying keyword, `case` seemed to be most in line with Python's style and traditions.

由于 Python 是一种继承了 Algol 传统的面向语句的语言，并且每个复合语句都以一个可识别的关键字开始，`case` 似乎最符合 Python 的风格和传统。

### [Match Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id16)

匹配语义

The patterns of different case clauses might overlap in that more than one case clause would match a given subject. The first-to-match rule ensures that the selection of a case clause for a given subject is unambiguous. Furthermore, case clauses can have increasingly general patterns matching wider sets of subjects. The first-to-match rule then ensures that the most precise pattern can be chosen (although it is the programmer's responsibility to order the case clauses correctly).

不同的case语句的模式可能会重叠，即不止一个case语句会与一个给定的主语相匹配。先到先得的规则保证了为一个给定的主题选择一个case语句是明确的。此外，case语句可以有越来越普遍的模式来匹配更多的主题。首先匹配规则可以确保选择最精确的模式（尽管程序员有责任正确排列case语句）。

> 这里指通常情况下应当将具有“更精确”模式的case块放在靠前的位置，而“更粗略”模式所属的case块放在靠后的位置，当然为了匹配所有结果，在最后添加上`case _:`是个良好习惯，译者注。

In a statically typed language, the match statement would be compiled to a decision tree to select a matching pattern quickly and very efficiently. This would, however, require that all patterns be purely declarative and static, running against the established dynamic semantics of Python. The proposed semantics thus represent a path incorporating the best of both worlds: patterns are tried in a strictly sequential order so that each case clause constitutes an actual statement. At the same time, we allow the interpreter to cache any information about the subject or change the order in which subpatterns are tried. In other words: if the interpreter has found that the subject is not an instance of a class `C`, it can directly skip case clauses testing for this again, without having to perform repeated instance-checks. If a guard stipulates that a variable `x` must be positive, say (i.e. `if x > 0`), the interpreter might check this directly after binding `x` and before any further subpatterns are considered.

在静态类型的语言中，匹配语句将被编译成一个决策树，以快速和非常有效地选择一个匹配模式。然而，这就要求所有的模式都是纯粹的声明性的和静态的，与Python的既定动态语义相违背。因此，所提出的语义代表了一条融合了两个世界的最佳方案的道路：模式以严格的顺序被尝试，因此每个case子句构成了一个实际的声明。同时，我们允许解释器缓存关于主题的任何信息，或者改变子模式的尝试顺序。换句话说：如果解释器发现主体不是类`C`的实例，它可以直接跳过case子句再次进行测试，而不需要重复进行实例检查。如果一个守卫规定一个变量`x`必须是正数，比如说（即`if x>0`），解释器可以在绑定`x`之后，在考虑任何进一步的子模式之前直接检查这一点。

> 在PEP 634 和 PEP636中，有多处强调了守卫包含的表达式的评估是在模式匹配和绑定行为之后的，这与这里的描述并不冲突，因为前者是语法宏观层面的规则，而这里是说在解释器执行的细节中，是可以通过某种跳过不必要的子模式匹配而直接进行守卫评估，以达到性能优化的目的。而这个技术细节并不会与宏观语法相违背，译者注。

*Binding and scoping.* In many pattern matching implementations, each case clause would establish a separate scope of its own. Variables bound by a pattern would then only be visible inside the corresponding case block. In Python, however, this does not make sense. Establishing separate scopes would essentially mean that each case clause is a separate function without direct access to the variables in the surrounding scope (without having to resort to `nonlocal` that is). Moreover, a case clause could no longer influence any surrounding control flow through standard statement such as `return` or `break`. Hence, such strict scoping would lead to unintuitive and surprising behavior.

*绑定和范围。*在许多模式匹配的实现中，每个case子句都会建立一个独立的范围。被模式绑定的变量将只在相应的 case 块中可见。然而，在 Python 中，这是不可能的。建立独立的作用域实质上意味着每个 case 子句是一个独立的函数，不能直接访问周围作用域中的变量 (不必求助于非局部)。此外，一个case子句不能再通过标准语句（如`return`或`break`）影响周围的控制流。因此，这种严格的范围界定会导致不直观的和令人惊讶的行为。

> 这点同样是与其它主流编程语言，如C++或Java是极为不同的，在C++或Java中，是可以用`{}`来显示划分局部作用域的，译者注。

A direct consequence of this is that any variable bindings outlive the respective case or match statements. Even patterns that only match a subject partially might bind local variables (this is, in fact, necessary for guards to function properly). However, these semantics for variable binding are in line with existing Python structures such as for loops and with statements.

这样做的一个直接后果是，任何变量的绑定都会超过各自的case或匹配语句。即使是只与主题部分匹配的模式也可能会绑定本地变量（事实上，这对于守卫的正常运作是必要的）。然而，这些变量绑定的语义与现有的 Python 结构一致，比如 for 循环和 with 语句。

### [Guards](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id17)

守卫

Some constraints cannot be adequately expressed through patterns alone. For instance, a 'less' or 'greater than' relationship defies the usual 'equal' semantics of patterns. Moreover, different subpatterns are independent and cannot refer to each other. The addition of *guards* addresses these restrictions: a guard is an arbitrary expression attached to a pattern and that must evaluate to a "truthy" value for the pattern to succeed.

有些约束不能仅仅通过模式来充分表达。例如，"小于 "或 "大于 "的关系违背了模式的通常的 "相等 "语义。此外，不同的子模式是独立的，不能相互引用。守卫的加入解决了这些限制：守卫是一个附加在模式上的任意表达式，它必须评估为一个 "真" 的值，模式才能成功。

For example, `case [x, y] if x < y:` uses a guard (`if x < y`) to express a 'less than' relationship between two otherwise disjoint capture patterns `x` and `y`.

例如，`case [x, y] if x < y:` 使用一个守卫（`if x < y`）来表达两个不相干的捕获模式`x`和`y`之间的 "小于 "关系。

From a conceptual point of view, patterns describe structural constraints on the subject in a declarative style, ideally without any side-effects. Recall, in particular, that patterns are clearly distinct from expressions, following different objectives and semantics. Guards then enhance case blocks in a highly controlled way with arbitrary expressions (that might have side effects). Splitting the overall functionality into a static structural and a dynamically evaluated part not only helps with readability, but can also introduce dramatic potential for compiler optimizations. To keep this clear separation, guards are only supported on the level of case clauses and not for individual patterns.

从概念的角度来看，模式以声明的方式描述了对主题的结构约束，最好是没有任何副作用。特别提醒一下，模式与表达式明显不同，遵循不同的目标和语义。然后，守卫以高度控制的方式用任意的表达式（可能有副作用）来增强case块。将整体功能分成静态结构和动态评估部分，不仅有助于提高可读性，而且还能为编译器优化带来巨大的潜力。为了保持这种清晰的分离，守卫只支持在case子句的层面上，而不是针对单个模式。

> 这里明确了，模式是静态的，是结构化的，而守卫是动态评估的，两者是截然不同的。这大概也是为什么`match...case`被命名为结构化模式匹配的原因，译者注。

**Example** using guards:

守卫的使用**案例**：

```python
def sort(seq):
    match seq:
        case [] | [_]:
            return seq
        case [x, y] if x <= y:
            return seq
        case [x, y]:
            return [y, x]
        case [x, y, z] if x <= y <= z:
            return seq
        case [x, y, z] if x >= y >= z:
            return [z, y, x]
        case [p, *rest]:
            a = sort([x for x in rest if x <= p])
            b = sort([x for x in rest if p < x])
            return a + [p] + b
```

## [Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id18)

模式

Patterns fulfill two purposes: they impose (structural) constraints on the subject and they specify which data values should be extracted from the subject and bound to variables. In iterable unpacking, which can be seen as a prototype to pattern matching in Python, there is only one *structural pattern* to express sequences while there is a rich set of *binding patterns* to assign a value to a specific variable or field. Full pattern matching differs from this in that there is more variety in structural patterns but only a minimum of binding patterns.

模式实现了两个目的：它们对主题施加了（结构上的）约束，它们指定了哪些数据值应该从主体中提取并绑定到变量上。在可迭代解包中，可以看作是Python中模式匹配的原型，只有一种结构模式来表达序列，而有一套丰富的绑定模式来给特定的变量或字段赋值。完整的模式匹配与此不同，因为结构模式有更多的种类，但只有最少的绑定模式。

Patterns differ from assignment targets (as in iterable unpacking) in two ways: they impose additional constraints on the structure of the subject, and a subject may safely fail to match a specific pattern at any point (in iterable unpacking, this constitutes an error). The latter means that pattern should avoid side effects wherever possible.

模式在两个方面不同于赋值目标（如在可迭代解包中）：它们对主题的结构施加了额外的约束，而且主题在任何时候都可以安全地不匹配特定的模式（在可迭代解包中，这构成一个错误）。后者意味着模式应该尽可能避免副作用。

> 这里明确了模式匹配和可迭代解包最大的不同，解包是必须成功的，如果出现长度不一致等情况就会导致错误并退出程序，而模式匹配就宽松的多，一个模式匹配不了就下一个，译者注。

This desire to avoid side effects is one reason why capture patterns don't allow binding values to attributes or subscripts: if the containing pattern were to fail in a later step, it would be hard to revert such bindings.

这种避免副作用的愿望是捕获模式不允许将值绑定到属性或下标的原因之一：如果包含的模式在后面的步骤中失败，就很难恢复这种绑定。

> 这里的意思是，通常我们是使用一些简单的局部变量去捕获模式，比如通过`case x,y:`来绑定主题中的一对二维坐标。即使模式匹配成功，但守卫评估失败也不会影响到后续代码执行，因为`x`、`y`这样的简单变量很难对既定代码造成深远影响。但是如果可以绑定对象的属性，或者是列表的下标，就可能对既定代码造成深远影响，进而导致一些难以预料的bug，译者注。

A cornerstone of pattern matching is the possibility of arbitrarily *nesting patterns*. The nesting allows expressing deep tree structures (for an example of nested class patterns, see the motivation section above) as well as alternatives.

模式匹配的一个基石是任意嵌套模式的可能性。嵌套允许表达深层的树状结构（关于嵌套类模式的例子，见上面的动机部分）以及替代物。

Although patterns might superficially look like expressions, it is important to keep in mind that there is a clear distinction. In fact, no pattern is or contains an expression. It is more productive to think of patterns as declarative elements similar to the formal parameters in a function definition.

尽管模式从表面上看可能像表达式，但重要的是要记住这两者之间有明显的区别。事实上，没有任何模式是表达式、或者包含表达式。把模式看作是类似于函数定义中的形式参数的声明性元素，会更有成效。

### [AS Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id19)

AS 模式

Patterns fall into two categories: most patterns impose a (structural) constraint that the subject needs to fulfill, whereas the capture pattern binds the subject to a name without regard for the subject's structure or actual value. Consequently, a pattern can either express a constraint or bind a value, but not both. AS patterns fill this gap in that they allow the user to specify a general pattern as well as capture the subject in a variable.

模式分为两类：大多数模式施加了一个主题需要满足的（结构性）约束，而捕获模式则将主题与一个名称绑定，而不考虑主题的结构或实际价值。因此，一个模式既可以表达一个约束条件，也可以绑定一个值，但不能两者兼得。AS模式填补了这一空白，因为它们允许用户指定一个一般的模式，并在一个变量中捕获主题。

Typical use cases for the AS pattern include OR and Class patterns together with a binding name as in, e.g., `case BinOp('+'|'-' as op, ...):` or `case [int() as first, int() as second]:`. The latter could be understood as saying that the subject must fulfil two distinct pattern: `[first, second]` as well as `[int(), int()]`. The AS pattern can thus be seen as a special case of an 'and' pattern (see OR patterns below for an additional discussion of 'and' patterns).

AS模式的典型用例包括OR和Class模式以及一个绑定名称，例如，`case BinOp('+'|'-' as op，...):` 或`case [int() as first, int() as second]:`。后者可以理解为主体必须满足两个不同的模式。`[first, second]`以及`[int(), int()]`。因此，AS模式可以被看作是 "和 "模式的一个特例（关于 "和 "模式的额外讨论，见下文的OR模式）。

In an earlier version, the AS pattern was devised as a 'Walrus pattern', written as `case [first:=int(), second:=int()]`. However, using `as` offers some advantages over `:=`:

在早期版本中，AS模式被设计为 "海象模式"，写成`case [first:=int(), second:=int()]`。然而，使用`as`比`:=`有一些优势：

> 关于赋值表达式和海象操作符的内容可以阅读[PEP 572 -- Assignment Expressions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md)，译者注。

- The walrus operator `:=` is used to capture the result of an expression on the right hand side, whereas `as` generally indicates some form of 'processing' as in `import foo as bar` or `except E as err:`. Indeed, the pattern `P as x` does not assign the pattern `P` to `x`, but rather the subject that successfully matches `P`.

  海象操作符 := 用于捕获表达式右侧的结果，而 as 通常表示某种形式的 "处理"，如 `import foo as bar` 或 `except E as err:`。事实上，模式`P as x`并没有把模式`P`分配给`x`，而是分配给成功匹配`P`的主题。

  > 这段描述表达的相当准确，说到底，模式匹配是一种机制，而非表达式评估。所以使用海象操作符在这里的确不合适，译者注。

- `as` allows for a more consistent data flow from left to right (the attributes in Class patterns also follow a left-to-right data flow).

  `as`允许从左到右更一致的数据流（类模式中的属性也遵循从左到右的数据流）。

- The walrus operator looks very similar to the syntax for matching attributes in the Class pattern, potentially leading to some confusion.

  海象操作符看起来与Class模式中匹配属性的语法非常相似，可能会导致一些混淆。

**Example** using the AS pattern:

使用AS模式的**例子**：

```python
def simplify_expr(tokens):
    match tokens:
        case [('('|'[') as l, *expr, (')'|']') as r] if (l+r) in ('()', '[]'):
            return simplify_expr(expr)
        case [0, ('+'|'-') as op, right]:
            return UnaryOp(op, right)
        case [(int() | float() as left) | Num(left), '+', (int() | float() as right) | Num(right)]:
            return Num(left + right)
        case [(int() | float()) as value]:
            return Num(value)
```

### [OR Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id20)

OR模式

The OR pattern allows you to combine 'structurally equivalent' alternatives into a new pattern, i.e. several patterns can share a common handler. If any of an OR pattern's subpatterns matches the subject, the entire OR pattern succeeds.

OR模式允许你将 "结构上相等的 "备选方案组合成一个新的模式，也就是说，几个模式可以共享一个共同的处理器。如果一个OR模式的任何一个子模式与主题匹配，整个OR模式就会成功。

Statically typed languages prohibit the binding of names (capture patterns) inside an OR pattern because of potential conflicts concerning the types of variables. As a dynamically typed language, Python can be less restrictive here and allow capture patterns inside OR patterns. However, each subpattern must bind the same set of variables so as not to leave potentially undefined names. With two alternatives `P | Q`, this means that if *P* binds the variables *u* and *v*, *Q* must bind exactly the same variables *u* and *v*.

静态类型语言禁止在一个OR模式中绑定名称（捕获模式），因为这与变量的类型有潜在的冲突。作为一种动态类型的语言，Python 在这里可以不那么严格，允许在 OR 模式中使用捕获模式。然而，每个子模式必须绑定相同的变量集，以避免留下潜在的未定义名称。对于两个备选方案`P | Q`，这意味着如果`P`绑定了变量`u`和`v`，`Q`必须绑定完全相同的变量`u`和`v`。

> 这里指OR模式中的各个子模式所绑定的变量行为必须一致，不能出现`case [x,y] | [0,x] :`这样的情况，这个例子中第一个子模式中绑定行为是`x=subject[0];y=subject[1]`，但第二个子模式中绑定行为是`x=subject[1]`，这显然是互相矛盾的，译者注。

There was some discussion on whether to use the bar symbol `|` or the `or` keyword to separate alternatives. The OR pattern does not fully fit the existing semantics and usage of either of these two symbols. However, `|` is the symbol of choice in all programming languages with support of the OR pattern and is used in that capacity for regular expressions in Python as well. It is also the traditional separator between alternatives in formal grammars (including Python's). Moreover, `|` is not only used for bitwise OR, but also for set unions and dict merging ([PEP 584](https://www.python.org/dev/peps/pep-0584)).

关于是用条形符号`|`还是用`or`关键字来分隔替代物，我们进行了一些讨论。OR模式并不完全符合这两个符号的现有语义和用法。然而，`|`是所有支持OR模式的编程语言中的首选符号，并且在Python中也以这种身份用于正则表达式。它也是形式化语法(包括Python)中备选方案之间的传统分隔符。此外，`|`不仅用于位数OR，而且还用于集合联合和字典合并（[PEP 584](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md)）。

Other alternatives were considered as well, but none of these would allow OR-patterns to be nested inside other patterns:

我们也考虑了其他的选择，但这些都不允许OR模式嵌套在其他模式中：

- *Using a comma*:

  使用逗号：

  ```python
  case 401, 403, 404:
      print("Some HTTP error")
  ```

  This looks too much like a tuple -- we would have to find a different way to spell tuples, and the construct would have to be parenthesized inside the argument list of a class pattern. In general, commas already have many different meanings in Python, we shouldn't add more.

  这看起来太像一个元组了 -- 我们必须找到一种不同的方式来拼写元组，而且这个结构必须在一个类模式的参数列表中使用括号。总的来说，逗号在 Python 中已经有很多不同的含义，我们不应该再增加更多。

- *Using stacked cases*:

  *使用堆叠的case*：

  ```
  case 401:
  case 403:
  case 404:
      print("Some HTTP error")
  ```

  This is how this would be done in *C*, using its fall-through semantics for cases. However, we don't want to mislead people into thinking that match/case uses fall-through semantics (which are a common source of bugs in *C*). Also, this would be a novel indentation pattern, which might make it harder to support in IDEs and such (it would break the simple rule "add an indentation level after a line ending in a colon"). Finally, this would not support OR patterns nested inside other patterns, either.

  在*C*中就是这样做的，在case中使用其跌落语义。然而，我们不希望误导人们认为match/case使用跌落语义（这在*C*中是一个常见的错误来源）。另外，这将是一个新的缩进模式，这可能会使它更难在IDE等中得到支持（它将打破 "在以冒号结束的行后增加一个缩进级别 "的简单规则）。最后，这也不支持嵌套在其他模式中的OR模式。

- *Using "case in" followed by a comma-separated list*:

  *使用 "case in"，后面有一个逗号分隔的列表*：

  ```python
  case in 401, 403, 404:
      print("Some HTTP error")
  ```

  This would not work for OR patterns nested inside other patterns, like:

  这对嵌套在其他模式中的OR模式不起作用，例如：

  ```python
  case Point(0|1, 0|1):
      print("A corner of the unit square")
  ```

**AND and NOT Patterns**

**AND和NOT模式**

Since this proposal defines an OR-pattern (`|`) to match one of several alternates, why not also an AND-pattern (`&`) or even a NOT-pattern (`!`)? Especially given that some other languages (`F#` for example) support AND-patterns.

既然这个建议定义了一个OR模式(`|`)来匹配几个备选方案中的一个，为什么不同时定义一个AND模式(`&`)或者甚至一个NOT模式(`!`)？特别是考虑到一些其他语言（例如`F#`）支持AND-模式。

However, it is not clear how useful this would be. The semantics for matching dictionaries, objects and sequences already incorporates an implicit 'and': all attributes and elements mentioned must be present for the match to succeed. Guard conditions can also support many of the use cases that a hypothetical 'and' operator would be used for.

然而，目前还不清楚这有多大用处。匹配字典、对象和序列的语义已经包含了一个隐含的 "和"：所有提到的属性和元素都必须存在，匹配才能成功。守卫条件也可以支持许多假设的 "and "运算符将被用于的使用情况。

A negation of a match pattern using the operator `!` as a prefix would match exactly if the pattern itself does not match. For instance, `!(3 | 4)` would match anything except `3` or `4`. However, there is [evidence from other languages](https://dl.acm.org/doi/abs/10.1145/2480360.2384582) that this is rarely useful, and primarily used as double negation `!!` to control variable scopes and prevent variable bindings (which does not apply to Python). Other use cases are better expressed using guards.

使用操作符`！`作为前缀的匹配模式的否定，将在模式本身不匹配的情况下准确匹配。例如，`!(3 | 4)`将匹配除`3`或`4`以外的任何内容。然而，有[来自其它语言的证据](https://dl.acm.org/doi/abs/10.1145/2480360.2384582)表明这很少有用，主要作为双重否定`!!`来控制变量范围和防止变量绑定(这不适用于Python)。其他用例最好用守卫来表达。

In the end, it was decided that this would make the syntax more complex without adding a significant benefit. It can always be added later.

最后，我们决定这将使语法更加复杂，而不会增加明显的好处。它总是可以在以后添加。

**Example** using the OR pattern:

使用OR模式的**例子**：

```python
def simplify(expr):
    match expr:
        case ('/', 0, 0):
            return expr
        case ('*'|'/', 0, _):
            return 0
        case ('+'|'-', x, 0) | ('+', 0, x) | ('*', 1, x) | ('*'|'/', x, 1):
            return x
    return expr
```

### [Literal Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id21)

字面模式

Literal patterns are a convenient way for imposing constraints on the value of a subject, rather than its type or structure. They also allow you to emulate a switch statement using pattern matching.

字面模式是一种方便的方式，可以对一个主题的值而不是其类型或结构施加约束。它们还允许你使用模式匹配来模拟一个开关语句。

Generally, the subject is compared to a literal pattern by means of standard equality (`x == y` in Python syntax). Consequently, the literal patterns `1.0` and `1` match exactly the same set of objects, i.e. `case 1.0:` and `case 1:` are fully interchangeable. In principle, `True` would also match the same set of objects because `True == 1` holds. However, we believe that many users would be surprised finding that `case True:` matched the subject `1.0`, resulting in some subtle bugs and convoluted workarounds. We therefore adopted the rule that the three singleton patterns `None`, `False` and `True` match by identity (`x is y` in Python syntax) rather than equality. Hence, `case True:` will match only `True` and nothing else. Note that `case 1:` would still match `True`, though, because the literal pattern `1` works by equality and not identity.

一般来说，主题是通过标准的等价法 (Python 语法中的` x == y`) 与字面模式进行比较。因此，字面模式`1.0`和`1`完全匹配同一组对象，即`case 1.0:`和`case 1:`是完全可以互换的。原则上，`True`也可以匹配相同的对象集，因为`True == 1`成立。然而，我们相信许多用户会惊讶地发现，`case True:`与主题`1.0`相匹配，从而导致一些微妙的错误和复杂的变通方法。因此，我们采用了这样的规则：三个单例模式`None`、`False`和`True`通过同一性（在Python语法中`x is y`）而不是平等性进行匹配。因此，`case True:` 将只匹配`True`，而不匹配其他。需要注意的是，`case 1:`还是会匹配`True`，因为字面模式`1`是通过平等而不是身份来工作的。

> 这是一个很容易被忽略的细节，但是大多数情况下都不会遇到这种问题，译者注。

Early ideas to induce a hierarchy on numbers so that `case 1.0` would match both the integer `1` and the floating point number `1.0`, whereas `case 1:` would only match the integer `1` were eventually dropped in favor of the simpler and more consistent rule based on equality. Moreover, any additional checks whether the subject is an instance of `numbers.Integral` would come at a high runtime cost to introduce what would essentially be a novel idea in Python. When needed, the explicit syntax `case int(1):` can be used.

早期的想法是对数字进行分级，使`case 1.0`同时匹配整数`1`和浮点数`1.0`，而`case 1:`只匹配整数`1`，但这些想法最终被放弃，转而采用更简单、更一致的基于平等的规则。此外，任何额外的检查主题是否是 `numbers.Integral` 的一个实例都会带来很高的运行时间成本，而这在 Python 中本质上是一个新的想法。当需要时，可以使用明确的语法`case int(1):`。

> 这里的`case int(1):`是`case int() as x if x==1`的一种简略写法，实质上是利用了类模式结合位置模式的写法，其中`1`是作为`int`类的一个位置参数进行指定，译者注。

Recall that literal patterns are *not* expressions, but directly denote a specific value. From a pragmatic point of view, we want to allow using negative and even complex values as literal patterns, but they are not atomic literals (only unsigned real and imaginary numbers are). E.g., `-3+4j` is syntactically an expression of the form `BinOp(UnaryOp('-', 3), '+', 4j)`. Since expressions are not part of patterns, we had to add explicit syntactic support for such values without having to resort to full expressions.

回顾一下，字面模式*不是*表达式，而是直接表示一个特定的值。从实用的角度来看，我们希望允许使用负值甚至复数作为字面模式，但它们不是原子字面（只有无符号实数和虚数是）。例如，`-3+4j`在语法上是一个`BinOp(UnaryOp('-', 3), '+', 4j)`形式的表达。由于表达式不是模式的一部分，我们必须为这种值增加明确的语法支持，而不必求助于完整的表达式。

Interpolated *f*-strings, on the other hand, are not literal values, despite their appearance and can therefore not be used as literal patterns (string concatenation, however, is supported).

另一方面，插值的格式化字符串不是字面值，尽管它们外观表现的一致。因此不能作为字面模式使用（不过，支持字符串连接）。

Literal patterns not only occur as patterns in their own right, but also as keys in *mapping patterns*.

字面模式不仅作为模式本身出现，而且还作为*映射模式*的键出现。

**Range matching patterns.** This would allow patterns such as `1...6`. However, there are a host of ambiguities:

**范围匹配模式。**这将允许诸如1...6这样的模式。然而，这有很多模糊之处：

- Is the range open, half-open, or closed? (I.e. is `6` included in the above example or not?)

  这个范围是开放的、半开放的还是封闭的？(例如，`6`是否包括在上面的例子中？)

- Does the range match a single number, or a range object?

  这个范围是匹配一个单一的数字，还是一个范围对象？

- Range matching is often used for character ranges ('a'...'z') but that won't work in Python since there's no character data type, just strings.

  范围匹配通常用于字符范围 ('a'...'z')，但这在 Python 中不起作用，因为没有字符数据类型，只有字符串。

- Range matching can be a significant performance optimization if you can pre-build a jump table, but that's not generally possible in Python due to the fact that names can be dynamically rebound.

  如果你能预先建立一个跳转表，范围匹配可以是一个显著的性能优化，但是在Python中一般是不可能的，因为名字可以动态地反弹。

Rather than creating a special-case syntax for ranges, it was decided that allowing custom pattern objects (`InRange(0, 6)`) would be more flexible and less ambiguous; however those ideas have been postponed for the time being.

与其为范围创建一个特例语法，不如决定允许自定义模式对象(`InRange(0, 6)`)会更灵活，更不含糊；然而这些想法暂时被推迟了。

**Example** using Literal patterns:

使用字面模式的**例子**：

```python
def simplify(expr):
    match expr:
        case ('+', 0, x):
            return x
        case ('+' | '-', x, 0):
            return x
        case ('and', True, x):
            return x
        case ('and', False, x):
            return False
        case ('or', False, x):
            return x
        case ('or', True, x):
            return True
        case ('not', ('not', x)):
            return x
    return expr
```

### [Capture Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id22)

捕获模式

Capture patterns take on the form of a name that accepts any value and binds it to a (local) variable (unless the name is declared as `nonlocal` or `global`). In that sense, a capture pattern is similar to a parameter in a function definition (when the function is called, each parameter binds the respective argument to a local variable in the function's scope).

捕获模式的形式是一个接受任何值的名称，并将其绑定到一个（局部）变量（除非该名称被声明为`nonlocal`或`global`）。在这个意义上，捕获模式类似于函数定义中的参数（当函数被调用时，每个参数将各自的参数绑定到函数范围内的一个局部变量）。

A name used for a capture pattern must not coincide with another capture pattern in the same pattern. This, again, is similar to parameters, which equally require each parameter name to be unique within the list of parameters. It differs, however, from iterable unpacking assignment, where the repeated use of a variable name as target is permissible (e.g., `x, x = 1, 2`). The rationale for not supporting `(x, x)` in patterns is its ambiguous reading: it could be seen as in iterable unpacking where only the second binding to `x` survives. But it could be equally seen as expressing a tuple with two equal elements (which comes with its own issues). Should the need arise, then it is still possible to introduce support for repeated use of names later on.

用于捕获模式的名称不能与同一模式中的另一个捕获模式重合。这又与参数类似，同样要求每个参数的名称在参数列表中是唯一的。然而，它与可迭代解包赋值不同，在可迭代解包赋值中，重复使用一个变量名作为目标是允许的（例如，`x，x=1，2`）。在模式中不支持`(x, x)`的理由是其模糊的解读：它可以被看作是在可迭代解包中，只有对`x`的第二次绑定才能存活。但它同样可以被看作是表达一个具有两个相等元素的元组（这有它自己的问题）。如果需要的话，以后仍有可能引入对名称重复使用的支持。

> 老实说，我并没有注意到在解包时可以重复使用同一个变量名，译者注。

There were calls to explicitly mark capture patterns and thus identify them as binding targets. According to that idea, a capture pattern would be written as, e.g. `?x`, `$x` or `=x`. The aim of such explicit capture markers is to let an unmarked name be a value pattern (see below). However, this is based on the misconception that pattern matching was an extension of *switch* statements, placing the emphasis on fast switching based on (ordinal) values. Such a *switch* statement has indeed been proposed for Python before (see [PEP 275](https://www.python.org/dev/peps/pep-0275) and [PEP 3103](https://www.python.org/dev/peps/pep-3103)). Pattern matching, on the other hand, builds a generalized concept of iterable unpacking. Binding values extracted from a data structure is at the very core of the concept and hence the most common use case. Explicit markers for capture patterns would thus betray the objective of the proposed pattern matching syntax and simplify a secondary use case at the expense of additional syntactic clutter for core cases.

有人呼吁明确地标记捕获模式，从而将它们确定为绑定目标。根据这个想法，一个捕获模式将被写成，例如：`?x`、`$x`或`=x`。这种显式捕获标记的目的是让一个没有标记的名字成为一个值模式（见下文）。然而，这是基于这样的误解：模式匹配是switch语句的延伸，将重点放在基于（序数）值的快速切换上。这样的 switch 语句确实曾经被提议用于 Python (见 PEP 275 和 PEP 3103)。另一方面，模式匹配建立了一个通用的可迭代解包的概念。绑定从数据结构中提取的值是这个概念的核心，因此是最常见的使用情况。因此，捕获模式的明确标记将背叛所提议的模式匹配语法的目标，并以核心案例的额外语法混乱为代价简化了次要用例。

It has been proposed that capture patterns are not needed at all, since the equivalent effect can be obtained by combining an AS pattern with a wildcard pattern (e.g., `case _ as x` is equivalent to `case x`). However, this would be unpleasantly verbose, especially given that we expect capture patterns to be very common.

有人建议根本不需要捕获模式，因为可以通过将AS模式和通配符模式结合起来获得同等效果（例如，`case _ as x`等同于case x）。然而，这将是令人不快的冗长，特别是考虑到我们期望捕获模式是非常普遍的。

**Example** using Capture patterns:

使用捕获模式的**例子**：

```python
def average(*args):
    match args:
        case [x, y]:           # captures the two elements of a sequence
            return (x + y) / 2
        case [x]:              # captures the only element of a sequence
            return x
        case []:
            return 0
        case a:                # captures the entire sequence
            return sum(a) / len(a)
```

### [Wildcard Pattern](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id23)

通配符模式

The wildcard pattern is a special case of a 'capture' pattern: it accepts any value, but does not bind it to a variable. The idea behind this rule is to support repeated use of the wildcard in patterns. While `(x, x)` is an error, `(_, _)` is legal.

通配符模式是 "捕获 "模式的一个特例：它接受任何值，但不将其绑定到一个变量。这个规则背后的想法是支持在模式中重复使用通配符。虽然`(x, x)`是一个错误，但`(_, _)`是合法的。

Particularly in larger (sequence) patterns, it is important to allow the pattern to concentrate on values with actual significance while ignoring anything else. Without a wildcard, it would become necessary to 'invent' a number of local variables, which would be bound but never used. Even when sticking to naming conventions and using e.g. `_1, _2, _3` to name irrelevant values, say, this still introduces visual clutter and can hurt performance (compare the sequence pattern `(x, y, *z)` to `(_, y, *_)`, where the `*z` forces the interpreter to copy a potentially very long sequence, whereas the second version simply compiles to code along the lines of `y = seq[1]`).

特别是在较大的（序列）模式中，重要的是允许模式集中在具有实际意义的值上，而忽略其他的东西。如果没有通配符，就有必要 "发明 "一些局部变量，这些变量将被绑定，但从未被使用。即使坚持命名惯例，使用例如`_1, _2, _3`来命名不相关的值，例如，这仍然会引入视觉上的混乱，并且会损害性能（比较序列模式`(x, y, *z)`和`(_, y, *_)`，其中`*z`迫使解释器复制一个潜在的非常长的序列，而第二个版本只是编译成`y=seq[1]`这样的代码）。

There has been much discussion about the choice of the underscore as `_` as a wildcard pattern, i.e. making this one name non-binding. However, the underscore is already heavily used as an 'ignore value' marker in iterable unpacking. Since the wildcard pattern `_` never binds, this use of the underscore does not interfere with other uses such as inside the REPL or the `gettext` module.

关于选择下划线作为`_`的通配符模式，即让这一个名字没有约束力，已经有了很多讨论。然而，下划线在迭代解包中已经被大量地用作 "忽略值 "标记。由于通配符模式`_`从不绑定，下划线的使用不会干扰其他用途，例如在 REPL 或 `gettext` 模块内。

It has been proposed to use `...` (i.e., the ellipsis token) or `*` (star) as a wildcard. However, both these look as if an arbitrary number of items is omitted:

有人建议使用`...` (即省略号标记）或`*`（星号）作为通配符。然而，这两种方法看起来都像是省略了任意数量的项目。

```python
case [a, ..., z]: ...
case [a, *, z]: ...
```

Either example looks like it would match a sequence of two or more items, capturing the first and last values. While that may be the ultimate "wildcard", it does not convey the desired semantics.

这两个例子看起来都可以匹配两个或更多项目的序列，捕捉到第一个和最后一个值。虽然这可能是最终的 "通配符"，但它并没有传达所需的语义。

An alternative that does not suggest an arbitrary number of items would be `?`. This is even being proposed independently from pattern matching in [PEP 640](https://www.python.org/dev/peps/pep-0640). We feel however that using `?` as a special "assignment" target is likely more confusing to Python users than using `_`. It violates Python's (admittedly vague) principle of using punctuation characters only in ways similar to how they are used in common English usage or in high school math, unless the usage is *very* well established in other programming languages (like, e.g., using a dot for member access).

另一个不表示任意数量的项目的替代方法是`?`。这甚至是在[PEP 640](https://www.python.org/dev/peps/pep-0640)中独立于模式匹配提出的。然而我们觉得使用`?`作为一个特殊的 "赋值 "目标可能比使用`_`更让Python用户感到困惑。它违反了Python的原则(承认是模糊的)，即标点符号的使用方式只能类似于它们在普通英语中或高中数学中的使用方式，除非这种用法在其它编程语言中是*非常*成熟的(例如，使用点表示成员访问)。

The question mark fails on both counts: its use in other programming languages is a grab-bag of usages only vaguely suggested by the idea of a "question". For example, it means "any character" in shell globbing, "maybe" in regular expressions, "conditional expression" in C and many C-derived languages, "predicate function" in Scheme, "modify error handling" in Rust, "optional argument" and "optional chaining" in TypeScript (the latter meaning has also been proposed for Python by [PEP 505](https://www.python.org/dev/peps/pep-0505)). An as yet unnamed PEP proposes it to mark optional types, e.g. `int?`.

问号在这两点上都失败了：它在其他编程语言中的使用是一种抓取式的使用，只是由 "问题 "的概念模糊地暗示出来。例如，它在shell globbing中表示 "任何字符"，在正则表达式中表示 "也许"，在C和许多C衍生语言中表示 "条件表达式"，在Scheme中表示 "谓词函数"，在Rust中表示 "修改错误处理"，在TypeScript中表示 "可选的参数 "和 "可选的链"（后者的含义也被[PEP 505](https://www.python.org/dev/peps/pep-0505)为Python提出）。一个尚未命名的PEP提议用它来标记可选的类型，例如`int?`。

Another common use of `?` in programming systems is "help", for example, in IPython and Jupyter Notebooks and many interactive command-line utilities.

在编程系统中，`?`的另一个常见用法是 "帮助"，例如，在IPython和Jupyter Notebooks以及许多交互式命令行工具。

In addition, this would put Python in a rather unique position: The underscore is as a wildcard pattern in *every* programming language with pattern matching that we could find (including *C#*, *Elixir*, *Erlang*, *F#*, *Grace*, *Haskell*, *Mathematica*, *OCaml*, *Ruby*, *Rust*, *Scala*, *Swift*, and *Thorn*). Keeping in mind that many users of Python also work with other programming languages, have prior experience when learning Python, and may move on to other languages after having learned Python, we find that such well-established standards are important and relevant with respect to readability and learnability. In our view, concerns that this wildcard means that a regular name received special treatment are not strong enough to introduce syntax that would make Python special.

此外，这将使Python处于一个相当独特的位置。下划线在我们能找到的*所有*具有模式匹配功能的编程语言中都是通配符模式（包括*C#*、*Elixir*、*Erlang*、*F#*、*Grace*、*Haskell*、*Mathematica*、*OCaml*、*Ruby*、*Rust*、*Scala*、*Swift*、和*Thorn*）。考虑到Python的许多用户也在使用其他编程语言，在学习Python时有过经验，并且在学习了Python之后可能会转向其他语言，我们发现这种成熟的标准在可读性和可学习性方面是重要的和相关的。在我们看来，对这个通配符意味着一个普通的名字得到了特殊的对待的担心，还不足以引入会使Python变得特殊的语法。

*Else blocks.* A case block without a guard whose pattern is a single wildcard (i.e., `case _:`) accepts any subject without binding it to a variable or performing any other operation. It is thus semantically equivalent to `else:`, if it were supported. However, adding such an else block to the match statement syntax would not remove the need for the wildcard pattern in other contexts. Another argument against this is that there would be two plausible indentation levels for an else block: aligned with `case` or aligned with `match`. The authors have found it quite contentious which indentation level to prefer.

*Else 块。*一个没有守卫的case块，其模式是一个通配符(即`case _:`)，它接受任何主题，而不把它绑定到一个变量或执行任何其他操作。因此它在语义上等同于`else:`，如果它被支持的话。然而，在匹配语句语法中加入这样的else块，并不能消除在其他情况下对通配符模式的需求。另一个反对的理由是，else块有两个合理的缩进级别：与`case`对齐或与`match`对齐。作者发现哪种缩进级别更合适是有争议的。

**Example** using the Wildcard pattern:

使用通配符模式的**例子**：

```python
def is_closed(sequence):
    match sequence:
        case [_]:               # any sequence with a single element
            return True
        case [start, *_, end]:  # a sequence with at least two elements
            return start == end
        case _:                 # anything
            return False
```

### [Value Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id24)

值模式

It is good programming style to use named constants for parametric values or to clarify the meaning of particular values. Clearly, it would be preferable to write `case (HttpStatus.OK, body):` over `case (200, body):`, for example. The main issue that arises here is how to distinguish capture patterns (variable bindings) from value patterns. The general discussion surrounding this issue has brought forward a plethora of options, which we cannot all fully list here.

对于参数值或澄清特定值的含义，使用命名的常量是一种良好的编程风格。显然，写`case (HttpStatus.OK, body):`比写`case (200, body):`更好，例如。这里出现的主要问题是如何区分捕获模式（变量绑定）和值模式。围绕这个问题的一般性讨论提出了大量的选择，我们无法在此全部列出。

Strictly speaking, value patterns are not really necessary, but could be implemented using guards, i.e. `case (status, body) if status == HttpStatus.OK:`. Nonetheless, the convenience of value patterns is unquestioned and obvious.

严格来说，值模式并不是真正必要的，但可以使用守卫来实现，即`case (status, body) if status == HttpStatus.OK:`。尽管如此，值模式的便利性是毋庸置疑和显而易见的。

The observation that constants tend to be written in uppercase letters or collected in enumeration-like namespaces suggests possible rules to discern constants syntactically. However, the idea of using upper- vs. lowercase as a marker has been met with scepticism since there is no similar precedence in core Python (although it is common in other languages). We therefore only adopted the rule that any dotted name (i.e., attribute access) is to be interpreted as a value pattern, for example `HttpStatus.OK` above. This precludes, in particular, local variables and global variables defined in the current module from acting as constants.

观察到常量倾向于用大写字母书写或收集在类似枚举的命名空间中，这表明可能的规则可以从语法上辨别常量。然而，使用大写与小写作为标记的想法遭到了怀疑，因为在核心Python中没有类似的先例（尽管它在其他语言中很常见）。因此我们只采用了这样的规则：任何带点的名字（即属性访问）都要被解释为一个值模式，例如上面的`HttpStatus.OK`。这特别排除了在当前模块中定义的局部变量和全局变量作为常量的作用。

A proposed rule to use a leading dot (e.g. `.CONSTANT`) for that purpose was criticised because it was felt that the dot would not be a visible-enough marker for that purpose. Partly inspired by forms found in other programming languages, a number of different markers/sigils were proposed (such as `^CONSTANT`, `$CONSTANT`, `==CONSTANT`, `CONSTANT?`, or the word enclosed in backticks), although there was no obvious or natural choice. The current proposal therefore leaves the discussion and possible introduction of such a 'constant' marker for a future PEP.

一个提议的使用前导点（例如：`.CONSTANT`）的规则被批评了，因为人们认为这个点对这个目的来说不是一个足够明显的标记。部分受其他编程语言的启发，提出了一些不同的标记/符号（如`^CONSTANT`、`$CONSTANT`、`==CONSTANT`、`CONSTANT?`，或用反斜线括起来的词），尽管没有明显或自然的选择。因此，目前的建议将讨论和可能引入这样一个 "常量 "标记的问题留给未来的PEP。

Distinguishing the semantics of names based on whether it is a global variable (i.e. the compiler would treat global variables as constants rather than capture patterns) leads to various issues. The addition or alteration of a global variable in the module could have unintended side effects on patterns. Moreover, pattern matching could not be used directly inside a module's scope because all variables would be global, making capture patterns impossible.

根据它是否是一个全局变量来区分名称的语义（即编译器将把全局变量作为常量而不是捕获模式）会导致各种问题。在模块中增加或改变一个全局变量可能会对模式产生意想不到的副作用。此外，模式匹配不能直接在模块的范围内使用，因为所有的变量都是全局变量，使得捕获模式不可能。

> 从结论上看，捕获模式和值模式都不会使用全局变量，译者注。

**Example** using the Value pattern:

使用价值模式的**例子**：

```python
def handle_reply(reply):
    match reply:
        case (HttpStatus.OK, MimeType.TEXT, body):
            process_text(body)
        case (HttpStatus.OK, MimeType.APPL_ZIP, body):
            text = deflate(body)
            process_text(text)
        case (HttpStatus.MOVED_PERMANENTLY, new_URI):
            resend_request(new_URI)
        case (HttpStatus.NOT_FOUND):
            raise ResourceNotFound()
```

### [Group Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id25)

群组模式

Allowing users to explicitly specify the grouping is particularly helpful in case of OR patterns.

允许用户明确指定分组在OR模式的情况下特别有帮助。

### [Sequence Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id26)

序列模式

Sequence patterns follow as closely as possible the already established syntax and semantics of iterable unpacking. Of course, subpatterns take the place of assignment targets (variables, attributes and subscript). Moreover, the sequence pattern only matches a carefully selected set of possible subjects, whereas iterable unpacking can be applied to any iterable.

序列模式尽可能地遵循已经建立的可迭代解包的语法和语义。当然，子模式取代了赋值目标（变量、属性和下标）的位置。此外，序列模式只匹配一组精心挑选的可能的主题，而可迭代解包可以应用于任何可迭代的对象。

- As in iterable unpacking, we do not distinguish between 'tuple' and 'list' notation. `[a, b, c]`, `(a, b, c)` and `a, b, c` are all equivalent. While this means we have a redundant notation and checking specifically for lists or tuples requires more effort (e.g. `case list([a, b, c])`), we mimic iterable unpacking as much as possible.

  正如在可迭代的解包中，我们不区分 "元组 "和 "列表 "符号。[a, b, c], (a, b, c)和a, b, c都是等同的。虽然这意味着我们有一个多余的符号，而且专门检查列表或元组需要更多的努力（例如`case list([a, b, c])`），但我们尽可能地模仿可迭代解包。

- A starred pattern will capture a sub-sequence of arbitrary length, again mirroring iterable unpacking. Only one starred item may be present in any sequence pattern. In theory, patterns such as `(*_, 3, *_)` could be understood as expressing any sequence containing the value `3`. In practice, however, this would only work for a very narrow set of use cases and lead to inefficient backtracking or even ambiguities otherwise.

  一个带星号的模式将捕获一个任意长度的子序列，这也是对可迭代解包的模仿。在任何序列模式中只能有一个星号项目。在理论上，像`(*_, 3, *_)`这样的模式可以被理解为表达任何含有3这个值的序列。然而，在实践中，这只适用于非常狭窄的用例，并且会导致低效的回溯，甚至是模糊不清的情况。

- The sequence pattern does *not* iterate through an iterable subject. All elements are accessed through subscripting and slicing, and the subject must be an instance of `collections.abc.Sequence`. This includes, of course, lists and tuples, but excludes e.g. sets and dictionaries. While it would include strings and bytes, we make an exception for these (see below).

  序列模式并不通过一个可迭代的主题进行迭代。所有的元素都是通过下标和切分来访问的，并且主体必须是`collections.abc.Sequence`的实例。这当然包括列表和元组，但不包括例如集合和字典。虽然它包括了字符串和字节，但我们为这些做了一个例外（见下文）。

A sequence pattern cannot just iterate through any iterable object. The consumption of elements from the iteration would have to be undone if the overall pattern fails, which is not feasible.

一个序列模式不能仅仅通过任何可迭代对象进行迭代。如果整个模式失败了，就必须撤销迭代所消耗的元素，这是不可行的。

> 这里解释了为什么匹配模式不能在主题中使用可迭代对象，可迭代对象本质上是可以在迭代元素时“临时生成的”，这一点在生成器上体现的很明显，所以每次遍历可能会有相当的开销，而且在某种程度上还不能缓存一次遍历后的结果然后用于多次匹配，而这些问题在只能用下标来遍历的序列中是不存在的，译者注。

To identify sequences we cannot rely on `len()` and subscripting and slicing alone, because sequences share these protocols with mappings (e.g. `dict`) in this regard. It would be surprising if a sequence pattern also matched a dictionaries or other objects implementing the mapping protocol (i.e. `__getitem__`). The interpreter therefore performs an instance check to ensure that the subject in question really is a sequence (of known type). (As an optimization of the most common case, if the subject is exactly a list or a tuple, the instance check can be skipped.)

为了识别序列，我们不能仅仅依靠`len()`和下标以及切片，因为序列在这方面与映射（例如`dict`）共享这些协议。如果一个序列模式也与字典或其他实现映射协议的对象（即`__getitem__`）相匹配，这将是令人惊讶的。因此，解释器执行了一个实例检查，以确保有关的主题确实是一个序列（已知类型）。(作为对最常见情况的优化，如果主题正好是一个列表或元组，那么实例检查可以被跳过)。

String and bytes objects have a dual nature: they are both 'atomic' objects in their own right, as well as sequences (with a strongly recursive nature in that a string is a sequence of strings). The typical behavior and use cases for strings and bytes are different enough from those of tuples and lists to warrant a clear distinction. It is in fact often unintuitive and unintended that strings pass for sequences, as evidenced by regular questions and complaints. Strings and bytes are therefore not matched by a sequence pattern, limiting the sequence pattern to a very specific understanding of 'sequence'. The built-in `bytearray` type, being a mutable version of `bytes`, also deserves an exception; but we don't intend to enumerate all other types that may be used to represent bytes (e.g. some, but not all, instances of `memoryview` and `array.array`).

字符串和字节对象具有双重性质：它们本身既是 "原子 "对象，也是序列（具有强递归性质，因为字符串是字符串的序列）。字符串和字节的典型行为和用例与元组和列表的典型行为和用例有很大的不同，因此有必要进行明确的区分。事实上，字符串被当作序列往往是不直观的和非故意的，这一点从经常性的问题和投诉中可以看出。因此，字符串和字节不能被序列模式匹配，将序列模式限制在对 "序列 "的一个非常具体的理解上。内置的`bytearray`类型，作为字节的可变版本，也值得例外；但我们并不打算列举所有可能用来表示字节的其他类型（例如，一些，但不是所有，`memoryview`和`array.array`的实例）。

### [Mapping Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id27)

映射模式

Dictionaries or mappings in general are one of the most important and most widely used data structures in Python. In contrast to sequences, mappings are built for fast direct access to arbitrary elements identified by a key. In most cases an element is retrieved from a dictionary by a known key without regard for any ordering or other key-value pairs stored in the same dictionary. Particularly common are string keys.

字典或一般的映射是Python中最重要和最广泛使用的数据结构之一。与序列相反，映射是为快速直接访问由一个键确定的任意元素而建立的。在大多数情况下，一个元素是通过一个已知的键从字典中检索出来的，而不考虑任何排序或存储在同一字典中的其他键值对。特别常见的是字符串键。

The mapping pattern reflects the common usage of dictionary lookup: it allows the user to extract some values from a mapping by means of constant/known keys and have the values match given subpatterns. Extra keys in the subject are ignored even if `**rest` is not present. This is different from sequence patterns, where extra items will cause a match to fail. But mappings are actually different from sequences: they have natural structural sub-typing behavior, i.e., passing a dictionary with extra keys somewhere will likely just work. Should it be necessary to impose an upper bound on the mapping and ensure that no additional keys are present, then the usual double-star-pattern `**rest` can be used. The special case `**_` with a wildcard, however, is not supported as it would not have any effect, but might lead to an incorrect understanding of the mapping pattern's semantics.

映射模式反映了字典查找的常见用法：它允许用户通过常量/已知键从映射中提取一些值，并让这些值与给定的子模式相匹配。即使`**rest`不存在，主题中的额外键也会被忽略。这与序列模式不同，序列模式中的额外项会导致匹配失败。但是映射实际上与序列不同：它们有自然的结构性子模式行为，也就是说，传递一个在某处有额外键的字典，很可能只是工作。如果有必要对映射施加一个上限，并确保没有额外的键存在，那么可以使用通常的双星h号模式`**rest`。然而，不支持带有通配符的特殊情况`**_`，因为它不会有任何效果，但可能导致对映射模式语义的不正确理解。

> 很多人诟病模式匹配中映射模式和序列模式的行为不一致的问题，这里的解释并不能让我信服，但社区做出了这样的决定，我们只能无奈接受了，译者注。

To avoid overly expensive matching algorithms, keys must be literals or value patterns.

为了避免过于昂贵的匹配算法，键必须是字面或值模式。

There is a subtle reason for using `get(key, default)` instead of `__getitem__(key)` followed by a check for `AttributeError`: if the subject happens to be a `defaultdict`, calling `__getitem__` for a non-existent key would add the key. Using `get()` avoids this unexpected side effect.

使用 `get(key, default)` 而不是 `__getitem__(key)` 后面的 `AttributeError` 检查有一个微妙的原因：如果主题恰好是一个 `defaultdict`，为一个不存在的键调用 `__getitem__` 会增加这个键。使用 `get()` 可以避免这种意外的副作用。

> Python的确经常会出现协议相关的一些微妙区别，怎么说呢，协议带来很强的灵活性，但也造成了一些难以预料的问题，译者注。

**Example** using the Mapping pattern:

使用映射模式的**例子**：

```python
def change_red_to_blue(json_obj):
    match json_obj:
        case { 'color': ('red' | '#FF0000') }:
            json_obj['color'] = 'blue'
        case { 'children': children }:
            for child in children:
                change_red_to_blue(child)
```

### [Class Patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id28)

类模式

Class patterns fulfill two purposes: checking whether a given subject is indeed an instance of a specific class, and extracting data from specific attributes of the subject. Anecdotal evidence revealed that `isinstance()` is one of the most often used functions in Python in terms of static occurrences in programs. Such instance checks typically precede a subsequent access to information stored in the object, or a possible manipulation thereof. A typical pattern might be along the lines of:

类模式实现了两个目的：检查一个给定的主题是否确实是一个特定类的实例，以及从主题的特定属性中提取数据。传闻显示，就程序中的静态出现（统计）而言，`isinstance()`是Python中最常使用的函数之一。这种实例检查通常是在随后访问存储在对象中的信息或可能对其进行操作之前。一个典型的模式可能是这样的：

```python
def traverse_tree(node):
    if isinstance(node, Node):
        traverse_tree(node.left)
        traverse_tree(node.right)
    elif isinstance(node, Leaf):
        print(node.value)
```

In many cases class patterns occur nested, as in the example given in the motivation:

在许多情况下，类模式是嵌套出现的，如动机中给出的例子：

```python
if (isinstance(node, BinOp) and node.op == "+"
        and isinstance(node.right, BinOp) and node.right.op == "*"):
    a, b, c = node.left, node.right.left, node.right.right
    # Handle a + b*c
```

The class pattern lets you concisely specify both an instance check and relevant attributes (with possible further constraints). It is thereby very tempting to write, e.g., `case Node(left, right):` in the first case above and `case Leaf(value):` in the second. While this indeed works well for languages with strict algebraic data types, it is problematic with the structure of Python objects.

类模式让你简明地指定一个实例检查和相关的属性（可能有进一步的约束）。因此，非常诱人的是，例如，在上面的第一种情况下写`case Node(left, right):`在第二种情况下写`case Leaf(value):`。虽然这对于具有严格代数数据类型的语言来说确实很好用，但是对于Python对象的结构来说，这是有问题的。

When dealing with general Python objects, we face a potentially very large number of unordered attributes: an instance of `Node` contains a large number of attributes (most of which are 'special methods' such as `__repr__`). Moreover, the interpreter cannot reliably deduce the ordering of attributes. For an object that represents a circle, say, there is no inherently obvious ordering of the attributes `x`, `y` and `radius`.

当处理一般的 Python 对象时，我们面临着潜在的非常多的无序属性：`Node` 的一个实例包含了大量的属性 (其中大部分是 "特殊方法"，如 `__repr__`)。此外，解释器不能可靠地推断出属性的顺序。比如说，对于一个表示圆的对象来说，属性`x`、`y`和`radius`没有内在明显的排序。

We envision two possibilities for dealing with this issue: either explicitly name the attributes of interest, or provide an additional mapping that tells the interpreter which attributes to extract and in which order. Both approaches are supported. Moreover, explicitly naming the attributes of interest lets you further specify the required structure of an object; if an object lacks an attribute specified by the pattern, the match fails.

我们设想了两种处理这个问题的可能性：要么明确地命名感兴趣的属性，要么提供一个额外的映射，告诉解释器要提取哪些属性，以何种顺序提取。这两种方法都支持。此外，明确地命名感兴趣的属性可以让你进一步指定对象的所需结构；如果一个对象缺乏模式所指定的属性，则匹配失败。

- Attributes that are explicitly named pick up the syntax of named arguments. If an object of class `Node` has two attributes `left` and `right` as above, the pattern `Node(left=x, right=y)` will extract the values of both attributes and assign them to `x` and `y`, respectively. The data flow from left to right seems unusual, but is in line with mapping patterns and has precedents such as assignments via `as` in *with*- or *import*-statements (and indeed AS patterns).

  明确命名的属性会获得命名参数的语法。如果一个`Node`类的对象有上面的两个属性`left`和`right`，模式`Node(left=x, right=y)`将提取这两个属性的值，并将它们分别赋给`x`和`y`。从左到右的数据流似乎很不寻常，但它符合映射模式，并且有先例，比如通过 *with-* 或 *import-statements*（实际上还有AS模式）中的赋值。

  Naming the attributes in question explicitly will be mostly used for more complex cases where the positional form (below) is insufficient.

  明确地命名有关的属性将主要用于更复杂的情况，在这种情况下，位置形式（如下）是不够的。

- The class field `__match_args__` specifies a number of attributes together with their ordering, allowing class patterns to rely on positional sub-patterns without having to explicitly name the attributes in question. This is particularly handy for smaller objects or instances of data classes, where the attributes of interest are rather obvious and often have a well-defined ordering. In a way, `__match_args__` is similar to the declaration of formal parameters, which allows calling functions with positional arguments rather than naming all the parameters.

  类字段`__match_args__`指定了一些属性和它们的顺序，允许类模式依赖位置性子模式而不必明确地命名有关的属性。这对于较小的对象或数据类的实例来说特别方便，在这些对象中，感兴趣的属性是相当明显的，而且往往有一个明确的顺序。在某种程度上，`__match_args__`类似于形式参数的声明，它允许用位置参数调用函数，而不是命名所有参数。

  This is a class attribute, because it needs to be looked up on the class named in the class pattern, not on the subject instance.

  这是一个类属性，因为它需要在类模式中命名的类上查找，而不是在主题实例上查找。

The syntax of class patterns is based on the idea that de-construction mirrors the syntax of construction. This is already the case in virtually any Python construct, be assignment targets, function definitions or iterable unpacking. In all these cases, we find that the syntax for sending and that for receiving 'data' are virtually identical.

类模式的语法是基于这样的想法：去结构化反映了结构化的语法。这在几乎所有的 Python 构造中都是如此，无论是赋值目标、函数定义还是可迭代的解包。在所有这些情况下，我们发现发送和接收 "数据 "的语法几乎是相同的。

- Assignment targets such as variables, attributes and subscripts: `foo.bar[2] = foo.bar[3]`;

  赋值目标，如变量、属性和子标号：`foo.bar[2] = foo.bar[3]`。

- Function definitions: a function defined with `def foo(x, y, z=6)` is called as, e.g., `foo(123, y=45)`, where the actual arguments provided at the call site are matched against the formal parameters at the definition site;

  函数定义：用`def foo(x, y, z=6)`定义的函数被调用为，例如，`foo(123, y=45)`，其中在调用处提供的实际参数与定义处的形式参数相匹配。

- Iterable unpacking: `a, b = b, a` or `[a, b] = [b, a]` or `(a, b) = (b, a)`, just to name a few equivalent possibilities.

  Iterable解包：`a, b = b, a`或`[a, b] = [b, a]`或`(a, b) = (b, a)`，仅举几个等价的可能性。

Using the same syntax for reading and writing, l- and r-values, or construction and de-construction is widely accepted for its benefits in thinking about data, its flow and manipulation. This equally extends to the explicit construction of instances, where class patterns `C(p, q)` deliberately mirror the syntax of creating instances.

使用相同的语法进行读和写、l-和r-值，或者构造和去构造，由于其在思考数据、数据流和操作方面的好处而被广泛接受。这同样延伸到了实例的显式构造，其中类模式`C(p, q)`故意反映了创建实例的语法。

The special case for the built-in classes `bool`, `bytearray` etc. (where e.g. `str(x)` captures the subject value in `x`) can be emulated by a user-defined class as follows:

内置类`bool`、`bytearray`等的特殊情况（例如`str(x)`将主题值捕获到`x`中）可以由用户定义的类来模拟，如下所示。

```python
class MyClass:
    __match_args__ = ["__myself__"]
    __myself__ = property(lambda self: self)
```

**Type annotations for pattern variables.** The proposal was to combine patterns with type annotations:

**模式变量的类型注解。**建议将模式与类型注解相结合。

```python
match x:
    case [a: int, b: str]: print(f"An int {a} and a string {b}:)
    case [a: int, b: int, c: int]: print("Three ints", a, b, c)
    ...
```

This idea has a lot of problems. For one, the colon can only be used inside of brackets or parentheses, otherwise the syntax becomes ambiguous. And because Python disallows `isinstance()` checks on generic types, type annotations containing generics will not work as expected.

这个想法有很多问题。首先，冒号只能在括号或小括号内使用，否则语法会变得模糊不清。因为Python不允许对泛型进行`isinstance()`检查，所以包含泛型的类型注解将不会像预期的那样工作。

> 其实我还是挺喜欢注解这种风格的，只能表示遗憾了，译者注。

# [History and Context](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id29)

历史和背景

Pattern matching emerged in the late 1970s in the form of tuple unpacking and as a means to handle recursive data structures such as linked lists or trees (object-oriented languages usually use the visitor pattern for handling recursive data structures). The early proponents of pattern matching organised structured data in 'tagged tuples' rather than `struct` as in *C* or the objects introduced later. A node in a binary tree would, for instance, be a tuple with two elements for the left and right branches, respectively, and a `Node` tag, written as `Node(left, right)`. In Python we would probably put the tag inside the tuple as `('Node', left, right)` or define a data class `Node` to achieve the same effect.

模式匹配在20世纪70年代末以元组解包的形式出现，并作为处理递归数据结构的一种手段，如链接列表或树（面向对象语言通常使用访问者模式来处理递归数据结构）。模式匹配的早期支持者将结构化数据组织在 "标记元组 "中，而不是像C语言那样的结构或后来引入的对象。例如，二叉树中的一个节点是一个元组，有两个元素分别代表左和右分支，还有一个 `Node` 标签，写成 `Node(left, right)`。在 Python 中，我们可能会把标签放在元组里面，写成` ('Node', left, right)` 或者定义一个数据类 `Node` 来达到同样的效果。

> 下面的内容对帮助理解Python中的模式匹配意义不大，所以我就不翻译了，译者注。

Using modern syntax, a depth-first tree traversal would then be written as follows:

```
def traverse(node):
    match node:
        case Node(left, right):
            traverse(left)
            traverse(right)
        case Leaf(value):
            handle(value)
```

The notion of handling recursive data structures with pattern matching immediately gave rise to the idea of handling more general recursive 'patterns' (i.e. recursion beyond recursive data structures) with pattern matching. Pattern matching would thus also be used to define recursive functions such as:

```
def fib(arg):
    match arg:
        case 0:
            return 1
        case 1:
            return 1
        case n:
            return fib(n-1) + fib(n-2)
```

As pattern matching was repeatedly integrated into new and emerging programming languages, its syntax slightly evolved and expanded. The two first cases in the `fib` example above could be written more succinctly as `case 0 | 1:` with `|` denoting alternative patterns. Moreover, the underscore `_` was widely adopted as a wildcard, a filler where neither the structure nor value of parts of a pattern were of substance. Since the underscore is already frequently used in equivalent capacity in Python's iterable unpacking (e.g., `_, _, third, _* = something`) we kept these universal standards.

It is noteworthy that the concept of pattern matching has always been closely linked to the concept of functions. The different case clauses have always been considered as something like semi-independent functions where pattern variables take on the role of parameters. This becomes most apparent when pattern matching is written as an overloaded function, along the lines of (Standard ML):

```
fun fib 0 = 1
  | fib 1 = 1
  | fib n = fib (n-1) + fib (n-2)
```

Even though such a strict separation of case clauses into independent functions does not apply in Python, we find that patterns share many syntactic rules with parameters, such as binding arguments to unqualified names only or that variable/parameter names must not be repeated for a particular pattern/function.

With its emphasis on abstraction and encapsulation, object-oriented programming posed a serious challenge to pattern matching. In short: in object-oriented programming, we can no longer view objects as tagged tuples. The arguments passed into the constructor do not necessarily specify the attributes or fields of the objects. Moreover, there is no longer a strict ordering of an object's fields and some of the fields might be private and thus inaccessible. And on top of this, the given object might actually be an instance of a subclass with slightly different structure.

To address this challenge, patterns became increasingly independent of the original tuple constructors. In a pattern like `Node(left, right)`, `Node` is no longer a passive tag, but rather a function that can actively check for any given object whether it has the right structure and extract a `left` and `right` field. In other words: the `Node`-tag becomes a function that transforms an object into a tuple or returns some failure indicator if it is not possible.

In Python, we simply use `isinstance()` together with the `__match_args__` field of a class to check whether an object has the correct structure and then transform some of its attributes into a tuple. For the `Node` example above, for instance, we would have `__match_args__ = ('left', 'right')` to indicate that these two attributes should be extracted to form the tuple. That is, `case Node(x, y)` would first check whether a given object is an instance of `Node` and then assign `left` to `x` and `right` to `y`, respectively.

Paying tribute to Python's dynamic nature with 'duck typing', however, we also added a more direct way to specify the presence of, or constraints on specific attributes. Instead of `Node(x, y)` you could also write `object(left=x, right=y)`, effectively eliminating the `isinstance()` check and thus supporting any object with `left` and `right` attributes. Or you would combine these ideas to write `Node(right=y)` so as to require an instance of `Node` but only extract the value of the `right` attribute.

# [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id30)

向后兼容

Through its use of "soft keywords" and the new PEG parser ([PEP 617](https://www.python.org/dev/peps/pep-0617)), the proposal remains fully backwards compatible. However, 3rd party tooling that uses a LL(1) parser to parse Python source code may be forced to switch parser technology to be able to support those same features.

通过使用 "软关键字 "和新的 PEG 解析器 (PEP 617)，该提案仍然完全向后兼容。然而，使用LL(1)解析器来解析Python源代码的第三方工具可能会被迫转换解析器技术，以便能够支持这些相同的特性。

# [Security Implications](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id31)

安全方面的影响

We do not expect any security implications from this language feature.

我们不期望这个语言特性会产生任何安全影响。

# [Reference Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id32)

参考实现

A [feature-complete CPython implementation](https://github.com/brandtbucher/cpython/tree/patma) is available on GitHub.

GitHub上有一个功能完整的CPython实现。

An [interactive playground](https://mybinder.org/v2/gh/gvanrossum/patma/master?urlpath=lab/tree/playground-622.ipynb) based on the above implementation was created using Binder [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id7) and Jupyter [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id8).

使用Binder[2]和Jupyter[3]创建了一个基于上述实现的交互式游乐场。

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id33)

参考文献

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[1\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id1) | Kohn et al., Dynamic Pattern Matching with Python https://gvanrossum.github.io/docs/PyPatternMatching.pdf |
|                                                              |                                                              |
| [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id4) | Binder [https://mybinder.org](https://mybinder.org/)         |
|                                                              |                                                              |
| [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id5) | Jupyter [https://jupyter.org](https://jupyter.org/)          |
|                                                              |                                                              |

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20635%20--%20Structural%20Pattern%20Matching%20Motivation%20and%20Rationale.md#id34)

版权声明

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

Source: https://github.com/python/peps/blob/master/pep-0635.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。