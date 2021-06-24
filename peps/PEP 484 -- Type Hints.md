# PEP 484 -- Type Hints

PEP 484 -- 类型提示

> 原文地址：<https://www.python.org/dev/peps/pep-0484/>

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 484                                                          |
| Title:          | Type Hints                                                   |
| Author:         | Guido van Rossum <guido at python.org>, Jukka Lehtosalo <jukka.lehtosalo at iki.fi>, Łukasz Langa <lukasz at python.org> |
| BDFL-Delegate:  | Mark Shannon                                                 |
| Discussions-To: | Python-Dev <[python-dev at python.org](mailto:python-dev@python.org?subject=PEP 484)> |
| Status:         | Provisional                                                  |
| Type:           | Standards Track                                              |
| Created:        | 29-Sep-2014                                                  |
| Python-Version: | 3.5                                                          |
| Post-History:   | 16-Jan-2015,20-Mar-2015,17-Apr-2015,20-May-2015,22-May-2015  |
| Resolution:     | https://mail.python.org/pipermail/python-dev/2015-May/140104.html |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#abstract)，摘要
- Rationale and Goals，理由和目标
  - [Non-goals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#non-goals)，非目标
- [The meaning of annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-meaning-of-annotations)，注释的意义
- Type Definition Syntax，类型定义语法
  - [Acceptable type hints](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#acceptable-type-hints)，可接受的类型提示
  - [Using None](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#using-none)，使用None
  - [Type aliases](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#type-aliases)，类型别名
  - [Callable](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#callable)
  - [Generics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#generics)，泛型
  - [User-defined generic types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#user-defined-generic-types)，用户自定义泛型
  - [Scoping rules for type variables](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#scoping-rules-for-type-variables)，类型变量的范围界定规则
  - [Instantiating generic classes and type erasure](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#instantiating-generic-classes-and-type-erasure)，实例化泛型类和类型擦除
  - [Arbitrary generic types as base classes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#arbitrary-generic-types-as-base-classes)，任意泛型作为基类
  - [Abstract generic types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#abstract-generic-types)，抽象泛型
  - [Type variables with an upper bound](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#type-variables-with-an-upper-bound)，有上限的类型变量
  - [Covariance and contravariance](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#covariance-and-contravariance)，协变和反协变
  - [The numeric tower](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-numeric-tower)，数字塔
  - [Forward references](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#forward-references)，前向引用
  - [Union types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#union-types)，联合类型
  - [Support for singleton types in unions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#support-for-singleton-types-in-unions)，union对于单例类型的支持
  - [The `Any` type](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-any-type)，`Any`类型
  - [The `NoReturn` type](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-noreturn-type)，`NoReturn`类型
  - [The type of class objects](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-type-of-class-objects)，类对象的类型
  - [Annotating instance and class methods](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#annotating-instance-and-class-methods)，注释实例和类方法
  - [Version and platform checking](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#version-and-platform-checking)，版本和平台检查
  - [Runtime or type checking?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#runtime-or-type-checking)，运行时或类型检查
  - [Arbitrary argument lists and default argument values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#arbitrary-argument-lists-and-default-argument-values)，任意参数列表和默认参数值
  - [Positional-only arguments](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#positional-only-arguments)，位置限定参数
  - [Annotating generator functions and coroutines](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#annotating-generator-functions-and-coroutines)，注释生成器函数和协程
- [Compatibility with other uses of function annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#compatibility-with-other-uses-of-function-annotations)，与函数注解的其他用途的兼容性
- [Type comments](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#type-comments)，类型注释
- [Casts](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#casts)，推断
- [NewType helper function](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#newtype-helper-function)，NewType 辅助函数
- Stub Files，存根文件
  - [Function/method overloading](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#function-method-overloading)，函数、方法重载
  - [Storing and distributing stub files](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#storing-and-distributing-stub-files)，存根文件的存储和发布
  - [The Typeshed Repo](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-typeshed-repo)，Typeshed 资源库
- [Exceptions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#exceptions)，异常
- [The `typing` Module](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-typing-module)，`typing`模块
- [Suggested syntax for Python 2.7 and straddling code](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#suggested-syntax-for-python-2-7-and-straddling-code)，建议的Python 2.7和过渡代码的语法
- Rejected Alternatives，被拒绝的替代方案
  - [Which brackets for generic type parameters?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#which-brackets-for-generic-type-parameters)，哪种括号用于泛型参数？
  - [What about existing uses of annotations?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#what-about-existing-uses-of-annotations)，注释的现有用途是什么？
  - [The problem of forward declarations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-problem-of-forward-declarations)，前向声明的问题
  - [The double colon](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#the-double-colon)，双冒号
  - [Other forms of new syntax](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#other-forms-of-new-syntax)，其它形式的新语法
  - [Other backwards compatible conventions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#other-backwards-compatible-conventions)，其他向后兼容的惯例
- [PEP Development Process](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#pep-development-process)，PEP开发过程
- [Acknowledgements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#acknowledgements)，致谢
- [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#references)，参考文献
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id10)

摘要

[PEP 3107](https://www.python.org/dev/peps/pep-3107) introduced syntax for function annotations, but the semantics were deliberately left undefined. There has now been enough 3rd party usage for static type analysis that the community would benefit from a standard vocabulary and baseline tools within the standard library.

[PEP 3107](https://www.python.org/dev/peps/pep-3107)引入了函数注解的语法，但语义却故意没有被定义。现在已经有足够多的第三方使用静态类型分析，社区将从标准词汇和标准库中的基线工具中受益。

This PEP introduces a provisional module to provide these standard definitions and tools, along with some conventions for situations where annotations are not available.

本PEP介绍了一个临时模块，以提供这些标准的定义和工具，以及一些针对没有注释的情况的约定。

Note that this PEP still explicitly does NOT prevent other uses of annotations, nor does it require (or forbid) any particular processing of annotations, even when they conform to this specification. It simply enables better coordination, as [PEP 333](https://www.python.org/dev/peps/pep-0333) did for web frameworks.

请注意，这个PEP仍然明确地没有阻止注解的其他用途，也没有要求（或禁止）对注解的任何特定处理，即使它们符合这个规范。它只是实现了更好的协调，就像PEP 333对网络框架所做的那样。

For example, here is a simple function whose argument and return type are declared in the annotations:

例如，这里有一个简单的函数，其参数和返回类型在注释中被声明：

```python
def greeting(name: str) -> str:
    return 'Hello ' + name
```

While these annotations are available at runtime through the usual `__annotations__` attribute, *no type checking happens at runtime*. Instead, the proposal assumes the existence of a separate off-line type checker which users can run over their source code voluntarily. Essentially, such a type checker acts as a very powerful linter. (While it would of course be possible for individual users to employ a similar checker at run time for Design By Contract enforcement or JIT optimization, those tools are not yet as mature.)

虽然这些注解在运行时可以通过通常的`__annotations__`属性获得，但*在运行时不会发生类型检查*。相反，该建议假定存在一个单独的离线类型检查器，用户可以自愿地在他们的源代码上运行。从本质上讲，这样的类型检查器充当了一个非常强大的linter。(当然，个别用户有可能在运行时使用类似的检查器来执行契约设计或JIT优化，但这些工具还没有成熟。)

> linter指语法检查工具，可以参考[**程序开发中的linter是什么意思？**](https://www.zhihu.com/question/28421865)中的回答，译者注。

The proposal is strongly inspired by mypy [[mypy\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#mypy). For example, the type "sequence of integers" can be written as `Sequence[int]`. The square brackets mean that no new syntax needs to be added to the language. The example here uses a custom type `Sequence`, imported from a pure-Python module `typing`. The `Sequence[int]` notation works at runtime by implementing `__getitem__()` in the metaclass (but its significance is primarily to an offline type checker).

这个提议受到mypy[mypy]的强烈启发。例如，"整数序列 "这个类型可以写成`Sequence[int]`。方括号意味着不需要在语言中添加新的语法。这里的例子使用了一个自定义的类型`Sequence`，从一个纯Python模块`typing`中导入。`Sequence[int]` 符号通过在元类中实现 `__getitem__()` 在运行时起作用（但它的意义主要是对离线类型检查器而言）。

The type system supports unions, generic types, and a special type named `Any` which is consistent with (i.e. assignable to and from) all types. This latter feature is taken from the idea of gradual typing. Gradual typing and the full type system are explained in [PEP 483](https://www.python.org/dev/peps/pep-0483).

该类型系统支持联合体、通用类型和一个名为`Any`的特殊类型，它与所有类型一致（即可分配给所有类型）。后者的特征来自于渐进式类型的概念。渐进式类型和完整的类型系统在PEP 483中解释。

Other approaches from which we have borrowed or to which ours can be compared and contrasted are described in [PEP 482](https://www.python.org/dev/peps/pep-0482).

PEP482中描述了我们借鉴的其他方法，或者我们的方法可以与之进行比较和对照。

# [Rationale and Goals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id11)

理由和目标

[PEP 3107](https://www.python.org/dev/peps/pep-3107) added support for arbitrary annotations on parts of a function definition. Although no meaning was assigned to annotations then, there has always been an implicit goal to use them for type hinting [[gvr-artima\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#gvr-artima), which is listed as the first possible use case in said PEP.

[PEP 3107](https://www.python.org/dev/peps/pep-3107) 增加了对函数定义部分的任意注释的支持。虽然当时没有给注解赋予任何意义，但一直以来都有一个隐含的目标，那就是将它们用于类型提示[gvr-artima]，这在上述PEP中被列为第一个可能的用例。

This PEP aims to provide a standard syntax for type annotations, opening up Python code to easier static analysis and refactoring, potential runtime type checking, and (perhaps, in some contexts) code generation utilizing type information.

这个 PEP 旨在为类型注释提供一个标准的语法，使 Python 代码更容易进行静态分析和重构，潜在的运行时类型检查，以及 (也许，在某些情况下) 利用类型信息生成代码。

Of these goals, static analysis is the most important. This includes support for off-line type checkers such as mypy, as well as providing a standard notation that can be used by IDEs for code completion and refactoring.

在这些目标中，静态分析是最重要的。这包括对离线类型检查器的支持，如mypy，以及提供一个标准的符号，可以被IDE用于代码完成和重构。

## [Non-goals](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id12)

非目标

While the proposed typing module will contain some building blocks for runtime type checking -- in particular the `get_type_hints()` function -- third party packages would have to be developed to implement specific runtime type checking functionality, for example using decorators or metaclasses. Using type hints for performance optimizations is left as an exercise for the reader.

虽然提议的类型化模块将包含一些运行时类型检查的构件--特别是`get_type_hints()`函数--但第三方软件包将必须被开发以实现特定的运行时类型检查功能，例如使用装饰器或元类。使用类型提示进行性能优化是留给读者的一个练习。

It should also be emphasized that **Python will remain a dynamically typed language, and the authors have no desire to ever make type hints mandatory, even by convention.**

还应该强调的是，**Python 仍将是一种动态类型的语言，作者并不想让类型提示成为强制性的，即使是按照惯例。**

# [The meaning of annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id13)

注释的意义

Any function without annotations should be treated as having the most general type possible, or ignored, by any type checker. Functions with the `@no_type_check` decorator should be treated as having no annotations.

任何没有注解的函数都应该被视为具有最一般的类型，或者被任何类型检查器所忽略。带有`@no_type_check`装饰器的函数应该被视为没有注释。

It is recommended but not required that checked functions have annotations for all arguments and the return type. For a checked function, the default annotation for arguments and for the return type is `Any`. An exception is the first argument of instance and class methods. If it is not annotated, then it is assumed to have the type of the containing class for instance methods, and a type object type corresponding to the containing class object for class methods. For example, in class `A` the first argument of an instance method has the implicit type `A`. In a class method, the precise type of the first argument cannot be represented using the available type notation.

建议但不要求被检查的函数对所有参数和返回类型进行注释。对于一个被检查的函数，参数和返回类型的默认注解是`Any`。异常是实例和类方法的第一个参数。如果它没有被注解，那么对于实例方法来说，它被认为具有包含类的类型，而对于类方法来说，它具有与包含类对象相对应的类型。例如，在类`A`中，实例方法的第一个参数具有隐含的类型`A`。在类方法中，第一个参数的精确类型不能用可用的类型符号表示。

(Note that the return type of `__init__` ought to be annotated with `-> None`. The reason for this is subtle. If `__init__` assumed a return annotation of `-> None`, would that mean that an argument-less, un-annotated `__init__` method should still be type-checked? Rather than leaving this ambiguous or introducing an exception to the exception, we simply say that `__init__` ought to have a return annotation; the default behavior is thus the same as for other methods.)

(注意 `__init__` 的返回类型应该被注解为 `-> None`。这样做的原因很微妙。如果 `__init__` 的返回注释是 `-> None`，这是否意味着一个没有参数的、没有注释的 `__init__` 方法仍然应该被类型检查？我们没有让这个问题含糊不清或引入一个例外，而是简单地说 `__init__` 应该有一个返回注解；因此默认行为与其他方法相同)。

A type checker is expected to check the body of a checked function for consistency with the given annotations. The annotations may also be used to check correctness of calls appearing in other checked functions.

类型检查器应该检查被检查函数的主体是否与给定的注解一致。注释也可以用来检查其他被检查函数中出现的调用的正确性。

Type checkers are expected to attempt to infer as much information as necessary. The minimum requirement is to handle the builtin decorators `@property`, `@staticmethod` and `@classmethod`.

类型检查器被期望尝试推断出尽可能多的必要信息。最低要求是处理内置的装饰器`@property`、`@staticmethod`和`@classmethod`。

# [Type Definition Syntax](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id14)

类型定义语法

The syntax leverages [PEP 3107](https://www.python.org/dev/peps/pep-3107)-style annotations with a number of extensions described in sections below. In its basic form, type hinting is used by filling function annotation slots with classes:

该语法利用了PEP 3107风格的注释，并在下面的章节中描述了一些扩展。在其基本形式中，类型提示是通过用类来填充函数注解槽来使用的：

```
def greeting(name: str) -> str:
    return 'Hello ' + name
```

This states that the expected type of the `name` argument is `str`. Analogically, the expected return type is `str`.

这说明`name`参数的预期类型是`str`。类似地，期望的返回类型是`str`。

Expressions whose type is a subtype of a specific argument type are also accepted for that argument.

类型为特定参数类型的子类型的表达式也被接受用于该参数。

## [Acceptable type hints](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id15)

可接受的类型提示

Type hints may be built-in classes (including those defined in standard library or third-party extension modules), abstract base classes, types available in the `types` module, and user-defined classes (including those defined in the standard library or third-party modules).

类型提示可以是内置类（包括那些在标准库或第三方扩展模块中定义的类）、抽象基类、`types`模块中可用的类型，以及用户定义的类（包括那些在标准库或第三方模块中定义的类）。

While annotations are normally the best format for type hints, there are times when it is more appropriate to represent them by a special comment, or in a separately distributed stub file. (See below for examples.)

虽然注释通常是类型提示的最佳格式，但有些时候，用一个特殊的注释，或在一个单独分发的存根文件中表示它们更为合适。(见下面的例子)。

Annotations must be valid expressions that evaluate without raising exceptions at the time the function is defined (but see below for forward references).

注释必须是有效的表达式，在定义函数时不会产生异常（但见下文的前向引用）。

Annotations should be kept simple or static analysis tools may not be able to interpret the values. For example, dynamically computed types are unlikely to be understood. (This is an intentionally somewhat vague requirement, specific inclusions and exclusions may be added to future versions of this PEP as warranted by the discussion.)

注释应该保持简单，否则静态分析工具可能无法解释这些值。例如，动态计算的类型不太可能被理解。这是一个有意的有点模糊的要求，具体的包含和排除可能会根据讨论的需要被添加到本PEP的未来版本中）。

In addition to the above, the following special constructs defined below may be used: `None`, `Any`, `Union`, `Tuple`, `Callable`, all ABCs and stand-ins for concrete classes exported from `typing` (e.g. `Sequence` and `Dict`), type variables, and type aliases.

除了上述之外，还可以使用下面定义的特殊结构体。`None`, `Any`, `Union`, `Tuple`, `Callable`, 所有ABC和从`typing`导出的具体类的替身（例如`Sequence`和`Dict`），类型变量，以及类型别名。

> ABC指抽象基类，译者注。

All newly introduced names used to support features described in following sections (such as `Any` and `Union`) are available in the `typing` module.

所有新引入的用于支持以下章节中描述的功能的名称（如`Any`和`Union`）都可以在`typing`模块中使用。

## [Using None](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id16)

使用None

When used in a type hint, the expression `None` is considered equivalent to `type(None)`.

当在类型提示中使用时，表达式`None`被认为等同于`type(None)`。

## [Type aliases](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id17)

类型别名

Type aliases are defined by simple variable assignments:

类型别名是通过简单的变量赋值来定义的：

```python
Url = str

def retry(url: Url, retry_count: int) -> None: ...
```

Note that we recommend capitalizing alias names, since they represent user-defined types, which (like user-defined classes) are typically spelled that way.

注意，我们建议将别名大写，因为它们代表了用户定义的类型，（就像用户定义的类）通常是这样拼写的。

Type aliases may be as complex as type hints in annotations -- anything that is acceptable as a type hint is acceptable in a type alias:

类型别名可以像注解中的类型提示一样复杂--任何可以作为类型提示的东西在类型别名中都可以接受：

```python
from typing import TypeVar, Iterable, Tuple

T = TypeVar('T', int, float, complex)
Vector = Iterable[Tuple[T, T]]

def inproduct(v: Vector[T]) -> T:
    return sum(x*y for x, y in v)
def dilate(v: Vector[T], scale: T) -> Vector[T]:
    return ((x * scale, y * scale) for x, y in v)
vec = []  # type: Vector[float]
```

This is equivalent to:

这等价于：

```python
from typing import TypeVar, Iterable, Tuple

T = TypeVar('T', int, float, complex)

def inproduct(v: Iterable[Tuple[T, T]]) -> T:
    return sum(x*y for x, y in v)
def dilate(v: Iterable[Tuple[T, T]], scale: T) -> Iterable[Tuple[T, T]]:
    return ((x * scale, y * scale) for x, y in v)
vec = []  # type: Iterable[Tuple[float, float]]
```

## [Callable](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id18)

Frameworks expecting callback functions of specific signatures might be type hinted using `Callable[[Arg1Type, Arg2Type], ReturnType]`. Examples:

框架期待特定签名的回调函数可以使用 `Callable[[Arg1Type, Arg2Type], ReturnType]` 的方式进行类型提示。举例来说：

```python
from typing import Callable

def feeder(get_next_item: Callable[[], str]) -> None:
    # Body

def async_query(on_success: Callable[[int], None],
                on_error: Callable[[int, Exception], None]) -> None:
    # Body
```

It is possible to declare the return type of a callable without specifying the call signature by substituting a literal ellipsis (three dots) for the list of arguments:

通过用省略号（三个点）代替参数列表，可以在不指定调用签名的情况下声明一个可调用的返回类型：

```python
def partial(func: Callable[..., str], *args) -> Callable[..., str]:
    # Body
```

Note that there are no square brackets around the ellipsis. The arguments of the callback are completely unconstrained in this case (and keyword arguments are acceptable).

注意，省略号周围没有方括号。在这种情况下，回调的参数是完全不受限制的（关键字参数也是可以接受的）。

Since using callbacks with keyword arguments is not perceived as a common use case, there is currently no support for specifying keyword arguments with `Callable`. Similarly, there is no support for specifying callback signatures with a variable number of arguments of a specific type.

由于使用带有关键字参数的回调不被认为是一种常见的使用情况，目前不支持用`Callable`指定关键字参数。同样地，也不支持用特定类型的可变数量的参数来指定回调签名。

Because `typing.Callable` does double-duty as a replacement for `collections.abc.Callable`, `isinstance(x, typing.Callable)` is implemented by deferring to `isinstance(x, collections.abc.Callable)`. However, `isinstance(x, typing.Callable[...])` is not supported.

因为`typing.Callable`具有替代`collection.abc.Callable`的双重功能，所以`isinstance(x, typing.Callable)`是按照`isinstance(x, collections.abc.Callable)`来实现的。然而，`isinstance(x, typing.Callable[...])`并不被支持。

## [Generics](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id19)

泛型

Since type information about objects kept in containers cannot be statically inferred in a generic way, abstract base classes have been extended to support subscription to denote expected types for container elements. Example:

由于保存在容器中的对象的类型信息不能以通用的方式静态推断，抽象基类已经被扩展，以支持订阅来表示容器元素的预期类型。比如：

```python
from typing import Mapping, Set

def notify_by_email(employees: Set[Employee], overrides: Mapping[str, str]) -> None: ...
```

Generics can be parameterized by using a new factory available in `typing` called `TypeVar`. Example:

泛型可以通过使用`typing`中新的工厂函数`TypeVar`来进行参数化。比如：

```python
from typing import Sequence, TypeVar

T = TypeVar('T')      # Declare type variable

def first(l: Sequence[T]) -> T:   # Generic function
    return l[0]
```

In this case the contract is that the returned value is consistent with the elements held by the collection.

在这种情况下，约定是返回的值与集合所持有的元素一致。

A `TypeVar()` expression must always directly be assigned to a variable (it should not be used as part of a larger expression). The argument to `TypeVar()` must be a string equal to the variable name to which it is assigned. Type variables must not be redefined.

`TypeVar()`表达式必须总是直接分配给一个变量（它不应该被用作更大表达式的一部分）。`TypeVar()`的参数必须是一个字符串，等于它被分配到的变量名称。类型变量不能被重新定义。

> 这里的`TypeVar`的作用类似于类工厂函数，可以阅读[**Python学习笔记38：类元编程**](https://blog.icexmoon.xyz/archives/161.html)进行了解，译者注。

`TypeVar` supports constraining parametric types to a fixed set of possible types (note: those types cannot be parameterized by type variables). For example, we can define a type variable that ranges over just `str` and `bytes`. By default, a type variable ranges over all possible types. Example of constraining a type variable:

`TypeVar`支持将参数类型限制在一组固定的可能类型中（注意：这些类型不能被类型变量所参数化）。例如，我们可以定义一个类型变量，其范围只有`str`和`bytes`。默认情况下，一个类型变量的范围是所有可能的类型。限制类型变量的例子：

```python
from typing import TypeVar, Text

AnyStr = TypeVar('AnyStr', Text, bytes)

def concat(x: AnyStr, y: AnyStr) -> AnyStr:
    return x + y
```

The function `concat` can be called with either two `str` arguments or two `bytes` arguments, but not with a mix of `str` and `bytes` arguments.

函数`concat`可以用两个`str`参数或两个`bytes`参数来调用，但不能用`str`和`bytes`的混合参数。

There should be at least two constraints, if any; specifying a single constraint is disallowed.

如果有的话，至少应该有两个约束，指定一个约束是不允许的。

Subtypes of types constrained by a type variable should be treated as their respective explicitly listed base types in the context of the type variable. Consider this example:

在类型变量的上下文中，由类型变量约束的类型的子类型应该被视为它们各自明确列出的基本类型。考虑一下这个例子：

```python
class MyStr(str): ...

x = concat(MyStr('apple'), MyStr('pie'))
```

The call is valid but the type variable `AnyStr` will be set to `str` and not `MyStr`. In effect, the inferred type of the return value assigned to `x` will also be `str`.

这个调用是有效的，但是类型变量`AnyStr`将被设置为`str`而不是`MyStr`。实际上，分配给`x`的返回值的推断类型也将是`str`。

Additionally, `Any` is a valid value for every type variable. Consider the following:

此外，`Any`是每个类型变量的一个有效值。考虑一下下面的情况：

```python
def count_truthy(elements: List[Any]) -> int:
    return sum(1 for elem in elements if elem)
```

This is equivalent to omitting the generic notation and just saying `elements: List`.

这相当于省略了通用符号，只说`elements:List`。

## [User-defined generic types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id20)

用户自定义泛型

You can include a `Generic` base class to define a user-defined class as generic. Example:

你可以包含一个泛型基类来定义一个用户定义的类为泛型。例子：

```python
from typing import TypeVar, Generic
from logging import Logger

T = TypeVar('T')

class LoggedVar(Generic[T]):
    def __init__(self, value: T, name: str, logger: Logger) -> None:
        self.name = name
        self.logger = logger
        self.value = value

    def set(self, new: T) -> None:
        self.log('Set ' + repr(self.value))
        self.value = new

    def get(self) -> T:
        self.log('Get ' + repr(self.value))
        return self.value

    def log(self, message: str) -> None:
        self.logger.info('{}: {}'.format(self.name, message))
```

`Generic[T]` as a base class defines that the class `LoggedVar` takes a single type parameter `T`. This also makes `T` valid as a type within the class body.

`Generic[T]`作为一个基类，定义了`LoggedVar`类只接受一个类型参数`T`，这也使得`T`在类体内作为一个类型有效。

The `Generic` base class uses a metaclass that defines `__getitem__` so that `LoggedVar[t]` is valid as a type:

`Generic`基类使用了一个定义了`__getitem__`的元类，所以`LoggedVar[t]`作为一个类型是有效的：

```python
from typing import Iterable

def zero_all_vars(vars: Iterable[LoggedVar[int]]) -> None:
    for var in vars:
        var.set(0)
```

A generic type can have any number of type variables, and type variables may be constrained. This is valid:

一个通用类型可以有任意数量的类型变量，而且类型变量可以被约束。这一点是有效的：

```python
from typing import TypeVar, Generic
...

T = TypeVar('T')
S = TypeVar('S')

class Pair(Generic[T, S]):
    ...
```

Each type variable argument to `Generic` must be distinct. This is thus invalid:

对`Generic`的每个类型变量参数必须是不同的。因此这是无效的：

```python
from typing import TypeVar, Generic
...

T = TypeVar('T')

class Pair(Generic[T, T]):   # INVALID
    ...
```

The `Generic[T]` base class is redundant in simple cases where you subclass some other generic class and specify type variables for its parameters:

在简单的情况下，使用`Generic[T]`基类是多余的，你可以将其他一些泛型类进行子类化，并为其参数指定类型变量：

```python
from typing import TypeVar, Iterator

T = TypeVar('T')

class MyIter(Iterator[T]):
    ...
```

That class definition is equivalent to:

该类定义等同于：

```python
class MyIter(Iterator[T], Generic[T]):
    ...
```

You can use multiple inheritance with `Generic`:

你可以用`Generic`使用多个继承：

```python
from typing import TypeVar, Generic, Sized, Iterable, Container, Tuple

T = TypeVar('T')

class LinkedList(Sized, Generic[T]):
    ...

K = TypeVar('K')
V = TypeVar('V')

class MyMapping(Iterable[Tuple[K, V]],
                Container[Tuple[K, V]],
                Generic[K, V]):
    ...
```

Subclassing a generic class without specifying type parameters assumes `Any` for each position. In the following example, `MyIterable` is not generic but implicitly inherits from `Iterable[Any]`:

不指定类型参数的泛型类的子类，假设每个位置都是`Any`。在下面的例子中，`MyIterable`不是泛型的，而是隐含地继承于`Iterable[Any]`。

```python
from typing import Iterable

class MyIterable(Iterable):  # Same as Iterable[Any]
    ...
```

Generic metaclasses are not supported.

不支持泛型的元类。

## [Scoping rules for type variables](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id21)

类型变量的范围界定规则

Type variables follow normal name resolution rules. However, there are some special cases in the static typechecking context:

类型变量遵循正常的名称解析规则。然而，在静态类型检查的背景下，有一些特殊情况：

- A type variable used in a generic function could be inferred to represent different types in the same code block. Example:

  在普通函数中使用的类型变量可以被推断为在同一个代码块中代表不同的类型。例子：

  ```python
  from typing import TypeVar, Generic
  
  T = TypeVar('T')
  
  def fun_1(x: T) -> T: ...  # T here
  def fun_2(x: T) -> T: ...  # and here could be different
  
  fun_1(1)                   # This is OK, T is inferred to be int
  fun_2('a')                 # This is also OK, now T is str
  ```

- A type variable used in a method of a generic class that coincides with one of the variables that parameterize this class is always bound to that variable. Example:

  在泛型类的方法中使用的类型变量，如果与该类的参数化变量之一重合，则总是与该变量绑定。例子：

  ```python
  from typing import TypeVar, Generic
  
  T = TypeVar('T')
  
  class MyClass(Generic[T]):
      def meth_1(self, x: T) -> T: ...  # T here
      def meth_2(self, x: T) -> T: ...  # and here are always the same
  
  a = MyClass()  # type: MyClass[int]
  a.meth_1(1)    # OK
  a.meth_2('a')  # This is an error!
  ```

- A type variable used in a method that does not match any of the variables that parameterize the class makes this method a generic function in that variable:

  在一个方法中使用的类型变量，如果不匹配任何对类进行参数化的变量，那么这个方法在该变量中就是一个通用函数：

  > 这里的意思应该是没有通过继承泛型进行类型绑定的类型变量，其行为就像普通函数中的类型变量一样，会在具体使用中进行临时绑定，不会被实例所限制，译者注。

  ```python
  T = TypeVar('T')
  S = TypeVar('S')
  class Foo(Generic[T]):
      def method(self, x: T, y: S) -> S:
          ...
  
  x = Foo()               # type: Foo[int]
  y = x.method(0, "abc")  # inferred type of y is str
  ```

- Unbound type variables should not appear in the bodies of generic functions, or in the class bodies apart from method definitions:

  未绑定的类型变量不应该出现在泛型函数的主体中，也不应该出现在除了方法定义之外的类主体中。

  ```python
  T = TypeVar('T')
  S = TypeVar('S')
  
  def a_fun(x: T) -> None:
      # this is OK
      y = []  # type: List[T]
      # but below is an error!
      y = []  # type: List[S]
  
  class Bar(Generic[T]):
      # this is also an error
      an_attr = []  # type: List[S]
  
      def do_something(x: S) -> S:  # this is OK though
          ...
  ```

- A generic class definition that appears inside a generic function should not use type variables that parameterize the generic function:

  出现在泛型函数内的泛型类定义不应该使用给泛型函数设置参数的类型变量。

  ```python
  from typing import List
  
  def a_fun(x: T) -> None:
  
      # This is OK
      a_list = []  # type: List[T]
      ...
  
      # This is however illegal
      class MyGeneric(Generic[T]):
          ...
  ```

- A generic class nested in another generic class cannot use same type variables. The scope of the type variables of the outer class doesn't cover the inner one:

  一个嵌套在另一个泛型类中的泛型类不能使用相同的类型变量。外层类的类型变量的范围并不包括内层类：

  ```python
  T = TypeVar('T')
  S = TypeVar('S')
  
  class Outer(Generic[T]):
      class Bad(Iterable[T]):       # Error
          ...
      class AlsoBad:
          x = None  # type: List[T] # Also an error
  
      class Inner(Iterable[S]):     # OK
          ...
      attr = None  # type: Inner[T] # Also OK
  ```

## [Instantiating generic classes and type erasure](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id22)

实例化泛型类和类型擦除

User-defined generic classes can be instantiated. Suppose we write a `Node` class inheriting from `Generic[T]`:

用户定义的泛型类可以被实例化。假设我们写一个继承自`Generic[T]`的Node类。

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Node(Generic[T]):
    ...
```

To create `Node` instances you call `Node()` just as for a regular class. At runtime the type (class) of the instance will be `Node`. But what type does it have to the type checker? The answer depends on how much information is available in the call. If the constructor (`__init__` or `__new__`) uses `T` in its signature, and a corresponding argument value is passed, the type of the corresponding argument(s) is substituted. Otherwise, `Any` is assumed. Example:

要创建 `Node`实例，你需要调用 `Node()`，就像对一个普通的类一样。在运行时，实例的类型（类）将是`Node`。但是对于类型检查器来说，它有什么类型呢？答案取决于在调用时有多少信息可用。如果构造函数（`__init__`或`__new__`）在其签名中使用了`T`，并且传递了相应的参数值，那么相应参数的类型将被替换。否则，假定为 `Any`。例子。

```python
from typing import TypeVar, Generic

T = TypeVar('T')

class Node(Generic[T]):
    x = None  # type: T # Instance attribute (see below)
    def __init__(self, label: T = None) -> None:
        ...

x = Node('')  # Inferred type is Node[str]
y = Node(0)   # Inferred type is Node[int]
z = Node()    # Inferred type is Node[Any]
```

In case the inferred type uses `[Any]` but the intended type is more specific, you can use a type comment (see below) to force the type of the variable, e.g.:

如果推断的类型使用[Any]，但预期的类型更具体，你可以使用类型注释（见下文）来强制指定变量的类型，例如：

```python
# (continued from previous example)
a = Node()  # type: Node[int]
b = Node()  # type: Node[str]
```

Alternatively, you can instantiate a specific concrete type, e.g.:

或者，你可以实例化一个特定的具体类型，例如：

```python
# (continued from previous example)
p = Node[int]()
q = Node[str]()
r = Node[int]('')  # Error
s = Node[str](0)   # Error
```

Note that the runtime type (class) of `p` and `q` is still just `Node` -- `Node[int]` and `Node[str]` are distinguishable class objects, but the runtime class of the objects created by instantiating them doesn't record the distinction. This behavior is called "type erasure"; it is common practice in languages with generics (e.g. Java, TypeScript).

请注意，`p`和`q`的运行时类型（类）仍然只是`Node`--`Node[int]`和`Node[str]`是可区分的类对象，但通过实例化创建的对象的运行时类并不记录这种区别。这种行为被称为 "类型清除"；它是具有泛型的语言（如Java、TypeScript）中的常见做法。

Using generic classes (parameterized or not) to access attributes will result in type check failure. Outside the class definition body, a class attribute cannot be assigned, and can only be looked up by accessing it through a class instance that does not have an instance attribute with the same name:

使用泛型类（无论是否参数化）来访问属性将导致类型检查失败。在类定义体之外，一个类的属性不能被赋值，只能通过一个没有同名实例属性的类实例来访问它，才能查询到：

> 这是因为泛型是在实例创建时进行的类型绑定，所以不能不使用实例，直接使用类的方式访问具有泛型的类属性。而所谓的没有同名实例属性，值的是如果实例创建了一个与类属性同名的属性，会实现“访问屏蔽”。如果没有，则会正常访问类属性。译者注。

```python
# (continued from previous example)
Node[int].x = 1  # Error
Node[int].x      # Error
Node.x = 1       # Error
Node.x           # Error
type(p).x        # Error
p.x              # Ok (evaluates to None)
Node[int]().x    # Ok (evaluates to None)
p.x = 1          # Ok, but assigning to instance attribute
```

Generic versions of abstract collections like `Mapping` or `Sequence` and generic versions of built-in classes -- `List`, `Dict`, `Set`, and `FrozenSet` -- cannot be instantiated. However, concrete user-defined subclasses thereof and generic versions of concrete collections can be instantiated:

像`Mapping`或`Sequence`这样的抽象集合的泛型版本和内置类的泛型版本--`List`、`Dict`、`Set`和`FrozenSet`--不能被实例化。然而，具体的用户定义的子类和具体集合的通用版本可以被实例化。

```python
data = DefaultDict[int, bytes]()
```

Note that one should not confuse static types and runtime classes. The type is still erased in this case and the above expression is just a shorthand for:

注意，不应该混淆静态类型和运行时类。在这种情况下，类型仍然被擦除，上述表达式只是一种速记。

```python
data = collections.defaultdict()  # type: DefaultDict[int, bytes]
```

It is not recommended to use the subscripted class (e.g. `Node[int]`) directly in an expression -- using a type alias (e.g. `IntNode = Node[int]`) instead is preferred. (First, creating the subscripted class, e.g. `Node[int]`, has a runtime cost. Second, using a type alias is more readable.)

不建议在表达式中直接使用下标类（例如：`Node[int]`）--最好使用类型别名（例如：`IntNode = Node[int]`）。首先，创建下标类，例如`Node[int]`，有一个运行时间成本。其次，使用类型别名更容易阅读）。)

## [Arbitrary generic types as base classes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id23)

任意泛型作为基类

`Generic[T]` is only valid as a base class -- it's not a proper type. However, user-defined generic types such as `LinkedList[T]` from the above example and built-in generic types and ABCs such as `List[T]` and `Iterable[T]` are valid both as types and as base classes. For example, we can define a subclass of `Dict` that specializes type arguments:

`Generic[T]`只作为基类有效--它不是一个合适的类型。然而，用户定义的泛型，如上面例子中的`LinkedList[T]`，以及内置的泛型和ABC，如`List[T]`和`Iterable[T]`，作为类型和基类都有效。例如，我们可以定义一个`Dict`的子类，专门处理类型参数：

```python
from typing import Dict, List, Optional

class Node:
    ...

class SymbolTable(Dict[str, List[Node]]):
    def push(self, name: str, node: Node) -> None:
        self.setdefault(name, []).append(node)

    def pop(self, name: str) -> Node:
        return self[name].pop()

    def lookup(self, name: str) -> Optional[Node]:
        nodes = self.get(name)
        if nodes:
            return nodes[-1]
        return None
```

`SymbolTable` is a subclass of `dict` and a subtype of `Dict[str, List[Node]]`.

`SymbolTable` 是`dict`的一个子类，是`Dict[str, List[Node]]`的一个子类型。

If a generic base class has a type variable as a type argument, this makes the defined class generic. For example, we can define a generic `LinkedList` class that is iterable and a container:

如果一个泛型基类有一个类型变量作为类型参数，这使得定义的类成为泛型。例如，我们可以定义一个泛型的`LinkedList`类，它是可迭代的，是一个容器：

```python
from typing import TypeVar, Iterable, Container

T = TypeVar('T')

class LinkedList(Iterable[T], Container[T]):
    ...
```

Now `LinkedList[int]` is a valid type. Note that we can use `T` multiple times in the base class list, as long as we don't use the same type variable `T` multiple times within `Generic[...]`.

现在`LinkedList[int]`是一个有效的类型。注意，我们可以在基类`list`中多次使用`T`，只要我们不在`Generic[...]`中多次使用同一个类型变量T。

Also consider the following example:

还可以考虑以下例子：

```python
from typing import TypeVar, Mapping

T = TypeVar('T')

class MyDict(Mapping[str, T]):
    ...
```

In this case MyDict has a single parameter, T.

在这种情况下，MyDict有一个单一的参数，即T。

## [Abstract generic types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id24)

抽象泛型

The metaclass used by `Generic` is a subclass of `abc.ABCMeta`. A generic class can be an ABC by including abstract methods or properties, and generic classes can also have ABCs as base classes without a metaclass conflict.

`Generic`使用的元类是`abc.ABCMeta`的子类。一个泛型类可以通过包含抽象方法或属性来成为一个ABC，泛型类也可以将ABC作为基类，而不会发生元类冲突。

## [Type variables with an upper bound](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id25)

有上限的类型变量

A type variable may specify an upper bound using `bound=<type>` (note: <type> itself cannot be parameterized by type variables). This means that an actual type substituted (explicitly or implicitly) for the type variable must be a subtype of the boundary type. Example:

一个类型变量可以使用`bound=<type>`来指定一个上界（注意：<type>本身不能被类型变量所参数化）。这意味着类型变量所替代的实际类型（显式或隐式）必须是边界类型的一个子类型。例子：

```python
from typing import TypeVar, Sized

ST = TypeVar('ST', bound=Sized)

def longer(x: ST, y: ST) -> ST:
    if len(x) > len(y):
        return x
    else:
        return y

longer([1], [1, 2])  # ok, return type List[int]
longer({1}, {1, 2})  # ok, return type Set[int]
longer([1], {1, 2})  # ok, return type Collection[int]
```

An upper bound cannot be combined with type constraints (as in used `AnyStr`, see the example earlier); type constraints cause the inferred type to be _exactly_ one of the constraint types, while an upper bound just requires that the actual type is a subtype of the boundary type.

上限不能与类型约束相结合（如使用`AnyStr`，见前面的例子）；类型约束导致推断的类型是*刚好是*约束类型之一，而上限只要求实际类型是边界类型的一个子类型。

## [Covariance and contravariance](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id26)

协变和反协变

Consider a class `Employee` with a subclass `Manager`. Now suppose we have a function with an argument annotated with `List[Employee]`. Should we be allowed to call this function with a variable of type `List[Manager]` as its argument? Many people would answer "yes, of course" without even considering the consequences. But unless we know more about the function, a type checker should reject such a call: the function might append an `Employee` instance to the list, which would violate the variable's type in the caller.

考虑一个带有子类`Manager`的`Employee`类。现在假设我们有一个函数，它的参数被注解为`List[Employee]`。我们是否应该允许用`List[Manager]`类型的变量作为参数来调用这个函数？很多人会回答 "是的，当然"，甚至不考虑其后果。但除非我们对该函数有更多的了解，否则类型检查器应该拒绝这样的调用：该函数可能会将一个`Employee`实例追加到列表中，这将违反调用者的变量类型。

It turns out such an argument acts *contravariantly*, whereas the intuitive answer (which is correct in case the function doesn't mutate its argument!) requires the argument to act *covariantly*. A longer introduction to these concepts can be found on Wikipedia [[wiki-variance\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#wiki-variance) and in [PEP 483](https://www.python.org/dev/peps/pep-0483); here we just show how to control a type checker's behavior.

事实证明，这样的参数是变异的，而直观的答案（在函数没有变异其参数的情况下是正确的！）要求参数是协变的。关于这些概念的详细介绍可以在维基百科[wiki-variance]和PEP 483中找到；这里我们只是展示如何控制类型检查器的行为。

By default generic types are considered *invariant* in all type variables, which means that values for variables annotated with types like `List[Employee]` must exactly match the type annotation -- no subclasses or superclasses of the type parameter (in this example `Employee`) are allowed.

默认情况下，泛型在所有类型变量中被认为是不变的，这意味着用`List[Employee]`这样的类型注释的变量的值必须与类型注释完全匹配--不允许类型参数（在这个例子中是`Employee`）的子类或超类。

To facilitate the declaration of container types where covariant or contravariant type checking is acceptable, type variables accept keyword arguments `covariant=True` or `contravariant=True`. At most one of these may be passed. Generic types defined with such variables are considered covariant or contravariant in the corresponding variable. By convention, it is recommended to use names ending in `_co` for type variables defined with `covariant=True` and names ending in `_contra` for that defined with `contravariant=True`.

为了方便容器类型的声明，在可以接受协变或反变类型检查的地方，类型变量接受关键字参数`covariant=True`或`contravariant=True`。这些参数中最多可以有一个被传递。用这种变量定义的泛型在相应的变量中被认为是协变或反协变的。根据惯例，建议使用以`_co`结尾的名字来表示定义为`covariant=True`的类型变量，以`_contra`结尾的名字来表示定义为 `contravariant=True`的类型变量。

A typical example involves defining an immutable (or read-only) container class:

一个典型的例子是定义一个不可变的（或只读的）容器类：

```python
from typing import TypeVar, Generic, Iterable, Iterator

T_co = TypeVar('T_co', covariant=True)

class ImmutableList(Generic[T_co]):
    def __init__(self, items: Iterable[T_co]) -> None: ...
    def __iter__(self) -> Iterator[T_co]: ...
    ...

class Employee: ...

class Manager(Employee): ...

def dump_employees(emps: ImmutableList[Employee]) -> None:
    for emp in emps:
        ...

mgrs = ImmutableList([Manager()])  # type: ImmutableList[Manager]
dump_employees(mgrs)  # OK
```

The read-only collection classes in `typing` are all declared covariant in their type variable (e.g. `Mapping` and `Sequence`). The mutable collection classes (e.g. `MutableMapping` and `MutableSequence`) are declared invariant. The one example of a contravariant type is the `Generator` type, which is contravariant in the `send()` argument type (see below).

`typing`中的只读集合类的类型变量都被声明为可变的（例如`Mapping`和`Sequence`）。可变的集合类（例如`MutableMapping`和`MutableSequence`）被声明为不变的。反协变类型的一个例子是`Generator`类型，它在`send()`参数类型中是反协变的（见下文）。

Note: Covariance or contravariance is *not* a property of a type variable, but a property of a generic class defined using this variable. Variance is only applicable to generic types; generic functions do not have this property. The latter should be defined using only type variables without `covariant` or `contravariant` keyword arguments. For example, the following example is fine:

注意：协变和反协变不是类型变量的属性，而是使用该变量定义的泛型类的属性。协变只适用于泛型，泛型函数不具有这个属性。后者应该只使用没有协变或反协变关键字参数的类型变量来定义。例如，下面的例子就可以：

```python
from typing import TypeVar

class Employee: ...

class Manager(Employee): ...

E = TypeVar('E', bound=Employee)

def dump_employee(e: E) -> None: ...

dump_employee(Manager())  # OK
```

while the following is prohibited:

而下列情况是禁止的：

```python
B_co = TypeVar('B_co', covariant=True)

def bad_func(x: B_co) -> B_co:  # Flagged as error by a type checker
    ...
```

## [The numeric tower](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id27)

数字塔

[PEP 3141](https://www.python.org/dev/peps/pep-3141) defines Python's numeric tower, and the stdlib module `numbers` implements the corresponding ABCs (`Number`, `Complex`, `Real`, `Rational` and `Integral`). There are some issues with these ABCs, but the built-in concrete numeric classes `complex`, `float` and `int` are ubiquitous (especially the latter two :-).

[PEP 3141](https://www.python.org/dev/peps/pep-3141)定义了Python的数字塔，stdlib模块`numbers`实现了相应的ABC(`Number`, `Complex`, `Real`, `Rational` 和`Integral`) 。这些ABC有一些问题，但内置的具体数字类`complex`、`float`和`int`是无处不在的（尤其是后两者:-）。

Rather than requiring that users write `import numbers` and then use `numbers.Float` etc., this PEP proposes a straightforward shortcut that is almost as effective: when an argument is annotated as having type `float`, an argument of type `int` is acceptable; similar, for an argument annotated as having type `complex`, arguments of type `float` or `int` are acceptable. This does not handle classes implementing the corresponding ABCs or the `fractions.Fraction` class, but we believe those use cases are exceedingly rare.

与其要求用户编写`import numbers`，然后使用`numbers.Float`等，本PEP提出了一个几乎同样有效的直接捷径：当一个参数被注释为具有`float`类型时，可以接受`int`类型的参数；类似地，对于一个被注释为具有`complex`类型的参数，可以接受`float`或`int`类型的参数。这并没有处理实现相应ABC的类或`fractions.Fraction`类，但我们相信这些用例是极其罕见的。

## [Forward references](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id28)

前向引用

When a type hint contains names that have not been defined yet, that definition may be expressed as a string literal, to be resolved later.

当一个类型提示包含尚未定义的名称时，该定义可以被表达为一个字符串字面量，以便以后解决。

A situation where this occurs commonly is the definition of a container class, where the class being defined occurs in the signature of some of the methods. For example, the following code (the start of a simple binary tree implementation) does not work:

这种情况通常发生在一个容器类的定义中，被定义的类出现在一些方法的签名中。例如，下面的代码（一个简单的二叉树实现的开始）不起作用。

```python
class Tree:
    def __init__(self, left: Tree, right: Tree):
        self.left = left
        self.right = right
```

To address this, we write:

为了解决这个问题，我们这样写：

```python
class Tree:
    def __init__(self, left: 'Tree', right: 'Tree'):
        self.left = left
        self.right = right
```

The string literal should contain a valid Python expression (i.e., `compile(lit, '', 'eval')` should be a valid code object) and it should evaluate without errors once the module has been fully loaded. The local and global namespace in which it is evaluated should be the same namespaces in which default arguments to the same function would be evaluated.

字符串应该包含一个有效的 Python 表达式 (即，`compile(lit, '', 'eval')` 应该是一个有效的代码对象)，而且一旦模块被完全加载，它的评估应该没有错误。评估它的局部和全局命名空间应该是评估同一函数的默认参数的相同命名空间。

Moreover, the expression should be parseable as a valid type hint, i.e., it is constrained by the rules from the section [Acceptable type hints](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#acceptable-type-hints) above.

此外，该表达式应该可以被解析为有效的类型提示，也就是说，它受到上面 "可接受的类型提示 "一节中规则的约束。

It is allowable to use string literals as *part* of a type hint, for example:

允许使用字符串字面量作为类型提示的一部分，比如说：

```python
class Tree:
    ...
    def leaves(self) -> List['Tree']:
        ...
```

A common use for forward references is when e.g. Django models are needed in the signatures. Typically, each model is in a separate file, and has methods taking arguments whose type involves other models. Because of the way circular imports work in Python, it is often not possible to import all the needed models directly:

前向引用的一个常见用途是在签名中需要Django模型。通常情况下，每个模型都在一个单独的文件中，并且有接收参数的方法，这些参数的类型涉及其他模型。由于 Python 中循环导入的工作方式，通常不可能直接导入所有需要的模型。

```python
# File models/a.py
from models.b import B
class A(Model):
    def foo(self, b: B): ...

# File models/b.py
from models.a import A
class B(Model):
    def bar(self, a: A): ...

# File main.py
from models.a import A
from models.b import B
```

Assuming main is imported first, this will fail with an ImportError at the line `from models.a import A` in models/b.py, which is being imported from models/a.py before a has defined class A. The solution is to switch to module-only imports and reference the models by their _module_._class_ name:

假设main是先导入的，那么在models/b.py中的`from models.a import A`这一行就会出现ImportError，因为在a定义A类之前就已经从models/a.py导入了。解决办法是切换到仅有模块的导入，并通过 _module_._class_ 的名称引用模型。

```python
# File models/a.py
from models import b
class A(Model):
    def foo(self, b: 'b.B'): ...

# File models/b.py
from models import a
class B(Model):
    def bar(self, a: 'a.A'): ...

# File main.py
from models.a import A
from models.b import B
```

## [Union types](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id29)

联合类型

Since accepting a small, limited set of expected types for a single argument is common, there is a new special factory called `Union`. Example:

由于为一个参数接受一个小的、有限的预期类型集是很常见的，所以有一个新的特殊工厂叫做`Union`。例子：

```python
from typing import Union

def handle_employees(e: Union[Employee, Sequence[Employee]]) -> None:
    if isinstance(e, Employee):
        e = [e]
    ...
```

A type factored by `Union[T1, T2, ...]` is a supertype of all types `T1`, `T2`, etc., so that a value that is a member of one of these types is acceptable for an argument annotated by `Union[T1, T2, ...]`.

由`Union[T1, T2, ...]`分解的类型是所有类型`T1`、`T2`等的超类型，因此对于由`Union[T1, T2, ...]`注释的参数来说，属于这些类型中的一个成员的值是可以接受的。

One common case of union types are *optional* types. By default, `None` is an invalid value for any type, unless a default value of `None` has been provided in the function definition. Examples:

联合类型的一个常见情况是*可选*类型。默认情况下，`None`对于任何类型都是一个无效的值，除非在函数定义中提供了`None`的默认值。例子：

```python
def handle_employee(e: Union[Employee, None]) -> None: ...
```

As a shorthand for `Union[T1, None]` you can write `Optional[T1]`; for example, the above is equivalent to:

作为`Union[T1, None]`的缩写，你可以写成`Optional[T1]`；例如，上面相当于：

```python
from typing import Optional

def handle_employee(e: Optional[Employee]) -> None: ...
```

A past version of this PEP allowed type checkers to assume an optional type when the default value is `None`, as in this code:

本PEP过去的一个版本允许类型检查器在默认值为`None`时假设一个可选类型，如这段代码：

```python
def handle_employee(e: Employee = None): ...
```

This would have been treated as equivalent to:

这将被视作等同于：

```python
def handle_employee(e: Optional[Employee] = None) -> None: ...
```

This is no longer the recommended behavior. Type checkers should move towards requiring the optional type to be made explicit.

这已经不再是推荐的行为了。类型检查器应该朝着要求可选类型被明确化的方向发展。

## [Support for singleton types in unions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id30)

union对于单例类型的支持

A singleton instance is frequently used to mark some special condition, in particular in situations where `None` is also a valid value for a variable. Example:

一个单例经常被用来标记一些特殊条件，特别是在 `None`也是一个变量的有效值的情况下。例子：

```python
_empty = object()

def func(x=_empty):
    if x is _empty:  # default argument value
        return 0
    elif x is None:  # argument was provided and it's None
        return 1
    else:
        return x * 2
```

To allow precise typing in such situations, the user should use the `Union` type in conjunction with the `enum.Enum` class provided by the standard library, so that type errors can be caught statically:

为了在这种情况下实现精确类型，用户应该将`Union`类型与标准库提供的`enum.Enum`类结合起来使用，这样就可以静态地捕获类型错误：

```python
from typing import Union
from enum import Enum

class Empty(Enum):
    token = 0
_empty = Empty.token

def func(x: Union[int, None, Empty] = _empty) -> int:

    boom = x * 42  # This fails type check

    if x is _empty:
        return 0
    elif x is None:
        return 1
    else:  # At this point typechecker knows that x can only have type int
        return x * 2
```

Since the subclasses of `Enum` cannot be further subclassed, the type of variable `x` can be statically inferred in all branches of the above example. The same approach is applicable if more than one singleton object is needed: one can use an enumeration that has more than one value:

由于`Enum`的子类不能被进一步子类化，在上述例子的所有分支中，变量`x`的类型可以被静态地推断出来。如果需要一个以上的单例对象，同样的方法也适用：可以使用一个有一个以上值的枚举：

```python
class Reason(Enum):
    timeout = 1
    error = 2

def process(response: Union[str, Reason] = '') -> str:
    if response is Reason.timeout:
        return 'TIMEOUT'
    elif response is Reason.error:
        return 'ERROR'
    else:
        # response can be only str, all other possible values exhausted
        return 'PROCESSED: ' + response
```

## [The `Any` type](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id31)

`Any`类型

A special kind of type is `Any`. Every type is consistent with `Any`. It can be considered a type that has all values and all methods. Note that `Any` and builtin type `object` are completely different.

一种特殊的类型是`Any`。每个类型都与`Any`一致。它可以被认为是一个拥有所有值和所有方法的类型。请注意，`Any`和内置类型`object`是完全不同的。

When the type of a value is `object`, the type checker will reject almost all operations on it, and assigning it to a variable (or using it as a return value) of a more specialized type is a type error. On the other hand, when a value has type `Any`, the type checker will allow all operations on it, and a value of type `Any` can be assigned to a variable (or used as a return value) of a more constrained type.

当一个值的类型是`object`时，类型检查器将拒绝对它的几乎所有操作，把它赋值给一个更具体的类型的变量（或把它作为返回值）是一个类型错误。另一方面，当一个值的类型为`Any`时，类型检查器将允许对它进行所有的操作，并且一个`Any`类型的值可以被分配到一个类型更为严格的变量中（或作为返回值）。

A function parameter without an annotation is assumed to be annotated with `Any`. If a generic type is used without specifying type parameters, they are assumed to be `Any`:

一个没有注释的函数参数被认为是用`Any`注释的。如果使用通用类型而不指定类型参数，则假定它们是`Any`：

```python
from typing import Mapping

def use_map(m: Mapping) -> None:  # Same as Mapping[Any, Any]
    ...
```

This rule also applies to `Tuple`, in annotation context it is equivalent to `Tuple[Any, ...]` and, in turn, to `tuple`. As well, a bare `Callable` in an annotation is equivalent to `Callable[..., Any]` and, in turn, to `collections.abc.Callable`:

这个规则也适用于 `Tuple`，在注解上下文中，它等同于 `Tuple[Any, ...]`，反过来也等同于 `tuple`。同样，注解中的单纯 `Callable` 等同于 `Callable[..., Any]`，反过来也等同于 `collections.abc.Callable`。

```python
from typing import Tuple, List, Callable

def check_args(args: Tuple) -> bool:
    ...

check_args(())           # OK
check_args((42, 'abc'))  # Also OK
check_args(3.14)         # Flagged as error by a type checker

# A list of arbitrary callables is accepted by this function
def apply_callbacks(cbs: List[Callable]) -> None:
    ...
```

## [The `NoReturn` type](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id32)

`NoReturn`类型

The `typing` module provides a special type `NoReturn` to annotate functions that never return normally. For example, a function that unconditionally raises an exception:

`typing`模块提供了一个特殊的`NoReturn`类型，用来注释那些从不正常返回的函数。例如，一个无条件地引发异常的函数。

```python
from typing import NoReturn

def stop() -> NoReturn:
    raise RuntimeError('no way')
```

The `NoReturn` annotation is used for functions such as `sys.exit`. Static type checkers will ensure that functions annotated as returning `NoReturn` truly never return, either implicitly or explicitly:

`NoReturn`注解用于诸如`sys.exit`等函数。静态类型检查器将确保被注解为返回`NoReturn`的函数真正不会返回，无论是隐式还是显式。

```python
import sys
from typing import NoReturn

  def f(x: int) -> NoReturn:  # Error, f(0) implicitly returns None
      if x != 0:
          sys.exit(1)
```

> 值得注意的是在Python中，没有显式地使用`return`进行返回，依然被视为`return None`，这和上面演示的“不会正常地返回”的情形是完全不同的，译者注。

The checkers will also recognize that the code after calls to such functions is unreachable and will behave accordingly:

检查器也会识别出调用此类函数后的代码是不可达的，并会有相应的行为：

```python
# continue from first example
def g(x: int) -> int:
    if x > 0:
        return x
    stop()
    return 'whatever works'  # Error might be not reported by some checkers
                             # that ignore errors in unreachable blocks
```

The `NoReturn` type is only valid as a return annotation of functions, and considered an error if it appears in other positions:

`NoReturn`类型只作为函数的返回注释有效，如果它出现在其他位置，则被认为是一个错误。

```python
from typing import List, NoReturn

# All of the following are errors
def bad1(x: NoReturn) -> int:
    ...
bad2 = None  # type: NoReturn
def bad3() -> List[NoReturn]:
    ...
```

## [The type of class objects](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id33)

类对象的类型

Sometimes you want to talk about class objects, in particular class objects that inherit from a given class. This can be spelled as `Type[C]` where `C` is a class. To clarify: while `C` (when used as an annotation) refers to instances of class `C`, `Type[C]` refers to *subclasses* of `C`. (This is a similar distinction as between `object` and `type`.)

有时你想谈论类对象，特别是继承自某一特定类的类对象。这可以拼写成`Type[C]`，其中`C`是一个类。澄清一下：`C`（当作为注释使用时）指的是`C`类的实例，而`Type[C]`指的是`C`的*子类*。这与 `object`和 `type`之间的区别相似）。

For example, suppose we have the following classes:

例如，假设我们有以下的类：

```python
class User: ...  # Abstract base for User classes
class BasicUser(User): ...
class ProUser(User): ...
class TeamUser(User): ...
```

And suppose we have a function that creates an instance of one of these classes if you pass it a class object:

假设我们有一个函数，如果你传给它一个类对象，它就会创建这些类的一个实例。

```python
def new_user(user_class):
    user = user_class()
    # (Here we could write the user object to a database)
    return user
```

Without `Type[]` the best we could do to annotate `new_user()` would be:

如果没有`Type[]`，我们能为`new_user()`做的最好的注释就是。

```python
def new_user(user_class: type) -> User:
    ...
```

However using `Type[]` and a type variable with an upper bound we can do much better:

然而，使用`Type[]`和一个有上限的类型变量，我们可以做得更好：

```python
U = TypeVar('U', bound=User)
def new_user(user_class: Type[U]) -> U:
    ...
```

Now when we call `new_user()` with a specific subclass of `User` a type checker will infer the correct type of the result:

现在，当我们用`User`的特定子类调用`new_user()`时，类型检查器将推断出结果的正确类型。

```python
joe = new_user(BasicUser)  # Inferred type is BasicUser
```

The value corresponding to `Type[C]` must be an actual class object that's a subtype of `C`, not a special form. In other words, in the above example calling e.g. `new_user(Union[BasicUser, ProUser])` is rejected by the type checker (in addition to failing at runtime because you can't instantiate a union).

对应于`Type[C]`的值必须是一个实际的类对象，它是`C`的一个子类型，而不是一个特殊形式。换句话说，在上面的例子中，调用例如`new_user(Union[BasicUser, ProUser])`会被类型检查器拒绝（此外在运行时同样会失败，因为你不能实例化一个联合）。

Note that it is legal to use a union of classes as the parameter for `Type[]`, as in:

注意，使用类的联合作为`Type[]`的参数是合法的，如：

```python
def new_non_team_user(user_class: Type[Union[BasicUser, ProUser]]):
    user = new_user(user_class)
    ...
```

However the actual argument passed in at runtime must still be a concrete class object, e.g. in the above example:

然而在运行时传入的实际参数仍然必须是一个具体的类对象，例如在上面的例子中：

```python
new_non_team_user(ProUser)  # OK
new_non_team_user(TeamUser)  # Disallowed by type checker
```

`Type[Any]` is also supported (see below for its meaning).

也支持 `Type[Any]`（其含义见下文）。

`Type[T]` where `T` is a type variable is allowed when annotating the first argument of a class method (see the relevant section).

当注释类方法的第一个参数时，允许使用`Type[T]`，其中`T`是一个类型变量（见相关章节）。

Any other special constructs like `Tuple` or `Callable` are not allowed as an argument to `Type`.

任何其他的特殊结构如`Tuple`或`Callable`都不允许作为`Type`的参数。

There are some concerns with this feature: for example when `new_user()` calls `user_class()` this implies that all subclasses of `User` must support this in their constructor signature. However this is not unique to `Type[]`: class methods have similar concerns. A type checker ought to flag violations of such assumptions, but by default constructor calls that match the constructor signature in the indicated base class (`User` in the example above) should be allowed. A program containing a complex or extensible class hierarchy might also handle this by using a factory class method. A future revision of this PEP may introduce better ways of dealing with these concerns.

这个特性有一些值得关注的地方：例如，当`new_user()`调用`user_class()`时，这意味着`User`的所有子类必须在其构造函数签名中支持这一点。然而这并不是`Type[]`所独有的：类方法也有类似的问题。类型检查器应该标记出对这种假设的违反，但默认情况下，与指定基类（上例中的`User`）中的构造函数签名相匹配的构造函数调用应该被允许。一个包含复杂或可扩展的类层次结构的程序也可以通过使用工厂类方法来处理这个问题。本PEP的未来修订版可能会引入更好的方法来处理这些问题。

When `Type` is parameterized it requires exactly one parameter. Plain `Type` without brackets is equivalent to `Type[Any]` and this in turn is equivalent to `type` (the root of Python's metaclass hierarchy). This equivalence also motivates the name, `Type`, as opposed to alternatives like `Class` or `SubType`, which were proposed while this feature was under discussion; this is similar to the relationship between e.g. `List` and `list`.

当 `Type` 被参数化时，它正好需要一个参数。没有括号的普通 `Type` 等同于 `Type[Any]`，而这又等同于 `type` (Python 元类层次结构的根)。这种等价关系也是命名 `Type` 的原因，而不是像 `Class` 或 `SubType` 这样的替代品，后者是在讨论这个特性时提出的；这类似于例如 `List` 和 `list` 之间的关系。

Regarding the behavior of `Type[Any]` (or `Type` or `type`), accessing attributes of a variable with this type only provides attributes and methods defined by `type` (for example, `__repr__()` and `__mro__`). Such a variable can be called with arbitrary arguments, and the return type is `Any`.

关于`Type[Any]`（或`Type`或`type`）的行为，访问具有这种类型的变量的属性只提供由`type`定义的属性和方法（例如，`__repr__()`和`__mro__`）。这样的变量可以用任意的参数来调用，并且返回类型是`Any`。

`Type` is covariant in its parameter, because `Type[Derived]` is a subtype of `Type[Base]`:

`Type`在其参数中是协变的，因为`Type[Derived]`是`Type[Base]`的一个子类型：

```python
def new_pro_user(pro_user_class: Type[ProUser]):
    user = new_user(pro_user_class)  # OK
    ...
```

## [Annotating instance and class methods](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id34)

注释实例和类方法

In most cases the first argument of class and instance methods does not need to be annotated, and it is assumed to have the type of the containing class for instance methods, and a type object type corresponding to the containing class object for class methods. In addition, the first argument in an instance method can be annotated with a type variable. In this case the return type may use the same type variable, thus making that method a generic function. For example:

在大多数情况下，类方法和实例方法的第一个参数不需要被注释，对于实例方法，它被假定为具有包含类的类型，对于类方法，具有与包含类对象相对应的类型对象类型。此外，实例方法中的第一个参数可以用一个类型变量来注释。在这种情况下，返回类型可以使用相同的类型变量，从而使该方法成为一个通用函数。比如说：

```python
T = TypeVar('T', bound='Copyable')
class Copyable:
    def copy(self: T) -> T:
        # return a copy of self

class C(Copyable): ...
c = C()
c2 = c.copy()  # type here should be C
```

The same applies to class methods using `Type[]` in an annotation of the first argument:

这同样适用于在第一个参数的注释中使用`Type[]`的类方法：

```python
T = TypeVar('T', bound='C')
class C:
    @classmethod
    def factory(cls: Type[T]) -> T:
        # make a new instance of cls

class D(C): ...
d = D.factory()  # type here should be D
```

Note that some type checkers may apply restrictions on this use, such as requiring an appropriate upper bound for the type variable used (see examples).

请注意，一些类型检查器可能会对这种使用进行限制，例如要求对所使用的类型变量有一个适当的上界（见示例）。

## [Version and platform checking](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id35)

版本和平台检查

Type checkers are expected to understand simple version and platform checks, e.g.:

类型检查器应能理解简单的版本和平台检查，例如：

```python
import sys

if sys.version_info[0] >= 3:
    # Python 3 specific definitions
else:
    # Python 2 specific definitions

if sys.platform == 'win32':
    # Windows specific definitions
else:
    # Posix specific definitions
```

Don't expect a checker to understand obfuscations like `"".join(reversed(sys.platform)) == "xunil"`.

不要指望检查器能理解`"".join(reversed(sys.platform)) == "xunil "`这样的混淆。

## [Runtime or type checking?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id36)

运行时或类型检查

Sometimes there's code that must be seen by a type checker (or other static analysis tools) but should not be executed. For such situations the `typing` module defines a constant, `TYPE_CHECKING`, that is considered `True` during type checking (or other static analysis) but `False` at runtime. Example:

有时，有一些代码必须被类型检查器（或其他静态分析工具）看到，但不应该被执行。对于这种情况，类型化模块定义了一个常数，`TYPE_CHECKING`，在类型检查（或其他静态分析）期间被认为是真，但在运行时是假的。例子。

```python
import typing

if typing.TYPE_CHECKING:
    import expensive_mod

def a_func(arg: 'expensive_mod.SomeClass') -> None:
    a_var = arg  # type: expensive_mod.SomeClass
    ...
```

(Note that the type annotation must be enclosed in quotes, making it a "forward reference", to hide the `expensive_mod` reference from the interpreter runtime. In the `# type` comment no quotes are needed.)

(注意，类型注释必须用引号括起来，使其成为一个 "前向引用"，以便在解释器的运行时隐藏`expensive_mod`引用。在`# type`注释中不需要引号）。)

This approach may also be useful to handle import cycles.

这种方法对于处理导入循环也很有用。

## [Arbitrary argument lists and default argument values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id37)

任意参数列表和默认参数值

Arbitrary argument lists can as well be type annotated, so that the definition:

任意参数列表也可以进行类型注解，因此，定义：

```python
def foo(*args: str, **kwds: int): ...
```

is acceptable and it means that, e.g., all of the following represent function calls with valid types of arguments:

是可以接受的，它意味着，例如，以下所有代表具有有效参数类型的函数调用：

```python
foo('a', 'b', 'c')
foo(x=1, y=2)
foo('', z=0)
```

In the body of function `foo`, the type of variable `args` is deduced as `Tuple[str, ...]` and the type of variable `kwds` is `Dict[str, int]`.

在函数`foo`的主体中，变量`args`的类型被推断为`Tuple[str, ...]`，变量`kwds`的类型为`Dict[str, int]`。

In stubs it may be useful to declare an argument as having a default without specifying the actual default value. For example:

在存根中，声明一个参数具有默认值而不指定实际的默认值可能是有用的。例如：

```python
def foo(x: AnyStr, y: AnyStr = ...) -> AnyStr: ...
```

What should the default value look like? Any of the options `""`, `b""` or `None` fails to satisfy the type constraint.

默认值应该是什么样子的？任何选项`""`、`b""`或`None`都不能满足类型约束。

In such cases the default value may be specified as a literal ellipsis, i.e. the above example is literally what you would write.

在这种情况下，默认值可以被指定为字面量的省略号，也就是说，上面的例子就是你要写的字面量。

> 这里的意思是其它类型都不合适作为任意默认值，所以就直接使用省略号`...`进行表示，译者注。

## [Positional-only arguments](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id38)

位置限定参数

Some functions are designed to take their arguments only positionally, and expect their callers never to use the argument's name to provide that argument by keyword. All arguments with names beginning with `__` are assumed to be positional-only, except if their names also end with `__`:

一些函数被设计成只接受位置限定参数，并期望它们的调用者永远不要使用参数的名字来通过关键字提供该参数。所有名字以`__`开头的参数都被认为是只取位置的，除非它们的名字也以`__`结尾：

```python
def quux(__x: int, __y__: int = 0) -> None: ...

quux(3, __y__=1)  # This call is fine.

quux(__x=3)  # This call is an error.
```

## [Annotating generator functions and coroutines](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id39)

注释生成器函数和协程

The return type of generator functions can be annotated by the generic type `Generator[yield_type, send_type, return_type]` provided by `typing.py` module:

生成器函数的返回类型可以由`typing.py`模块提供的通用类型`Generator[yield_type, send_type, return_type]`来注解。

```python
def echo_round() -> Generator[int, float, str]:
    res = yield
    while res:
        res = yield round(res)
    return 'OK'
```

Coroutines introduced in [PEP 492](https://www.python.org/dev/peps/pep-0492) are annotated with the same syntax as ordinary functions. However, the return type annotation corresponds to the type of `await` expression, not to the coroutine type:

在PEP 492中引入的协程被注释为与普通函数相同的语法。然而，返回类型注释对应于`await`表达式的类型，而不是协程的类型：

```python
async def spam(ignored: int) -> str:
    return 'spam'

async def foo() -> None:
    bar = await spam(42)  # type: str
```

The `typing.py` module provides a generic version of ABC `collections.abc.Coroutine` to specify awaitables that also support `send()` and `throw()` methods. The variance and order of type variables correspond to those of `Generator`, namely `Coroutine[T_co, T_contra, V_co]`, for example:

`typing.py`模块提供了一个ABC`collections.abc.Coroutine`的通用版本，用于指定也支持`send()`和`throw()`方法的异步可等待对象。类型变量的变量和顺序与`Generator`的变量相对应，即`Coroutine[T_co, T_contra, V_co]`，例如：

```python
from typing import List, Coroutine
c = None  # type: Coroutine[List[str], str, int]
...
x = c.send('hi')  # type: List[str]
async def bar() -> None:
    x = await c  # type: int
```

The module also provides generic ABCs `Awaitable`, `AsyncIterable`, and `AsyncIterator` for situations where more precise types cannot be specified:

该模块还提供了通用的ABC`Awaitable`、`AsyncIterable`和`AsyncIterator`，用于不能指定更精确类型的情况：

```python
def op() -> typing.Awaitable[str]:
    if cond:
        return spam(42)
    else:
        return asyncio.Future(...)
```

# [Compatibility with other uses of function annotations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id40)

与函数注解的其他用途的兼容性

A number of existing or potential use cases for function annotations exist, which are incompatible with type hinting. These may confuse a static type checker. However, since type hinting annotations have no runtime behavior (other than evaluation of the annotation expression and storing annotations in the `__annotations__` attribute of the function object), this does not make the program incorrect -- it just may cause a type checker to emit spurious warnings or errors.

有许多现有的或潜在的函数注解的用例存在，它们与类型提示不兼容。这些可能会使静态类型检查器感到困惑。然而，由于类型提示注解没有运行时行为（除了评估注解表达式和将注解存储在函数对象的`__annotations__`属性中），这不会使程序不正确 -- 它只是可能导致类型检查器发出虚假的警告或错误。

To mark portions of the program that should not be covered by type hinting, you can use one or more of the following:

为了标记程序中不应该被类型提示覆盖的部分，你可以使用以下一种或多种方法：

- a `# type: ignore` comment;

  一个 `# type: ignore` 注释

- a `@no_type_check` decorator on a class or function;

  一个用于类或函数的 `@no_type_check` 装饰器

- a custom class or function decorator marked with `@no_type_check_decorator`.

  使用 `@no_type_check_decorator` 标记的自定义类或函数

For more details see later sections.

更多细节见后面的章节。

In order for maximal compatibility with offline type checking it may eventually be a good idea to change interfaces that rely on annotations to switch to a different mechanism, for example a decorator. In Python 3.5 there is no pressure to do this, however. See also the longer discussion under [Rejected alternatives](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#rejected-alternatives) below.

为了最大限度地兼容离线类型检查，最终可能是一个好主意，将依赖注解的接口改成不同的机制，例如装饰器。不过在 Python 3.5 中，没有这样做的压力。也请看下面拒绝的替代方案下的较长讨论。

# [Type comments](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id41)

类型注释

No first-class syntax support for explicitly marking variables as being of a specific type is added by this PEP. To help with type inference in complex cases, a comment of the following format may be used:

本PEP没有增加对明确标记变量为特定类型的第一类语法支持。为了帮助在复杂情况下进行类型推断，可以使用以下格式的注释：

```python
x = []                # type: List[Employee]
x, y, z = [], [], []  # type: List[int], List[int], List[str]
x, y, z = [], [], []  # type: (List[int], List[int], List[str])
a, b, *c = range(5)   # type: float, float, List[float]
x = [1, 2]            # type: List[int]
```

Type comments should be put on the last line of the statement that contains the variable definition. They can also be placed on `with` statements and `for` statements, right after the colon.

类型注释应该放在包含变量定义的语句的最后一行。它们也可以放在with语句和for语句中，紧随冒号之后。

Examples of type comments on `with` and `for` statements:

类型注释在with和for语句中的例子：

```python
with frobnicate() as foo:  # type: int
    # Here foo is an int
    ...

for x, y in points:  # type: float, float
    # Here x and y are floats
    ...
```

In stubs it may be useful to declare the existence of a variable without giving it an initial value. This can be done using [PEP 526](https://www.python.org/dev/peps/pep-0526) variable annotation syntax:

在存根中，声明一个变量的存在而不给它一个初始值可能是有用的。这可以使用PEP 526变量注解语法来完成：

```
from typing import IO

stream: IO[str]
```

The above syntax is acceptable in stubs for all versions of Python. However, in non-stub code for versions of Python 3.5 and earlier there is a special case:

上述语法在所有版本的Python的存根中都可以接受。然而，在Python 3.5和更早版本的非存根代码中，有一种特殊情况：

```python
from typing import IO

stream = None  # type: IO[str]
```

Type checkers should not complain about this (despite the value `None` not matching the given type), nor should they change the inferred type to `Optional[...]` (despite the rule that does this for annotated arguments with a default value of `None`). The assumption here is that other code will ensure that the variable is given a value of the proper type, and all uses can assume that the variable has the given type.

类型检查器不应该抱怨这一点（尽管值`None`与给定的类型不匹配），也不应该将推断的类型改为`Optional[...]`（尽管规则对默认值为`None`的注解参数是这样的）。这里的假设是，其他代码将确保变量被赋予适当类型的值，而所有的使用都可以假设变量具有给定的类型。

The `# type: ignore` comment should be put on the line that the error refers to:

`# type: ignore`注释应该放在错误所指的行上。

```python
import http.client
errors = {
    'not_found': http.client.NOT_FOUND  # type: ignore
}
```

A `# type: ignore` comment on a line by itself at the top of a file, before any docstrings, imports, or other executable code, silences all errors in the file. Blank lines and other comments, such as shebang lines and coding cookies, may precede the `# type: ignore` comment.

一个`# type: ignore`注释在文件顶部的一行，在任何文档串、导入或其他可执行代码之前，使文件中的所有错误保持沉默。空白行和其他注释，如shebang行和编码cookies，可以在`# type: ignore`注释之前。

In some cases, linting tools or other comments may be needed on the same line as a type comment. In these cases, the type comment should be before other comments and linting markers:

在某些情况下，提示工具或其他注释可能需要与类型注释在同一行。在这些情况下，类型注释应该放在其他注释和提示性标记之前。

> \# type: ignore # <comment or other marker>

If type hinting proves useful in general, a syntax for typing variables may be provided in a future Python version. (**UPDATE**: This syntax was added in Python 3.6 through [PEP 526](https://www.python.org/dev/peps/pep-0526).)

如果类型提示被证明在一般情况下是有用的，那么在未来的 Python 版本中可能会提供一种类型化变量的语法。(**更新**：这种语法已经在Python 3.6中通过PEP 526添加。)

# [Casts](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id42)

推断

Occasionally the type checker may need a different kind of hint: the programmer may know that an expression is of a more constrained type than a type checker may be able to infer. For example:

偶尔，类型检查器可能需要一种不同的提示：程序员可能知道一个表达式的类型比类型检查器能够推断出的类型更受限制。比如说：

```python
from typing import List, cast

def find_first_str(a: List[object]) -> str:
    index = next(i for i, x in enumerate(a) if isinstance(x, str))
    # We only get here if there's at least one string in a
    return cast(str, a[index])
```

Some type checkers may not be able to infer that the type of `a[index]` is `str` and only infer `object` or `Any`, but we know that (if the code gets to that point) it must be a string. The `cast(t, x)` call tells the type checker that we are confident that the type of `x` is `t`. At runtime a cast always returns the expression unchanged -- it does not check the type, and it does not convert or coerce the value.

一些类型检查器可能无法推断出`a[index]`的类型是str，而只能推断出`object`或`Any`，但我们知道（如果代码到了这一步）它一定是一个字符串。`cast(t, x)`调用告诉类型检查器，我们确信`x`的类型是`t`。在运行时，cast总是不变地返回表达式--它不检查类型，也不转换或强制取值。

> 这更像是程序员对于类型注释的机制的一种不得已情况下的人为干预，译者注。

Casts differ from type comments (see the previous section). When using a type comment, the type checker should still verify that the inferred type is consistent with the stated type. When using a cast, the type checker should blindly believe the programmer. Also, casts can be used in expressions, while type comments only apply to assignments.

推断（casts）与类型注释不同（见上一节）。当使用类型注释时，类型检查器仍然应该验证推断的类型是否与声明的类型一致。当使用推断时，类型检查器应该盲目地相信程序员。另外，推断可以在表达式中使用，而类型注释只适用于赋值。

# [NewType helper function](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id43)

NewType 辅助函数

There are also situations where a programmer might want to avoid logical errors by creating simple classes. For example:

还有一些情况，程序员可能想通过创建简单的类来避免逻辑错误。比如说：

```python
class UserId(int):
    pass

get_by_user_id(user_id: UserId):
    ...
```

However, this approach introduces a runtime overhead. To avoid this, `typing.py` provides a helper function `NewType` that creates simple unique types with almost zero runtime overhead. For a static type checker `Derived = NewType('Derived', Base)` is roughly equivalent to a definition:

然而，这种方法带来了运行时的开销。为了避免这一点，`typing.py`提供了一个辅助函数`NewType`，它可以创建简单的唯一类型，而运行时开销几乎为零。对于一个静态类型检查器来说，`Derived = NewType('Derived', Base)`大致等同于一个定义：

```python
class Derived(Base):
    def __init__(self, _x: Base) -> None:
        ...
```

While at runtime, `NewType('Derived', Base)` returns a dummy function that simply returns its argument. Type checkers require explicit casts from `int` where `UserId` is expected, while implicitly casting from `UserId` where `int` is expected. Examples:

而在运行时，`NewType('Derived', Base)`会返回一个假的函数，只是返回它的参数。类型检查器要求从`int`显式转换到预期的`UserId`，而从`UserId`隐式转换到预期的`int`。例子：

```python
UserId = NewType('UserId', int)

def name_by_id(user_id: UserId) -> str:
    ...

UserId('user')          # Fails type check

name_by_id(42)          # Fails type check
name_by_id(UserId(42))  # OK

num = UserId(5) + 1     # type: int
```

`NewType` accepts exactly two arguments: a name for the new unique type, and a base class. The latter should be a proper class (i.e., not a type construct like `Union`, etc.), or another unique type created by calling `NewType`. The function returned by `NewType` accepts only one argument; this is equivalent to supporting only one constructor accepting an instance of the base class (see above). Example:

`NewType`完全接受两个参数：一个新的独特类型的名称，和一个基类。后者应该是一个适当的类（即，不是像`Union`这样的类型结构），或者是通过调用`NewType`创建的另一个独特类型。由`NewType`返回的函数只接受一个参数；这相当于只支持一个接受基类实例的构造函数（见上文）。例子：

```python
class PacketId:
    def __init__(self, major: int, minor: int) -> None:
        self._major = major
        self._minor = minor

TcpPacketId = NewType('TcpPacketId', PacketId)

packet = PacketId(100, 100)
tcp_packet = TcpPacketId(packet)  # OK

tcp_packet = TcpPacketId(127, 0)  # Fails in type checker and at runtime
```

Both `isinstance` and `issubclass`, as well as subclassing will fail for `NewType('Derived', Base)` since function objects don't support these operations.

`isinstance`和`issubclass`，以及subclassing对于`NewType('Derived', Base)`都会失败，因为函数对象不支持这些操作。

# [Stub Files](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id44)

存根文件

Stub files are files containing type hints that are only for use by the type checker, not at runtime. There are several use cases for stub files:

存根文件是包含类型提示的文件，只供类型检查器使用，而不是在运行时使用。存根文件有几种使用情况：

- Extension modules

  扩展模块

- Third-party modules whose authors have not yet added type hints

  其作者尚未添加类型提示的第三方模块

- Standard library modules for which type hints have not yet been written

  尚未编写类型提示的标准库模块

- Modules that must be compatible with Python 2 and 3

  必须与 Python 2 和 3 兼容的模块

- Modules that use annotations for other purposes

  为其他目的使用注解的模块

Stub files have the same syntax as regular Python modules. There is one feature of the `typing` module that is different in stub files: the `@overload` decorator described below.

存根文件的语法与普通 Python 模块相同。在存根文件中，有一个`typing`模块的特征是不同的：下面描述的 `@overload` 装饰器。

The type checker should only check function signatures in stub files; It is recommended that function bodies in stub files just be a single ellipsis (`...`).

类型检查器应该只检查存根文件中的函数签名；建议存根文件中的函数体只是一个省略号（`...`）。

The type checker should have a configurable search path for stub files. If a stub file is found the type checker should not read the corresponding "real" module.

类型检查器应该有一个可配置的存根文件的搜索路径。如果发现一个存根文件，类型检查器不应该读取相应的 "真实 "模块。

While stub files are syntactically valid Python modules, they use the `.pyi` extension to make it possible to maintain stub files in the same directory as the corresponding real module. This also reinforces the notion that no runtime behavior should be expected of stub files.

虽然存根文件在语法上是有效的 Python 模块，但它们使用了 `.pyi` 扩展名，以使存根文件与相应的真实模块保持在同一目录下。这也加强了一个概念，即不应该期待存根文件的运行时行为。

Additional notes on stub files:

其它关于存根文件需要注意的事项：

- Modules and variables imported into the stub are not considered exported from the stub unless the import uses the `import ... as ...` form or the equivalent `from ... import ... as ...` form. (*UPDATE:* To clarify, the intention here is that only names imported using the form `X as X` will be exported, i.e. the name before and after `as` must be the same.)

  除非导入时使用了`import ... as ...` 的形式或相当于 `from ... import ... as ...` 的形式，否则导入到存根中的模块和变量不被认为是从存根中导出的。(更新：为了澄清，这里的意图是只有使用`X as X`的形式导入的名字才会被导出，即作为前后的名字必须是相同的。)

- However, as an exception to the previous bullet, all objects imported into a stub using `from ... import *` are considered exported. (This makes it easier to re-export all objects from a given module that may vary by Python version.)

  然而，作为前一条的例外，所有使用 `from ... import *` 导入存根的对象都被认为是导出的。(这使得从一个给定的模块中重新导出所有对象变得更容易，这些对象可能因 Python 的版本而不同)。

- Just like in normal Python files [[importdocs\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#importdocs), submodules automatically become exported attributes of their parent module when imported. For example, if the `spam` package has the following directory structure:

  就像普通的 Python 文件 [importdocs] 一样，子模块在导入时自动成为其父模块的导出属性。例如，如果`spam`包有以下目录结构：

  ```python
  spam/
      __init__.pyi
      ham.pyi
  ```

  where `__init__.pyi` contains a line such as `from . import ham` or `from .ham import Ham`, then `ham` is an exported attribute of `spam`.

  其中 `__init__.pyi` 包含一行诸如 `from . import ham` 或 `from .ham import Ham`，那么 `ham` 是 `spam` 的一个导出属性。

- Stub files may be incomplete. To make type checkers aware of this, the file can contain the following code:

  存根文件可能是不完整的。为了让类型检查器意识到这一点，该文件可以包含以下代码：
  
  ```python
  def __getattr__(name) -> Any: ...
  ```
  
  Any identifier not defined in the stub is therefore assumed to be of type `Any`.
  
  因此，任何未在存根中定义的标识符被假定为`Any`类型。

## [Function/method overloading](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id45)

函数、方法重载

The `@overload` decorator allows describing functions and methods that support multiple different combinations of argument types. This pattern is used frequently in builtin modules and types. For example, the `__getitem__()` method of the `bytes` type can be described as follows:

`@overload`装饰器允许描述支持多种不同参数类型组合的函数和方法。这种模式在内置模块和类型中被频繁使用。例如，`bytes` 类型的`__getitem__()` 方法可以被描述如下。

```python
from typing import overload

class bytes:
    ...
    @overload
    def __getitem__(self, i: int) -> int: ...
    @overload
    def __getitem__(self, s: slice) -> bytes: ...
```

This description is more precise than would be possible using unions (which cannot express the relationship between the argument and return types):

这种描述比使用联合体（它不能表达参数和返回类型之间的关系）更精确：

```python
from typing import Union

class bytes:
    ...
    def __getitem__(self, a: Union[int, slice]) -> Union[int, bytes]: ...
```

Another example where `@overload` comes in handy is the type of the builtin `map()` function, which takes a different number of arguments depending on the type of the callable:

另一个`@overload`派上用场的例子是内建的`map()`函数的类型，它根据可调用对象的类型需要不同数量的参数。

```python
from typing import Callable, Iterable, Iterator, Tuple, TypeVar, overload

T1 = TypeVar('T1')
T2 = TypeVar('T2')
S = TypeVar('S')

@overload
def map(func: Callable[[T1], S], iter1: Iterable[T1]) -> Iterator[S]: ...
@overload
def map(func: Callable[[T1, T2], S],
        iter1: Iterable[T1], iter2: Iterable[T2]) -> Iterator[S]: ...
# ... and we could add more items to support more than two iterables
```

Note that we could also easily add items to support `map(None, ...)`:

请注意，我们也可以很容易地添加项目来支持`map(None, ...)`：

```python
@overload
def map(func: None, iter1: Iterable[T1]) -> Iterable[T1]: ...
@overload
def map(func: None,
        iter1: Iterable[T1],
        iter2: Iterable[T2]) -> Iterable[Tuple[T1, T2]]: ...
```

Uses of the `@overload` decorator as shown above are suitable for stub files. In regular modules, a series of `@overload`-decorated definitions must be followed by exactly one non-`@overload`-decorated definition (for the same function/method). The `@overload`-decorated definitions are for the benefit of the type checker only, since they will be overwritten by the non-`@overload`-decorated definition, while the latter is used at runtime but should be ignored by a type checker. At runtime, calling a `@overload`-decorated function directly will raise `NotImplementedError`. Here's an example of a non-stub overload that can't easily be expressed using a union or a type variable:

如上所示，`@overload`装饰器的使用适用于存根文件。在常规模块中，一系列`@overload`装饰的定义后面必须有一个非`@overload`装饰的定义（对于同一个函数/方法）。`@overload`装饰的定义只是为了类型检查器的利益，因为它们将被非`@overload`装饰的定义所覆盖，而后者在运行时使用，但应该被类型检查器所忽略。在运行时，直接调用一个`@overload`装饰的函数将引发`NotImplementedError`。下面是一个非存根重载的例子，它不容易用联合或类型变量来表达。

```python
@overload
def utf8(value: None) -> None:
    pass
@overload
def utf8(value: bytes) -> bytes:
    pass
@overload
def utf8(value: unicode) -> bytes:
    pass
def utf8(value):
    <actual implementation>
```

NOTE: While it would be possible to provide a multiple dispatch implementation using this syntax, its implementation would require using `sys._getframe()`, which is frowned upon. Also, designing and implementing an efficient multiple dispatch mechanism is hard, which is why previous attempts were abandoned in favor of `functools.singledispatch()`. (See [PEP 443](https://www.python.org/dev/peps/pep-0443), especially its section "Alternative approaches".) In the future we may come up with a satisfactory multiple dispatch design, but we don't want such a design to be constrained by the overloading syntax defined for type hints in stub files. It is also possible that both features will develop independent from each other (since overloading in the type checker has different use cases and requirements than multiple dispatch at runtime -- e.g. the latter is unlikely to support generic types).

注意：虽然有可能使用这种语法提供一个多重分发的实现，但其实现需要使用`sys._getframe()`，这是不允许的。另外，设计和实现一个有效的多重分发机制是很难的，这就是为什么以前的尝试被放弃了，转而支持`functools.singleispatch()`。（见PEP 443，特别是它的 "替代方法 "一节。）未来我们可能会想出一个令人满意的多重分发设计，但我们不希望这样的设计被定义在存根文件中的类型提示的重载语法所限制。也有可能这两种功能会相互独立发展（因为类型检查器中的重载与运行时的多重分发有不同的用例和要求--例如，后者不太可能支持通用类型）。

A constrained `TypeVar` type can often be used instead of using the `@overload` decorator. For example, the definitions of `concat1` and `concat2` in this stub file are equivalent:

一个受约束的`TypeVar`类型通常可以用来代替使用`@overload`装饰器。例如，这个存根文件中的`concat1`和`concat2`的定义是等同的：

```python
from typing import TypeVar, Text

AnyStr = TypeVar('AnyStr', Text, bytes)

def concat1(x: AnyStr, y: AnyStr) -> AnyStr: ...

@overload
def concat2(x: str, y: str) -> str: ...
@overload
def concat2(x: bytes, y: bytes) -> bytes: ...
```

Some functions, such as `map` or `bytes.__getitem__` above, can't be represented precisely using type variables. However, unlike `@overload`, type variables can also be used outside stub files. We recommend that `@overload` is only used in cases where a type variable is not sufficient, due to its special stub-only status.

一些函数，如上面展示的 `map` 或 `bytes.__getitem__`，不能用类型变量精确表示。然而，与`@overload`不同，类型变量也可以在存根文件之外使用。我们建议只有在类型变量不够用的情况下才使用`@overload`，因为它具有特殊的存根专用地位。

Another important difference between type variables such as `AnyStr` and using `@overload` is that the prior can also be used to define constraints for generic class type parameters. For example, the type parameter of the generic class `typing.IO` is constrained (only `IO[str]`, `IO[bytes]` and `IO[Any]` are valid):

`AnyStr`等类型变量与使用`@overload`的另一个重要区别是，前者也可以用来定义泛型类类型参数的约束。例如，泛型类`typing.IO`的类型参数是受约束的（只有`IO[str]`、`IO[bytes]`和`IO[Any]`有效）。

```python
class IO(Generic[AnyStr]): ...
```

## [Storing and distributing stub files](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id46)

存根文件的存储和发布

The easiest form of stub file storage and distribution is to put them alongside Python modules in the same directory. This makes them easy to find by both programmers and the tools. However, since package maintainers are free not to add type hinting to their packages, third-party stubs installable by `pip` from 
PyPI are also supported. In this case we have to consider three issues: naming, versioning, installation path.

存根文件的最简单的存储和分发方式是把它们和 Python 模块一起放在同一个目录下。这使得程序员和工具都很容易找到它们。然而，由于软件包维护者可以自由地不在他们的软件包中添加类型提示，因此也支持可由PyPI的`pip`安装的第三方存根。在这种情况下，我们必须考虑三个问题：命名、版本管理、安装路径。

This PEP does not provide a recommendation on a naming scheme that should be used for third-party stub file packages. Discoverability will hopefully be based on package popularity, like with Django packages for example.

本PEP并没有对第三方存根文件包应该使用的命名方案提供建议。希望可发现性将基于软件包的受欢迎程度，就像Django软件包那样。

Third-party stubs have to be versioned using the lowest version of the source package that is compatible. Example: FooPackage has versions 1.0, 1.1, 1.2, 1.3, 2.0, 2.1, 2.2. There are API changes in versions 1.1, 2.0 and 2.2. The stub file package maintainer is free to release stubs for all versions but at least 1.0, 1.1, 2.0 and 2.2 are needed to enable the end user type check all versions. This is because the user knows that the closest *lower or equal* version of stubs is compatible. In the provided example, for FooPackage 1.3 the user would choose stubs version 1.1.

第三方存根必须使用兼容的最低版本的源包进行版本管理。例如。FooPackage有1.0、1.1、1.2、1.3、2.0、2.1、2.2版本。在1.1、2.0和2.2版本中存在API变化。存根文件包维护者可以自由地发布所有版本的存根，但至少需要1.0、1.1、2.0和2.2版本，以使最终用户能够检查所有版本的类型。这是因为用户知道最接近的低版本或相等版本的存根是兼容的。在所提供的例子中，对于FooPackage 1.3，用户会选择stubs 1.1版本。

Note that if the user decides to use the "latest" available source package, using the "latest" stub files should generally also work if they're updated often.

请注意，如果用户决定使用 "最新 "的可用源码包，使用 "最新 "的存根文件一般也应该可以，如果它们经常更新的话。

Third-party stub packages can use any location for stub storage. Type checkers should search for them using PYTHONPATH. A default fallback directory that is always checked is `shared/typehints/pythonX.Y/` (for some PythonX.Y as determined by the type checker, not just the installed version). Since there can only be one package installed for a given Python version per environment, no additional versioning is performed under that directory (just like bare directory installs by `pip` in site-packages). Stub file package authors might use the following snippet in `setup.py`:

第三方存根包可以使用任何位置来存储存根。类型检查器应该使用 `PYTHONPATH` 来搜索它们。一个总是被检查的默认后备目录是 `shared/typehints/pythonX.Y/` (对于一些由类型检查器决定的 `PythonX.Y`，而不仅仅是安装的版本)。由于每个环境中只能为一个给定的 Python 版本安装一个包，所以在该目录下不进行额外的版本管理 (就像在 site-packages 中由 `pip` 安装的裸目录)。存根文件包作者可以在 `setup.py` 中使用下面的代码片段：

```python
...
data_files=[
    (
        'shared/typehints/python{}.{}'.format(*sys.version_info[:2]),
        pathlib.Path(SRC_PATH).glob('**/*.pyi'),
    ),
],
...
```

(*UPDATE:* As of June 2018 the recommended way to distribute type hints for third-party packages has changed -- in addition to typeshed (see the next section) there is now a standard for distributing type hints, [PEP 561](https://www.python.org/dev/peps/pep-0561). It supports separately installable packages containing stubs, stub files included in the same distribution as the executable code of a package, and inline type hints, the latter two options enabled by including a file named `py.typed` in the package.)

(*更新*：从2018年6月起，为第三方软件包分发类型提示的推荐方式已经改变--除了typeshed（见下一节），现在还有一个分发类型提示的标准，即PEP 561。它支持包含存根的可单独安装的包，存根文件与包的可执行代码包含在同一个分发中，以及内联类型提示，后两个选项通过在包中包含一个名为`py.typed`的文件来启用。)

## [The Typeshed Repo](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id47)

Typeshed 资源库

There is a shared repository where useful stubs are being collected [[typeshed\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#typeshed). Policies regarding the stubs collected here will be decided separately and reported in the repo's documentation. Note that stubs for a given package will not be included here if the package owners have specifically requested that they be omitted.

有一个共享资源库，正在收集有用的存根[[typeshed]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#typeshed)。有关这里收集的存根的政策将被单独决定，并在该仓库的文档中报告。请注意，如果软件包的所有者特别要求省略某个软件包的存根，那么这些存根就不会被包括在这里。

# [Exceptions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id48)

异常

No syntax for listing explicitly raised exceptions is proposed. Currently the only known use case for this feature is documentational, in which case the recommendation is to put this information in a docstring.

没有提出列出明确提出的异常的语法。目前，这个特性的唯一已知用例是文档化，在这种情况下，建议将这些信息放在文档串中。

# [The `typing` Module](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id49)

`typing`模块

To open the usage of static type checking to Python 3.5 as well as older versions, a uniform namespace is required. For this purpose, a new module in the standard library is introduced called `typing`.

为了向Python 3.5以及旧版本开放静态类型检查的使用，需要一个统一的命名空间。为此，在标准库中引入了一个名为 `typing` 的新模块。

It defines the fundamental building blocks for constructing types (e.g. `Any`), types representing generic variants of builtin collections (e.g. `List`), types representing generic collection ABCs (e.g. `Sequence`), and a small collection of convenience definitions.

它定义了构造类型的基本构件 (如 `Any`)，代表内置集合泛型变体的类型 (如 `List`)，代表通用集合 ABC 的类型 (如 `Sequence`)，以及一小部分方便定义。

Note that special type constructs, such as `Any`, `Union`, and type variables defined using `TypeVar` are only supported in the type annotation context, and `Generic` may only be used as a base class. All of these (except for unparameterized generics) will raise `TypeError` if appear in `isinstance` or `issubclass`.

请注意，特殊的类型结构，如`Any`、`Union`和使用`TypeVar`定义的类型变量，只在类型注释的上下文中支持，而`Generic`只能作为基类使用。所有这些（除了无参数的泛型）如果出现在`isinstance`或`issubclass`中，将引发`TypeError`。

Fundamental building blocks:

基本构件：

- Any, used as `def get(key: str) -> Any: ...`

  Any, 用作 `def get(key: str) -> Any: ...`

- Union, used as `Union[Type1, Type2, Type3]`

  Union, 用作 `Union[Type1, Type2, Type3]`

- Callable, used as `Callable[[Arg1Type, Arg2Type], ReturnType]`

  Callable, 用作 `Callable[[Arg1Type, Arg2Type], ReturnType]`

- Tuple, used by listing the element types, for example `Tuple[int, int, str]`. The empty tuple can be typed as `Tuple[()]`. Arbitrary-length homogeneous tuples can be expressed using one type and ellipsis, for example `Tuple[int, ...]`. (The `...` here are part of the syntax, a literal ellipsis.)

  元组，通过列出元素类型来使用，例如`Tuple[int, int, str]`。空元组可以被打成`Tuple[()]`。任意长度的同质元组可以用一种类型和省略号来表达，例如`Tuple[int, ...]`。这里的`...`是语法的一部分，是字面的省略号）。

- TypeVar, used as `X = TypeVar('X', Type1, Type2, Type3)` or simply `Y = TypeVar('Y')` (see above for more details)

  TypeVar, 用作`X = TypeVar('X', Type1, Type2, Type3)`或者简单的`Y = TypeVar('Y')` (更多细节见上文)。

- Generic, used to create user-defined generic classes

  Generic，用于创建用户定义的泛型类。

- Type, used to annotate class objects

  Type，用于注解类对象

Generic variants of builtin collections:

内置集合的泛型：

- Dict, used as `Dict[key_type, value_type]`

  Dict, 用作 `Dict[key_type, value_type]`

- DefaultDict, used as `DefaultDict[key_type, value_type]`, a generic variant of `collections.defaultdict`

  DefaultDict, 用作 `DefaultDict[key_type, value_type]`,是 `collections.defaultdict`的泛型。

- List, used as `List[element_type]`

  List, 用作 `List[element_type]`

- Set, used as `Set[element_type]`. See remark for `AbstractSet` below.

  Set, 用作`Set[element_type]`. 见下面 `AbstractSet` 的注释.

- FrozenSet, used as `FrozenSet[element_type]`

  FrozenSet, 用作 `FrozenSet[element_type]`

Note: `Dict`, `DefaultDict`, `List`, `Set` and `FrozenSet` are mainly useful for annotating return values. For arguments, prefer the abstract collection types defined below, e.g. `Mapping`, `Sequence` or `AbstractSet`.

注意: `Dict`, `DefaultDict`, `List`, `Set` 和 `FrozenSet` 主要用于注释返回值。对于参数，最好使用下面定义的抽象集合类型，例如：`Mapping`, `Sequence`或`AbstractSet`。

> 这是解耦思想的体现，即“向上编程”，此外，Python 3.9 通过[**PEP 585 -- 标准集合中的类型提示泛型**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20585%20--%20Type%20Hinting%20Generics%20In%20Standard%20Collections.md)废弃了`typing.Dict`等，已经使用`dict`等类型泛型进行了统一，译者注。

Generic variants of container ABCs (and a few non-containers):

容器ABC（和一些非容器）的泛型：

- Awaitable

- AsyncIterable

- AsyncIterator

- ByteString

- Callable (see above, listed here for completeness)

  Callable（见上文，为完整起见在此列出）

- Collection

- Container

- ContextManager

- Coroutine

- Generator, used as `Generator[yield_type, send_type, return_type]`. This represents the return value of generator functions. It is a subtype of `Iterable` and it has additional type variables for the type accepted by the `send()` method (it is contravariant in this variable -- a generator that accepts sending it `Employee` instance is valid in a context where a generator is required that accepts sending it `Manager` instances) and the return type of the generator.

  生成器，作为`Generator[yield_type, send_type, return_type]`使用。这代表了生成器函数的返回值。它是`Iterable`的一个子类型，它有额外的类型变量，用于表示`send()`方法所接受的类型（它在这个变量中是不变的--一个接受发送`Employee`实例的生成器在一个需要接受发送`Manager`实例的生成器的环境中是有效的）和生成器的返回类型。

- Hashable (not generic, but present for completeness)

  Hashable（不是泛型，但为了完整起见而存在）

- ItemsView

- Iterable

- Iterator

- KeysView

- Mapping

- MappingView

- MutableMapping

- MutableSequence

- MutableSet

- Sequence

- Set, renamed to `AbstractSet`. This name change was required because `Set` in the `typing` module means `set()` with generics.

  Set，改名为`AbstractSet`。这一名称的改变是必要的，因为在 `typing` 模块中的 `Set` 是指具有泛型的 `set()`。

- Sized (not generic, but present for completeness)

  Sized (不是泛型，但为了完整起见而存在)

- ValuesView

A few one-off types are defined that test for single special methods (similar to `Hashable` or `Sized`):

定义了一些一次性的类型，用于测试单一的特殊方法（类似于`Hashable`或`Sized`）：

- Reversible, to test for `__reversed__`
- SupportsAbs, to test for `__abs__`
- SupportsComplex, to test for `__complex__`
- SupportsFloat, to test for `__float__`
- SupportsInt, to test for `__int__`
- SupportsRound, to test for `__round__`
- SupportsBytes, to test for `__bytes__`

Convenience definitions:

方便的定义：

- Optional, defined by `Optional[t] == Union[t, None]`

  Optional，定义为`Optional[t] == Union[t, None]`

- Text, a simple alias for `str` in Python 3, for `unicode` in Python 2

  Text，在Python 3中是`str`的简单别名，在Python 2中是`unicode`。

- AnyStr, defined as `TypeVar('AnyStr', Text, bytes)`

  AnyStr，定义为`TypeVar('AnyStr', Text, bytes)`

- NamedTuple, used as `NamedTuple(type_name, [(field_name, field_type), ...])` and equivalent to `collections.namedtuple(type_name, [field_name, ...])`. This is useful to declare the types of the fields of a named tuple type.

  NamedTuple，作为`NamedTuple(type_name, [(field_name, field_type), ...])`使用，相当于 `collections.namedtuple(type_name, [field_name, ...])`。这对于声明一个命名元组类型的字段的类型很有用。

- NewType, used to create unique types with little runtime overhead `UserId = NewType('UserId', int)`

  NewType，用于创建唯一的类型，运行时开销很小 `UserId = NewType('UserId', int)`

- cast(), described earlier

  cast()，前面已经介绍过

- @no_type_check, a decorator to disable type checking per class or function (see below)

  @no_type_check，一个装饰器，用于禁用每个类或函数的类型检查（见下文）。

- @no_type_check_decorator, a decorator to create your own decorators with the same meaning as `@no_type_check` (see below)

  @no_type_check_decorator, 一个装饰器，用于创建你自己的装饰器，其含义与 `@no_type_check` 相同（见下文）。

- @type_check_only, a decorator only available during type checking for use in stub files (see above); marks a class or function as unavailable during runtime

  @type_check_only，一个只在类型检查期间可用的装饰器，用于存根文件（见上文）；将一个类或函数标记为在运行期间不可用。

- @overload, described earlier

  @overload, 前面已经介绍过

- get_type_hints(), a utility function to retrieve the type hints from a function or method. Given a function or method object, it returns a dict with the same format as `__annotations__`, but evaluating forward references (which are given as string literals) as expressions in the context of the original function or method definition.

  get_type_hints()，一个从函数或方法中检索类型提示的实用函数。给定一个函数或方法对象，它返回一个与 `__annotations__` 格式相同的字典，但在原始函数或方法定义的上下文中，将前向引用（以字符串字面形式给出）作为表达式进行评估。

- TYPE_CHECKING, `False` at runtime but `True` to type checkers

  TYPE_CHECKING，在运行时为 `False`，但对类型检查器为 `True`。

I/O related types:

与I/O相关的类型：

- IO (generic over `AnyStr`)

  IO（在`AnyStr`上通用）

- BinaryIO (a simple subtype of `IO[bytes]`)

  BinaryIO（`IO[bytes]`的一个简单的子类型）

- TextIO (a simple subtype of `IO[str]`)

  TextIO（`IO[str]`的一个简单的子类型）

Types related to regular expressions and the `re` module:

与正则表达式和`re`模块相关的类型：

- Match and Pattern, types of `re.match()` and `re.compile()` results (generic over `AnyStr`)

  Match 和 Pattern，`re.match()`和`re.compile()`结果的类型（`AnyStr`的泛型）。

# [Suggested syntax for Python 2.7 and straddling code](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id50)

建议的Python 2.7和过渡代码的语法：

Some tools may want to support type annotations in code that must be compatible with Python 2.7. For this purpose this PEP has a suggested (but not mandatory) extension where function annotations are placed in a `# type:` comment. Such a comment must be placed immediately following the function header (before the docstring). An example: the following Python 3 code:

一些工具可能希望在必须与Python 2.7兼容的代码中支持类型注解。为此，本PEP有一个建议的(但不是必须的)扩展，即把函数注解放在`# type:` 注释中。这样的注释必须紧跟在函数头的后面(在文档串之前)。一个例子：下面的Python 3代码。

```python
def embezzle(self, account: str, funds: int = 1000000, *fake_receipts: str) -> None:
    """Embezzle funds from account using fake receipts."""
    <code goes here>
```

is equivalent to the following:

等效于：

```python
def embezzle(self, account, funds=1000000, *fake_receipts):
    # type: (str, int, *str) -> None
    """Embezzle funds from account using fake receipts."""
    <code goes here>
```

Note that for methods, no type is needed for `self`.

注意，对于这个方法，`self`不需要类型注解。

For an argument-less method it would look like this:

对没有其他参数的方法，应该写成这样：

```python
def load_cache(self):
    # type: () -> bool
    <code>
```

Sometimes you want to specify the return type for a function or method without (yet) specifying the argument types. To support this explicitly, the argument list may be replaced with an ellipsis. Example:

有时你想为一个函数或方法指定返回类型，而不（还）指定参数类型。为了明确地支持这一点，参数列表可以用省略号来代替。例子：

```python
def send_email(address, sender, cc, bcc, subject, body):
    # type: (...) -> bool
    """Send an email message.  Return True if successful."""
    <code>
```

Sometimes you have a long list of parameters and specifying their types in a single `# type:` comment would be awkward. To this end you may list the arguments one per line and add a `# type:` comment per line after an argument's associated comma, if any. To specify the return type use the ellipsis syntax. Specifying the return type is not mandatory and not every argument needs to be given a type. A line with a `# type:` comment should contain exactly one argument. The type comment for the last argument (if any) should precede the close parenthesis. Example:

有时你有一长串的参数，在一个`# type:` comment中指定它们的类型会很尴尬。为此，你可以每行列出一个参数，并在一个参数的相关逗号（如果有的话）之后每行添加一个`# type:` comment。要指定返回类型，请使用省略号的语法。指定返回类型并不是强制性的，也不是每个参数都需要给出一个类型。带有#` type:`注释的一行应该正好包含一个参数。最后一个参数（如果有的话）的类型注释应该在闭合括号之前。例子：

```python
def send_email(address,     # type: Union[str, List[str]]
               sender,      # type: str
               cc,          # type: Optional[List[str]]
               bcc,         # type: Optional[List[str]]
               subject='',
               body=None    # type: List[str]
               ):
    # type: (...) -> bool
    """Send an email message.  Return True if successful."""
    <code>
```

> 剩余的2.7兼容内容就不翻译了，精力有限ORZ，译者注。

Notes:

- Tools that support this syntax should support it regardless of the Python version being checked. This is necessary in order to support code that straddles Python 2 and Python 3.

- It is not allowed for an argument or return value to have both a type annotation and a type comment.

- When using the short form (e.g. `# type: (str, int) -> None`) every argument must be accounted for, except the first argument of instance and class methods (those are usually omitted, but it's allowed to include them).

- The return type is mandatory for the short form. If in Python 3 you would omit some argument or the return type, the Python 2 notation should use `Any`.

- When using the short form, for `*args` and `**kwds`, put 1 or 2 stars in front of the corresponding type annotation. (As with Python 3 annotations, the annotation here denotes the type of the individual argument values, not of the tuple/dict that you receive as the special argument value `args` or `kwds`.)

- Like other type comments, any names used in the annotations must be imported or defined by the module containing the annotation.

- When using the short form, the entire annotation must be one line.

- The short form may also occur on the same line as the close parenthesis, e.g.:

  ```
  def add(a, b):  # type: (int, int) -> int
      return a + b
  ```

- Misplaced type comments will be flagged as errors by a type checker. If necessary, such comments could be commented twice. For example:

  ```
  def f():
      '''Docstring'''
      # type: () -> None  # Error!
  
  def g():
      '''Docstring'''
      # # type: () -> None  # This is OK
  ```

When checking Python 2.7 code, type checkers should treat the `int` and `long` types as equivalent. For parameters typed as `Text`, arguments of type `str` as well as `unicode` should be acceptable.

# [Rejected Alternatives](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id51)

被拒绝的替代方案

During discussion of earlier drafts of this PEP, various objections were raised and alternatives were proposed. We discuss some of these here and explain why we reject them.

在讨论本PEP的早期草案时，有人提出了各种反对意见和替代方案。我们在此讨论其中的一些，并解释为什么我们拒绝它们。

Several main objections were raised.

提出了几个主要的反对意见。

## [Which brackets for generic type parameters?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id52)

哪种括号用于泛型参数？

Most people are familiar with the use of angular brackets (e.g. `List<int>`) in languages like C++, Java, C# and Swift to express the parameterization of generic types. The problem with these is that they are really hard to parse, especially for a simple-minded parser like Python. In most languages the ambiguities are usually dealt with by only allowing angular brackets in specific syntactic positions, where general expressions aren't allowed. (And also by using very powerful parsing techniques that can backtrack over an arbitrary section of code.)

大多数人都熟悉在 C++、Java、C# 和 Swift 等语言中使用尖括号（例如 `List<int>`）来表达泛型的参数化。这些的问题是它们真的很难解析，特别是对于像Python这样头脑简单的解析器。在大多数语言中，通常通过在特定的语法位置上只允许使用角括号来处理这种模糊性，而一般的表达式是不允许的。(也可以通过使用非常强大的解析技术，在代码的任意部分上进行回溯。)

But in Python, we'd like type expressions to be (syntactically) the same as other expressions, so that we can use e.g. variable assignment to create type aliases. Consider this simple type expression:

但是在 Python 中，我们希望类型表达式 (语法上) 与其它表达式相同，这样我们就可以使用例如变量赋值来创建类型别名。考虑一下这个简单的类型表达式：

```python
List<int>
```

From the Python parser's perspective, the expression begins with the same four tokens (NAME, LESS, NAME, GREATER) as a chained comparison:

从Python解析器的角度来看，这个表达式以同样的四个标记(NAME, LESS, NAME, GREATER)开始，作为一个链式比较：

```python
a < b > c  # I.e., (a < b) and (b > c)
```

We can even make up an example that could be parsed both ways:

我们甚至可以编造一个例子，可以从两个方面进行解析：

```python
a < b > [ c ]
```

Assuming we had angular brackets in the language, this could be interpreted as either of the following two:

假设我们的语言中有尖括号，这可以被解释为以下两种情况中的任何一种：

```python
(a<b>)[c]      # I.e., (a<b>).__getitem__(c)
a < b > ([c])  # I.e., (a < b) and (b > [c])
```

It would surely be possible to come up with a rule to disambiguate such cases, but to most users the rules would feel arbitrary and complex. It would also require us to dramatically change the CPython parser (and every other parser for Python). It should be noted that Python's current parser is intentionally "dumb" -- a simple grammar is easier for users to reason about.

当然有可能想出一个规则来消除这种情况，但对大多数用户来说，这些规则会让人感觉很随意和复杂。这也需要我们极大地改变 CPython 解析器 (以及其他所有 Python 的解析器)。应该注意的是，Python 当前的解析器是故意 "笨 "的 -- 一个简单的语法对用户来说更容易推理。

For all these reasons, square brackets (e.g. `List[int]`) are (and have long been) the preferred syntax for generic type parameters. They can be implemented by defining the `__getitem__()` method on the metaclass, and no new syntax is required at all. This option works in all recent versions of Python (starting with Python 2.2). Python is not alone in this syntactic choice -- generic classes in Scala also use square brackets.

由于所有这些原因，方括号 (例如 `List[int]`) 是 (而且长期以来一直是) 泛型参数化的首选语法。它们可以通过在元类上定义`__getitem__()`方法来实现，而且根本不需要新的语法。这个选项在所有最近版本的Python中都有效 (从Python 2.2开始)。在这种语法选择上，Python并不孤单--Scala中的泛型类也使用方括号。

## [What about existing uses of annotations?](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id53)

注释的现有用途是什么？

One line of argument points out that [PEP 3107](https://www.python.org/dev/peps/pep-3107) explicitly supports the use of arbitrary expressions in function annotations. The new proposal is then considered incompatible with the specification of [PEP 3107](https://www.python.org/dev/peps/pep-3107).

有一种说法指出，PEP 3107明确支持在函数注释中使用任意表达式。于是，新提案被认为与PEP 3107的规范不兼容。

Our response to this is that, first of all, the current proposal does not introduce any direct incompatibilities, so programs using annotations in Python 3.4 will still work correctly and without prejudice in Python 3.5.

我们对此的回应是，首先，目前的提议并没有引入任何直接的不兼容，所以在Python 3.4中使用注解的程序在Python 3.5中仍然可以正确无误地工作。

We do hope that type hints will eventually become the sole use for annotations, but this will require additional discussion and a deprecation period after the initial roll-out of the typing module with Python 3.5. The current PEP will have provisional status (see [PEP 411](https://www.python.org/dev/peps/pep-0411)) until Python 3.6 is released. The fastest conceivable scheme would introduce silent deprecation of non-type-hint annotations in 3.6, full deprecation in 3.7, and declare type hints as the only allowed use of annotations in Python 3.8. This should give authors of packages that use annotations plenty of time to devise another approach, even if type hints become an overnight success.

我们确实希望类型提示最终成为注解的唯一用途，但这需要额外的讨论，并在Python 3.5最初推出类型化模块后有一个废弃期。在Python 3.6发布之前，当前的PEP将具有临时状态（见PEP 411）。可以想象的最快方案是在 3.6 中引入非类型提示注解的沉默废弃，在 3.7 中完全废弃，并宣布类型提示是 Python 3.8 中唯一允许使用的注解。这应该给使用注解的包的作者足够的时间来设计另一种方法，即使类型提示在一夜之间成功。

(*UPDATE:* As of fall 2017, the timeline for the end of provisional status for this PEP and for the `typing.py` module has changed, and so has the deprecation schedule for other uses of annotations. For the updated schedule see [PEP 563](https://www.python.org/dev/peps/pep-0563).)

(更新：从2017年秋天开始，这个PEP和`typing.py`模块的临时状态结束的时间表已经改变，其他注解的使用的废弃时间表也已经改变。最新的时间表见PEP 563）。)

Another possible outcome would be that type hints will eventually become the default meaning for annotations, but that there will always remain an option to disable them. For this purpose the current proposal defines a decorator `@no_type_check` which disables the default interpretation of annotations as type hints in a given class or function. It also defines a meta-decorator `@no_type_check_decorator` which can be used to decorate a decorator (!), causing annotations in any function or class decorated with the latter to be ignored by the type checker.

另一个可能的结果是，类型提示最终将成为注解的默认含义，但仍有一个选项可以禁用它们。为此，目前的提案定义了一个装饰器`@no_type_check`，它可以在一个给定的类或函数中禁止将注释默认解释为类型提示。它还定义了一个元装饰器 `@no_type_check_decorator`，可以用来装饰一个装饰器（！），使任何用后者装饰的函数或类中的注释被类型检查器忽略。

There are also `# type: ignore` comments, and static checkers should support configuration options to disable type checking in selected packages.

还有`# type: ignore` comments，静态检查器应该支持配置选项，在选定的包中禁用类型检查。

Despite all these options, proposals have been circulated to allow type hints and other forms of annotations to coexist for individual arguments. One proposal suggests that if an annotation for a given argument is a dictionary literal, each key represents a different form of annotation, and the key `'type'` would be use for type hints. The problem with this idea and its variants is that the notation becomes very "noisy" and hard to read. Also, in most cases where existing libraries use annotations, there would be little need to combine them with type hints. So the simpler approach of selectively disabling type hints appears sufficient.

尽管有所有这些选项，有人提议允许类型提示和其他形式的注释在单个参数中共存。一个建议是，如果一个给定参数的注释是一个字典字面，每个键代表不同形式的注释，而键 "类型 "将被用于类型提示。这个想法及其变种的问题是，这个注释变得非常 "嘈杂"，难以阅读。而且，在大多数情况下，现有的库使用注释，几乎没有必要将它们与类型提示相结合。因此，选择性地禁用类型提示这种更简单的方法似乎就足够了。

## [The problem of forward declarations](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id54)

前向声明的问题

The current proposal is admittedly sub-optimal when type hints must contain forward references. Python requires all names to be defined by the time they are used. Apart from circular imports this is rarely a problem: "use" here means "look up at runtime", and with most "forward" references there is no problem in ensuring that a name is defined before the function using it is called.

当类型提示必须包含前向引用时，目前的建议无疑是次优的。Python 要求所有的名字在使用时都要被定义。除了循环导入之外，这很少是个问题："使用 "在这里意味着 "在运行时查找"，而且对于大多数 "前向 "引用来说，确保一个名字在使用它的函数被调用之前被定义是没有问题的。

The problem with type hints is that annotations (per [PEP 3107](https://www.python.org/dev/peps/pep-3107), and similar to default values) are evaluated at the time a function is defined, and thus any names used in an annotation must be already defined when the function is being defined. A common scenario is a class definition whose methods need to reference the class itself in their annotations. (More general, it can also occur with mutually recursive classes.) This is natural for container types, for example:

类型提示的问题是，注释（根据[PEP 3107](https://www.python.org/dev/peps/pep-3107)，与默认值类似）在定义函数时被评估，因此在注释中使用的任何名称必须在定义函数时已经被定义。一个常见的情况是一个类的定义，其方法需要在其注释中引用该类本身。(更一般地说，它也可能发生在相互递归的类中。)例如，这对容器类型来说是很自然的：

```python
class Node:
    """Binary tree node."""

    def __init__(self, left: Node, right: Node):
        self.left = left
        self.right = right
```

As written this will not work, because of the peculiarity in Python that class names become defined once the entire body of the class has been executed. Our solution, which isn't particularly elegant, but gets the job done, is to allow using string literals in annotations. Most of the time you won't have to use this though -- most *uses* of type hints are expected to reference builtin types or types defined in other modules.

在书面上这是行不通的，因为Python的特殊性，一旦类的整个主体被执行，类名就会被定义。我们的解决方案是允许在注释中使用字符串字面，这不是特别优雅，但可以完成工作。不过大多数情况下，你不必使用这个方法--大多数类型提示的使用都是为了引用内置类型或其他模块中定义的类型。

A counterproposal would change the semantics of type hints so they aren't evaluated at runtime at all (after all, type checking happens off-line, so why would type hints need to be evaluated at runtime at all). This of course would run afoul of backwards compatibility, since the Python interpreter doesn't actually know whether a particular annotation is meant to be a type hint or something else.

一个反建议是改变类型提示的语义，使它们在运行时根本不被评估（毕竟，类型检查是离线进行的，那么为什么类型提示需要在运行时被评估）。这当然会违背向后兼容的原则，因为 Python 解释器实际上并不知道一个特定的注释是指类型提示还是其他东西。

A compromise is possible where a `__future__` import could enable turning *all* annotations in a given module into string literals, as follows:

一个折中的办法是，一个 `__future__` 的导入可以使一个特定模块中的所有注解变成字符串字面，如下所示：

```python
from __future__ import annotations

class ImSet:
    def add(self, a: ImSet) -> List[ImSet]: ...

assert ImSet.add.__annotations__ == {'a': 'ImSet', 'return': 'List[ImSet]'}
```

Such a `__future__` import statement may be proposed in a separate PEP.

这样的`__future__`导入语句可以在一个单独的PEP中提出。

(*UPDATE:* That `__future__` import statement and its consequences are discussed in [PEP 563](https://www.python.org/dev/peps/pep-0563).)

(*更新*：该`__future__`导入语句和它的后果在PEP 563中讨论。)

## [The double colon](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id55)

双冒号

A few creative souls have tried to invent solutions for this problem. For example, it was proposed to use a double colon (`::`) for type hints, solving two problems at once: disambiguating between type hints and other annotations, and changing the semantics to preclude runtime evaluation. There are several things wrong with this idea, however.

一些有创造力的人曾试图为这个问题发明解决方案。例如，有人提议使用双冒号（::）来表示类型提示，这样可以同时解决两个问题：消除类型提示和其他注释之间的歧义，并改变语义以排除运行时评估。然而，这个想法有几个问题。

- It's ugly. The single colon in Python has many uses, and all of them look familiar because they resemble the use of the colon in English text. This is a general rule of thumb by which Python abides for most forms of punctuation; the exceptions are typically well known from other programming languages. But this use of `::` is unheard of in English, and in other languages (e.g. C++) it is used as a scoping operator, which is a very different beast. In contrast, the single colon for type hints reads naturally -- and no wonder, since it was carefully designed for this purpose (the idea long predates [PEP 3107](https://www.python.org/dev/peps/pep-3107) [[gvr-artima\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#gvr-artima)). It is also used in the same fashion in other languages from Pascal to Swift.

  它很难看。Python 中的单冒号有很多用法，所有这些用法看起来都很熟悉，因为它们与英语文本中冒号的用法相似。这是一个一般的经验法则，Python 对大多数形式的标点符号都会遵守；例外情况通常是其他编程语言中众所周知的。但是这种`::`的使用在英语中是闻所未闻的，在其它语言中(如C++)它被用作范围操作符，这是一个非常不同的野兽。相比之下，用于类型提示的单冒号读起来很自然--也难怪，因为它是为这个目的精心设计的（这个想法早在[PEP 3107](https://www.python.org/dev/peps/pep-3107)[[gvr-artima]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#gvr-artima)之前就有了）。在从Pascal到Swift的其他语言中，它也以同样的方式被使用。

- What would you do for return type annotations?

  对于返回类型的注释，你会怎么做？

- It's actually a feature that type hints are evaluated at runtime.
  
  实际上，类型提示是在运行时评估的，这是一个特点。
  
  - Making type hints available at runtime allows runtime type checkers to be built on top of type hints.
  
    使类型提示在运行时可用，允许运行时类型检查器建立在类型提示之上。
  
  - It catches mistakes even when the type checker is not run. Since it is a separate program, users may choose not to run it (or even install it), but might still want to use type hints as a concise form of documentation. Broken type hints are no use even for documentation.
  
    即使在类型检查器没有运行的情况下，它也能捕捉到错误。由于它是一个独立的程序，用户可以选择不运行它（甚至不安装它），但可能仍然想使用类型提示作为一种简洁的文档形式。破碎的类型提示甚至对文档也没有用。
  
- Because it's new syntax, using the double colon for type hints would limit them to code that works with Python 3.5 only. By using existing syntax, the current proposal can easily work for older versions of Python 3. (And in fact mypy supports Python 3.2 and newer.)

  因为这是新的语法，使用双冒号进行类型提示会把它们限制在只适用于 Python 3.5 的代码中。通过使用现有的语法，目前的建议可以很容易地适用于旧版本的Python 3。(而事实上mypy支持Python 3.2和更新的版本)。

- If type hints become successful we may well decide to add new syntax in the future to declare the type for variables, for example `var age: int = 42`. If we were to use a double colon for argument type hints, for consistency we'd have to use the same convention for future syntax, perpetuating the ugliness.

  如果类型提示变得成功，我们很可能决定在未来添加新的语法来声明变量的类型，例如`var age: int = 42`。如果我们使用双冒号来进行参数类型提示，为了保持一致性，我们将不得不在未来的语法中使用相同的约定，从而延续这种丑陋。

## [Other forms of new syntax](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id56)

其他形式的新语法

A few other forms of alternative syntax have been proposed, e.g. the introduction of a `where` keyword [[roberge\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#roberge), and Cobra-inspired `requires` clauses. But these all share a problem with the double colon: they won't work for earlier versions of Python 3. The same would apply to a new `__future__` import.

一些其他形式的替代语法已经被提出，例如引入`where`关键字[[roberge]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#roberge)，以及受Cobra启发的`requires`条款。但是这些都和双冒号有一个共同的问题：它们对Python 3的早期版本不起作用。这同样适用于新的 `__future__`导入。

## [Other backwards compatible conventions](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id57)

其他向后兼容的惯例

The ideas put forward include:

提出的想法包括：

- A decorator, e.g. `@typehints(name=str, returns=str)`. This could work, but it's pretty verbose (an extra line, and the argument names must be repeated), and a far cry in elegance from the [PEP 3107](https://www.python.org/dev/peps/pep-3107) notation.

  一个装饰器，例如：`@typehints(name=str, returns=str)`。这可能是可行的，但它相当繁琐（多了一行，而且参数名必须重复），而且与PEP 3107的符号相比，优雅程度相差甚远。

- Stub files. We do want stub files, but they are primarily useful for adding type hints to existing code that doesn't lend itself to adding type hints, e.g. 3rd party packages, code that needs to support both Python 2 and Python 3, and especially extension modules. For most situations, having the annotations in line with the function definitions makes them much more useful.

  存根文件。我们确实需要存根文件，但它们主要用于向现有的代码添加类型提示，而这些代码本身并不适合添加类型提示，例如第三方软件包，需要同时支持 Python 2 和 Python 3 的代码，特别是扩展模块。在大多数情况下，让注释与函数定义保持一致会使它们更加有用。

- Docstrings. There is an existing convention for docstrings, based on the Sphinx notation (`:type arg1: description`). This is pretty verbose (an extra line per parameter), and not very elegant. We could also make up something new, but the annotation syntax is hard to beat (because it was designed for this very purpose).

  文件串。有一个现有的文档字符串的约定，基于Sphinx符号 (`:type arg1: description`)。这是很冗长的（每个参数要多写一行），而且不是很优雅。我们也可以编造一些新的东西，但是注释语法是很难超越的（因为它就是为了这个目的而设计的）。

It's also been proposed to simply wait another release. But what problem would that solve? It would just be procrastination.

也有人提议，干脆再等一个版本。但这能解决什么问题呢？这只是拖延而已。

# [PEP Development Process](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id58)

PEP开发过程

A live draft for this PEP lives on GitHub [[github\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#github). There is also an issue tracker [[issues\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#issues), where much of the technical discussion takes place.

本PEP的实时草案存在于GitHub上[[github]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#github)。还有一个问题追踪器[[issues]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#issues)，很多技术讨论都是在这里进行的。

The draft on GitHub is updated regularly in small increments. The official PEPS repo [[peps](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#peps)] is (usually) only updated when a new draft is posted to python-dev.

GitHub上的草案会定期进行小范围的更新。PEPS 的官方 repo [[eps](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#peps)] （通常）只有在新的草案发布到 python-dev 时才会更新。

# [Acknowledgements](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id59)

致谢

This document could not be completed without valuable input, encouragement and advice from Jim Baker, Jeremy Siek, Michael Matson Vitousek, Andrey Vlasovskikh, Radomir Dopieralski, Peter Ludemann, and the BDFL-Delegate, Mark Shannon.

如果没有Jim Baker、Jeremy Siek、Michael Matson Vitousek、Andrey Vlasovskikh、Radomir Dopieralski、Peter Ludemann和BDFL-Delegate Mark Shannon的宝贵意见、鼓励和建议，本文件就无法完成。

Influences include existing languages, libraries and frameworks mentioned in [PEP 482](https://www.python.org/dev/peps/pep-0482). Many thanks to their creators, in alphabetical order: Stefan Behnel, William Edwards, Greg Ewing, Larry Hastings, Anders Hejlsberg, Alok Menghrajani, Travis E. Oliphant, Joe Pamer, Raoul-Gabriel Urma, and Julien Verlaguet.

影响因素包括[PEP 482](https://www.python.org/dev/peps/pep-0482)中提到的现有语言、库和框架。非常感谢他们的创造者，按字母顺序排列。Stefan Behnel, William Edwards, Greg Ewing, Larry Hastings, Anders Hejlsberg, Alok Menghrajani, Travis E. Oliphant, Joe Pamer, Raoul-Gabriel Urma, and Julien Verlaguet.

# [References](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id60)

参考文献

|                                                              |                                                              |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [[mypy\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id1)     | [http://mypy-lang.org](http://mypy-lang.org/)                |
|                                                              |                                                              |
| [gvr-artima]                                                 | *([1](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id2), [2](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id6))* http://www.artima.com/weblogs/viewpost.jsp?thread=85551 |
|                                                              |                                                              |
| [[wiki-variance\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id3) | [http://en.wikipedia.org/wiki/Covariance_and_contravariance_%28computer_science%29](http://en.wikipedia.org/wiki/Covariance_and_contravariance_(computer_science)) |
|                                                              |                                                              |
| [[typeshed\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id5) | https://github.com/python/typeshed/                          |
|                                                              |                                                              |
| [pyflakes]                                                   | https://github.com/pyflakes/pyflakes/                        |
|                                                              |                                                              |
| [pylint]                                                     | [http://www.pylint.org](http://www.pylint.org/)              |
|                                                              |                                                              |
| [[roberge\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id7)  | http://aroberge.blogspot.com/2015/01/type-hinting-in-python-focus-on.html |
|                                                              |                                                              |
| [[github\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id8)   | https://github.com/python/typing                             |
|                                                              |                                                              |
| [[issues\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id9)   | https://github.com/python/typing/issues                      |
|                                                              |                                                              |
| [peps]                                                       | https://hg.python.org/peps/file/tip/pep-0484.txt             |
|                                                              |                                                              |
| [[importdocs\]](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id4) | https://docs.python.org/3/reference/import.html#submodules   |
|                                                              |                                                              |

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md#id61)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0484.txt

> 这是翻译的最长的一篇PEP了，翻译到最后有点抓狂，有不足的地方望多多包涵，译者注。

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。