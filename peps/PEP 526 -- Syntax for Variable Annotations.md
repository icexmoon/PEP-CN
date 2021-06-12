



# PEP 526 -- Syntax for Variable Annotations

PEP 526 -- 变量注解语法

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 526                                                          |
| Title:          | Syntax for Variable Annotations                              |
| Author:         | Ryan Gonzalez <rymg19 at gmail.com>, Philip House <phouse512 at gmail.com>, Ivan Levkivskyi <levkivskyi at gmail.com>, Lisa Roach <lisaroach14 at gmail.com>, Guido van Rossum <guido at python.org> |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 09-Aug-2016                                                  |
| Python-Version: | 3.6                                                          |
| Post-History:   | 30-Aug-2016, 02-Sep-2016                                     |
| Resolution:     | https://mail.python.org/pipermail/python-dev/2016-September/146282.html |

------

Contents

- [Status](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#status)，状态
- [Notice for Reviewers](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#notice-for-reviewers)，审阅人的注意事项
- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#abstract)，摘要
- Rationale，缘由
  - [Non-goals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#non-goals)，非目标
- Specification，定义
  - [Global and local variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#global-and-local-variable-annotations)，全局和局部变量注解
  - [Class and instance variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#class-and-instance-variable-annotations)，类和实例变量的注解
  - [Annotating expressions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#annotating-expressions)，注解表达式
  - [Where annotations aren't allowed](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#where-annotations-aren-t-allowed)，不能使用注解的地方
  - [Variable annotations in stub files](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#variable-annotations-in-stub-files)，存根文件中的变量注解
  - [Preferred coding style for variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#preferred-coding-style-for-variable-annotations)，变量注解的推荐编码风格
- [Changes to Standard Library and Documentation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#changes-to-standard-library-and-documentation)，对标准库和文档的修改
- Runtime Effects of Type Annotations，变量注解的运行时影响
  - [Other uses of annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#other-uses-of-annotations)，变量注解的其它用途
- [Rejected/Postponed Proposals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#rejected-postponed-proposals)，被拒绝/推迟的提案
- [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#backwards-compatibility)，向后兼容
- [Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#implementation)，实现
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#copyright)，版权声明

# [Status](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id2)

状态

This PEP has been provisionally accepted by the BDFL. See the acceptance message for more color: https://mail.python.org/pipermail/python-dev/2016-September/146282.html

本PEP已被BDFL暂时接受。更多颜色请见接受信息：https://mail.python.org/pipermail/python-dev/2016-September/146282.html

# [Notice for Reviewers](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id3)

审稿人须知

This PEP was drafted in a separate repo: https://github.com/phouse512/peps/tree/pep-0526.

本PEP是在一个单独的repo中起草的：https://github.com/phouse512/peps/tree/pep-0526。

There was preliminary discussion on python-ideas and at https://github.com/python/typing/issues/258.

在 python-ideas 和 https://github.com/python/typing/issues/258 上进行了初步讨论。

Before you bring up an objection in a public forum please at least read the summary of [rejected](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#rejected) ideas listed at the end of this PEP.

在你在公共论坛上提出反对意见之前，请至少阅读列在本PEP末尾的被拒绝的观点摘要。

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id4)

摘要

[PEP 484](https://www.python.org/dev/peps/pep-0484) introduced type hints, a.k.a. type annotations. While its main focus was function annotations, it also introduced the notion of type comments to annotate variables:

PEP 484引入了类型提示，也就是类型注释。虽然它的主要焦点是函数注释，但它也引入了类型注释的概念来注释变量。

```python
# 'primes' is a list of integers
primes = []  # type: List[int]

# 'captain' is a string (Note: initial value is a problem)
captain = ...  # type: str

class Starship:
    # 'stats' is a class variable
    stats = {}  # type: Dict[str, int]
```

This PEP aims at adding syntax to Python for annotating the types of variables (including class variables and instance variables), instead of expressing them through comments:

本PEP旨在为Python添加语法，用于注释变量的类型（包括类变量和实例变量），而不是通过注释来表达它们。

```python
primes: List[int] = []

captain: str  # Note: no initial value!

class Starship:
    stats: ClassVar[Dict[str, int]] = {}
```

[PEP 484](https://www.python.org/dev/peps/pep-0484) explicitly states that type comments are intended to help with type inference in complex cases, and this PEP does not change this intention. However, since in practice type comments have also been adopted for class variables and instance variables, this PEP also discusses the use of type annotations for those variables.

PEP 484明确指出，类型注释的目的是帮助在复杂情况下进行类型推断，本PEP并没有改变这一意图。然而，由于在实践中类型注释也被用于类变量和实例变量，本PEP也讨论了对这些变量使用类型注解的问题。

# [Rationale](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id5)

理论依据

Although type comments work well enough, the fact that they're expressed through comments has some downsides:

尽管类型注释工作得足够好，但通过评论表达变量类型在实际中有一些不足：

- Text editors often highlight comments differently from type annotations.

  文本编辑器通常以不同的方式突出注释和类型注解。

- There's no way to annotate the type of an undefined variable; one needs to initialize it to `None` (e.g. `a = None # type: int`).

  没有办法注释未定义变量的类型；需要将其初始化为`None`（例如，`a = None # type: int`）。

- Variables annotated in a conditional branch are difficult to read:

  在条件分支中注释的变量很难阅读：

  ```python
  if some_value:
      my_var = function() # type: Logger
  else:
      my_var = another_function() # Why isn't there a type here?
  ```

- Since type comments aren't actually part of the language, if a Python script wants to parse them, it requires a custom parser instead of just using `ast`.

  由于类型注释实际上不是语言的一部分，如果一个 Python 脚本想解析它们，它需要一个自定义的解析器，而不是仅仅使用 `ast`。

- Type comments are used a lot in typeshed. Migrating typeshed to use the variable annotation syntax instead of type comments would improve readability of stubs.

  类型注释在 typeshed 中被大量使用。将typeshed迁移到使用变量注解语法而不是类型注释将提高存根的可读性。

  > typeshed 是一个 Github 上用于收集 Python 标准库和第三方库存根文件的资源库，译者注。

- In situations where normal comments and type comments are used together, it is difficult to distinguish them:

  在正常注释和类型注释一起使用的情况下，很难区分它们：

  ```python
  path = None  # type: Optional[str]  # Path to module source
  ```

- It's impossible to retrieve the annotations at runtime outside of attempting to find the module's source code and parse it at runtime, which is inelegant, to say the least.

  除了试图找到模块的源代码并在运行时进行解析外，不可能在运行时检索注释，至少可以说这是不优雅的。

The majority of these issues can be alleviated by making the syntax a core part of the language. Moreover, having a dedicated annotation syntax for class and instance variables (in addition to method annotations) will pave the way to static duck-typing as a complement to nominal typing defined by [PEP 484](https://www.python.org/dev/peps/pep-0484).

这些问题中的大部分可以通过使语法成为语言的核心部分而得到缓解。此外，为类和实例变量提供专门的注解语法（除了方法注解之外）将为静态鸭子类型化铺平道路，作为[PEP 484](https://www.python.org/dev/peps/pep-0484)定义的名义类型化的补充。

## [Non-goals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id6)

非目标

While the proposal is accompanied by an extension of the `typing.get_type_hints` standard library function for runtime retrieval of annotations, variable annotations are not designed for runtime type checking. Third party packages will have to be developed to implement such functionality.

虽然该提议伴随着`typing.get_type_hints`标准库函数的扩展，用于运行时检索注释，但变量注解并非为运行时类型检查而设计。必须开发第三方包来实现这种功能。

It should also be emphasized that **Python will remain a dynamically typed language, and the authors have no desire to ever make type hints mandatory, even by convention.** Type annotations should not be confused with variable declarations in statically typed languages. The goal of annotation syntax is to provide an easy way to specify structured type metadata for third party tools.

还应该强调的是，**Python仍将是一种动态类型的语言，作者并不想让类型提示成为强制性的，甚至是约定俗成的。**类型注释不应该与静态类型语言中的变量声明相混淆。注释语法的目的是为第三方工具提供一种简单的方法来指定结构化的类型元数据。

This PEP does not require type checkers to change their type checking rules. It merely provides a more readable syntax to replace type comments.

这个PEP并不要求类型检查器改变他们的类型检查规则。它只是提供了一种更可读的语法来替代类型注释。

# [Specification](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id7)

规范

Type annotation can be added to an assignment statement or to a single expression indicating the desired type of the annotation target to a third party type checker:

类型注解可以被添加到赋值语句或单个表达式中，表示注解目标的所需类型给第三方类型检查器：

```python
my_var: int
my_var = 5  # Passes type check.
other_var: int  = 'a'  # Flagged as error by type checker,
                       # but OK at runtime.
```

This syntax does not introduce any new semantics beyond [PEP 484](https://www.python.org/dev/peps/pep-0484), so that the following three statements are equivalent:

这种语法在PEP 484之外没有引入任何新的语义，因此，以下三种声明是等价的。

```python
var = value # type: annotation
var: annotation; var = value
var: annotation = value
```

Below we specify the syntax of type annotations in different contexts and their runtime effects.

下面我们指定了不同语境下的类型注解的语法以及它们的运行时效果。

We also suggest how type checkers might interpret annotations, but compliance to these suggestions is not mandatory. (This is in line with the attitude towards compliance in [PEP 484](https://www.python.org/dev/peps/pep-0484).)

我们还建议类型检查器如何解释注解，但对这些建议的遵守不是强制性的。（这与PEP 484中对遵守的态度是一致的）。

## [Global and local variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id8)

全局变量和局部变量注解

The types of locals and globals can be annotated as follows:

局部变量和局部变量的类型可以被注释为如下：

```python
some_number: int           # variable without initial value
some_list: List[int] = []  # variable with initial value
```

Being able to omit the initial value allows for easier typing of variables assigned in conditional branches:

能够省略初始值可以使条件分支中分配的变量更容易输入：

```python
sane_world: bool
if 2+2 == 4:
    sane_world = True
else:
    sane_world = False
```

Note that, although the syntax does allow tuple packing, it does *not* allow one to annotate the types of variables when tuple unpacking is used:

请注意，尽管该语法允许元组打包，但它不允许人们在使用元组解包时对变量的类型进行注解：

```python
# Tuple packing with variable annotation syntax
t: Tuple[int, ...] = (1, 2, 3)
# or
t: Tuple[int, ...] = 1, 2, 3  # This only works in Python 3.8+

# Tuple unpacking with variable annotation syntax
header: str
kind: int
body: Optional[List[str]]
header, kind, body = message
```

Omitting the initial value leaves the variable uninitialized:

省略初始值会使变量未被初始化：

```python
a: int
print(a)  # raises NameError
```

However, annotating a local variable will cause the interpreter to always make it a local:

然而，注解一个局部变量将导致解释器总是使其成为一个局部变量：

```python
def f():
    a: int
    print(a)  # raises UnboundLocalError
    # Commenting out the a: int makes it a NameError.
```

as if the code were:

就像这样的代码：

```python
def f():
    if False: a = 0
    print(a)  # raises UnboundLocalError
```

Duplicate type annotations will be ignored. However, static type checkers may issue a warning for annotations of the same variable by a different type:

重复的类型注释将被忽略。然而，静态类型检查器可能会对同一变量的不同类型注释发出警告：

```python
a: int
a: str  # Static type checker may or may not warn about this.
```

## [Class and instance variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id9)

类和实例变量注解

Type annotations can also be used to annotate class and instance variables in class bodies and methods. In particular, the value-less notation `a: int` allows one to annotate instance variables that should be initialized in `__init__` or `__new__`. The proposed syntax is as follows:

类型注解也可以用来注解类主体和方法中的类和实例变量。特别是，无值符号`a: int`允许人们注解应该在`__init__`或`__new__`中初始化的实例变量。建议的语法如下：

```python
class BasicStarship:
    captain: str = 'Picard'               # instance variable with default
    damage: int                           # instance variable without default
    stats: ClassVar[Dict[str, int]] = {}  # class variable
```

Here `ClassVar` is a special class defined by the typing module that indicates to the static type checker that this variable should not be set on instances.

这里`ClassVar`是一个由`typing`模块定义的特殊类，它向静态类型检查器表明这个变量不应该被设置在实例上。

Note that a `ClassVar` parameter cannot include any type variables, regardless of the level of nesting: `ClassVar[T]` and `ClassVar[List[Set[T]]]` are both invalid if `T` is a type variable.

注意，一个`ClassVar`参数不能包括任何类型变量，不管嵌套的程度如何。如果`T`是一个类型变量，`ClassVar[T]`和`ClassVar[List[Set[T]]]`都是无效的。

This could be illustrated with a more detailed example. In this class:

这可以用一个更详细的例子来说明。在这个类中：

```python
class Starship:
    captain = 'Picard'
    stats = {}

    def __init__(self, damage, captain=None):
        self.damage = damage
        if captain:
            self.captain = captain  # Else keep the default

    def hit(self):
        Starship.stats['hits'] = Starship.stats.get('hits', 0) + 1
```

`stats` is intended to be a class variable (keeping track of many different per-game statistics), while `captain` is an instance variable with a default value set in the class. This difference might not be seen by a type checker: both get initialized in the class, but `captain` serves only as a convenient default value for the instance variable, while `stats` is truly a class variable -- it is intended to be shared by all instances.

`stats`旨在作为一个类变量（记录许多不同的每场比赛的统计数据），而`captain`是一个实例变量，在类中设置了一个默认值。这种差异可能不会被类型检查器发现：两者都在类中被初始化，但`captain`只是作为实例变量的一个方便的默认值，而`stats`是真正的类变量--它打算被所有实例共享。

Since both variables happen to be initialized at the class level, it is useful to distinguish them by marking class variables as annotated with types wrapped in `ClassVar[...]`. In this way a type checker may flag accidental assignments to attributes with the same name on instances.

由于这两个变量恰好都是在类的层次上被初始化的，通过将类变量标记为用`ClassVar[...]`包裹的类型来区分它们是很有用的。通过这种方式，类型检查器可以标记出对实例上具有相同名称的属性的意外赋值。

For example, annotating the discussed class:

例如，对所讨论的类进行注解：

```python
class Starship:
    captain: str = 'Picard'
    damage: int
    stats: ClassVar[Dict[str, int]] = {}

    def __init__(self, damage: int, captain: str = None):
        self.damage = damage
        if captain:
            self.captain = captain  # Else keep the default

    def hit(self):
        Starship.stats['hits'] = Starship.stats.get('hits', 0) + 1

enterprise_d = Starship(3000)
enterprise_d.stats = {} # Flagged as error by a type checker
Starship.stats = {} # This is OK
```

As a matter of convenience (and convention), instance variables can be annotated in `__init__` or other methods, rather than in the class:

为了方便起见（和惯例），实例变量可以在 `__init__ `或其它方法中被注释，而不是在类中：

```python
from typing import Generic, TypeVar
T = TypeVar('T')

class Box(Generic[T]):
    def __init__(self, content):
        self.content: T = content
```

## [Annotating expressions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id10)

注解表达式

The target of the annotation can be any valid single assignment target, at least syntactically (it is up to the type checker what to do with this):

注解的目标可以是任何有效的单一赋值目标，至少在语法上是这样（如何处理这个问题由类型检查器决定）：

```python
class Cls:
    pass

c = Cls()
c.x: int = 0  # Annotates c.x with int.
c.y: int      # Annotates c.y with int.

d = {}
d['a']: int = 0  # Annotates d['a'] with int.
d['b']: int      # Annotates d['b'] with int.
```

Note that even a parenthesized name is considered an expression, not a simple name:

注意，即使是括号内的名字也被认为是一个表达式，而不是一个简单的名字：

```python
(x): int      # Annotates x with int, (x) treated as expression by compiler.
(y): int = 0  # Same situation here.
```

## [Where annotations aren't allowed](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id11)

哪些地方的注解是不被允许的

It is illegal to attempt to annotate variables subject to `global` or `nonlocal` in the same function scope:

试图在同一个函数范围内注释受`global`或`nonlocal`影响的变量是非法的：

```python
def f():
    global x: int  # SyntaxError

def g():
    x: int  # Also a SyntaxError
    global x
```

The reason is that `global` and `nonlocal` don't own variables; therefore, the type annotations belong in the scope owning the variable.

原因是`global`和`nonlocal`不拥有变量；因此，类型注解属于拥有变量的作用域。

Only single assignment targets and single right hand side values are allowed. In addition, one cannot annotate variables used in a `for` or `with` statement; they can be annotated ahead of time, in a similar manner to tuple unpacking:

只允许单一的赋值目标和单一的右侧值。此外，人们不能对`for`或`with`语句中使用的变量进行注解；它们可以提前被注解，其方式类似于元组解包：

```python
a: int
for a in my_iter:
    ...

f: MyFile
with myfunc() as f:
    ...
```

## [Variable annotations in stub files](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id12)

存根文件中的变量注解

As variable annotations are more readable than type comments, they are preferred in stub files for all versions of Python, including Python 2.7. Note that stub files are not executed by Python interpreters, and therefore using variable annotations will not lead to errors. Type checkers should support variable annotations in stubs for all versions of Python. For example:

由于变量注解比类型注释更易读，所以在所有版本的 Python 的存根文件中，包括 Python 2.7，它们都是首选。注意，存根文件不会被 Python 解释器执行，因此使用变量注解不会导致错误。类型检查器应该支持所有版本的 Python 的存根中的变量注解。比如说。

```python
# file lib.pyi

ADDRESS: unicode = ...

class Error:
    cause: Union[str, unicode]
```

## [Preferred coding style for variable annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id13)

变量注解的首选编码风格

Annotations for module level variables, class and instance variables, and local variables should have a single space after corresponding colon. There should be no space before the colon. If an assignment has right hand side, then the equality sign should have exactly one space on both sides. Examples:

模块级变量、类和实例变量以及局部变量的注解应该在相应的冒号后面有一个空格。冒号前不应该有空格。如果一个赋值有右首，那么等号的两边都应该有一个空格。例子。

- Yes:

  ```python
  code: int
  
  class Point:
      coords: Tuple[int, int]
      label: str = '<unknown>'
  ```

- No:

  ```python
  code:int  # No space after colon
  code : int  # Space before colon
  
  class Test:
      result: int=0  # No spaces around equality sign
  ```

# [Changes to Standard Library and Documentation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id14)

对标准库和文档的修改

- A new covariant type `ClassVar[T_co]` is added to the `typing` module. It accepts only a single argument that should be a valid type, and is used to annotate class variables that should not be set on class instances. This restriction is ensured by static checkers, but not at runtime. See the [classvar](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#classvar) section for examples and explanations for the usage of `ClassVar`, and see the [rejected](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#rejected) section for more information on the reasoning behind `ClassVar`.

  一个新的协变类型`ClassVar[T_co]`被添加到`typing`模块。它只接受一个参数，该参数应该是一个有效的类型，并用于注解不应该在类实例上设置的类变量。这个限制是由静态检查器保证的，但在运行时不会。参见[classvar](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#classvar)部分，了解`ClassVar`的使用实例和解释，参见[rejected](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#rejected)部分，了解更多关于`ClassVar`背后的原因。

- Function `get_type_hints` in the `typing` module will be extended, so that one can retrieve type annotations at runtime from modules and classes as well as functions. Annotations are returned as a dictionary mapping from variable or arguments to their type hints with forward references evaluated. For classes it returns a mapping (perhaps `collections.ChainMap`) constructed from annotations in method resolution order.

  在 `typing` 模块中的 `get_type_hints` 函数将被扩展，这样就可以在运行时从模块和类以及函数中获取类型注解。注解将以字典映射的形式返回，从变量或参数到它们的类型提示，并对前向引用进行评估。对于类，它返回一个由方法解析顺序中的注释构建的映射（也许是`collections.ChainMap`）。

- Recommended guidelines for using annotations will be added to the documentation, containing a pedagogical recapitulation of specifications described in this PEP and in [PEP 484](https://www.python.org/dev/peps/pep-0484). In addition, a helper script for translating type comments into type annotations will be published separately from the standard library.

  使用注解的推荐指南将被添加到文档中，其中包含本PEP和[PEP 484](https://www.python.org/dev/peps/pep-0484)中描述的规范的教学复述。此外，一个用于将类型注释翻译成类型注解的辅助脚本将从标准库中单独发布。

# [Runtime Effects of Type Annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id15)

类型注解的运行时影响

Annotating a local variable will cause the interpreter to treat it as a local, even if it was never assigned to. Annotations for local variables will not be evaluated:

注解一个局部变量将导致解释器把它当作一个局部变量，即使它从未被赋值过。对局部变量的注解将不会被评估。

```python
def f():
    x: NonexistentName  # No error.
```

However, if it is at a module or class level, then the type *will* be evaluated:

然而，如果是在模块或类的层面上，那么将对类型进行评估：

```python
x: NonexistentName  # Error!
class X:
    var: NonexistentName  # Error!
```

In addition, at the module or class level, if the item being annotated is a *simple name*, then it and the annotation will be stored in the `__annotations__` attribute of that module or class (mangled if private) as an ordered mapping from names to evaluated annotations. Here is an example:

此外，在模块或类的层次上，如果被注解的元素是一个简单的名字，那么它和注释将被保存在该模块或类的`__annotations__`属性中（如果是私有的，则被混合），作为一个从名字到被评估的注解的有序映射。这里有一个例子：

> 混合指类似于类的伪私有属性那样，被重命名为`_modelName__privateName`这样的形式，译者注。

```python
from typing import Dict
class Player:
    ...
players: Dict[str, Player]
__points: int

print(__annotations__)
# prints: {'players': typing.Dict[str, __main__.Player],
#          '_Player__points': <class 'int'>}
```

`__annotations__` is writable, so this is permitted:

`__annotations__`是可写的，所以可以这样：

```python
__annotations__['s'] = str
```

But attempting to update `__annotations__` to something other than an ordered mapping may result in a TypeError:

但如果试图将`__annotations__`更新为有序映射以外的东西，可能会导致一个TypeError：

```python
class C:
    __annotations__ = 42
    x: int = 5  # raises TypeError
```

(Note that the assignment to `__annotations__`, which is the culprit, is accepted by the Python interpreter without questioning it -- but the subsequent type annotation expects it to be a `MutableMapping` and will fail.)

(注意，对 `__annotations__` 的赋值是罪魁祸首，Python 解释器接受了它而没有提出质疑 -- 但随后的类型注解希望它是一个 `MutableMapping` 而会失败。)

The recommended way of getting annotations at runtime is by using `typing.get_type_hints` function; as with all dunder attributes, any undocummented use of `__annotations__` is subject to breakage without warning:

在运行时获取注解的推荐方式是使用 `typing.get_type_hints` 函数；就像所有的 dunder 属性一样，任何对 `__annotations__` 的非文档化使用都会被破坏，而且没有警告：

> dunder 属性指那些使用前后双下划线的方式命名的特殊属性，译者注。

```python
from typing import Dict, ClassVar, get_type_hints
class Starship:
    hitpoints: int = 50
    stats: ClassVar[Dict[str, int]] = {}
    shield: int = 100
    captain: str
    def __init__(self, captain: str) -> None:
        ...

assert get_type_hints(Starship) == {'hitpoints': int,
                                    'stats': ClassVar[Dict[str, int]],
                                    'shield': int,
                                    'captain': str}

assert get_type_hints(Starship.__init__) == {'captain': str,
                                             'return': None}
```

Note that if annotations are not found statically, then the `__annotations__` dictionary is not created at all. Also the value of having annotations available locally does not offset the cost of having to create and populate the annotations dictionary on every function call. Therefore, annotations at function level are not evaluated and not stored.

注意，如果注解没有被静态地找到，那么`__annotations__`字典根本就不会被创建。另外，在本地拥有注解的价值并不能抵消在每个函数调用中创建和填充注解字典的成本。因此，函数级的注解不被评估，也不被存储。

## [Other uses of annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id16)

注解的其它用途

While Python with this PEP will not object to:

虽然Python与这个PEP不会反对：

```python
alice: 'well done' = 'A+'
bob: 'what a shame' = 'F-'
```

since it will not care about the type annotation beyond "it evaluates without raising", a type checker that encounters it will flag it, unless disabled with `# type: ignore` or `@no_type_check`.

因为它不会关心除了 "它评估时没有提高 "之外的类型注解，一个遇到它的类型检查器将标记它，除非用 `# type: ignore` 或 `@no_type_check `来禁用它。

However, since Python won't care what the "type" is, if the above snippet is at the global level or in a class, `__annotations__` will include `{'alice': 'well done', 'bob': 'what a shame'}`.

然而，由于 Python 并不关心 "类型 "是什么，如果上面的片段是在全局级别或在一个类中，`__annotations__`将包括 `{'alice': 'well done', 'bob': 'what a shame'}`。

These stored annotations might be used for other purposes, but with this PEP we explicitly recommend type hinting as the preferred use of annotations.

这些存储的注释可能会被用于其他目的，但在这个 PEP 中，我们明确地推荐类型提示作为注释的首选用途。

# [Rejected/Postponed Proposals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id17)

被拒绝/推迟的提案

- **Should we introduce variable annotations at all?** Variable annotations have *already* been around for almost two years in the form of type comments, sanctioned by [PEP 484](https://www.python.org/dev/peps/pep-0484). They are extensively used by third party type checkers (mypy, pytype, PyCharm, etc.) and by projects using the type checkers. However, the comment syntax has many downsides listed in Rationale. This PEP is not about the need for type annotations, it is about what should be the syntax for such annotations.

  **我们到底要不要引入变量注解？**变量注解已经以类型注释的形式存在了近两年，得到了PEP 484的认可。它们被第三方类型检查器（mypy、pytype、PyCharm等）和使用类型检查器的项目广泛使用。然而，注释语法有很多弊端，在Rationale中列出。本 PEP 不是在讨论是否需要类型注解，而是在讨论这种注解的语法应该是什么样的。

- **Introduce a new keyword:** The choice of a good keyword is hard, e.g. it can't be `var` because that is way too common a variable name, and it can't be `local` if we want to use it for class variables or globals. Second, no matter what we choose, we'd still need a `__future__` import.

  **引入一个新的关键字：**选择一个好的关键字是很难的，例如，它不能是`var`，因为那是一个太过普通的变量名，而且如果我们想把它用于类变量或全局变量，它就不能是`local`。其次，不管我们选择什么，我们仍然需要一个 `__future__` 的导入。

- **Use** `def` **as a keyword:** The proposal would be:

  **使用`def`作为关键字**：这个提议像是这样：

  ```python
  def primes: List[int] = []
  def captain: str
  ```

  The problem with this is that `def` means "define a function" to generations of Python programmers (and tools!), and using it also to define variables does not increase clarity. (Though this is of course subjective.)

  这样做的问题是，对几代Python程序员来说，`def`意味着 "定义一个函数"(和工具！)，用它来定义变量并不能提高清晰度。(当然这是很主观的。)

- **Use function based syntax**: It was proposed to annotate types of variables using `var = cast(annotation[, value])`. Although this syntax alleviates some problems with type comments like absence of the annotation in AST, it does not solve other problems such as readability and it introduces possible runtime overhead.

  **使用基于函数的语法**。有人建议使用`var = cast(annotation[, value])`来注释变量的类型。虽然这种语法缓解了类型注释的一些问题，比如在AST中没有注释，但它并没有解决其他问题，比如可读性，而且还可能引入运行时开销。

- **Allow type annotations for tuple unpacking:** This causes ambiguity: it's not clear what this statement means:

  **允许对元组解包进行类型注释：**这导致了歧义：不清楚这个语句的意思：

  ```python
  x, y: T
  ```

  Are `x` and `y` both of type `T`, or do we expect `T` to be a tuple type of two items that are distributed over `x` and `y`, or perhaps `x` has type `Any` and `y` has type `T`? (The latter is what this would mean if this occurred in a function signature.) Rather than leave the (human) reader guessing, we forbid this, at least for now.

  `x`和`y`都是`T`类型，还是我们希望`T`是分布在`x`和`y`上的两个项目的元组类型，或者`x`的类型是`Any`，`y`的类型是`T`？ 后者是指如果这发生在一个函数签名中的话）。与其让（人类）读者猜测，我们禁止这样做，至少现在是这样。

- **Parenthesized form** `(var: type)` **for annotations:** It was brought up on python-ideas as a remedy for the above-mentioned ambiguity, but it was rejected since such syntax would be hairy, the benefits are slight, and the readability would be poor.

  **上层形式** `(var: type)` **用于注解：**在python-ideas上被提出来作为上述歧义的补救措施，但它被拒绝了，因为这样的语法会很毛糙，好处不大，可读性也很差。

- **Allow annotations in chained assignments:** This has problems of ambiguity and readability similar to tuple unpacking, for example in:

  **允许在链式赋值中进行注释：**这有类似于元组解包的模糊性和可读性问题，例如在：

  ```python
  x: int = y = 1
  z = w: int = 1
  ```

  it is ambiguous, what should the types of `y` and `z` be? Also the second line is difficult to parse.

  它是模糊的，`y`和`z`的类型应该是什么？另外，第二行也很难解析。

- **Allow annotations in** `with` **and** `for` **statement:** This was rejected because in `for` it would make it hard to spot the actual iterable, and in `with` it would confuse the CPython's LL(1) parser.

  **允许在`with`和`for`语句中进行注解**：这一点被否决了，因为在`for`中会使人很难发现实际的迭代对象，而在`with`中会使CPython的LL(1)解析器混淆。

- **Evaluate local annotations at function definition time:** This has been rejected by Guido because the placement of the annotation strongly suggests that it's in the same scope as the surrounding code.

  **在函数定义时评估局部注解**：这一点已经被Guido拒绝了，因为注解的位置强烈表明它与周围的代码处于同一范围。

- **Store variable annotations also in function scope:** The value of having the annotations available locally is just not enough to significantly offset the cost of creating and populating the dictionary on *each* function call.

  将变量注解也存储在函数范围内。让注解在本地可用的价值不足以大大抵消在每个函数调用中创建和填充字典的成本。

- **Initialize variables annotated without assignment:** It was proposed on python-ideas to initialize `x` in `x: int` to `None` or to an additional special constant like Javascript's `undefined`. However, adding yet another singleton value to the language would needed to be checked for everywhere in the code. Therefore, Guido just said plain "No" to this.

  **在没有赋值的情况下初始化被注解的变量**：有人在 python-ideas 上提议将 `x: int` 中的 `x` 初始化为 `None` 或像 Javascript 的 `undefined` 一样的额外的特殊常数。然而，在语言中添加另一个单子值，需要在代码中到处检查。因此，Guido对这个问题直接说 "不"。

- **Add also** `InstanceVar` **to the typing module:** This is redundant because instance variables are way more common than class variables. The more common usage deserves to be the default.

  **将`InstanceVar`也加入到 typing 模块中**。这是多余的，因为实例变量比类变量更常见。更常见的用法应该是默认的。

- **Allow instance variable annotations only in methods:** The problem is that many `__init__` methods do a lot of things besides initializing instance variables, and it would be harder (for a human) to find all the instance variable annotations. And sometimes `__init__` is factored into more helper methods so it's even harder to chase them down. Putting the instance variable annotations together in the class makes it easier to find them, and helps a first-time reader of the code.

  **只允许在方法中对实例变量进行注释**：问题是许多 `__init__` 方法除了初始化实例变量外还做了很多事情，而且（对人来说）要找到所有的实例变量注释会更难。而且有时 `__init__` 被分解成更多的辅助方法，所以更难找到它们。将实例变量注解放在类中，可以更容易地找到它们，并帮助第一次阅读代码的人。

- **Use syntax** `x: class t = v` **for class variables:** This would require a more complicated parser and the `class` keyword would confuse simple-minded syntax highlighters. Anyway we need to have `ClassVar` store class variables to `__annotations__`, so a simpler syntax was chosen.

  **对类的变量使用语法`x: class t = v`**：这需要一个更复杂的解析器，而且类的关键字会让头脑简单的语法高亮器感到困惑。总之我们需要让`ClassVar`将类变量存储到`__annotations__`中，所以选择了一种更简单的语法。

- **Forget about** `ClassVar` **altogether:** This was proposed since mypy seems to be getting along fine without a way to distinguish between class and instance variables. But a type checker can do useful things with the extra information, for example flag accidental assignments to a class variable via the instance (which would create an instance variable shadowing the class variable). It could also flag instance variables with mutable defaults, a well-known hazard.

  **完全忘记`ClassVar`**：这个提议是因为mypy似乎在没有区分类变量和实例变量的情况下也能正常运行。但是类型检查器可以利用额外的信息做一些有用的事情，例如标记通过实例对类变量的意外赋值（这将创建一个影射类变量的实例变量）。它还可以标记具有可变默认值的实例变量，这是一个众所周知的危险。

  > 在实例的同名属性可以覆盖类属性这个问题上，我同意《Fluent Python》中的观点，这是Python的一种特性，是灵活性的体现。所以这里的注解只是提供了一种额外方式，可以让我们将“纯类属性”，即那些永远不会用于实例赋值的属性声明为类属性，以进行严格约束，译者注。

- **Use** `ClassAttr` **instead of** `ClassVar`: The main reason why `ClassVar` is better is following: many things are class attributes, e.g. methods, descriptors, etc. But only specific attributes are conceptually class variables (or maybe constants).

  **使用 `ClassAttr` 而不是 `ClassVar`**：`ClassVar`更好的主要原因如下：许多东西都是类的属性，例如方法、描述符等。但只有特定的属性在概念上是类的变量（也可能是常量）。

- **Do not evaluate annotations, treat them as strings:** This would be inconsistent with the behavior of function annotations that are always evaluated. Although this might be reconsidered in future, it was decided in [PEP 484](https://www.python.org/dev/peps/pep-0484) that this would have to be a separate PEP.

  **不要评估注解，把它们当作字符串：**这将与总是被评估的函数注释的行为不一致。尽管将来可能会重新考虑这个问题，但在PEP 484中，我们决定这必须是一个单独的PEP。

- **Annotate variable types in class docstring:** Many projects already use various docstring conventions, often without much consistency and generally without conforming to the [PEP 484](https://www.python.org/dev/peps/pep-0484) annotation syntax yet. Also this would require a special sophisticated parser. This, in turn, would defeat the purpose of the PEP -- collaborating with the third party type checking tools.

  **在类的文档串中注解变量类型：**许多项目已经使用了不同的文档串约定，通常没有太多一致性，而且通常不符合PEP 484的注释语法。而且这需要一个特别复杂的解析器。这反过来又违背了PEP的目的--与第三方类型检查工具合作。

- **Implement** `__annotations__` **as a descriptor:** This was proposed to prohibit setting `__annotations__` to something non-dictionary or non-None. Guido has rejected this idea as unnecessary; instead a TypeError will be raised if an attempt is made to update `__annotations__` when it is anything other than a mapping.

  **将`__annotations__`作为一个描述符来实现：**这个建议是为了禁止将 `__annotations__` 设置为非字典或非 None。Guido 拒绝了这个想法，认为没有必要；相反，如果试图更新 `__annotations__` 时，如果它不是一个映射，将会引发一个 TypeError。

- **Treating bare annotations the same as global or nonlocal:** The rejected proposal would prefer that the presence of an annotation without assignment in a function body should not involve *any* evaluation. In contrast, the PEP implies that if the target is more complex than a single name, its "left-hand part" should be evaluated at the point where it occurs in the function body, just to enforce that it is defined. For example, in this example:

  **将裸注解与全局或非全局相同：**被否决的提案倾向于在函数体中存在一个没有赋值的注解不应涉及任何评估。与此相反，PEP暗示，如果目标比单一的名字更复杂，它的 "左手部分 "应该在它出现在函数体中的地方被评估，只是为了强制它被定义。例如，在这个例子中：

  ```python
  def foo(self):
      slef.name: str
  ```

  the name `slef` should be evaluated, just so that if it is not defined (as is likely in this example :-), the error will be caught at runtime. This is more in line with what happens when there *is* an initial value, and thus is expected to lead to fewer surprises. (Also note that if the target was `self.name` (this time correctly spelled :-), an optimizing compiler has no obligation to evaluate `self` as long as it can prove that it will definitely be defined.)

  `slef`这个名字应该被评估，这样如果它没有被定义（在这个例子中很可能是这样：-），错误将在运行时被捕获。这与有初始值时发生的情况更加一致，因此预计会导致更少的意外。(还要注意的是，如果目标是`self.name`（这次拼写正确 :-)，优化编译器没有义务去评估`self`，只要它能证明它肯定会被定义。)

# [Backwards Compatibility](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id18)

向后兼容

This PEP is fully backwards compatible.

这个PEP是完全向后兼容的。

# [Implementation](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id19)

实现

An implementation for Python 3.6 is found on GitHub repo at https://github.com/ilevkivskyi/cpython/tree/pep-526

在GitHub repo上可以找到Python 3.6的实现：https://github.com/ilevkivskyi/cpython/tree/pep-526

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md#id20)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0526.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。