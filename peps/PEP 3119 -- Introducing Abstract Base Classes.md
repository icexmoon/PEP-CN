# PEP 3119 -- Introducing Abstract Base Classes

介绍抽象基类

> 原文地址：<https://www.python.org/dev/peps/pep-3119/>

|               |                                                              |
| :------------ | ------------------------------------------------------------ |
| PEP:          | 3119                                                         |
| Title:        | Introducing Abstract Base Classes                            |
| Author:       | Guido van Rossum <guido at python.org>, Talin <viridia at gmail.com> |
| Status:       | Final                                                        |
| Type:         | Standards Track                                              |
| Created:      | 18-Apr-2007                                                  |
| Post-History: | 26-Apr-2007, 11-May-2007                                     |

------

Contents

- Abstract
  - [Acknowledgements](https://www.python.org/dev/peps/pep-3119/#acknowledgements)
- [Rationale](https://www.python.org/dev/peps/pep-3119/#rationale)
- Specification
  - [Overloading `isinstance()` and `issubclass()`](https://www.python.org/dev/peps/pep-3119/#overloading-isinstance-and-issubclass)
  - [The `abc` Module: an ABC Support Framework](https://www.python.org/dev/peps/pep-3119/#the-abc-module-an-abc-support-framework)
  - ABCs for Containers and Iterators
    - [One Trick Ponies](https://www.python.org/dev/peps/pep-3119/#one-trick-ponies)
    - [Sets](https://www.python.org/dev/peps/pep-3119/#sets)
    - [Mappings](https://www.python.org/dev/peps/pep-3119/#mappings)
    - [Sequences](https://www.python.org/dev/peps/pep-3119/#sequences)
  - [Strings](https://www.python.org/dev/peps/pep-3119/#strings)
- ABCs vs. Alternatives
  - [ABCs vs. Duck Typing](https://www.python.org/dev/peps/pep-3119/#abcs-vs-duck-typing)
  - [ABCs vs. Generic Functions](https://www.python.org/dev/peps/pep-3119/#abcs-vs-generic-functions)
  - [ABCs vs. Interfaces](https://www.python.org/dev/peps/pep-3119/#abcs-vs-interfaces)
- [References](https://www.python.org/dev/peps/pep-3119/#references)
- [Copyright](https://www.python.org/dev/peps/pep-3119/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-3119/#id29)

摘要

This is a proposal to add Abstract Base Class (ABC) support to Python 3000. It proposes:

这是一个为 Python 3 增加抽象基类 (ABC) 支持的提案。它建议：

- A way to overload `isinstance()` and `issubclass()`.

  一种重载 `isinstance()` 和 `issubclass()` 的方法。

- A new module `abc` which serves as an "ABC support framework". It defines a metaclass for use with ABCs and a decorator that can be used to define abstract methods.

  一个新的模块`abc`，作为一个 "ABC支持框架"。它定义了一个用于ABC的元类和一个可以用于定义抽象方法的装饰器。

- Specific ABCs for containers and iterators, to be added to the collections module.

  用于容器和迭代器的特定ABC，将被添加到 collections 模块中。

Much of the thinking that went into the proposal is not about the specific mechanism of ABCs, as contrasted with Interfaces or Generic Functions (GFs), but about clarifying philosophical issues like "what makes a set", "what makes a mapping" and "what makes a sequence".

该提案中的大部分思考不是关于ABC的具体机制，与接口或泛型函数（GF）相比，而是关于澄清哲学问题，如 "什么是集合"、"什么是映射 "和 "什么是序列"。

There's also a companion [PEP 3141](https://www.python.org/dev/peps/pep-3141), which defines ABCs for numeric types.

还有一个配套的PEP 3141，它为数字类型定义了ABC。

## [Acknowledgements](https://www.python.org/dev/peps/pep-3119/#id30)

致谢

Talin wrote the Rationale below [[1\]](https://www.python.org/dev/peps/pep-3119/#id13) as well as most of the section on ABCs vs. Interfaces. For that alone he deserves co-authorship. The rest of the PEP uses "I" referring to the first author.

Talin写了下面的理由[1]，以及ABC与界面的大部分章节。仅仅因为这一点，他就值得成为共同作者。PEP的其他部分用 "I "来指代第一作者。

# [Rationale](https://www.python.org/dev/peps/pep-3119/#id31)

理论依据

In the domain of object-oriented programming, the usage patterns for interacting with an object can be divided into two basic categories, which are 'invocation' and 'inspection'.

在面向对象编程领域，与对象交互的使用模式可以分为两个基本类别，即 "调用 "和 "检查"。

Invocation means interacting with an object by invoking its methods. Usually this is combined with polymorphism, so that invoking a given method may run different code depending on the type of an object.

调用是指通过调用一个对象的方法来与之交互。通常，这与多态相结合，因此，调用一个给定的方法可以根据对象的类型运行不同的代码。

Inspection means the ability for external code (outside of the object's methods) to examine the type or properties of that object, and make decisions on how to treat that object based on that information.

检查意味着外部代码（在对象的方法之外）能够检查该对象的类型或属性，并根据这些信息决定如何处理该对象。

Both usage patterns serve the same general end, which is to be able to support the processing of diverse and potentially novel objects in a uniform way, but at the same time allowing processing decisions to be customized for each different type of object.

这两种使用模式都是为同一个总体目标服务的，即能够以统一的方式支持对不同的和潜在的新对象的处理，但同时允许为每个不同类型的对象设置处理决策。

In classical OOP theory, invocation is the preferred usage pattern, and inspection is actively discouraged, being considered a relic of an earlier, procedural programming style. However, in practice this view is simply too dogmatic and inflexible, and leads to a kind of design rigidity that is very much at odds with the dynamic nature of a language like Python.

在经典的OOP理论中，调用是首选的使用模式，而检查是被积极阻止的，被认为是早期程序化编程风格的遗迹。然而，在实践中，这种观点实在是太教条和不灵活了，它导致了一种设计的僵化，与像Python这样的语言的动态特性非常不一致。

In particular, there is often a need to process objects in a way that wasn't anticipated by the creator of the object class. It is not always the best solution to build in to every object methods that satisfy the needs of every possible user of that object. Moreover, there are many powerful dispatch philosophies that are in direct contrast to the classic OOP requirement of behavior being strictly encapsulated within an object, examples being rule or pattern-match driven logic.

特别是，经常需要以一种对象类的创建者没有预料到的方式来处理对象。在每个对象中建立满足该对象的每个可能的用户的需求的方法并不总是最好的解决方案。此外，有许多强大的调度哲学与经典的OOP中行为必须被严格地封装在一个对象中的要求是截然相反的，例子是被规则或模式匹配所驱动的逻辑。

On the other hand, one of the criticisms of inspection by classic OOP theorists is the lack of formalisms and the ad hoc nature of what is being inspected. In a language such as Python, in which almost any aspect of an object can be reflected and directly accessed by external code, there are many different ways to test whether an object conforms to a particular protocol or not. For example, if asking 'is this object a mutable sequence container?', one can look for a base class of 'list', or one can look for a method named `__getitem__`. But note that although these tests may seem obvious, neither of them are correct, as one generates false negatives, and the other false positives.

另一方面，经典的OOP理论家对检查的批评之一是缺乏形式化，以及被检查的内容具有临时性。在像Python这样的语言中，对象的几乎任何方面都可以被外部代码反射和直接访问，有许多不同的方法来测试一个对象是否符合特定的协议。例如，如果问'这个对象是一个可变的序列容器吗'，我们可以寻找`list`的基类，或者寻找一个名为`__getitem__`的方法。但是请注意，尽管这些测试看起来很明显，但它们都不正确，因为一个产生了错误的否定，而另一个产生了错误的肯定。

> 这里指不以`list`为基类的类也可能是一个可变序列容器，而包含`__getitem__`方法的类也不一定是一个可变序列容器，译者注。

The generally agreed-upon remedy is to standardize the tests, and group them into a formal arrangement. This is most easily done by associating with each class a set of standard testable properties, either via the inheritance mechanism or some other means. Each test carries with it a set of promises: it contains a promise about the general behavior of the class, and a promise as to what other class methods will be available.

普遍同意的补救措施是将测试标准化，并将它们归入正式的安排。通过继承机制或其他方式，将一组标准的可测试属性与每个类联系起来，这是最容易做到的。每个测试都带有一组承诺：它包含对类的一般行为的承诺，以及对其他类方法的承诺。

This PEP proposes a particular strategy for organizing these tests known as Abstract Base Classes, or ABC. ABCs are simply Python classes that are added into an object's inheritance tree to signal certain features of that object to an external inspector. Tests are done using `isinstance()`, and the presence of a particular ABC means that the test has passed.

本 PEP 提出了一种组织这些测试的特殊策略，称为抽象基类，或 ABC。ABC是简单的Python类，它被添加到一个对象的继承树中，以向外部检查者传递该对象的某些特征。测试是使用`isinstance()`完成的，一个特定的ABC的存在意味着测试通过。

In addition, the ABCs define a minimal set of methods that establish the characteristic behavior of the type. Code that discriminates objects based on their ABC type can trust that those methods will always be present. Each of these methods are accompanied by an generalized abstract semantic definition that is described in the documentation for the ABC. These standard semantic definitions are not enforced, but are strongly recommended.

此外，ABC定义了一组最小的方法，建立了该类型的特征行为。根据ABC类型来判别对象的代码可以相信这些方法总是存在的。这些方法中的每一个都有一个概括性的抽象语义定义，在ABC的文档中有所描述。这些标准的语义定义并不强制执行，但强烈推荐。

Like all other things in Python, these promises are in the nature of a gentlemen's agreement, which in this case means that while the language does enforce some of the promises made in the ABC, it is up to the implementer of the concrete class to insure that the remaining ones are kept.

像 Python 中的所有其它东西一样，这些承诺的性质是君子协定，在这种情况下，这意味着虽然语言确实强制执行了 ABC 中的一些承诺，但要由具体类的实现者来确保其余的承诺得到遵守。

# [Specification](https://www.python.org/dev/peps/pep-3119/#id32)

定义

The specification follows the categories listed in the abstract:

该规范遵循抽象中列出的类别：

- A way to overload `isinstance()` and `issubclass()`.

  一种重载`isinstance()`和`issubclass()`的方法。

- A new module `abc` which serves as an "ABC support framework". It defines a metaclass for use with ABCs and a decorator that can be used to define abstract methods.

  一个新的模块`abc`，作为一个 "ABC支持框架"。它定义了一个用于ABC的元类和一个可以用来定义抽象方法的装饰器。

- Specific ABCs for containers and iterators, to be added to the collections module.

  用于容器和迭代器的特定ABC，将被添加到 collections 模块中。

## [Overloading `isinstance()` and `issubclass()`](https://www.python.org/dev/peps/pep-3119/#id33)

重载 `isinstance()` 和 `issubclass()`

During the development of this PEP and of its companion, [PEP 3141](https://www.python.org/dev/peps/pep-3141), we repeatedly faced the choice between standardizing more, fine-grained ABCs or fewer, coarse-grained ones. For example, at one stage, [PEP 3141](https://www.python.org/dev/peps/pep-3141) introduced the following stack of base classes used for complex numbers: MonoidUnderPlus, AdditiveGroup, Ring, Field, Complex (each derived from the previous). And the discussion mentioned several other algebraic categorizations that were left out: Algebraic, Transcendental, and IntegralDomain, and PrincipalIdealDomain. In earlier versions of the current PEP, we considered the use cases for separate classes like Set, ComposableSet, MutableSet, HashableSet, MutableComposableSet, HashableComposableSet.

在这个PEP及其配套的PEP 3141的开发过程中，我们反复面临这样的选择：是将更多的、细粒度的ABC标准化，还是将更少的、粗粒度的ABC标准化。例如，在一个阶段，PEP 3141引入了以下用于复数的基类堆栈。MonoidUnderPlus、AdditiveGroup、Ring、Field、Complex（每个都是从前一个派生出来的）。而讨论中提到了其他几个被遗漏的代数分类。代数域（Algebraic）、超越域（Transcendental）和积分域（IntegralDomain），以及主要理想域（PrincipalIdealDomain）。在当前PEP的早期版本中，我们考虑了像Set、ComposableSet、MutableSet、HashableSet、MutableComposableSet、HashableComposableSet这样独立类的用例。

The dilemma here is that we'd rather have fewer ABCs, but then what should a user do who needs a less refined ABC? Consider e.g. the plight of a mathematician who wants to define his own kind of Transcendental numbers, but also wants float and int to be considered Transcendental. [PEP 3141](https://www.python.org/dev/peps/pep-3141) originally proposed to patch `float.__bases__` for that purpose, but there are some good reasons to keep the built-in types immutable (for one, they are shared between all Python interpreters running in the same address space, as is used by mod_python [[16\]](https://www.python.org/dev/peps/pep-3119/#id28)).

这里的困境是，我们宁愿少一些ABC，但如果用户需要一个不那么精细的ABC，应该怎么办？例如，考虑到一个数学家的困境，他想定义他自己的那种超限数，但也希望float和int被认为是超限数。[PEP 3141](https://www.python.org/dev/peps/pep-3141) 最初提议为此目的修补 `float.__bases__`，但是有一些很好的理由来保持内置类型的不可变性 (其一，它们在所有运行在同一地址空间的 Python 解释器之间共享，正如 mod_python [[16\]](https://www.python.org/dev/peps/pep-3119/#id28) 使用的那样)。

Another example would be someone who wants to define a generic function ([PEP 3124](https://www.python.org/dev/peps/pep-3124)) for any sequence that has an `append()` method. The `Sequence` ABC (see below) doesn't promise the `append()` method, while `MutableSequence` requires not only `append()` but also various other mutating methods.

另一个例子是有人想为任何有`append()`方法的序列定义一个通用函数（PEP 3124）。`Sequence` ABC（见下文）不承诺`append()`方法，而`MutableSequence`不仅需要`append()`，还需要其他各种变异方法。

To solve these and similar dilemmas, the next section will propose a metaclass for use with ABCs that will allow us to add an ABC as a "virtual base class" (not the same concept as in C++) to any class, including to another ABC. This allows the standard library to define ABCs `Sequence` and `MutableSequence` and register these as virtual base classes for built-in types like `basestring`, `tuple` and `list`, so that for example the following conditions are all true:

为了解决这些和类似的困境，下一节将提出一个用于ABC的元类，它将允许我们把一个ABC作为 "虚拟基类"（与C++中的概念不同）添加到任何类中，包括另一个ABC。这允许标准库定义ABC的`Sequence`和`MutableSequence`，并将其注册为内置类型（如`basetring`、`tuple`和`list`）的虚拟基类，这样，例如以下条件都是真的：

```python
isinstance([], Sequence)
issubclass(list, Sequence)
issubclass(list, MutableSequence)
isinstance((), Sequence)
not issubclass(tuple, MutableSequence)
isinstance("", Sequence)
issubclass(bytearray, MutableSequence)
```

The primary mechanism proposed here is to allow overloading the built-in functions `isinstance()` and `issubclass()`. The overloading works as follows: The call `isinstance(x, C)` first checks whether `C.__instancecheck__` exists, and if so, calls `C.__instancecheck__(x)` instead of its normal implementation. Similarly, the call `issubclass(D, C)` first checks whether `C.__subclasscheck__` exists, and if so, calls `C.__subclasscheck__(D)` instead of its normal implementation.

这里提出的主要机制是允许重载内置函数`isinstance()`和`issubclass()`。重载的工作方式如下。调用 `isinstance(x, C)` 首先检查 `C.__instancecheck__` 是否存在，如果存在，则调用 `C.__instancecheck__(x)` 而不是它的正常实现。类似地，调用 `issubclass(D, C)` 首先检查 `C.__subclasscheck__` 是否存在，如果存在，调用 `C.__subclasscheck__(D)` 而不是它的正常实现。

Note that the magic names are not `__isinstance__` and `__issubclass__`; this is because the reversal of the arguments could cause confusion, especially for the `issubclass()` overloader.

注意，这些神奇的名字不是 `__isinstance__` 和 `__issubclass__`；这是因为参数的反转可能会引起混淆，特别是对于 `issubclass()` 的重载。

A prototype implementation of this is given in [[12\]](https://www.python.org/dev/peps/pep-3119/#id24).

在 [12] 中给出了一个原型的实现。

Here is an example with (naively simple) implementations of `__instancecheck__` and `__subclasscheck__`:

这里是一个例子，它包含了 `__instancecheck__` 和 `__subclasscheck__` 的 (理想化简单) 实现。

> 如果还不明白元类如何运作，可以阅读[**Python学习笔记38：类元编程**](https://blog.icexmoon.xyz/archives/161.html)，译者注。

```python
class ABCMeta(type):

    def __instancecheck__(cls, inst):
        """Implement isinstance(inst, cls)."""
        return any(cls.__subclasscheck__(c)
                   for c in {type(inst), inst.__class__})

    def __subclasscheck__(cls, sub):
        """Implement issubclass(sub, cls)."""
        candidates = cls.__dict__.get("__subclass__", set()) | {cls}
        return any(c in candidates for c in sub.mro())

class Sequence(metaclass=ABCMeta):
    __subclass__ = {list, tuple}

assert issubclass(list, Sequence)
assert issubclass(tuple, Sequence)

class AppendableSequence(Sequence):
    __subclass__ = {list}

assert issubclass(list, AppendableSequence)
assert isinstance([], AppendableSequence)

assert not issubclass(tuple, AppendableSequence)
assert not isinstance((), AppendableSequence)
```

The next section proposes a full-fledged implementation.

下一节提出了一个完整的实施方案。

## [The `abc` Module: an ABC Support Framework](https://www.python.org/dev/peps/pep-3119/#id34)

`abc`模块：一个ABC支持框架

The new standard library module `abc`, written in pure Python, serves as an ABC support framework. It defines a metaclass `ABCMeta` and decorators `@abstractmethod` and `@abstractproperty`. A sample implementation is given by [[13\]](https://www.python.org/dev/peps/pep-3119/#id25).

新的标准库模块`abc`是用纯Python编写的，作为一个ABC支持框架。它定义了一个元类`ABCMeta`和装饰器`@abstractmethod`和`@abstractproperty`。[13]给出了一个示例实现。

The `ABCMeta` class overrides `__instancecheck__` and `__subclasscheck__` and defines a `register` method. The `register` method takes one argument, which must be a class; after the call `B.register(C)`, the call `issubclass(C, B)` will return True, by virtue of `B.__subclasscheck__(C)` returning True. Also, `isinstance(x, B)` is equivalent to `issubclass(x.__class__, B) or issubclass(type(x), B)`. (It is possible `type(x)` and `x.__class__` are not the same object, e.g. when x is a proxy object.)

`ABCMeta`类覆盖了`__instancecheck__`和`__subclasscheck__`，并定义了一个 `register` 方法。`register` 方法需要一个参数，它必须是一个类；在调用 `B.register(C)` 之后，由于 `B.__subclasscheck__(C)` 返回 True，所以调用 `issubclass(C, B)` 将返回 True。另外，`isinstance(x, B)` 等同于 `issubclass(x.__class__, B)` 或 `issubclass(type(x), B)`。(`type(x)`和`x.__class__`有可能不是同一个对象，例如，当x是一个代理对象时)。

> 这里的`B.register(C)`相当于上边理想化实现中的`B.__subclass__.append(C)`，其目的都是将C标记为B的子类，译者注。

These methods are intended to be called on classes whose metaclass is (derived from) `ABCMeta`; for example:

这些方法的目的是在元类是（派生于） `ABCMeta`  的类上被调用；例如：

> 这里指通过指定`metaclass=ABCMeta`方式实现的抽象类，或者直接派生自`ABCMeta`的其它元类，译者注。

```python
from abc import ABCMeta

class MyABC(metaclass=ABCMeta):
    pass

MyABC.register(tuple)

assert issubclass(tuple, MyABC)
assert isinstance((), MyABC)
```

The last two asserts are equivalent to the following two:

最后两个断言与下面两个断言是等价的：

```python
assert MyABC.__subclasscheck__(tuple)
assert MyABC.__instancecheck__(())
```

Of course, you can also directly subclass MyABC:

当然，你也可以直接子类化MyABC：

```python
class MyClass(MyABC):
    pass

assert issubclass(MyClass, MyABC)
assert isinstance(MyClass(), MyABC)
```

Also, of course, a tuple is not a `MyClass`:

当然，一个元组也不是一个`MyClass`：

```python
assert not issubclass(tuple, MyClass)
assert not isinstance((), MyClass)
```

You can register another class as a subclass of `MyClass`:

你可以注册另一个类作为`MyClass`的一个子类：

```python
MyClass.register(list)

assert issubclass(list, MyClass)
assert issubclass(list, MyABC)
```

You can also register another ABC:

你也可以注册另一个ABC：

```python
class AnotherClass(metaclass=ABCMeta):
    pass

AnotherClass.register(basestring)

MyClass.register(AnotherClass)

assert isinstance(str, MyABC)
```

That last assert requires tracing the following superclass-subclass relationships:

最后一个断言需要追踪以下超类-子类关系：

```python
MyABC -> MyClass (using regular subclassing)
MyClass -> AnotherClass (using registration)
AnotherClass -> basestring (using registration)
basestring -> str (using regular subclassing)
```

The `abc` module also defines a new decorator, `@abstractmethod`, to be used to declare abstract methods. A class containing at least one method declared with this decorator that hasn't been overridden yet cannot be instantiated. Such methods may be called from the overriding method in the subclass (using `super` or direct invocation). For example:

`abc`模块还定义了一个新的装饰器 `@abstractmethod`，用来声明抽象方法。一个包含至少一个用这个装饰器声明的方法的类，如果还没有被重写，就不能被实例化。这样的方法可以从子类的覆盖方法中调用（使用`super`或直接调用，比如：

```python
from abc import ABCMeta, abstractmethod

class A(metaclass=ABCMeta):
    @abstractmethod
    def foo(self): pass

A()  # raises TypeError

class B(A):
    pass

B()  # raises TypeError

class C(A):
    def foo(self): print(42)

C()  # works
```

**Note:** The `@abstractmethod` decorator should only be used inside a class body, and only for classes whose metaclass is (derived from) `ABCMeta`. Dynamically adding abstract methods to a class, or attempting to modify the abstraction status of a method or class once it is created, are not supported. The `@abstractmethod` only affects subclasses derived using regular inheritance; "virtual subclasses" registered with the `register()` method are not affected.

**注意：** `@abstractmethod`装饰器只能在类体内使用，而且只能用于元类是（派生于）`ABCMeta`的类。不支持在类中动态添加抽象方法，或在方法或类创建后试图修改其抽象状态。`@abstractmethod`只影响到使用常规继承衍生的子类；用`register()`方法注册的 "虚拟子类 "不受影响。

**Implementation:** The `@abstractmethod` decorator sets the function attribute `__isabstractmethod__` to the value `True`. The `ABCMeta.__new__` method computes the type attribute `__abstractmethods__` as the set of all method names that have an `__isabstractmethod__` attribute whose value is true. It does this by combining the `__abstractmethods__` attributes of the base classes, adding the names of all methods in the new class dict that have a true `__isabstractmethod__` attribute, and removing the names of all methods in the new class dict that don't have a true `__isabstractmethod__` attribute. If the resulting `__abstractmethods__` set is non-empty, the class is considered abstract, and attempts to instantiate it will raise `TypeError`. (If this were implemented in CPython, an internal flag `Py_TPFLAGS_ABSTRACT` could be used to speed up this check [[6\]](https://www.python.org/dev/peps/pep-3119/#id18).)

**实现：** `@abstractmethod`装饰器将函数属性`__isabstractmethod__`设置为`True`值。`ABCMeta.__new__`方法计算类型属性`__abstractmethods__`为所有具有`__isabstractmethod__`属性的方法名的集合，其值为true。它通过合并基类的`__abstractmethods__`属性来实现，在新类的dict中加入所有具有 True 的`__isabstractmethod__`属性的方法名称，并删除新类dict中所有不具有 True 的`__isabstractmethod__`属性的方法名称。如果产生的`__abstractmethods__`集合不是空的，那么这个类就被认为是抽象的，试图实例化它将引发`TypeError`。(如果这在CPython中实现，可以使用一个内部标志`Py_TPFLAGS_ABSTRACT`来加速这个检查[[6](https://www.python.org/dev/peps/pep-3119/#id18)]。)

**Discussion:** Unlike Java's abstract methods or C++'s pure abstract methods, abstract methods as defined here may have an implementation. This implementation can be called via the `super` mechanism from the class that overrides it. This could be useful as an end-point for a super-call in framework using cooperative multiple-inheritance [[7\]](https://www.python.org/dev/peps/pep-3119/#id19), [[8\]](https://www.python.org/dev/peps/pep-3119/#id20).

讨论：与Java的抽象方法或C++的纯抽象方法不同，这里定义的抽象方法可以有一个实现。这个实现可以通过超机制从覆盖它的类中调用。在使用合作式多重继承的框架中，这可以作为超级调用的一个终端[7], [8]。

> 这段论述我的能力理解不能，所以仅供参考，如果你有更好的翻译，请联系我，译者注。

A second decorator, `@abstractproperty`, is defined in order to define abstract data attributes. Its implementation is a subclass of the built-in `property` class that adds an `__isabstractmethod__` attribute:

第二个装饰器，`@abstractproperty`，是为了定义抽象数据属性而定义的。它的实现是内置`property`类的一个子类，增加了一个`__isabstractmethod__`属性：

```python
class abstractproperty(property):
    __isabstractmethod__ = True
```

It can be used in two ways:

可以用两种方式使用：

```python
class C(metaclass=ABCMeta):

    # A read-only property:

    @abstractproperty
    def readonly(self):
        return self.__x

    # A read-write property (cannot use decorator syntax):

    def getx(self):
        return self.__x
    def setx(self, value):
        self.__x = value
    x = abstractproperty(getx, setx)
```

Similar to abstract methods, a subclass inheriting an abstract property (declared using either the decorator syntax or the longer form) cannot be instantiated unless it overrides that abstract property with a concrete property.

与抽象方法类似，继承了抽象特性（使用装饰器语法或较长形式声明）的子类不能被实例化，除非它用一个具体的特性覆盖该抽象特性。

> 这里使用《Fluent Python》中文版的翻译，将property翻译为特性，以与attribute进行区分，译者注。

## [ABCs for Containers and Iterators](https://www.python.org/dev/peps/pep-3119/#id35)

容器和迭代器的ABC

The `collections` module will define ABCs necessary and sufficient to work with sets, mappings, sequences, and some helper types such as iterators and dictionary views. All ABCs have the above-mentioned `ABCMeta` as their metaclass.

`collections`模块将定义必要的和足够的ABC来处理集合、映射、序列和一些辅助类型，如迭代器和字典视图。所有的ABC都有上面提到的`ABCMeta`作为它们的元类。

The ABCs provide implementations of their abstract methods that are technically valid but fairly useless; e.g. `__hash__` returns 0, and `__iter__` returns an empty iterator. In general, the abstract methods represent the behavior of an empty container of the indicated type.

ABC 提供了他们的抽象方法的实现，这些方法在技术上是有效的，但相当无用；例如 `__hash__` 返回 0，`__iter__` 返回一个空的迭代器。一般来说，这些抽象方法代表了指定类型的空容器的行为。

Some ABCs also provide concrete (i.e. non-abstract) methods; for example, the `Iterator` class has an `__iter__` method returning itself, fulfilling an important invariant of iterators (which in Python 2 has to be implemented anew by each iterator class). These ABCs can be considered "mix-in" classes.

一些ABC还提供了具体的(即非抽象的)方法；例如，`Iterator`类有一个返回自身的`__iter__`方法，实现了迭代器的一个重要不变性(在Python 2中，每个迭代器类都必须重新实现)。这些ABC可以被认为是 "混入 "类。

No ABCs defined in the PEP override `__init__`, `__new__`, `__str__` or `__repr__`. Defining a standard constructor signature would unnecessarily constrain custom container types, for example Patricia trees or gdbm files. Defining a specific string representation for a collection is similarly left up to individual implementations.

PEP 中定义的 ABC 没有覆盖 `__init__`, `__new__`, `__str__` 或 `__repr__`。定义一个标准的构造函数签名会不必要地限制自定义的容器类型，例如Patricia树或gdbm文件。为一个集合定义一个特定的字符串表示法，同样也是由个人实现来决定的。

**Note:** There are no ABCs for ordering operations (`__lt__`, `__le__`, `__ge__`, `__gt__`). Defining these in a base class (abstract or not) runs into problems with the accepted type for the second operand. For example, if class `Ordering` defined `__lt__`, one would assume that for any `Ordering` instances `x` and `y`, `x < y` would be defined (even if it just defines a partial ordering). But this cannot be the case: If both `list` and `str` derived from `Ordering`, this would imply that `[1, 2] < (1, 2)` should be defined (and presumably return False), while in fact (in Python 3000!) such "mixed-mode comparisons" operations are explicitly forbidden and raise `TypeError`. See [PEP 3100](https://www.python.org/dev/peps/pep-3100) and [[14\]](https://www.python.org/dev/peps/pep-3119/#id26) for more information. (This is a special case of a more general issue with operations that take another argument of the same type).

**注意：**对于排序操作（`__lt__`, `__le__`, `__ge__`, `__gt__`）没有 ABC。在一个基类中定义这些操作（无论是否抽象）会遇到第二操作数的可接受类型的问题。例如，如果类 `Ordering` 定义了 `__lt__`，我们会认为对于任何 `Ordering` 实例 `x` 和 `y`，`x < y` 将被定义 (即使它只是定义了一个部分排序)。但是情况不可能是这样的。如果 `list` 和 `str` 都来自 `Ordering`，这将意味着 `[1, 2] < (1, 2)` 应该被定义 (并且应该返回 False)，而事实上 (在 Python 3000 中!) 这种 "混合模式比较" 操作是被明确禁止的，并且引发 `TypeError`。参见 PEP 3100 和 [14] 以了解更多信息。(这是一个更普遍的问题的特例，该问题涉及到接受同一类型的另一个参数的操作)。

> 写错了？这里应该是`list`和`tuple`？译者注。

### [One Trick Ponies](https://www.python.org/dev/peps/pep-3119/#id36)



These abstract classes represent single methods like `__iter__` or `__len__`.

这些抽象类表示单一的方法，如`__iter__`或`__len__`：

- `Hashable`

  The base class for classes defining `__hash__`. The `__hash__` method should return an integer. The abstract `__hash__` method always returns 0, which is a valid (albeit inefficient) implementation. **Invariant:** If classes `C1` and `C2` both derive from `Hashable`, the condition `o1 == o2` must imply `hash(o1) == hash(o2)` for all instances `o1` of `C1` and all instances `o2` of `C2`. In other words, two objects should never compare equal if they have different hash values.Another constraint is that hashable objects, once created, should never change their value (as compared by `==`) or their hash value. If a class cannot guarantee this, it should not derive from `Hashable`; if it cannot guarantee this for certain instances, `__hash__` for those instances should raise a `TypeError` exception.**Note:** being an instance of this class does not imply that an object is immutable; e.g. a tuple containing a list as a member is not immutable; its `__hash__` method raises `TypeError`. (This is because it recursively tries to compute the hash of each member; if a member is unhashable it raises `TypeError`.)

  定义（实现）`__hash__`的类的基类。`__hash__`方法应该返回一个整数。抽象的 `__hash__` 方法总是返回 0，这是一个有效的 (尽管是低效的) 实现。**不变性**：如果类`C1`和`C2`都派生自`Hashable`，对于`C1`的所有实例`o1`和`C2`的所有实例`o2`，条件`o1 == o2`必须暗示`hash(o1) == hash(o2)`。换句话说，如果两个对象有不同的哈希值，它们就不应该比较相等。另一个约束条件是，可哈希的对象一旦创建，就不应该改变它们的值（通过`==`来比较）或它们的哈希值。如果一个类不能保证这一点，它不应该派生自 `Hashable`；如果它对某些实例不能保证这一点，那么这些实例的 `__hash__` 应该引发 `TypeError` 异常。**注意**：作为这个类的一个实例并不意味着这个对象一定是不可变的；例如，一个包含 list 成员的元组不是不可变的；它的 `__hash__` 方法会引发 `TypeError`。(这是因为它递归地试图计算每个成员的哈希值；如果一个成员是不可哈希的，它会引发 `TypeError`)。

  > 如果你还不了解哈希（散列），可以阅读[**Python学习笔记20：字典与集合**](https://blog.icexmoon.xyz/archives/93.html)，译者注。

- `Iterable`

  The base class for classes defining `__iter__`. The `__iter__` method should always return an instance of `Iterator` (see below). The abstract `__iter__` method returns an empty iterator.

  定义 `__iter__` 的类的基类。`__iter__`方法应该总是返回一个`Iterator`的实例(见下文)。抽象的 `__iter__` 方法返回一个空的迭代器。

- `Iterator`

  The base class for classes defining `__next__`. This derives from `Iterable`. The abstract `__next__` method raises `StopIteration`. The concrete `__iter__` method returns `self`. Note the distinction between `Iterable` and `Iterator`: an `Iterable` can be iterated over, i.e. supports the `__iter__` methods; an `Iterator` is what the built-in function `iter()` returns, i.e. supports the `__next__` method.

  定义 `__next__` 的类的基类。它派生自 `Iterable`。抽象的 `__next__` 方法引发 `StopIteration`。具体的 `__iter__` 方法返回 `self`。注意 `Iterable` 和 `Iterator` 之间的区别：一个 `Iterable` 可以被迭代，即支持 `__iter__` 方法；一个 `Iterator` 是内置函数 `iter()` 所返回的，即支持 `__next__` 方法。

- `Sized`

  The base class for classes defining `__len__`. The `__len__` method should return an `Integer` (see "Numbers" below) >= 0. The abstract `__len__` method returns 0. **Invariant:** If a class `C` derives from `Sized` as well as from `Iterable`, the invariant `sum(1 for x in c) == len(c)` should hold for any instance `c` of `C`.

  定义`__len__`的类的基类。`__len__`方法应该返回一个>=0的`Integer`（见下面的 "数字"）。抽象的`__len__`方法返回0。**不变性：** 如果一个类`C`派生自`Sized`和`Iterable`，那么对于`C`的任何实例`c`，不变量`sum(1 for x in c) == len(c)`应该成立。

- `Container`

  The base class for classes defining `__contains__`. The `__contains__` method should return a `bool`. The abstract `__contains__` method returns `False`. **Invariant:** If a class `C` derives from `Container` as well as from `Iterable`, then `(x in c for x in c)` should be a generator yielding only True values for any instance `c` of `C`.

  定义`__contains__`的类的基类。`__contains__`方法应该返回一个`bool`。抽象的`__contains__`方法返回`False`。**不变性：**如果一个类`C`派生自`Container`以及`Iterable`，那么`(x in c for x in c)`应该是一个生成器，对于`C`的任何实例`c`只产生True值。

  > `(x in c for x in c)`这个生成器表达式不易理解，可以将前部的`x in c`单独视作一个独立于后部`for/in`语句的bool表达式，译者注。

**Open issues:** Conceivably, instead of using the ABCMeta metaclass, these classes could override `__instancecheck__` and `__subclasscheck__` to check for the presence of the applicable special method; for example:

**未解决的问题：**可以想象，这些类可以覆盖`__instancecheck__`和`__subclasscheck__`来检查是否存在适用的特殊方法，而不是使用ABCMeta元类；例如。

```python
class Sized(metaclass=ABCMeta):
    @abstractmethod
    def __hash__(self):
        return 0
    @classmethod
    def __instancecheck__(cls, x):
        return hasattr(x, "__len__")
    @classmethod
    def __subclasscheck__(cls, C):
        return hasattr(C, "__bases__") and hasattr(C, "__len__")
```

This has the advantage of not requiring explicit registration. However, the semantics are hard to get exactly right given the confusing semantics of instance attributes vs. class attributes, and that a class is an instance of its metaclass; the check for `__bases__` is only an approximation of the desired semantics. **Strawman:** Let's do it, but let's arrange it in such a way that the registration API also works.

这样做的好处是不需要明确的注册。然而，考虑到实例属性与类属性的混乱语义，以及一个类是其元类的一个实例，语义很难完全正确；对 `__bases__` 的检查只是对所需语义的一种近似。**稻草人：**让我们来做吧，但是让我们以这样一种方式来安排它，使注册 API 也能工作。

### [Sets](https://www.python.org/dev/peps/pep-3119/#id37)

集合

These abstract classes represent read-only sets and mutable sets. The most fundamental set operation is the membership test, written as `x in s` and implemented by `s.__contains__(x)`. This operation is already defined by the `Container` class defined above. Therefore, we define a set as a sized, iterable container for which certain invariants from mathematical set theory hold.

这些抽象类表示只读集合和可变集合。最基本的集合操作是成员测试，写成`x in s`，由`s.__contains__(x)`实现。这个操作已经被上面定义的`Container`类所定义。因此，我们将集合定义为一个有尺寸的、可迭代的容器，对于它来说，数学集合理论中的某些不变性是成立的。

The built-in type `set` derives from `MutableSet`. The built-in type `frozenset` derives from `Set` and `Hashable`.

内置类型`set`派生自`MutableSet`。内置类型`frozenset`派生自`Set`和`Hashable`。

- `Set`

  This is a sized, iterable container, i.e., a subclass of `Sized`, `Iterable` and `Container`. Not every subclass of those three classes is a set though! Sets have the additional invariant that each element occurs only once (as can be determined by iteration), and in addition sets define concrete operators that implement the inequality operations as subclass/superclass tests. In general, the invariants for finite sets in mathematics hold. [[11\]](https://www.python.org/dev/peps/pep-3119/#id23)Sets with different implementations can be compared safely, (usually) efficiently and correctly using the mathematical definitions of the subclass/superclass operations for finite sets. The ordering operations have concrete implementations; subclasses may override these for speed but should maintain the semantics. Because `Set` derives from `Sized`, `__eq__` may take a shortcut and return `False` immediately if two sets of unequal length are compared. Similarly, `__le__` may return `False` immediately if the first set has more members than the second set. Note that set inclusion implements only a partial ordering; e.g. `{1, 2}` and `{1, 3}` are not ordered (all three of `<`, `==` and `>` return `False` for these arguments). Sets cannot be ordered relative to mappings or sequences, but they can be compared to those for equality (and then they always compare unequal).This class also defines concrete operators to compute union, intersection, symmetric and asymmetric difference, respectively `__or__`, `__and__`, `__xor__` and `__sub__`. These operators should return instances of `Set`. The default implementations call the overridable class method `_from_iterable()` with an iterable argument. This factory method's default implementation returns a `frozenset` instance; it may be overridden to return another appropriate `Set` subclass.Finally, this class defines a concrete method `_hash` which computes the hash value from the elements. Hashable subclasses of `Set` can implement `__hash__` by calling `_hash` or they can reimplement the same algorithm more efficiently; but the algorithm implemented should be the same. Currently the algorithm is fully specified only by the source code [[15\]](https://www.python.org/dev/peps/pep-3119/#id27).**Note:** the `issubset` and `issuperset` methods found on the set type in Python 2 are not supported, as these are mostly just aliases for `__le__` and `__ge__`.

  这是一个有大小的、可迭代的容器，也就是说，是`Sized`、`Iterable`和`Container`的子类。但并不是这三个类的每个子类都是一个集合! 集合有额外的不变性，即每个元素只出现一次（可以通过迭代来确定），此外，集合还定义了具体的操作符，实现了作为子类/超类测试的不等式操作。一般来说，数学中的有限集的不变量是成立的。[11]使用有限集的子类/超类运算的数学定义，可以安全、（通常）有效和正确地比较具有不同实现方式的集合。排序操作有具体的实现；子类可以覆盖这些操作以提高速度，但应该保持语义。因为 `Set` 派生自 `Sized`，所以 `__eq__` 可以走捷径，在两个长度不相等的集合被比较时立即返回 `False`。类似地，如果第一个集合比第二个集合有更多的成员，`__le__` 可以立即返回 `False`。请注意，集合包含只实现了部分排序；例如，`{1, 2}`和`{1, 3}`是没有顺序的 (对于这些参数，`<`, `==` 和 `>` 三个操作符都返回 `False`)。集合不能相对于映射或序列被排序，但是它们可以与那些相等的序列进行比较(然后它们总是比较不相等)。这个类也定义了具体的操作符来计算联合、相交、对称和不对称的差异，分别是`__or__`, `__and__`, `__xor__` 和 `__sub__`。这些运算符应该返回`Set`的实例。默认的实现是调用可重写的类方法 `_from_iterable()` ，并带有一个可迭代的参数。这个工厂方法的默认实现返回一个 `frozenset` 实例；它可以被重写以返回另一个合适的 `Set` 子类。最后，这个类定义了一个具体的方法 `_hash`，它从元素中计算出哈希值。Set的可散列子类可以通过调用`_hash`来实现`__hash__`，或者它们可以更有效地重新实现相同的算法；但是实现的算法应该是相同的。目前该算法仅由源代码[15]完全指定。注意：Python 2 中在集合类型上发现的 `issubset` 和 `issuperset` 方法不被支持，因为这些大多只是 `__le__` 和 `__ge__` 的别名。

  > 关于集合运算的更多介绍可以阅读[**Python学习笔记20：字典与集合**](https://blog.icexmoon.xyz/archives/93.html)，译者注。

- `MutableSet`

  可变集合
  
  This is a subclass of `Set` implementing additional operations to add and remove elements. The supported methods have the semantics known from the `set` type in Python 2 (except for `discard`, which is modeled after Java):`.add(x)`Abstract method returning a `bool` that adds the element `x` if it isn't already in the set. It should return `True` if `x` was added, `False` if it was already there. The abstract implementation raises `NotImplementedError`.`.discard(x)`Abstract method returning a `bool` that removes the element `x` if present. It should return `True` if the element was present and `False` if it wasn't. The abstract implementation raises `NotImplementedError`.`.pop()`Concrete method that removes and returns an arbitrary item. If the set is empty, it raises `KeyError`. The default implementation removes the first item returned by the set's iterator.`.toggle(x)`Concrete method returning a `bool` that adds x to the set if it wasn't there, but removes it if it was there. It should return `True` if `x` was added, `False` if it was removed.`.clear()`Concrete method that empties the set. The default implementation repeatedly calls `self.pop()` until `KeyError` is caught. (**Note:** this is likely much slower than simply creating a new set, even if an implementation overrides it with a faster approach; but in some cases object identity is important.)This also supports the in-place mutating operations `|=`, `&=`, `^=`, `-=`. These are concrete methods whose right operand can be an arbitrary `Iterable`, except for `&=`, whose right operand must be a `Container`. This ABC does not provide the named methods present on the built-in concrete `set` type that perform (almost) the same operations.
  
  这是 `Set` 的一个子类，实现了添加和删除元素的额外操作。支持的方法具有Python 2中`set`类型的语义 (除了`discard`，它是以Java为模型的)：抽象方法`.add(x)`返回一个`bool`，如果元素`x`还没有在集合中，它就会添加。如果 `x` 被添加，它应该返回 `True`，如果它已经在那里，则返回 `False`。抽象的实现会抛出`NotImplementedError`。抽象方法`.discard(x)`返回一个`bool`，如果元素`x`存在，就将其删除。如果该元素存在，它应该返回`True`，如果不存在，则返回`False`。抽象的实现会引发`NotImplementedError`。具体方法`.pop()`，删除并返回一个任意的项目。如果这个集合是空的，它会引发`KeyError`。默认实现会删除集合的迭代器返回的第一个项目。具体方法`.toggle(x)`返回一个`bool`，如果`x`不在集合中，则将其添加到集合中，如果它在集合中，则将其删除。如果`x`被添加，它应该返回`True`，如果被移除，它应该返回`False`。具体方法`.clear()`将清空集合。默认的实现是重复调用`self.pop()`，直到捕获`KeyError`。(注意：这可能比简单地创建一个新的集合要慢得多，即使一个实现用更快的方法来覆盖它；但在某些情况下，对象的身份是很重要的。)这也支持就地变异操作`|=`, `&=`, `^=`, `-=`。这些都是具体的方法，其右操作数可以是一个任意的`Iterable`，除了`&=`，其右操作数必须是一个容器。这个ABC不提供内置的具体`set`类型上的命名方法，这些方法执行（几乎）同样的操作。

### [Mappings](https://www.python.org/dev/peps/pep-3119/#id38)

映射

These abstract classes represent read-only mappings and mutable mappings. The `Mapping` class represents the most common read-only mapping API.

这些抽象类代表了只读映射和可变映射。`Mapping`类代表了最常见的只读映射API。

The built-in type `dict` derives from `MutableMapping`.

内置类型`dict`派生自`MutableMapping`。

- `Mapping`

  映射

  A subclass of `Container`, `Iterable` and `Sized`. The keys of a mapping naturally form a set. The (key, value) pairs (which must be tuples) are also referred to as items. The items also form a set. Methods:`.__getitem__(key)`Abstract method that returns the value corresponding to `key`, or raises `KeyError`. The implementation always raises `KeyError`.`.get(key, default=None)`Concrete method returning `self[key]` if this does not raise `KeyError`, and the `default` value if it does.`.__contains__(key)`Concrete method returning `True` if `self[key]` does not raise `KeyError`, and `False` if it does.`.__len__()`Abstract method returning the number of distinct keys (i.e., the length of the key set).`.__iter__()`Abstract method returning each key in the key set exactly once.`.keys()`Concrete method returning the key set as a `Set`. The default concrete implementation returns a "view" on the key set (meaning if the underlying mapping is modified, the view's value changes correspondingly); subclasses are not required to return a view but they should return a `Set`.`.items()`Concrete method returning the items as a `Set`. The default concrete implementation returns a "view" on the item set; subclasses are not required to return a view but they should return a `Set`.`.values()`Concrete method returning the values as a sized, iterable container (not a set!). The default concrete implementation returns a "view" on the values of the mapping; subclasses are not required to return a view but they should return a sized, iterable container.The following invariants should hold for any mapping `m`:`len(m.values()) == len(m.keys()) == len(m.items()) == len(m) [value for value in m.values()] == [m[key] for key in m.keys()] [item for item in m.items()] == [(key, m[key]) for key in m.keys()] `i.e. iterating over the items, keys and values should return results in the same order.

  `Container`、`Iterable`和`Sized`的一个子类。一个映射的键自然形成一个集合。`(key, value)` 对（必须是图元）也被称为项。这些项目也构成一个集合。方法：抽象方法`.__getitem__(key)`返回key对应的值，或者引发`KeyError`。抽象类的实现总是引发 `KeyError`。具体方法`.get(key, default=None)`，如果不引发 `KeyError`，返回 `self[key]`，如果引发 `KeyError`，返回默认值。抽象方法`.__iter__()`，会将键组中的每个键精确地返回一次。具体方法`.keys()`，返回的键组为一个 `Set`。默认的具体实现是在键的集合上返回一个 "视图"（意味着如果底层映射被修改，视图的值也会相应地改变）；子类不需要返回一个视图，但它们（至少）应该返回一个集合。具体方法`.items()`将项作为一个集合返回。默认的具体实现是在项目集上返回一个 "视图"；子类不需要返回一个视图，但它们应该返回一个`Set`。具体方法`.values()`，将值作为一个有大小的、可迭代的 container（不是一个集合！）返回。默认的具体实现会返回一个关于映射值的 "视图"；子类不需要返回一个视图，但它们应该返回一个有大小的可迭代的 container。对于任何映射`m`来说，以下不变性应该是成立的：`len(m.values()) == len(m.key()) == len(m.items()) == len(m) [value for value in m.values() ] == [m[key] for key in m.key() ] [item for item in m.items()] == [(key, m[key]) for key in m.key()]`也就是说，在item、key和value上迭代应该以相同的顺序返回结果。

  > 最后的一句话应该是指虽然我们不能依赖于映射的迭代顺序，但是对于给定义的一个已有映射，在某一个时刻其对`key\value\(key,value)`的分别迭代顺序应该是一致的，不应该存在歧义，译者注。

- `MutableMapping`

  可变映射
  
  A subclass of `Mapping` that also implements some standard mutating methods. Abstract methods include `__setitem__`, `__delitem__`. Concrete methods include `pop`, `popitem`, `clear`, `update`. **Note:** `setdefault` is *not* included. **Open issues:** Write out the specs for the methods.
  
  `Mapping`的一个子类，也实现了一些标准的变异方法。抽象方法包括`__setitem__`, `__delitem__`。具体的方法包括 `pop`, `popitem`, `clear`, `update`. **注意：** `setdefault`并不包括在内。**未解决的问题：**写出方法的规格。
  
  > 这里的Open issues简直是官方偷懒:smiley:，译者注。

### [Sequences](https://www.python.org/dev/peps/pep-3119/#id39)

序列

These abstract classes represent read-only sequences and mutable sequences.

这些抽象类表示只读序列和可变序列。

The built-in `list` and `bytes` types derive from `MutableSequence`. The built-in `tuple` and `str` types derive from `Sequence` and `Hashable`.

内置的`list`和`byte`类型衍生自`MutableSequence`。内置的`tuple`和`str`类型派生自`Sequence`和`Hashable`。

- `Sequence`

  序列

  A subclass of `Iterable`, `Sized`, `Container`. It defines a new abstract method `__getitem__` that has a somewhat complicated signature: when called with an integer, it returns an element of the sequence or raises `IndexError`; when called with a `slice` object, it returns another `Sequence`. The concrete `__iter__` method iterates over the elements using `__getitem__` with integer arguments 0, 1, and so on, until `IndexError` is raised. The length should be equal to the number of values returned by the iterator.**Open issues:** Other candidate methods, which can all have default concrete implementations that only depend on `__len__` and `__getitem__` with an integer argument: `__reversed__`, `index`, `count`, `__add__`, `__mul__`.

  `Iterable`, `Sized`, `Container`的一个子类。它定义了一个新的抽象方法 `__getitem__`，它有一个有点复杂的签名：当用一个整数调用时，它返回序列的一个元素或引发 `IndexError`；当用一个切片对象调用时，它返回另一个 `Sequence`。具体的 `__iter__` 方法使用 `__getitem__` 对元素进行迭代，整数参数为 0、1，以此类推，直到引发 `IndexError`。长度应该等于迭代器返回的值的数量。打开问题：其他候选方法，它们都可以有默认的具体实现，只依赖于 `__len__` 和 `__getitem__` 的整数参数。`__reversed__`, `index`, `count`, `__add__`, `__mul__`。

- `MutableSequence`

  可变序列
  
  A subclass of `Sequence` adding some standard mutating methods. Abstract mutating methods: `__setitem__` (for integer indices as well as slices), `__delitem__` (ditto), `insert`. Concrete mutating methods: `append`, `reverse`, `extend`, `pop`, `remove`. Concrete mutating operators: `+=`, `*=` (these mutate the object in place). **Note:** this does not define `sort()` -- that is only required to exist on genuine `list` instances.
  
  `Sequence`的一个子类，增加了一些标准的变异方法。抽象的变异方法。`__setitem__` (用于整数索引和切片), `__delitem__` (同上), `insert`。具体的变异方法：`append`, `reverse`, `extend`, `pop`, `remove`。具体的变异操作符。`+=`, `*=` (这些都是对对象进行原地变异的)。**注意：**这里没有定义 `sort()` -- 这只需要存在于真正的 `list` 实例上。

## [Strings](https://www.python.org/dev/peps/pep-3119/#id40)

字符串

Python 3000 will likely have at least two built-in string types: byte strings (`bytes`), deriving from `MutableSequence`, and (Unicode) character strings (`str`), deriving from `Sequence` and `Hashable`.

Python 3 可能至少有两种内置的字符串类型：从 `MutableSequence` 派生的字节字符串 (`bytes`)，以及从 `Sequence` 和 `Hashable` 派生的 (Unicode) 字符串 (`str`)。

**Open issues:** define the base interfaces for these so alternative implementations and subclasses know what they are in for. This may be the subject of a new PEP or PEPs ([PEP 358](https://www.python.org/dev/peps/pep-0358) should be co-opted for the `bytes` type).

**未解决的问题：**为这些定义基础接口，以便替代的实现和子类知道他们在做什么。这可能是一个新的PEP或PEP的主题（PEP 358应该被用于字节类型的合作）。

# [ABCs vs. Alternatives](https://www.python.org/dev/peps/pep-3119/#id41)

In this section I will attempt to compare and contrast ABCs to other approaches that have been proposed.

在本节中，我将尝试将ABC与其他已提出的方法进行比较和对比。

## [ABCs vs. Duck Typing](https://www.python.org/dev/peps/pep-3119/#id42)

ABC vs 鸭子类型

Does the introduction of ABCs mean the end of Duck Typing? I don't think so. Python will not require that a class derives from `BasicMapping` or `Sequence` when it defines a `__getitem__` method, nor will the `x[y]` syntax require that `x` is an instance of either ABC. You will still be able to assign any "file-like" object to `sys.stdout`, as long as it has a `write` method.

ABCs的引入是否意味着鸭子类型的结束？我不这么认为。当一个类定义 `__getitem__` 方法时，Python 将不要求它派生自 `BasicMapping` 或 `Sequence`，`x[y]` 语法也不要求 `x` 是 ABC 的一个实例。你仍然能够将任何 "类文件 "对象分配给 `sys.stdout`，只要它有一个写入方法。

Of course, there will be some carrots to encourage users to derive from the appropriate base classes; these vary from default implementations for certain functionality to an improved ability to distinguish between mappings and sequences. But there are no sticks. If `hasattr(x, "__len__")` works for you, great! ABCs are intended to solve problems that don't have a good solution at all in Python 2, such as distinguishing between mappings and sequences.

当然，会有一些胡萝卜来鼓励用户从适当的基类中派生出来；这些胡萝卜从某些功能的默认实现到改进区分映射和序列的能力不等。但并没有大棒。如果 `hasattr(x, "__len__")` 对你有用，那很好！你可以使用 ABC。ABC的目的是为了解决在Python 2中完全没有好的解决方案的那些问题，比如区分映射和序列。

## [ABCs vs. Generic Functions](https://www.python.org/dev/peps/pep-3119/#id43)

ABCs vs 泛函数

ABCs are compatible with Generic Functions (GFs). For example, my own Generic Functions implementation [[4\]](https://www.python.org/dev/peps/pep-3119/#id16) uses the classes (types) of the arguments as the dispatch key, allowing derived classes to override base classes. Since (from Python's perspective) ABCs are quite ordinary classes, using an ABC in the default implementation for a GF can be quite appropriate. For example, if I have an overloaded `prettyprint` function, it would make total sense to define pretty-printing of sets like this:

ABCs与泛函数（GFs）是兼容的。例如，我自己的泛函数实现 [4] 使用参数的类 (类型) 作为调度键，允许派生类覆盖基类。因为 (从 Python 的角度来看) ABC 是非常普通的类，所以在 GF 的默认实现中使用 ABC 可以非常合适。例如，如果我有一个重载的 `prettyprint` 函数，像这样定义集合的 pretty-printing 是完全合理的。

```python
@prettyprint.register(Set)
def pp_set(s):
    return "{" + ... + "}"  # Details left as an exercise
```

and implementations for specific subclasses of Set could be added easily.

而对于Set的特定子类的实现可以很容易地加入。

I believe ABCs also won't present any problems for RuleDispatch, Phillip Eby's GF implementation in PEAK [[5\]](https://www.python.org/dev/peps/pep-3119/#id17).

我相信ABC也不会给RuleDispatch带来任何问题，Phillip Eby在PEAK[[5](https://www.python.org/dev/peps/pep-3119/#id17)]中的GF实现。

Of course, GF proponents might claim that GFs (and concrete, or implementation, classes) are all you need. But even they will not deny the usefulness of inheritance; and one can easily consider the ABCs proposed in this PEP as optional implementation base classes; there is no requirement that all user-defined mappings derive from `BasicMapping`.

当然，GF的支持者可能会声称，GF（以及具体的，或者说实现类）就是你所需要的一切。但即使是他们也不会否认继承的有用性；我们可以很容易地将本PEP中提出的ABC视为可选的实现基类；并没有要求所有用户定义的映射都源自于`BasicMapping`。

## [ABCs vs. Interfaces](https://www.python.org/dev/peps/pep-3119/#id44)

ABC vs 接口

ABCs are not intrinsically incompatible with Interfaces, but there is considerable overlap. For now, I'll leave it to proponents of Interfaces to explain why Interfaces are better. I expect that much of the work that went into e.g. defining the various shades of "mapping-ness" and the nomenclature could easily be adapted for a proposal to use Interfaces instead of ABCs.

ABCs与接口在本质上并不冲突，但有相当多的重叠之处。现在，我让接口的支持者们来解释为什么接口更好。我期望许多用于定义不同程度的 "映射性 "和术语的工作可以很容易地被改编为使用接口而不是ABC的建议。

"Interfaces" in this context refers to a set of proposals for additional metadata elements attached to a class which are not part of the regular class hierarchy, but do allow for certain types of inheritance testing.

这里的 "接口 "指的是一组附加在类上的元数据元素的建议，这些元数据元素不是常规类层次结构的一部分，但允许某些类型的继承测试。

Such metadata would be designed, at least in some proposals, so as to be easily mutable by an application, allowing application writers to override the normal classification of an object.

至少在一些建议中，这样的元数据将被设计成容易被应用程序改变，允许应用程序编写者覆盖对象的正常分类。

The drawback to this idea of attaching mutable metadata to a class is that classes are shared state, and mutating them may lead to conflicts of intent. Additionally, the need to override the classification of an object can be done more cleanly using generic functions: In the simplest case, one can define a "category membership" generic function that simply returns False in the base implementation, and then provide overrides that return True for any classes of interest.

这种将可变元数据附加到类上的想法的缺点是，类是共享状态，对其进行变异可能会导致意图的冲突。此外，需要覆盖一个对象的分类，可以使用泛函数更干净地完成。在最简单的情况下，我们可以定义一个 "类别成员 "泛函数，在基本实现中简单地返回False，然后为任何感兴趣的类提供返回True的重写。

> 关于什么是泛函数，可以阅读[**Python 里的「单分派泛函数」到底是什么？**](https://www.cnblogs.com/wongbingming/p/10726698.html)译者注。

# [References](https://www.python.org/dev/peps/pep-3119/#id45)

参考文献

|                                                         |                                                              |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [[1\]](https://www.python.org/dev/peps/pep-3119/#id1)   | An Introduction to ABC's, by Talin (https://mail.python.org/pipermail/python-3000/2007-April/006614.html) |
|                                                         |                                                              |
| [2]                                                     | Incomplete implementation prototype, by GvR (http://svn.python.org/view/sandbox/trunk/abc/) |
|                                                         |                                                              |
| [3]                                                     | Possible Python 3K Class Tree?, wiki page created by Bill Janssen (http://wiki.python.org/moin/AbstractBaseClasses) |
|                                                         |                                                              |
| [[4\]](https://www.python.org/dev/peps/pep-3119/#id11)  | Generic Functions implementation, by GvR (http://svn.python.org/view/sandbox/trunk/overload/) |
|                                                         |                                                              |
| [[5\]](https://www.python.org/dev/peps/pep-3119/#id12)  | Charming Python: Scaling a new PEAK, by David Mertz (http://www-128.ibm.com/developerworks/library/l-cppeak2/) |
|                                                         |                                                              |
| [[6\]](https://www.python.org/dev/peps/pep-3119/#id5)   | Implementation of @abstractmethod (https://bugs.python.org/issue1706989) |
|                                                         |                                                              |
| [[7\]](https://www.python.org/dev/peps/pep-3119/#id6)   | Unifying types and classes in Python 2.2, by GvR (http://www.python.org/download/releases/2.2.3/descrintro/) |
|                                                         |                                                              |
| [[8\]](https://www.python.org/dev/peps/pep-3119/#id7)   | Putting Metaclasses to Work: A New Dimension in Object-Oriented Programming, by Ira R. Forman and Scott H. Danforth (http://www.amazon.com/gp/product/0201433052) |
|                                                         |                                                              |
| [9]                                                     | Partial order, in Wikipedia (http://en.wikipedia.org/wiki/Partial_order) |
|                                                         |                                                              |
| [10]                                                    | Total order, in Wikipedia (http://en.wikipedia.org/wiki/Total_order) |
|                                                         |                                                              |
| [[11\]](https://www.python.org/dev/peps/pep-3119/#id9)  | Finite set, in Wikipedia (http://en.wikipedia.org/wiki/Finite_set) |
|                                                         |                                                              |
| [[12\]](https://www.python.org/dev/peps/pep-3119/#id3)  | Make isinstance/issubclass overloadable (https://bugs.python.org/issue1708353) |
|                                                         |                                                              |
| [[13\]](https://www.python.org/dev/peps/pep-3119/#id4)  | ABCMeta sample implementation (http://svn.python.org/view/sandbox/trunk/abc/xyz.py) |
|                                                         |                                                              |
| [[14\]](https://www.python.org/dev/peps/pep-3119/#id8)  | python-dev email ("Comparing heterogeneous types") https://mail.python.org/pipermail/python-dev/2004-June/045111.html |
|                                                         |                                                              |
| [[15\]](https://www.python.org/dev/peps/pep-3119/#id10) | Function `frozenset_hash()` in Object/setobject.c (http://svn.python.org/view/python/trunk/Objects/setobject.c) |
|                                                         |                                                              |
| [[16\]](https://www.python.org/dev/peps/pep-3119/#id2)  | Multiple interpreters in mod_python (http://www.modpython.org/live/current/doc-html/pyapi-interps.html) |

# [Copyright](https://www.python.org/dev/peps/pep-3119/#id46)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-3119.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。