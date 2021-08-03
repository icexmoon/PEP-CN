# PEP 572 -- Assignment Expressions

PEP 572 -- 赋值表达式

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 572                                                          |
| Title:          | Assignment Expressions                                       |
| Author:         | Chris Angelico <rosuav at gmail.com>, Tim Peters <tim.peters at gmail.com>, Guido van Rossum <guido at python.org> |
| Status:         | Accepted                                                     |
| Type:           | Standards Track                                              |
| Created:        | 28-Feb-2018                                                  |
| Python-Version: | 3.8                                                          |
| Post-History:   | 28-Feb-2018, 02-Mar-2018, 23-Mar-2018, 04-Apr-2018, 17-Apr-2018, 25-Apr-2018, 09-Jul-2018, 05-Aug-2019 |
| Resolution:     | https://mail.python.org/pipermail/python-dev/2018-July/154601.html |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#abstract)，概述
- Rationale，目的
  - [The importance of real code](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#the-importance-of-real-code)，真实代码的重要性
- Syntax and semantics，语法和语义
  - [Exceptional cases](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#exceptional-cases)，例外的情况
  - [Scope of the target](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#scope-of-the-target)，目标的范围
  - [Relative precedence of `:=`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#relative-precedence-of)，`:=`的相对优先性
  - [Change to evaluation order](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#change-to-evaluation-order)，改变评价顺序
  - [Differences between assignment expressions and assignment statements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#differences-between-assignment-expressions-and-assignment-statements)，赋值表达式和赋值语句之间的区别
- [Specification changes during implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#specification-changes-during-implementation)，实施过程中的范围变化
- Examples，实例
  - Examples from the Python standard library，Python标准库中的例子
    - [site.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#site-py)
    - [_pydecimal.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#pydecimal-py)
    - [copy.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#copy-py)
    - [datetime.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#datetime-py)
    - [sysconfig.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#sysconfig-py)
  - [Simplifying list comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#simplifying-list-comprehensions)，简化列表表达式
  - [Capturing condition values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#capturing-condition-values)，捕获条件值
  - [Fork](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#fork)
- Rejected alternative proposals，被拒绝的替代性建议
  - [Changing the scope rules for comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#changing-the-scope-rules-for-comprehensions)，改推导式的作用域规则
  - [Alternative spellings](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#alternative-spellings)，替代拼写
  - [Special-casing conditional statements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#special-casing-conditional-statements)，特殊大小写的条件语句
  - [Special-casing comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#special-casing-comprehensions)，特殊编码的推导式
  - [Lowering operator precedence](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#lowering-operator-precedence)，降低运算符的优先级
  - [Allowing commas to the right](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#allowing-commas-to-the-right)，允许逗号在右边
  - [Always requiring parentheses](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#always-requiring-parentheses)，总是要求使用小括号
- Frequently Raised Objections，常见的反对意见
  - [Why not just turn existing assignment into an expression?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#why-not-just-turn-existing-assignment-into-an-expression)，为什么不把现有的赋值变成一个表达式？
  - [With assignment expressions, why bother with assignment statements?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#with-assignment-expressions-why-bother-with-assignment-statements)，有了赋值表达式，还用得着赋值语句吗？
  - [Why not use a sublocal scope and prevent namespace pollution?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#why-not-use-a-sublocal-scope-and-prevent-namespace-pollution)，为什么不使用子局域范围并防止命名空间污染？
- [Style guide recommendations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#style-guide-recommendations)，风格指南建议
- [Acknowledgements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#acknowledgements)，鸣谢
- Appendix A: Tim Peters's findings，附录A：Tim Peters的发现
  - [A numeric example](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#a-numeric-example)，一个数字的例子
- [Appendix B: Rough code translations for comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#appendix-b-rough-code-translations-for-comprehensions)，附录B：推导式的粗略代码翻译
- [Appendix C: No Changes to Scope Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#appendix-c-no-changes-to-scope-semantics)，附录 C：对范围语义没有改变
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#references)，参考文献
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id6)

概述

This is a proposal for creating a way to assign to variables within an expression using the notation `NAME := expr`.

这是一个关于创建一种使用 `NAME := expr` 符号在表达式中赋值给变量的方法的建议。

As part of this change, there is also an update to dictionary comprehension evaluation order to ensure key expressions are executed before value expressions (allowing the key to be bound to a name and then re-used as part of calculating the corresponding value).

作为这一变化的一部分，还对字典理解的评估顺序进行了更新，以确保键表达式在值表达式之前被执行（允许将键绑定到名称上，然后作为计算相应值的一部分重新使用）。

During discussion of this PEP, the operator became informally known as "the walrus operator". The construct's formal name is "Assignment Expressions" (as per the PEP title), but they may also be referred to as "Named Expressions" (e.g. the CPython reference implementation uses that name internally).

在本PEP的讨论中，该操作符被非正式地称为 "海象操作符"。该结构的正式名称是 "赋值表达式"（正如本PEP的标题），但它们也可以被称为 "命名表达式"（例如，CPython参考实现内部使用该名称）。

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id7)

目的

Naming the result of an expression is an important part of programming, allowing a descriptive name to be used in place of a longer expression, and permitting reuse. Currently, this feature is available only in statement form, making it unavailable in list comprehensions and other expression contexts.

命名表达式的结果是编程的一个重要部分，它允许用一个描述性的名字来代替一个较长的表达式，并允许重复使用。目前，这个功能只在语句形式中可用，因此在列表推导式和其他表达式环境中无法使用。

Additionally, naming sub-parts of a large expression can assist an interactive debugger, providing useful display hooks and partial results. Without a way to capture sub-expressions inline, this would require refactoring of the original code; with assignment expressions, this merely requires the insertion of a few `name :=` markers. Removing the need to refactor reduces the likelihood that the code be inadvertently changed as part of debugging (a common cause of Heisenbugs), and is easier to dictate to another programmer.

此外，命名一个大型表达式的子部分可以帮助交互式调试器，提供有用的显示钩子和部分结果。如果没有捕获内联子表达式的方法，这就需要对原始代码进行重构；对于赋值表达式，这只需要插入一些`name :=` 标记。消除重构的需要，可以减少在调试过程中无意中改变代码的可能性（这是导致海森堡错误的常见原因），而且更容易向另一个程序员说明。

> 这里的意思是在debug代码的时候很可能需要因为探查一些中间变量的需要而对现有代码的重构，进而因为一些错误的修改而导致新的bug，译者注。

## [The importance of real code](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id8)

真实代码的重要性

During the development of this PEP many people (supporters and critics both) have had a tendency to focus on toy examples on the one hand, and on overly complex examples on the other.

在这个PEP的开发过程中，许多人（支持者和批评者）都有一种倾向，即一方面关注玩具般的例子，另一方面又关注过于复杂的例子。

The danger of toy examples is twofold: they are often too abstract to make anyone go "ooh, that's compelling", and they are easily refuted with "I would never write it that way anyway".

玩具般的例子的危险有两个方面：它们往往太抽象，不会让人觉得 "哦，这很有说服力"，而且它们很容易被 "反正我也不会这么写 "所反驳。

The danger of overly complex examples is that they provide a convenient strawman for critics of the proposal to shoot down ("that's obfuscated").

过于复杂的例子的危险在于，它们为建议的批评者提供了一个方便的稻草人（"那是混淆视听的"）。

Yet there is some use for both extremely simple and extremely complex examples: they are helpful to clarify the intended semantics. Therefore, there will be some of each below.

然而，极其简单和极其复杂的例子都有一些用处：它们有助于澄清预期的语义。因此，下面会有一些各自的例子。

However, in order to be *compelling*, examples should be rooted in real code, i.e. code that was written without any thought of this PEP, as part of a useful application, however large or small. Tim Peters has been extremely helpful by going over his own personal code repository and picking examples of code he had written that (in his view) would have been *clearer* if rewritten with (sparing) use of assignment expressions. His conclusion: the current proposal would have allowed a modest but clear improvement in quite a few bits of code.

然而，为了让人信服，例子应该植根于真实的代码中，即在没有考虑到本PEP的情况下编写的代码，作为有用的应用的一部分，无论大小。Tim Peters非常有帮助，他查看了自己的个人代码库，并挑选了一些他写的代码的例子，（在他看来）如果重写时（少用）赋值表达式，会更加清晰。他的结论是：目前的建议将允许对相当多的代码进行适度但明显的改进。

Another use of real code is to observe indirectly how much value programmers place on compactness. Guido van Rossum searched through a Dropbox code base and discovered some evidence that programmers value writing fewer lines over shorter lines.

真实代码的另一个用途是间接观察程序员对紧凑性的重视程度。Guido van Rossum搜索了Dropbox的代码库，发现了一些证据，表明程序员重视写更少的行数而不是更短的行数。

Case in point: Guido found several examples where a programmer repeated a subexpression, slowing down the program, in order to save one line of code, e.g. instead of writing:

这是一个很好的例子。Guido发现了几个例子，一个程序员为了节省一行代码而重复一个子表达式，拖慢了程序的速度，例如，而不是这样写：

```python
match = re.match(data)
group = match.group(1) if match else None
```

they would write:

他们会这样写：

```python
group = re.match(data).group(1) if re.match(data) else None
```

> 这件事情一方面说明效率要为偷懒让路，另一方面说明算法导论这门课程的重要性，译者注。

Another example illustrates that programmers sometimes do more work to save an extra level of indentation:

另一个例子说明，程序员有时会做更多的工作来节省一个额外的缩进：

```python
match1 = pattern1.match(data)
match2 = pattern2.match(data)
if match1:
    result = match1.group(1)
elif match2:
    result = match2.group(2)
else:
    result = None
```

This code tries to match `pattern2` even if `pattern1` has a match (in which case the match on `pattern2` is never used). The more efficient rewrite would have been:

这段代码试图匹配pattern2，即使pattern1已经匹配了（在这种情况下，pattern2的匹配从未被使用）。更有效的重写方法是：

```python
match1 = pattern1.match(data)
if match1:
    result = match1.group(1)
else:
    match2 = pattern2.match(data)
    if match2:
        result = match2.group(2)
    else:
        result = None
```

> 这种代码我有时候也会写，有时候整洁的代码有一定意义，译者注。

# [Syntax and semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id9)

语法和语义

In most contexts where arbitrary Python expressions can be used, a **named expression** can appear. This is of the form `NAME := expr` where `expr` is any valid Python expression other than an unparenthesized tuple, and `NAME` is an identifier.

在大多数可以使用任意 Python 表达式的情况下，可以出现一个命名表达式。它的形式是`NAME := expr`，其中`expr`是任何有效的Python表达式，而不是一个无父无母的元组，`NAME`是一个标识符。

The value of such a named expression is the same as the incorporated expression, with the additional side-effect that the target is assigned that value:

这样的命名表达式的值与被合并的表达式相同，还有一个副作用，就是目标被分配了这个值。

> 这里的意思是在完成原有表达式用途的基础上实现了赋值功能，译者注。

```python
# Handle a matched regex
# 用于一个匹配的正则表达式
if (match := pattern.search(data)) is not None:
    # Do something with match

# A loop that can't be trivially rewritten using 2-arg iter()
# 一个不能用2-arg iter()琐碎地重写的循环
while chunk := file.read(8192):
   process(chunk)

# Reuse a value that's expensive to compute
# 重用一个计算成本很高的值
[y := f(x), y**2, y**3]

# Share a subexpression between a comprehension filter clause and its output
# 在理解性过滤子句和其输出之间共享一个子表达式
filtered_data = [y for x in data if (y := f(x)) is not None]
```

## [Exceptional cases](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id10)

例外的情况

There are a few places where assignment expressions are not allowed, in order to avoid ambiguities or user confusion:

有几个地方不允许使用赋值表达式，以避免产生歧义或用户的混淆。

- Unparenthesized assignment expressions are prohibited at the top level of an expression statement. Example:

  在表达式语句的顶层，禁止使用非亲和的赋值表达式。例如。

  ```python
  y := f(x)  # INVALID 非法
  (y := f(x))  # Valid, though not recommended 合法，但不推荐
  ```

  This rule is included to simplify the choice for the user between an assignment statement and an assignment expression -- there is no syntactic position where both are valid.

  包括这一规则是为了简化用户在赋值语句和赋值表达式之间的选择--没有一个语法位置是两者都有效的。

- Unparenthesized assignment expressions are prohibited at the top level of the right hand side of an assignment statement. Example:

  在赋值语句右侧的顶层，禁止使用非亲和的赋值表达式。例如。

  ```python
  y0 = y1 := f(x)  # INVALID 非法
  y0 = (y1 := f(x))  # Valid, though discouraged 合法，但不鼓励使用
  ```

  Again, this rule is included to avoid two visually similar ways of saying the same thing.

  同样，列入这一规则是为了避免用两种视觉上相似的方式来表达同一事物。

  > 我很奇怪为什么Python不支持`a=(b=1)`这样的写法，如果支持这种写法不就不需要这个所谓的海象运算符了么，译者注。

- Unparenthesized assignment expressions are prohibited for the value of a keyword argument in a call. Example:

  对于调用中的关键字参数的值，禁止使用非亲和的赋值表达式。例子：

  ```python
  foo(x = y := f(x))  # INVALID 非法
  foo(x=(y := f(x)))  # Valid, though probably confusing 合法，但可能让人迷惑
  ```

  This rule is included to disallow excessively confusing code, and because parsing keyword arguments is complex enough already.

  包括这条规则是为了不允许过分混乱的代码，而且因为解析关键字参数已经够复杂了。

- Unparenthesized assignment expressions are prohibited at the top level of a function default value. Example:

  在一个函数缺省值的顶层禁止使用非亲和的赋值表达式。例如：

  ```python
  def foo(answer = p := 42):  # INVALID 非法
      ...
  def foo(answer=(p := 42)):  # Valid, though not great style 合法，但不是一个好的风格
      ...
  ```

  This rule is included to discourage side effects in a position whose exact semantics are already confusing to many users (cf. the common style recommendation against mutable default values), and also to echo the similar prohibition in calls (the previous bullet).

  包括这条规则是为了阻止在一个位置上的副作用，这个位置的确切语义已经让许多用户感到困惑（参考通用风格建议，反对可变的默认值），同时也是为了呼应调用中的类似禁止（上一弹）。

- Unparenthesized assignment expressions are prohibited as annotations for arguments, return values and assignments. Example:

  禁止将非亲和的赋值表达式作为参数、返回值和赋值的注解。例如。

  ```python
  def foo(answer: p := 42 = 5):  # INVALID 非法
      ...
  def foo(answer: (p := 42) = 5):  # Valid, but probably never useful 合法，但没有意义
      ...
  ```

  The reasoning here is similar to the two previous cases; this ungrouped assortment of symbols and operators composed of `:` and `=` is hard to read correctly.

  这里的推理与前面两种情况类似；这种由`:`和`=`组成的未分组的符号和运算符很难正确阅读。

- Unparenthesized assignment expressions are prohibited in lambda functions. Example:

  在匿名函数中，禁止使用非亲和的赋值表达式。例子：

  ```python
  (lambda: x := 1) # INVALID 非法
  lambda: (x := 1) # Valid, but unlikely to be useful 合法，但好像没用
  (x := lambda: 1) # Valid 合法
  lambda line: (m := re.match(pattern, line)) and m.group(1) # Valid 合法
  ```

  This allows `lambda` to always bind less tightly than `:=`; having a name binding at the top level inside a lambda function is unlikely to be of value, as there is no way to make use of it. In cases where the name will be used more than once, the expression is likely to need parenthesizing anyway, so this prohibition will rarely affect code.

  这允许`lambda`总是比`:=`更紧密地绑定；在lambda函数内部的顶层有一个名字绑定不太可能有价值，因为没有办法利用它。在名字会被多次使用的情况下，表达式很可能需要加括号，所以这一禁令很少会影响代码。

  > 这里的意思是像`(x := lambda: 1)`这样使用赋值表达式的意义不大，如果你真的需要这么写，远不如定义一个明确的函数有效，译者注。

- Assignment expressions inside of f-strings require parentheses. Example:

  格式化字符串内的赋值表达式需要有括号。例子：

  ```python
  >>> f'{(x:=10)}'  # Valid, uses assignment expression
  '10'
  >>> x = 10
  >>> f'{x:=10}'    # Valid, passes '=10' to formatter
  '        10'
  ```

  This shows that what looks like an assignment operator in an f-string is not always an assignment operator. The f-string parser uses `:` to indicate formatting options. To preserve backwards compatibility, assignment operator usage inside of f-strings must be parenthesized. As noted above, this usage of the assignment operator is not recommended.

  这表明在格式化字符串中看起来像一个赋值运算符的东西并不总是一个赋值运算符。f-string解析器使用`:`来表示格式化选项。为了保持向后的兼容性，在格式化字符串中使用赋值运算符时必须使用括号。如上所述，我们不推荐使用赋值运算符的这种用法。

  > 这里的讨论充分说明了格式化字符串中使用赋值表达式的危害，最好不要这么使用，译者注。

## [Scope of the target](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id11)

目标的范围

An assignment expression does not introduce a new scope. In most cases the scope in which the target will be bound is self-explanatory: it is the current scope. If this scope contains a `nonlocal` or `global` declaration for the target, the assignment expression honors that. A lambda (being an explicit, if anonymous, function definition) counts as a scope for this purpose.

一个赋值表达式并不引入新的作用域。在大多数情况下，目标将被绑定的作用域是不言自明的：它是当前作用域。如果这个作用域包含目标的 `nonlocal` 或 `global` 声明，赋值表达式就会尊重它。lambda（是一个显式的，即使是匿名的函数定义）为此目的也算作一个作用域。

There is one special case: an assignment expression occurring in a list, set or dict comprehension or in a generator expression (below collectively referred to as "comprehensions") binds the target in the containing scope, honoring a `nonlocal` or `global` declaration for the target in that scope, if one exists. For the purpose of this rule the containing scope of a nested comprehension is the scope that contains the outermost comprehension. A lambda counts as a containing scope.

有一种特殊情况：出现在列表、集合或字典推导式中的赋值表达式或出现在生成器表达式中的赋值表达式（下面统称为 "推导式"）将目标绑定在包含的作用域中，如果该作用域中存在对目标的 `nonlocal` 或 `global` 声明，则遵从其声明。在本规则中，嵌套的推导式的包含范围是包含最外层推导式的范围。一个 lambda 也算作一个包含作用域。

The motivation for this special case is twofold. First, it allows us to conveniently capture a "witness" for an `any()` expression, or a counterexample for `all()`, for example:

这种特殊情况的动机是双重的。首先，它允许我们方便地捕捉 `any()` 表达式的 "证人"，或者例如 `all()` 的反例：

```python
if any((comment := line).startswith('#') for line in lines):
    print("First comment:", comment)
else:
    print("There are no comments")

if all((nonblank := line).strip() == '' for line in lines):
    print("All lines are blank")
else:
    print("First non-blank line:", nonblank)
```

Second, it allows a compact way of updating mutable state from a comprehension, for example:

第二，它允许以一种紧凑的方式从一个推导式中更新可变的状态，例如：

```python
# Compute partial sums in a list comprehension
# 在一个列表推导式中计算部分之和
total = 0
partial_sums = [total := total + v for v in values]
print("Total:", total)
```

> 之所以这里可以使用`[total := total + v for v in values]`但不能使用`[total = total + v for v in values]`，是因为后者作为普通的赋值语句其作用域仅限于列表推导式内部，所以是不能访问外部的`total`变量的，而前者使用的赋值表达式可以让`total`的作用域等于列表推导式所在的作用域，自然是可以访问外部的`total`变量的，译者注。

However, an assignment expression target name cannot be the same as a `for`-target name appearing in any comprehension containing the assignment expression. The latter names are local to the comprehension in which they appear, so it would be contradictory for a contained use of the same name to refer to the scope containing the outermost comprehension instead.

然而，赋值表达式的目标名称不能与出现在任何包含赋值表达式的推导式中的`for`-target名称相同。后者的名称是它们出现的推导式的本地名称，所以如果包含的相同名称的使用是指包含最外层推导式的作用域，这将是矛盾的。

For example, `[i := i+1 for i in range(5)]` is invalid: the `for i` part establishes that `i` is local to the comprehension, but the `i :=` part insists that `i` is not local to the comprehension. The same reason makes these examples invalid too:

例如，`[i := i+1 for i in range(5)]`是无效的：`for i`部分确定了`i`是推导式的局部，但`i :=`部分坚持认为`i`不是推导式的局部。同样的原因使得这些例子也是无效的。

```python
[[(j := j) for i in range(5)] for j in range(5)] # INVALID
[i := 0 for i, j in stuff]                       # INVALID
[i+1 for i in (i := stuff)]                      # INVALID
```

> 这里的意思是因为赋值表达式在推导式中的作用域等于推导式本身所在的作用域，是比推导式内的作用域大的，所以如果赋值表达式所赋值的变量名与推导式中的`for/in`所涉及的变量名相同就会产生冲突，即相同名称的变量具有两种不同的作用域，所以这种情况是不允许的，译者注。

While it's technically possible to assign consistent semantics to these cases, it's difficult to determine whether those semantics actually make *sense* in the absence of real use cases. Accordingly, the reference implementation will ensure that such cases raise `SyntaxError`, rather than executing with implementation defined behaviour.

虽然在技术上有可能给这些情况分配一致的语义，但在没有实际使用案例的情况下，很难确定这些语义是否真的有*意义*。因此，参考实现将确保这些案例引发语法错误，而不是以实现定义的行为执行。

This restriction applies even if the assignment expression is never executed:

即使赋值表达式从未被执行，这一限制也适用：

```python
[False and (i := 0) for i, j in stuff]     # INVALID
[i for i, j in stuff if True or (j := 1)]  # INVALID
```

For the comprehension body (the part before the first "for" keyword) and the filter expression (the part after "if" and before any nested "for"), this restriction applies solely to target names that are also used as iteration variables in the comprehension. Lambda expressions appearing in these positions introduce a new explicit function scope, and hence may use assignment expressions with no additional restrictions.

对于推导式本体（第一个 "for "关键字之前的部分）和过滤表达式（"if "之后和任何嵌套的 "for "之前的部分），这个限制只适用于在推导式中也被用作迭代变量的目标名称。出现在这些位置的Lambda表达式引入了一个新的显式函数范围，因此可以使用赋值表达式，没有额外的限制。

Due to design constraints in the reference implementation (the symbol table analyser cannot easily detect when names are re-used between the leftmost comprehension iterable expression and the rest of the comprehension), named expressions are disallowed entirely as part of comprehension iterable expressions (the part after each "in", and before any subsequent "if" or "for" keyword):

由于参考实现中的设计限制（符号表分析器不能轻易检测到名字在最左边的推导式可迭代表达式和推导式其他部分之间的重复使用），命名表达式完全不允许作为推导式可迭代表达式的一部分（每个 "in "后面的部分，以及任何后续 "if "或 "for "关键字之前）。

```python
[i+1 for i in (j := stuff)]                    # INVALID
[i+1 for i in range(2) for j in (k := stuff)]  # INVALID
[i+1 for i in [j for j in (k := stuff)]]       # INVALID
[i+1 for i in (lambda: (j := stuff))()]        # INVALID
```

A further exception applies when an assignment expression occurs in a comprehension whose containing scope is a class scope. If the rules above were to result in the target being assigned in that class's scope, the assignment expression is expressly invalid. This case also raises `SyntaxError`:

还有一个例外，当赋值表达式出现在包含作用域是类作用域的推导式时。如果上面的规则导致目标被赋值在那个类的作用域中，那么这个赋值表达式就明确无效了。这种情况也会引发 `SyntaxError`：

```python
class Example:
    [(j := i) for i in range(5)]  # INVALID
```

(The reason for the latter exception is the implicit function scope created for comprehensions -- there is currently no runtime mechanism for a function to refer to a variable in the containing class scope, and we do not want to add such a mechanism. If this issue ever gets resolved this special case may be removed from the specification of assignment expressions. Note that the problem already exists for *using* a variable defined in the class scope from a comprehension.)

(造成后一种例外的原因是为推导式而创建的隐式函数范围--目前没有任何运行时机制可以让函数引用包含类范围的变量，而且我们也不想增加这样的机制。如果这个问题得到解决，这种特殊情况可以从赋值表达式的规范中删除。请注意，对于使用从推导式中定义在类作用域中的变量来说，这个问题已经存在了）。)

> 关于这段论述我也不是很懂，似乎和Python的语法解析逻辑相关，作为普通的开发者只要知道这种例外情况不能使用赋值表达式即可，译者注。

See Appendix B for some examples of how the rules for targets in comprehensions translate to equivalent code.

关于理解中的目标规则如何转化为等价代码的一些例子，见附录B。

## [Relative precedence of `:=`](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id12)

`:=`的相对优先性

The `:=` operator groups more tightly than a comma in all syntactic positions where it is legal, but less tightly than all other operators, including `or`, `and`, `not`, and conditional expressions (`A if C else B`). As follows from section "Exceptional cases" above, it is never allowed at the same level as `=`. In case a different grouping is desired, parentheses should be used.

`:=`运算符在所有它合法的语法位置上比逗号更紧密地分组，但比所有其他运算符更不紧密，包括`or`、`and`、`not`和条件表达式（`A if C else B`）。从上面的 "例外情况 "一节可以看出，它永远不允许与`=`处于同一层次。

The `:=` operator may be used directly in a positional function call argument; however it is invalid directly in a keyword argument.

`:=`操作符可以直接用于位置函数调用参数中；但是直接用于关键字参数是无效的。

Some examples to clarify what's technically valid or invalid:

一些例子可以说明什么是技术上有效或无效的。

```python
# INVALID
x := 0

# Valid alternative
(x := 0)

# INVALID
x = y := 0

# Valid alternative
x = (y := 0)

# Valid
len(lines := f.readlines())

# Valid
foo(x := 3, cat='vector')

# INVALID
foo(cat=category := 'vector')

# Valid alternative
foo(cat=(category := 'vector'))
```

> 从上边的例子看，如果你在某个不寻常的地方要使用赋值表达式，但提示语法错误，那加一对括号可能就会解决这个问题，译者注。

Most of the "valid" examples above are not recommended, since human readers of Python source code who are quickly glancing at some code may miss the distinction. But simple cases are not objectionable:

上面大多数 "有效 "的例子都是不推荐的，因为快速扫视一些代码的Python源代码的人类读者可能会错过这种区别。但简单的例子并不令人反感。

```python
# Valid
if any(len(longline := line) >= 100 for line in lines):
    print("Extremely long line:", longline)
```

This PEP recommends always putting spaces around `:=`, similar to [PEP 8](https://www.python.org/dev/peps/pep-0008)'s recommendation for `=` when used for assignment, whereas the latter disallows spaces around `=` used for keyword arguments.)

本PEP建议在`:=`周围加上空格，类似于[PEP 8](https://www.python.org/dev/peps/pep-0008)对用于赋值的`=`的建议，而后者不允许在用于关键字参数的`=`周围加上空格。)

## [Change to evaluation order](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id13)

改变评价顺序

In order to have precisely defined semantics, the proposal requires evaluation order to be well-defined. This is technically not a new requirement, as function calls may already have side effects. Python already has a rule that subexpressions are generally evaluated from left to right. However, assignment expressions make these side effects more visible, and we propose a single change to the current evaluation order:

为了有精确定义的语义，该提案要求评价顺序要有明确的定义。这在技术上不是一个新的要求，因为函数调用可能已经有副作用了。Python 已经有一个规则，即子表达式通常从左到右进行评估。然而，赋值表达式使这些副作用更加明显，我们建议对当前的评估顺序做一个单一的改变。

- In a dict comprehension `{X: Y for ...}`, `Y` is currently evaluated before `X`. We propose to change this so that `X` is evaluated before `Y`. (In a dict display like `{X: Y}` this is already the case, and also in `dict((X, Y) for ...)` which should clearly be equivalent to the dict comprehension.)

  在一个dict推导式`{X: Y for ...}`中，`Y`目前是在`X`之前被评估的。我们建议改变这一点，使`X`在`Y`之前被评估。(在像`{X: Y}`这样的dict显示中已经是这样了，在`dict((X, Y) for ...)`中也是如此，这显然应该等同于dict理解。)

## [Differences between assignment expressions and assignment statements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id14)

赋值表达式和赋值语句之间的区别

Most importantly, since `:=` is an expression, it can be used in contexts where statements are illegal, including lambda functions and comprehensions.

最重要的是，由于 `:=` 是一个表达式，它可以在语句不合法的情况下使用，包括 lambda 函数和推导式。

Conversely, assignment expressions don't support the advanced features found in assignment statements:

相反，赋值表达式并不支持赋值语句中的高级功能。

- Multiple targets are not directly supported:

  不直接支持多个目标：

  ```python
  x = y = z = 0  # Equivalent: (z := (y := (x := 0)))
  ```

- Single assignment targets other than a single `NAME` are not supported:

  不支持单一`NAME`以外的单一分配目标：

  ```python
  # No equivalent
  a[i] = x
  self.rest = []
  ```

- Priority around commas is different:

  逗号周围的优先级是不同的：

  ```python
  x = 1, 2  # Sets x to (1, 2)
  (x := 1, 2)  # Sets x to 1
  ```

- Iterable packing and unpacking (both regular or extended forms) are not supported:

  不支持可迭代的打包和拆包（包括常规或扩展形式）：

  ```python
  # Equivalent needs extra parentheses
  loc = x, y  # Use (loc := (x, y))
  info = name, phone, *rest  # Use (info := (name, phone, *rest))
  
  # No equivalent
  px, py, pz = position
  name, phone, email, *other_info = contact
  ```

- Inline type annotations are not supported:

  不支持内联类型注解：

  ```python
  # Closest equivalent is "p: Optional[int]" as a separate declaration
  # 最接近的等价物是 "p: Optional[int]"作为一个单独的声明
  p: Optional[int] = None
  ```

- Augmented assignment is not :

  不支持累加赋值：
  
  ```python
  total += tax  # Equivalent: (total := total + tax)
  ```

# [Specification changes during implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id15)

实施过程中的规范变化

The following changes have been made based on implementation experience and additional review after the PEP was first accepted and before Python 3.8 was released:

在PEP首次被接受后和Python 3.8发布前，根据实施经验和额外的审查，做了以下修改：

- for consistency with other similar exceptions, and to avoid locking in an exception name that is not necessarily going to improve clarity for end users, the originally proposed `TargetScopeError` subclass of `SyntaxError` was dropped in favour of just raising `SyntaxError` directly. [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id5)

  为了与其他类似的异常保持一致，并避免锁定一个不一定能提高最终用户清晰度的异常名称，最初提议的`SyntaxError`子类`TargetScopeError`被放弃，而直接引发`SyntaxError`。[[3]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id5)

- due to a limitation in CPython's symbol table analysis process, the reference implementation raises `SyntaxError` for all uses of named expressions inside comprehension iterable expressions, rather than only raising them when the named expression target conflicts with one of the iteration variables in the comprehension. This could be revisited given sufficiently compelling examples, but the extra complexity needed to implement the more selective restriction doesn't seem worthwhile for purely hypothetical use cases.

  由于CPython的符号表分析过程的限制，参考实现对所有在推导式的可迭代表达式中使用的命名表达式提出了`SyntaxError`，而不是只在命名表达式的目标与推导式中的一个迭代变量冲突时提出。这一点可以在有足够说服力的例子的情况下重新考虑，但对于纯粹的假设性使用情况来说，实现更有选择性的限制所需的额外复杂性似乎并不值得。

# [Examples](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id16)

实例

## [Examples from the Python standard library](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id17)

Python标准库中的例子

### [site.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id18)

*env_base* is only used on these lines, putting its assignment on the if moves it as the "header" of the block.

`env_base`只在这几行中使用，把它的赋值放在if上，使它成为这个块的 "头"。

- Current:

  ```python
  env_base = os.environ.get("PYTHONUSERBASE", None)
  if env_base:
      return env_base
  ```

- Improved:

  ```python
  if env_base := os.environ.get("PYTHONUSERBASE", None):
      return env_base
  ```

### [_pydecimal.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id19)

Avoid nested `if` and remove one indentation level.

避免嵌套的 `if` 并删除一个缩进层次。

- Current:

  ```python
  if self._is_special:
      ans = self._check_nans(context=context)
      if ans:
          return ans
  ```

- Improved:

  ```python
  if self._is_special and (ans := self._check_nans(context=context)):
      return ans
  ```

### [copy.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id20)

Code looks more regular and avoid multiple nested if. (See Appendix A for the origin of this example.)

代码看起来更有规律，避免了多个嵌套的if。(这个例子的来源见附录A)。

- Current:

  ```python
  reductor = dispatch_table.get(cls)
  if reductor:
      rv = reductor(x)
  else:
      reductor = getattr(x, "__reduce_ex__", None)
      if reductor:
          rv = reductor(4)
      else:
          reductor = getattr(x, "__reduce__", None)
          if reductor:
              rv = reductor()
          else:
              raise Error(
                  "un(deep)copyable object of type %s" % cls)
  ```

- Improved:

  ```python
  if reductor := dispatch_table.get(cls):
      rv = reductor(x)
  elif reductor := getattr(x, "__reduce_ex__", None):
      rv = reductor(4)
  elif reductor := getattr(x, "__reduce__", None):
      rv = reductor()
  else:
      raise Error("un(deep)copyable object of type %s" % cls)
  ```

### [datetime.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id21)

*tz* is only used for `s += tz`, moving its assignment inside the if helps to show its scope.

*tz* 只用于`s += tz`，把它的赋值移到if里面有助于显示其范围。

- Current:

  ```python
  s = _format_time(self._hour, self._minute,
                   self._second, self._microsecond,
                   timespec)
  tz = self._tzstr()
  if tz:
      s += tz
  return s
  ```

- Improved:

  ```python
  s = _format_time(self._hour, self._minute,
                   self._second, self._microsecond,
                   timespec)
  if tz := self._tzstr():
      s += tz
  return s
  ```

### [sysconfig.py](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id22)

Calling `fp.readline()` in the `while` condition and calling `.match()` on the if lines make the code more compact without making it harder to understand.

在`while`条件下调用`fp.readline()`，在if行中调用`.match()`，使代码更加紧凑，而不会使其更难理解。

- Current:

  ```python
  while True:
      line = fp.readline()
      if not line:
          break
      m = define_rx.match(line)
      if m:
          n, v = m.group(1, 2)
          try:
              v = int(v)
          except ValueError:
              pass
          vars[n] = v
      else:
          m = undef_rx.match(line)
          if m:
              vars[m.group(1)] = 0
  ```

- Improved:

  ```python
  while line := fp.readline():
      if m := define_rx.match(line):
          n, v = m.group(1, 2)
          try:
              v = int(v)
          except ValueError:
              pass
          vars[n] = v
      elif m := undef_rx.match(line):
          vars[m.group(1)] = 0
  ```

## [Simplifying list comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id23)

简化列表表达式

A list comprehension can map and filter efficiently by capturing the condition:

列表表达式可以通过捕捉条件来有效地映射和过滤。

```python
results = [(x, y, x/y) for x in input_data if (y := f(x)) > 0]
```

Similarly, a subexpression can be reused within the main expression, by giving it a name on first use:

类似地，一个子表达式可以在主表达式中重复使用，只要在第一次使用时给它一个名字。

```python
stuff = [[y := f(x), x/y] for x in range(5)]
```

Note that in both cases the variable `y` is bound in the containing scope (i.e. at the same level as `results` or `stuff`).

注意，在这两种情况下，变量y都被绑定在包含的作用域中（即与`results`或`stuff`处于同一层次）。

## [Capturing condition values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id24)

捕获条件值

Assignment expressions can be used to good effect in the header of an `if` or `while` statement:

赋值表达式可以在`if`或`while`语句的头部使用，效果很好：

```python
# Loop-and-a-half
while (command := input("> ")) != "quit":
    print("You entered:", command)

# Capturing regular expression match objects
# See, for instance, Lib/pydoc.py, which uses a multiline spelling
# of this effect
if match := re.search(pat, text):
    print("Found:", match.group(0))
# The same syntax chains nicely into 'elif' statements, unlike the
# equivalent using assignment statements.
elif match := re.search(otherpat, text):
    print("Alternate found:", match.group(0))
elif match := re.search(third, text):
    print("Fallback found:", match.group(0))

# Reading socket data until an empty string is returned
while data := sock.recv(8192):
    print("Received data:", data)
```

Particularly with the `while` loop, this can remove the need to have an infinite loop, an assignment, and a condition. It also creates a smooth parallel between a loop which simply uses a function call as its condition, and one which uses that as its condition but also uses the actual value.

特别是对于`while`循环，这可以消除对无限循环、赋值和条件的需要。它还在一个简单地使用函数调用作为条件的循环和一个使用函数调用作为条件但也使用实际值的循环之间建立了平稳的平行关系。

## [Fork](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id25)

An example from the low-level UNIX world:

一个来自底层的UNIX世界的例子：

```python
if pid := os.fork():
    # Parent code
else:
    # Child code
```

# [Rejected alternative proposals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id26)

被拒绝的替代性建议

Proposals broadly similar to this one have come up frequently on python-ideas. Below are a number of alternative syntaxes, some of them specific to comprehensions, which have been rejected in favour of the one given above.

在python-ideas上经常出现与此大致相似的建议。下面是一些替代性的语法，其中一些是专门针对编译器的，它们已经被拒绝了，只支持上面给出的那一种。

## [Changing the scope rules for comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id27)

改推导式的作用域规则

A previous version of this PEP proposed subtle changes to the scope rules for comprehensions, to make them more usable in class scope and to unify the scope of the "outermost iterable" and the rest of the comprehension. However, this part of the proposal would have caused backwards incompatibilities, and has been withdrawn so the PEP can focus on assignment expressions.

本PEP的前一个版本提议对推导式的作用域规则进行细微的修改，以使它们在类的作用域中更易于使用，并统一 "最外层迭代 "的作用域和推导式的其他部分。然而，这部分建议会导致向后的不兼容，因此已经被撤回，以便本PEP能够专注于赋值表达式。

## [Alternative spellings](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id28)

替代拼写

Broadly the same semantics as the current proposal, but spelled differently.

大体上与目前的提案语义相同，但拼法不同。

1. `EXPR as NAME`:

   ```python
   stuff = [[f(x) as y, x/y] for x in range(5)]
   ```

   Since `EXPR as NAME` already has meaning in `import`, `except` and `with` statements (with different semantics), this would create unnecessary confusion or require special-casing (e.g. to forbid assignment within the headers of these statements).

   由于 `EXPR as NAME` 已经在 `import`、`except` 和 `with` 语句中具有意义（具有不同的语义），这将造成不必要的混乱，或者需要特殊处理（例如禁止在这些语句的标题中赋值）。

   (Note that `with EXPR as VAR` does *not* simply assign the value of `EXPR` to `VAR` -- it calls `EXPR.__enter__()` and assigns the result of *that* to `VAR`.)

   (注意`with EXPR as VAR`并*不是*简单地把`EXPR`的值赋给`VAR` -- 它调用`EXPR.__enter__()`并把*调用*的结果赋给`VAR`。)

   Additional reasons to prefer `:=` over this spelling include:

   喜欢`:=`而不是这种拼写方式的其他原因包括。

   - In `if f(x) as y` the assignment target doesn't jump out at you -- it just reads like `if f x blah blah` and it is too similar visually to `if f(x) and y`.

     在`if f(x) as y`中，赋值目标并没有让你眼前一亮--它只是读作`if f x blah blah`，在视觉上与`if f(x) and y`过于相似。

   - In all other situations where an `as` clause is allowed, even readers with intermediary skills are led to anticipate that clause (however optional) by the keyword that starts the line, and the grammar ties that keyword closely to the as clause:

     在所有其他允许使用 `as`子句的情况下，即使是中等水平的读者也会通过开始的关键词来预测该子句（无论如何是可选的），而且语法将该关键词与作为子句紧密相连。

     - `import foo as bar`
     - `except Exc as var`
     - `with ctxmgr() as var`

     To the contrary, the assignment expression does not belong to the `if` or `while` that starts the line, and we intentionally allow assignment expressions in other contexts as well.

     相反，赋值表达式不属于开始行的 `if` 或 `while`，我们有意在其他情况下也允许赋值表达。

   - The parallel cadence between

     - `NAME = EXPR`
     - `if NAME := EXPR`

     reinforces the visual recognition of assignment expressions.
     
     加强了对赋值表达的视觉识别。

2. `EXPR -> NAME`:

   ```python
   stuff = [[f(x) -> y, x/y] for x in range(5)]
   ```

   This syntax is inspired by languages such as R and Haskell, and some programmable calculators. (Note that a left-facing arrow `y <- f(x)` is not possible in Python, as it would be interpreted as less-than and unary minus.) This syntax has a slight advantage over 'as' in that it does not conflict with `with`, `except` and `import`, but otherwise is equivalent. But it is entirely unrelated to Python's other use of `->` (function return type annotations), and compared to `:=` (which dates back to Algol-58) it has a much weaker tradition.

   这种语法的灵感来自R和Haskell等语言，以及一些可编程的计算器。(注意，在Python中不可能有一个朝左的箭头`y <- f(x)`，因为它将被解释为小于和单数减。) 这种语法比`as`稍有优势，因为它不与`with`、`except`和`import`冲突，但除此之外都是等同的。但它与Python对`->`的其他使用完全无关 (函数返回类型注释)，而且与`:=`相比 (可以追溯到Algol-58)，它的传统要弱得多。

3. Adorning statement-local names with a leading dot:

   用一个领先的点来装饰声明中的本地名称：

   ```python
   stuff = [[(f(x) as .y), x/.y] for x in range(5)] # with "as"
   stuff = [[(.y := f(x)), x/.y] for x in range(5)] # with ":="
   ```

   This has the advantage that leaked usage can be readily detected, removing some forms of syntactic ambiguity. However, this would be the only place in Python where a variable's scope is encoded into its name, making refactoring harder.

   这样做的好处是可以很容易地检测到泄漏的用法，消除某些形式的语法歧义。然而，这将是Python中唯一一个将变量的范围编码到其名称中的地方，这使得重构变得更加困难。

4. Adding a `where:` to any statement to create local name bindings:

   在任何语句中添加`where:`来创建本地名称绑定：

   ```python
   value = x**2 + 2*x where:
       x = spam(1, 4, 7, q)
   ```

   Execution order is inverted (the indented body is performed first, followed by the "header"). This requires a new keyword, unless an existing keyword is repurposed (most likely `with:`). See [PEP 3150](https://www.python.org/dev/peps/pep-3150) for prior discussion on this subject (with the proposed keyword being `given:`).

   执行顺序是颠倒的（首先执行缩进的主体，然后是 "头"）。这需要一个新的关键字，除非现有的关键字被重新使用（最可能是`with:`）。关于这个问题的先前讨论，请参见PEP 3150（建议的关键字`given:`）。

5. `TARGET from EXPR`:

   ```python
   stuff = [[y from f(x), x/y] for x in range(5)]
   ```

   This syntax has fewer conflicts than `as` does (conflicting only with the `raise Exc from Exc` notation), but is otherwise comparable to it. Instead of paralleling `with expr as target:` (which can be useful but can also be confusing), this has no parallels, but is evocative.

   这种语法比`as`的冲突更少（只与`Exc`符号中的`raise Exc`冲突），但在其他方面与它相当。而不是并列的`with expr as target:`。(这可能是有用的，但也可能是混乱的)，这没有相似之处，但却令人回味。

## [Special-casing conditional statements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id29)

特殊大小写的条件语句

One of the most popular use-cases is `if` and `while` statements. Instead of a more general solution, this proposal enhances the syntax of these two statements to add a means of capturing the compared value:

最流行的用例之一是`if`和`while`语句。这个建议不是一个更普遍的解决方案，而是增强了这两个语句的语法，增加了一个捕捉比较值的方法。

```python
if re.search(pat, text) as match:
    print("Found:", match.group(0))
```

This works beautifully if and ONLY if the desired condition is based on the truthiness of the captured value. It is thus effective for specific use-cases (regex matches, socket reads that return `''` when done), and completely useless in more complicated cases (e.g. where the condition is `f(x) < 0` and you want to capture the value of `f(x)`). It also has no benefit to list comprehensions.

如果且仅当所需的条件是基于捕获值的真实性时，这个方法效果很好。因此，它对特定的使用情况是有效的（重词匹配，完成后返回`''`的套接字读取），而在更复杂的情况下（例如，条件是`f(x) < 0`，而你想捕获`f(x)`的值）完全无用。它对列表推导式也没有好处。

Advantages: No syntactic ambiguities. Disadvantages: Answers only a fraction of possible use-cases, even in `if`/`while` statements.

优点：没有语法上的歧义。缺点：只能回答一小部分可能的用例，即使在`if`/`while`语句中也是如此。

## [Special-casing comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id30)

特殊编码的推导式

Another common use-case is comprehensions (list/set/dict, and genexps). As above, proposals have been made for comprehension-specific solutions.

另一个常见的使用情况是推导式（list/set/dict，以及genexps）。如上所述，已经有人提出了针对推导式的解决方案。

1. `where`, `let`, or `given`:

   ```python
   stuff = [(y, x/y) where y = f(x) for x in range(5)]
   stuff = [(y, x/y) let y = f(x) for x in range(5)]
   stuff = [(y, x/y) given y = f(x) for x in range(5)]
   ```

   This brings the subexpression to a location in between the 'for' loop and the expression. It introduces an additional language keyword, which creates conflicts. Of the three, `where` reads the most cleanly, but also has the greatest potential for conflict (e.g. SQLAlchemy and numpy have `where` methods, as does `tkinter.dnd.Icon` in the standard library).

   这就把子表达式带到了 "for "循环和表达式之间的一个位置。它引入了一个额外的语言关键字，这就产生了冲突。在这三种方法中，`where`读起来最干净，但也最有可能产生冲突（例如，SQLAlchemy和numpy都有`where`方法，标准库中的`tkinter.dnd.Icon`也有）。

2. `with NAME = EXPR`:

   ```python
   stuff = [(y, x/y) with y = f(x) for x in range(5)]
   ```

   As above, but reusing the `with` keyword. Doesn't read too badly, and needs no additional language keyword. Is restricted to comprehensions, though, and cannot as easily be transformed into "longhand" for-loop syntax. Has the C problem that an equals sign in an expression can now create a name binding, rather than performing a comparison. Would raise the question of why "with NAME = EXPR:" cannot be used as a statement on its own.

   如上所述，但重复使用`with`关键字。读起来不难，而且不需要额外的语言关键字。不过，它被限制在推导式中，而且不能轻易地转化为 "长手 "for-loop语法。有一个C语言的问题，即表达式中的等号现在可以创建一个名称绑定，而不是进行比较。这将引起一个问题：为什么 "with NAME = EXPR: "不能单独作为一个语句使用。

3. `with EXPR as NAME`:

   ```python
   stuff = [(y, x/y) with f(x) as y for x in range(5)]
   ```

   As per option 2, but using `as` rather than an equals sign. Aligns syntactically with other uses of `as` for name binding, but a simple transformation to for-loop longhand would create drastically different semantics; the meaning of `with` inside a comprehension would be completely different from the meaning as a stand-alone statement, while retaining identical syntax.
   
   按照选项2，但使用`as`而不是等号。在语法上与其他使用`as`进行名字绑定的方法一致，但简单地转换为for-loop的长句会产生截然不同的语义；`with`在理解中的含义将与作为独立语句的含义完全不同，同时保留相同的语法。

Regardless of the spelling chosen, this introduces a stark difference between comprehensions and the equivalent unrolled long-hand form of the loop. It is no longer possible to unwrap the loop into statement form without reworking any name bindings. The only keyword that can be repurposed to this task is `with`, thus giving it sneakily different semantics in a comprehension than in a statement; alternatively, a new keyword is needed, with all the costs therein.

无论选择哪种拼写方式，这都会在理解性和循环的等效解卷长手形式之间引入一个明显的区别。在不重做任何名称绑定的情况下，不再可能将循环解包成语句形式。唯一可以重新使用的关键字是`with`，因此在理解中的语义与在语句中的语义偷偷地不同；或者，需要一个新的关键字，以及其中的所有费用。

## [Lowering operator precedence](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id31)

降低运算符的优先级

There are two logical precedences for the `:=` operator. Either it should bind as loosely as possible, as does statement-assignment; or it should bind more tightly than comparison operators. Placing its precedence between the comparison and arithmetic operators (to be precise: just lower than bitwise OR) allows most uses inside `while` and `if` conditions to be spelled without parentheses, as it is most likely that you wish to capture the value of something, then perform a comparison on it:

`:=`运算符有两个逻辑优先级。要么它应该尽可能松散地绑定，就像声明-赋值一样；要么它应该比比较运算符更紧密地绑定。把它的优先级放在比较运算符和算术运算符之间（准确地说：仅低于位数OR），允许在`while`和`if`条件中的大多数使用不使用括号，因为你很可能希望捕获某物的值，然后对它进行比较。

```python
pos = -1
while pos := buffer.find(search_term, pos + 1) >= 0:
    ...
```

Once find() returns -1, the loop terminates. If `:=` binds as loosely as `=` does, this would capture the result of the comparison (generally either `True` or `False`), which is less useful.

一旦find()返回-1，循环就结束了。如果`:=`像`=`那样松散地绑定，这将捕获比较的结果（通常是`True`或`False`），这就不太有用了。

While this behaviour would be convenient in many situations, it is also harder to explain than "the := operator behaves just like the assignment statement", and as such, the precedence for `:=` has been made as close as possible to that of `=` (with the exception that it binds tighter than comma).

虽然这种行为在许多情况下很方便，但它也比":=操作符的行为与赋值语句一样 "更难解释，因此，:=的优先级已经尽可能地接近=（例外是它比逗号结合得更紧）。

> 这里很清楚的解释了，海象操作符`:=`的优先级与`=`相似，唯一的例外是其优先级高于`,`，译者注。

## [Allowing commas to the right](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id32)

允许逗号在右边

Some critics have claimed that the assignment expressions should allow unparenthesized tuples on the right, so that these two would be equivalent:

一些批评者声称，赋值表达式应该允许右边的非亲属大小的图元，这样一来，这两个就等同了。

```python
(point := (x, y))
(point := x, y)
```

(With the current version of the proposal, the latter would be equivalent to `((point := x), y)`.)

(在当前版本的建议中，后者将等同于`((point := x), y)`。)

However, adopting this stance would logically lead to the conclusion that when used in a function call, assignment expressions also bind less tight than comma, so we'd have the following confusing equivalence:

然而，采用这种立场会在逻辑上导致这样的结论：当在函数调用中使用时，赋值表达式也比逗号结合得不紧密，所以我们会有以下混乱的等价关系。

```python
foo(x := 1, y)
foo(x := (1, y))
```

The less confusing option is to make `:=` bind more tightly than comma.

不太混乱的选择是让`:=`比逗号更紧密地结合在一起。

## [Always requiring parentheses](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id33)

总是要求使用小括号

It's been proposed to just always require parenthesize around an assignment expression. This would resolve many ambiguities, and indeed parentheses will frequently be needed to extract the desired subexpression. But in the following cases the extra parentheses feel redundant:

有人提议在赋值表达式周围总是要求使用小括号。这将解决许多模糊不清的问题，而且确实经常需要小括号来提取所需的子表达式。但在以下情况下，额外的小括号就显得多余了：

```python
# Top level in if
if match := pattern.match(line):
    return match.group(1)

# Short call
len(lines := f.readlines())
```

# [Frequently Raised Objections](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id34)

经常提出的反对意见

## [Why not just turn existing assignment into an expression?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id35)

为什么不把现有的赋值变成一个表达式？

C and its derivatives define the `=` operator as an expression, rather than a statement as is Python's way. This allows assignments in more contexts, including contexts where comparisons are more common. The syntactic similarity between `if (x == y)` and `if (x = y)` belies their drastically different semantics. Thus this proposal uses `:=` to clarify the distinction.

C语言及其衍生语言将`=`运算符定义为一个表达式，而不是像Python那样定义为一个语句。这允许在更多的情况下进行赋值，包括比较更常见的情况。`if (x == y)`和`if (x = y)`在语法上的相似性掩盖了它们截然不同的语义。因此，本建议使用`:=`来澄清两者的区别。

> 这里解释了我的疑问，但是我并不赞同，只要稍微深入学习编程，都不会将一个逻辑表达式`x==y`与赋值表达式`x=y`混为一谈，译者注。

## [With assignment expressions, why bother with assignment statements?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id36)

有了赋值表达式，还用得着赋值语句吗？

The two forms have different flexibilities. The `:=` operator can be used inside a larger expression; the `=` statement can be augmented to `+=` and its friends, can be chained, and can assign to attributes and subscripts.

这两种形式有不同的灵活性。`:=`操作符可以在一个更大的表达式内使用；`=`语句可以增强到`+=`和它的朋友，可以连锁，并且可以赋值给属性和子标号。

## [Why not use a sublocal scope and prevent namespace pollution?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id37)

为什么不使用子局域范围并防止命名空间污染？

Previous revisions of this proposal involved sublocal scope (restricted to a single statement), preventing name leakage and namespace pollution. While a definite advantage in a number of situations, this increases complexity in many others, and the costs are not justified by the benefits. In the interests of language simplicity, the name bindings created here are exactly equivalent to any other name bindings, including that usage at class or module scope will create externally-visible names. This is no different from `for` loops or other constructs, and can be solved the same way: `del` the name once it is no longer needed, or prefix it with an underscore.

这个建议以前的修订涉及到子局部范围（限制在单个语句中），防止名称泄漏和名称空间污染。虽然在一些情况下有一定的优势，但在其他许多情况下，这增加了复杂性，而且成本与收益不相称。为了简化语言，这里创建的名字绑定与任何其他名字绑定完全等同，包括在类或模块范围内的使用将创建外部可见的名字。这与`for`循环或其他结构没有区别，可以用同样的方法解决：一旦不再需要这个名字，就`del`它，或者在它前面加上下划线。

(The author wishes to thank Guido van Rossum and Christoph Groth for their suggestions to move the proposal in this direction. [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id4))

(作者希望感谢Guido van Rossum和Christoph Groth的建议，使提案朝这个方向发展。[2])

> 这里解释了赋值表达式的作用域绑定方式与Python中的循环语句等其它结构相同，与C++或者Java中的局部作用域大为不同，译者注。

# [Style guide recommendations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id38)

风格指南建议

As expression assignments can sometimes be used equivalently to statement assignments, the question of which should be preferred will arise. For the benefit of style guides such as [PEP 8](https://www.python.org/dev/peps/pep-0008), two recommendations are suggested.

由于表达式赋值有时可以与语句赋值等同使用，因此会出现哪种赋值更合适的问题。为了使风格指南（如PEP 8）受益，我们提出了两个建议。

1. If either assignment statements or assignment expressions can be used, prefer statements; they are a clear declaration of intent.

   如果赋值语句或赋值表达式都可以使用，那么首选语句；它们是对意图的明确声明。

2. If using assignment expressions would lead to ambiguity about execution order, restructure it to use statements instead.

   如果使用赋值表达式会导致执行顺序的不明确，则应将其重组为使用语句。

# [Acknowledgements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id39)

鸣谢

The authors wish to thank Nick Coghlan and Steven D'Aprano for their considerable contributions to this proposal, and members of the core-mentorship mailing list for assistance with implementation.

作者要感谢Nick Coghlan和Steven D'Aprano对本建议的巨大贡献，以及核心导师邮件列表中的成员对实施的帮助。

# [Appendix A: Tim Peters's findings](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id40)

[附录A：Tim Peters的发现](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id40)

Here's a brief essay Tim Peters wrote on the topic.

这里有一篇Tim Peters写的关于这个话题的简短文章。

I dislike "busy" lines of code, and also dislike putting conceptually unrelated logic on a single line. So, for example, instead of:

我不喜欢 "繁忙 "的代码行，也不喜欢把概念上不相关的逻辑放在一行。因此，举例来说，与其说是。

```python
i = j = count = nerrors = 0
```

I prefer:

我更喜欢：

```python
i = j = 0
count = 0
nerrors = 0
```

instead. So I suspected I'd find few places I'd want to use assignment expressions. I didn't even consider them for lines already stretching halfway across the screen. In other cases, "unrelated" ruled:

而不是前边那样。所以我怀疑我很少会发现我想使用赋值表达式的地方。对于已经延伸到半个屏幕的行，我甚至没有考虑它们。在其他情况下，"不相关 "就成了主宰。

```python
mylast = mylast[1]
yield mylast[0]
```

is a vast improvement over the briefer:

是一个巨大的改进，比起简写：

```python
yield (mylast := mylast[1])[0]
```

The original two statements are doing entirely different conceptual things, and slamming them together is conceptually insane.

原来的两个语句做的是完全不同的概念性事情，把它们摔在一起在概念上是疯狂的。

In other cases, combining related logic made it harder to understand, such as rewriting:

在其他情况下，将相关的逻辑组合在一起使其更难理解，比如重写：

```python
while True:
    old = total
    total += term
    if old == total:
        return total
    term *= mx2 / (i*(i+1))
    i += 2
```

as the briefer:

作为简写：

```python
while total != (total := total + term):
    term *= mx2 / (i*(i+1))
    i += 2
return total
```

The `while` test there is too subtle, crucially relying on strict left-to-right evaluation in a non-short-circuiting or method-chaining context. My brain isn't wired that way.

那里的`while`测试太微妙了，关键是在非短时循环或方法连锁的情况下依赖于严格的从左到右的评估。我的大脑不是这样的。

But cases like that were rare. Name binding is very frequent, and "sparse is better than dense" does not mean "almost empty is better than sparse". For example, I have many functions that return `None` or `0` to communicate "I have nothing useful to return in this case, but since that's expected often I'm not going to annoy you with an exception". This is essentially the same as regular expression search functions returning `None` when there is no match. So there was lots of code of the form:

但是像这样的情况是很少的。名称绑定是非常频繁的，而且 "稀疏比密集好 "并不意味着 "几乎空的比稀疏好"。例如，我有许多函数返回`None`或`0`，以传达 "在这种情况下我没有什么有用的东西可以返回，但由于这经常被期待，我不会用一个异常来烦扰你"。这与正则表达式搜索函数在没有匹配时返回`None`的情况基本相同。所以有很多这样的代码。

```python
result = solution(xs, n)
if result:
    # use result
```

I find that clearer, and certainly a bit less typing and pattern-matching reading, as:

我觉得这样更清楚，当然也少了一点打字和模式匹配的阅读，因为：

```python
if result := solution(xs, n):
    # use result
```

It's also nice to trade away a small amount of horizontal whitespace to get another _line_ of surrounding code on screen. I didn't give much weight to this at first, but it was so very frequent it added up, and I soon enough became annoyed that I couldn't actually run the briefer code. That surprised me!

用少量的水平空白来换取屏幕上的另一行周围的代码也很不错。起初我并不重视这一点，但它是如此频繁地出现，而且我很快就感到恼火，因为我实际上无法运行更简短的代码。这让我很吃惊!

There are other cases where assignment expressions really shine. Rather than pick another from my code, Kirill Balunov gave a lovely example from the standard library's `copy()` function in `copy.py`:

在其他情况下，赋值表达式也会大放异彩。Kirill Balunov没有从我的代码中挑选另一个，而是从标准库的copy.py中的copy()函数中举了一个可爱的例子。

```python
reductor = dispatch_table.get(cls)
if reductor:
    rv = reductor(x)
else:
    reductor = getattr(x, "__reduce_ex__", None)
    if reductor:
        rv = reductor(4)
    else:
        reductor = getattr(x, "__reduce__", None)
        if reductor:
            rv = reductor()
        else:
            raise Error("un(shallow)copyable object of type %s" % cls)
```

The ever-increasing indentation is semantically misleading: the logic is conceptually flat, "the first test that succeeds wins":

不断增加的缩进在语义上有误导性：逻辑在概念上是扁平的，"第一个测试成功的就赢了"：

```python
if reductor := dispatch_table.get(cls):
    rv = reductor(x)
elif reductor := getattr(x, "__reduce_ex__", None):
    rv = reductor(4)
elif reductor := getattr(x, "__reduce__", None):
    rv = reductor()
else:
    raise Error("un(shallow)copyable object of type %s" % cls)
```

Using easy assignment expressions allows the visual structure of the code to emphasize the conceptual flatness of the logic; ever-increasing indentation obscured it.

使用简单的赋值表达式可以使代码的视觉结构强调逻辑的概念平坦性；不断增加的缩进使它变得模糊不清。

A smaller example from my code delighted me, both allowing to put inherently related logic in a single line, and allowing to remove an annoying "artificial" indentation level:

我的代码中的一个小例子让我很高兴，既允许把固有的相关逻辑放在一行中，又允许去除恼人的 "人工 "缩进程度。

```python
diff = x - x_base
if diff:
    g = gcd(diff, n)
    if g > 1:
        return g
```

became:

```python
if (diff := x - x_base) and (g := gcd(diff, n)) > 1:
    return g
```

That `if` is about as long as I want my lines to get, but remains easy to follow.

这个 `if` 是我想让我的线条变得尽可能长，但仍然容易理解。

So, in all, in most lines binding a name, I wouldn't use assignment expressions, but because that construct is so very frequent, that leaves many places I would. In most of the latter, I found a small win that adds up due to how often it occurs, and in the rest I found a moderate to major win. I'd certainly use it more often than ternary `if`, but significantly less often than augmented assignment.

所以，总的来说，在大多数绑定名字的行中，我不会使用赋值表达式，但由于这种结构非常频繁，这就使得很多地方我都会使用。在后者中，我发现由于它出现的频率很高，所以我发现了一个小的胜利，而在其余的地方，我发现了一个中度到高度的胜利。我当然会比三元`if`更经常地使用它，但明显比增强赋值的频率低。

## [A numeric example](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id41)

一个数字的例子

I have another example that quite impressed me at the time.

我还有一个例子，当时给我留下了相当深刻的印象。

Where all variables are positive integers, and a is at least as large as the n'th root of x, this algorithm returns the floor of the n'th root of x (and roughly doubling the number of accurate bits per iteration):

在所有变量都是正整数，且a至少与x的n'th根一样大的情况下，这个算法返回x的n'th根的下限（且每次迭代的精确位数大约翻倍）。

```python
while a > (d := x // a**(n-1)):
    a = ((n-1)*a + d) // n
return a
```

It's not obvious why that works, but is no more obvious in the "loop and a half" form. It's hard to prove correctness without building on the right insight (the "arithmetic mean - geometric mean inequality"), and knowing some non-trivial things about how nested floor functions behave. That is, the challenges are in the math, not really in the coding.

这并不明显，但在 "循环和半 "的形式中也不明显。如果没有正确的见解（"算术平均数-几何平均数不等式"），以及知道一些关于嵌套底层函数行为的非微不足道的事情，就很难证明正确性。也就是说，挑战是在数学上，而不是在编码上。

If you do know all that, then the assignment-expression form is easily read as "while the current guess is too large, get a smaller guess", where the "too large?" test and the new guess share an expensive sub-expression.

如果你知道所有这些，那么任务表达式很容易被理解为 "当当前猜测太大，得到一个较小的猜测"，其中 "太大？"测试和新的猜测共享一个昂贵的子表达式。

To my eyes, the original form is harder to understand:

在我看来，原来的形式更难理解：

```python
while True:
    d = x // a**(n-1)
    if a <= d:
        break
    a = ((n-1)*a + d) // n
return a
```

# [Appendix B: Rough code translations for comprehensions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id42)

附录B：推导式的粗略代码翻译

This appendix attempts to clarify (though not specify) the rules when a target occurs in a comprehension or in a generator expression. For a number of illustrative examples we show the original code, containing a comprehension, and the translation, where the comprehension has been replaced by an equivalent generator function plus some scaffolding.

本附录试图澄清（尽管不是具体说明）目标出现在推导式或生成器表达式中时的规则。对于一些说明性的例子，我们展示了包含推导式的原始代码和翻译，其中推导式已经被一个等价的生成器函数和一些支架所取代。

Since `[x for ...]` is equivalent to `list(x for ...)` these examples all use list comprehensions without loss of generality. And since these examples are meant to clarify edge cases of the rules, they aren't trying to look like real code.

由于 `[x for ...]` 等同于 `list(x for ...)` 这些例子都使用了列表推导式，而不失其一般性。而且，由于这些例子是为了澄清规则的边缘情况，它们并不试图看起来像真正的代码。

Note: comprehensions are already implemented via synthesizing nested generator functions like those in this appendix. The new part is adding appropriate declarations to establish the intended scope of assignment expression targets (the same scope they resolve to as if the assignment were performed in the block containing the outermost comprehension). For type inference purposes, these illustrative expansions do not imply that assignment expression targets are always Optional (but they do indicate the target binding scope).

注意：推导式已经通过合成嵌套的生成器函数实现了，比如本附录中的那些。新的部分是添加适当的声明，以建立赋值表达式目标的预期范围（与它们解析到的范围相同，就像赋值是在包含最外层的理解力的块中执行一样）。为了类型推理的目的，这些说明性的扩展并不意味着赋值表达式目标总是可选的（但它们确实表明了目标的绑定范围）。

Let's start with a reminder of what code is generated for a generator expression without assignment expression.

让我们先来提醒一下没有赋值表达式的生成器表达式会生成什么代码。

- Original code (EXPR usually references VAR):

  原始代码（EXPR通常引用VAR）：

  ```python
  def f():
      a = [EXPR for VAR in ITERABLE]
  ```

- Translation (let's not worry about name conflicts):

  翻译（我们不要担心名字冲突）：
  
  ```python
  def f():
      def genexpr(iterator):
          for VAR in iterator:
              yield EXPR
      a = list(genexpr(iter(ITERABLE)))
  ```

Let's add a simple assignment expression.

现在让我们添加一个简单的赋值表达式。

- Original code:

  原始代码：

  ```python
  def f():
      a = [TARGET := EXPR for VAR in ITERABLE]
  ```

- Translation:

  翻译：
  
  ```python
  def f():
      if False:
          TARGET = None  # Dead code to ensure TARGET is a local variable
      def genexpr(iterator):
          nonlocal TARGET
          for VAR in iterator:
              TARGET = EXPR
              yield TARGET
      a = list(genexpr(iter(ITERABLE)))
  ```

Let's add a `global TARGET` declaration in `f()`.

让我们在`f()`中添加一个`global TARGET`声明：

- Original code:

  原始代码：

  ```python
  def f():
      global TARGET
      a = [TARGET := EXPR for VAR in ITERABLE]
  ```

- Translation:

  翻译：
  
  ```python
  def f():
      global TARGET
      def genexpr(iterator):
          global TARGET
          for VAR in iterator:
              TARGET = EXPR
              yield TARGET
      a = list(genexpr(iter(ITERABLE)))
  ```

Or instead let's add a `nonlocal TARGET` declaration in `f()`.

或者让我们在`f()`中添加一个`nonlocal TARGET`声明。

- Original code:

  原始代码：

  ```python
  def g():
      TARGET = ...
      def f():
          nonlocal TARGET
          a = [TARGET := EXPR for VAR in ITERABLE]
  ```

- Translation:

  翻译：
  
  ```python
  def g():
      TARGET = ...
      def f():
          nonlocal TARGET
          def genexpr(iterator):
              nonlocal TARGET
              for VAR in iterator:
                  TARGET = EXPR
                  yield TARGET
          a = list(genexpr(iter(ITERABLE)))
  ```

Finally, let's nest two comprehensions.

最后，让我们来嵌套两个推导式。

- Original code:

  原始代码：

  ```python
  def f():
      a = [[TARGET := i for i in range(3)] for j in range(2)]
      # I.e., a = [[0, 1, 2], [0, 1, 2]]
      print(TARGET)  # prints 2
  ```

- Translation:

  翻译：
  
  ```python
  def f():
      if False:
          TARGET = None
      def outer_genexpr(outer_iterator):
          nonlocal TARGET
          def inner_generator(inner_iterator):
              nonlocal TARGET
              for i in inner_iterator:
                  TARGET = i
                  yield i
          for j in outer_iterator:
              yield list(inner_generator(range(3)))
      a = list(outer_genexpr(range(2)))
      print(TARGET)
  ```

# [Appendix C: No Changes to Scope Semantics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id43)

[附录 C：对范围语义没有改变](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id43)

Because it has been a point of confusion, note that nothing about Python's scoping semantics is changed. Function-local scopes continue to be resolved at compile time, and to have indefinite temporal extent at run time ("full closures"). Example:

因为这一直是一个混淆点，请注意 Python 的范围语义没有任何改变。函数局部作用域继续在编译时被解析，并且在运行时有不确定的时间范围 ("完全闭包")。例子。

```python
a = 42
def f():
    # `a` is local to `f`, but remains unbound
    # until the caller executes this genexp:
    yield ((a := i) for i in range(3))
    yield lambda: a + 100
    print("done")
    try:
        print(f"`a` is bound to {a}")
        assert False
    except UnboundLocalError:
        print("`a` is not yet bound")
```

Then:

```python
>>> results = list(f()) # [genexp, lambda]
done
`a` is not yet bound
# The execution frame for f no longer exists in CPython,
# but f's locals live so long as they can still be referenced.
>>> list(map(type, results))
[<class 'generator'>, <class 'function'>]
>>> list(results[0])
[0, 1, 2]
>>> results[1]()
102
>>> a
42
```

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id44)

参考文献

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [1]                                                          | Proof of concept implementation (https://github.com/Rosuav/cpython/tree/assignment-expressions) |
|                                                              |                                                              |
| [[2\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id2) | Pivotal post regarding inline assignment semantics (https://mail.python.org/pipermail/python-ideas/2018-March/049409.html) |
|                                                              |                                                              |
| [[3\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id1) | Discussion of [PEP 572](https://www.python.org/dev/peps/pep-0572) TargetScopeError (https://mail.python.org/archives/list/python-dev@python.org/thread/FXVSYCTQOTT7JCFACKPGPXKULBCGEPQY/) |
|                                                              |                                                              |

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20572%20--%20Assignment%20Expressions.md#id45)

版权声明

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0572.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。