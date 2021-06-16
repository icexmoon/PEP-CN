# PEP 435 -- Adding an Enum type to the Python standard library

PEP 435 -- 在Python标准库中添加一个枚举类型

> 原文地址：<https://www.python.org/dev/peps/pep-0435/>

| PEP:            | 435                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Adding an Enum type to the Python standard library           |
| Author:         | Barry Warsaw <barry at python.org>, Eli Bendersky <eliben at gmail.com>, Ethan Furman <ethan at stoneleaf.us> |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 23-Feb-2013                                                  |
| Python-Version: | 3.4                                                          |
| Post-History:   | 2013-02-23, 2013-05-02                                       |
| Replaces:       | [354](https://www.python.org/dev/peps/pep-0354)              |
| Resolution:     | https://mail.python.org/pipermail/python-dev/2013-May/126112.html |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0435/#abstract)，摘要
- [Status of discussions](https://www.python.org/dev/peps/pep-0435/#status-of-discussions)，讨论的状态
- [Motivation](https://www.python.org/dev/peps/pep-0435/#motivation)，动机
- [Module and type name](https://www.python.org/dev/peps/pep-0435/#module-and-type-name)，模块和类型名称
- Proposed semantics for the new enumeration type，新枚举类型的拟定的语法
  - [Creating an Enum](https://www.python.org/dev/peps/pep-0435/#creating-an-enum)，创建一个枚举
  - [Programmatic access to enumeration members](https://www.python.org/dev/peps/pep-0435/#programmatic-access-to-enumeration-members)，程序化访问枚举成员
  - [Duplicating enum members and values](https://www.python.org/dev/peps/pep-0435/#duplicating-enum-members-and-values)，重复枚举成员和值
  - [Comparisons](https://www.python.org/dev/peps/pep-0435/#comparisons)，比较
  - [Allowed members and attributes of enumerations](https://www.python.org/dev/peps/pep-0435/#allowed-members-and-attributes-of-enumerations)，允许的枚举成员和属性
  - [Restricted subclassing of enumerations](https://www.python.org/dev/peps/pep-0435/#restricted-subclassing-of-enumerations)，枚举的限制性子类化
  - [IntEnum](https://www.python.org/dev/peps/pep-0435/#intenum)
  - [Other derived enumerations](https://www.python.org/dev/peps/pep-0435/#other-derived-enumerations)，其它衍生的枚举类型
  - [Pickling](https://www.python.org/dev/peps/pep-0435/#pickling)，序列化
  - [Functional API](https://www.python.org/dev/peps/pep-0435/#functional-api)，功能性API
- Proposed variations，建议的演变
  - [flufl.enum](https://www.python.org/dev/peps/pep-0435/#flufl-enum)
  - [Not having to specify values for enums](https://www.python.org/dev/peps/pep-0435/#not-having-to-specify-values-for-enums)，不必为枚举指定值
  - [Using special names or forms to auto-assign enum values](https://www.python.org/dev/peps/pep-0435/#using-special-names-or-forms-to-auto-assign-enum-values)，使用特殊名称或形式来自动分配枚举值
- [Use-cases in the standard library](https://www.python.org/dev/peps/pep-0435/#use-cases-in-the-standard-library)，标准库中的用例
- [Acknowledgments](https://www.python.org/dev/peps/pep-0435/#acknowledgments)，致谢
- [References](https://www.python.org/dev/peps/pep-0435/#references)，参考文献
- [Copyright](https://www.python.org/dev/peps/pep-0435/#copyright)，版权声明

# [Abstract](https://www.python.org/dev/peps/pep-0435/#id21)

摘要

This PEP proposes adding an enumeration type to the Python standard library.

这个 PEP 建议在 Python 标准库中增加一个枚举类型。

An enumeration is a set of symbolic names bound to unique, constant values. Within an enumeration, the values can be compared by identity, and the enumeration itself can be iterated over.

枚举是一组与唯一的、常量值绑定的符号名称。在一个枚举中，这些值可以通过身份进行比较，而且枚举本身可以被迭代。

# [Status of discussions](https://www.python.org/dev/peps/pep-0435/#id22)

讨论的状态

The idea of adding an enum type to Python is not new - [PEP 354](https://www.python.org/dev/peps/pep-0354) [[2\]](https://www.python.org/dev/peps/pep-0435/#id12) is a previous attempt that was rejected in 2005. Recently a new set of discussions was initiated [[3\]](https://www.python.org/dev/peps/pep-0435/#id13) on the `python-ideas` mailing list. Many new ideas were proposed in several threads; after a lengthy discussion Guido proposed adding `flufl.enum` to the standard library [[4\]](https://www.python.org/dev/peps/pep-0435/#id14). During the PyCon 2013 language summit the issue was discussed further. It became clear that many developers want to see an enum that subclasses `int`, which can allow us to replace many integer constants in the standard library by enums with friendly string representations, without ceding backwards compatibility. An additional discussion among several interested core developers led to the proposal of having `IntEnum` as a special case of `Enum`.

为Python添加枚举类型的想法并不新鲜--PEP 354 [2] 是之前的一次尝试，但在2005年被拒绝了。最近，在`python-ideas`邮件列表中发起了一系列新的讨论[3]。在几个线程中提出了许多新的想法；经过长时间的讨论，Guido提议将`flufl.enum`加入到标准库中[4]。在 PyCon 2013 语言峰会上，这个问题被进一步讨论。很明显，许多开发者希望看到一个子类`int`的枚举，这可以让我们在标准库中用友好的字符串表示的枚举来替代许多整数常量，而不放弃向后的兼容性。在几个感兴趣的核心开发者之间的额外讨论导致了将`IntEnum`作为`Enum`的一个特例的提议。

The key dividing issue between `Enum` and `IntEnum` is whether comparing to integers is semantically meaningful. For most uses of enumerations, it's a **feature** to reject comparison to integers; enums that compare to integers lead, through transitivity, to comparisons between enums of unrelated types, which isn't desirable in most cases. For some uses, however, greater interoperability with integers is desired. For instance, this is the case for replacing existing standard library constants (such as `socket.AF_INET`) with enumerations.

`Enum`和`IntEnum`之间的关键划分问题是与整数比较是否有语义。对于枚举的大多数用途来说，拒绝与整数比较是一个**特性**；与整数比较的枚举通过反证法会导致不相关类型的枚举之间的比较，这在大多数情况下是不可取的。然而，对于某些用途来说，更多的与整数的互操作性是需要的。例如，用枚举代替现有的标准库常量（如`socket.AF_INET`）就是这种情况。

Further discussion in late April 2013 led to the conclusion that enumeration members should belong to the type of their enum: `type(Color.red) == Color`. Guido has pronounced a decision on this issue [[5\]](https://www.python.org/dev/peps/pep-0435/#id15), as well as related issues of not allowing to subclass enums [[6\]](https://www.python.org/dev/peps/pep-0435/#id16), unless they define no enumeration members [[7\]](https://www.python.org/dev/peps/pep-0435/#id17).

2013年4月底的进一步讨论得出结论，枚举成员应该属于其枚举的类型：`type(Color.red) == Color`。Guido已经宣布了关于这个问题的决定[5]，以及不允许对枚举进行子类化的相关问题[6]，除非它们没有定义枚举成员[7]。

The PEP was accepted by Guido on May 10th, 2013 [[1\]](https://www.python.org/dev/peps/pep-0435/#id11).

该PEP于2013年5月10日被Guido接受[1]。

# [Motivation](https://www.python.org/dev/peps/pep-0435/#id23)

动机

*[Based partly on the Motivation stated in PEP 354]*

*[部分基于PEP 354中所述的动机]*。

The properties of an enumeration are useful for defining an immutable, related set of constant values that may or may not have a semantic meaning. Classic examples are days of the week (Sunday through Saturday) and school assessment grades ('A' through 'D', and 'F'). Other examples include error status values and states within a defined process.

枚举的属性对于定义一个不可改变的、相关的、可能有也可能没有语义的常量值集是很有用的。经典的例子是一周中的几天（周日到周六）和学校评估等级（'A'到'D'，以及'F'）。其他的例子包括错误状态值和定义过程中的状态。

It is possible to simply define a sequence of values of some other basic type, such as `int` or `str`, to represent discrete arbitrary values. However, an enumeration ensures that such values are distinct from any others including, importantly, values within other enumerations, and that operations without meaning ("Wednesday times two") are not defined for these values. It also provides a convenient printable representation of enum values without requiring tedious repetition while defining them (i.e. no `GREEN = 'green'`).

可以简单地定义一些其他基本类型的值的序列，如`int`或`str`，来表示离散的任意值。然而，一个枚举需要确保这些值与其他所被包含的重要的枚举值不同，而且没有意义的操作（"星期三乘以二"）不会为这些值定义。它还为枚举值提供了一种方便的可打印的表示方法，而不需要在定义它们时进行繁琐的重复（例如，无须`GREEN = 'green'`）。

# [Module and type name](https://www.python.org/dev/peps/pep-0435/#id24)

模块和类型名称

We propose to add a module named `enum` to the standard library. The main type exposed by this module is `Enum`. Hence, to import the `Enum` type user code will run:

我们建议在标准库中增加一个名为 `enum` 的模块。这个模块的主要类型是 `Enum`。因此，要导入`Enum`类型的用户代码将运行。

```python
>>> from enum import Enum
```

# [Proposed semantics for the new enumeration type](https://www.python.org/dev/peps/pep-0435/#id25)

新枚举类型的拟定语义

## [Creating an Enum](https://www.python.org/dev/peps/pep-0435/#id26)

创建一个枚举

Enumerations are created using the class syntax, which makes them easy to read and write. An alternative creation method is described in [Functional API](https://www.python.org/dev/peps/pep-0435/#functional-api). To define an enumeration, subclass `Enum` as follows:

枚举是使用类的语法来创建的，这使得它们易于阅读和书写。另一种创建方法在Functional API中描述。要定义一个枚举，请按如下方式子类化`Enum`。

```python
>>> from enum import Enum
>>> class Color(Enum):
...     red = 1
...     green = 2
...     blue = 3
```

**A note on nomenclature**: we call `Color` an *enumeration* (or *enum*) and `Color.red`, `Color.green` are *enumeration members* (or *enum members*). Enumeration members also have *values* (the value of `Color.red` is `1`, etc.)

**关于命名的说明**：我们称`Color`为*枚举*（或*enum*），`Color.red`、`Color.green`是枚举成员（或*enum成员*）。枚举成员也有值（`Color.red`的值是1，等等）。

Enumeration members have human readable string representations:

枚举成员有人类可读的字符串表示。

```python
>>> print(Color.red)
Color.red
```

...while their `repr` has more information:

...而他们的`repr`有更多信息。

```python
>>> print(repr(Color.red))
<Color.red: 1>
```

The *type* of an enumeration member is the enumeration it belongs to:

枚举成员的*类型*是它所属的枚举。

```python
>>> type(Color.red)
<Enum 'Color'>
>>> isinstance(Color.green, Color)
True
>>>
```

Enums also have a property that contains just their item name:

枚举也有一个属性，只包含其项目名称：

```python
>>> print(Color.red.name)
red
```

Enumerations support iteration, in definition order:

枚举支持迭代，按定义顺序：

```python
>>> class Shake(Enum):
...   vanilla = 7
...   chocolate = 4
...   cookies = 9
...   mint = 3
...
>>> for shake in Shake:
...   print(shake)
...
Shake.vanilla
Shake.chocolate
Shake.cookies
Shake.mint
```

Enumeration members are hashable, so they can be used in dictionaries and sets:

枚举成员是可散列的，所以它们可以在字典和集合中使用：

```python
>>> apples = {}
>>> apples[Color.red] = 'red delicious'
>>> apples[Color.green] = 'granny smith'
>>> apples
{<Color.red: 1>: 'red delicious', <Color.green: 2>: 'granny smith'}
```

## [Programmatic access to enumeration members](https://www.python.org/dev/peps/pep-0435/#id27)

对枚举成员的程序性访问

Sometimes it's useful to access members in enumerations programmatically (i.e. situations where `Color.red` won't do because the exact color is not known at program-writing time). `Enum` allows such access:

有时，以编程方式访问枚举中的成员是很有用的（例如，`Color.red`不适用的情况，因为在编写程序时不知道确切的颜色是什么）。`Enum`允许这种访问。

```python
>>> Color(1)
<Color.red: 1>
>>> Color(3)
<Color.blue: 3>
```

If you want to access enum members by *name*, use item access:

如果你想通过*名称*访问枚举成员，使用项目访问：

```python
>>> Color['red']
<Color.red: 1>
>>> Color['green']
<Color.green: 2>
```

## [Duplicating enum members and values](https://www.python.org/dev/peps/pep-0435/#id28)

重复枚举成员和值

Having two enum members with the same name is invalid:

拥有两个同名的枚举成员是无效的：

```python
>>> class Shape(Enum):
...   square = 2
...   square = 3
...
Traceback (most recent call last):
...
TypeError: Attempted to reuse key: square
```

However, two enum members are allowed to have the same value. Given two members A and B with the same value (and A defined first), B is an alias to A. By-value lookup of the value of A and B will return A. By-name lookup of B will also return A:

然而，允许两个枚举成员有相同的值。如果有两个成员A和B具有相同的值（并且A先定义），B是A的别名。对A和B的值进行逐一查找将返回A，对B进行逐一查找也将返回A。

```python
>>> class Shape(Enum):
...   square = 2
...   diamond = 1
...   circle = 3
...   alias_for_square = 2
...
>>> Shape.square
<Shape.square: 2>
>>> Shape.alias_for_square
<Shape.square: 2>
>>> Shape(2)
<Shape.square: 2>
```

Iterating over the members of an enum does not provide the aliases:

遍历一个枚举的成员并不能提供别名：

```python
>>> list(Shape)
[<Shape.square: 2>, <Shape.diamond: 1>, <Shape.circle: 3>]
```

The special attribute `__members__` is an ordered dictionary mapping names to members. It includes all names defined in the enumeration, including the aliases:

特殊属性`__members__`是一个有序的字典，将名字映射到成员。它包括所有在枚举中定义的名字，包括别名。

```python
>>> for name, member in Shape.__members__.items():
...   name, member
...
('square', <Shape.square: 2>)
('diamond', <Shape.diamond: 1>)
('circle', <Shape.circle: 3>)
('alias_for_square', <Shape.square: 2>)
```

The `__members__` attribute can be used for detailed programmatic access to the enumeration members. For example, finding all the aliases:

`__members__`属性可以用来对枚举成员进行详细的程序化访问。例如，找到所有的别名：

```python
>>> [name for name, member in Shape.__members__.items() if member.name != name]
['alias_for_square']
```

## [Comparisons](https://www.python.org/dev/peps/pep-0435/#id29)

比较

Enumeration members are compared by identity:

枚举成员通过标识符进行比较：

```python
>>> Color.red is Color.red
True
>>> Color.red is Color.blue
False
>>> Color.red is not Color.blue
True
```

Ordered comparisons between enumeration values are *not* supported. Enums are not integers (but see [IntEnum](https://www.python.org/dev/peps/pep-0435/#intenum) below):

不支持在枚举值之间进行有序的比较。枚举不是整数（下面的IntEnum是一个例外）：

```python
>>> Color.red < Color.blue
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unorderable types: Color() < Color()
```

Equality comparisons are defined though:

虽然定义了相等的比较：

```python
>>> Color.blue == Color.red
False
>>> Color.blue != Color.red
True
>>> Color.blue == Color.blue
True
```

Comparisons against non-enumeration values will always compare not equal (again, `IntEnum` was explicitly designed to behave differently, see below):

对非枚举值的比较将总是不相等的（同样，IntEnum被明确设计为不同的行为，见下文）。

```python
>>> Color.blue == 2
False
```

## [Allowed members and attributes of enumerations](https://www.python.org/dev/peps/pep-0435/#id30)

枚举的允许成员和属性

The examples above use integers for enumeration values. Using integers is short and handy (and provided by default by the [Functional API](https://www.python.org/dev/peps/pep-0435/#functional-api)), but not strictly enforced. In the vast majority of use-cases, one doesn't care what the actual value of an enumeration is. But if the value *is* important, enumerations can have arbitrary values.

上面的例子使用整数作为枚举值。使用整数是简短而方便的（由[Functional API](https://www.python.org/dev/peps/pep-0435/#functional-api)默认提供），但不是严格执行的。在绝大多数情况下，人们并不关心枚举的实际值是什么。但是如果值*确实*重要，枚举可以有任意的值。

Enumerations are Python classes, and can have methods and special methods as usual. If we have this enumeration:

枚举是Python类，可以像往常一样拥有方法和特殊方法。如果我们有这个枚举：

```python
class Mood(Enum):
  funky = 1
  happy = 3

  def describe(self):
    # self is the member here
    return self.name, self.value

  def __str__(self):
    return 'my custom str! {0}'.format(self.value)

  @classmethod
  def favorite_mood(cls):
    # cls here is the enumeration
    return cls.happy
```

Then:

```python
>>> Mood.favorite_mood()
<Mood.happy: 3>
>>> Mood.happy.describe()
('happy', 3)
>>> str(Mood.funky)
'my custom str! 1'
```

The rules for what is allowed are as follows: all attributes defined within an enumeration will become members of this enumeration, with the exception of *__dunder__* names and descriptors [[9\]](https://www.python.org/dev/peps/pep-0435/#id20); methods are descriptors too.

允许的规则如下：在一个枚举中定义的所有属性都将成为这个枚举的成员，但*__dounder__*的名称和描述符[[9](https://www.python.org/dev/peps/pep-0435/#id20)]除外；方法也是描述符。

## [Restricted subclassing of enumerations](https://www.python.org/dev/peps/pep-0435/#id31)

枚举的限制性子类化

Subclassing an enumeration is allowed only if the enumeration does not define any members. So this is forbidden:

只有当枚举没有定义任何成员时，才允许对枚举进行子类化。所以这是被禁止的：

```python
>>> class MoreColor(Color):
...   pink = 17
...
TypeError: Cannot extend enumerations
```

But this is allowed:

但是这样是允许的：

```
>>> class Foo(Enum):
...   def some_behavior(self):
...     pass
...
>>> class Bar(Foo):
...   happy = 1
...   sad = 2
...
```

The rationale for this decision was given by Guido in [[6\]](https://www.python.org/dev/peps/pep-0435/#id16). Allowing to subclass enums that define members would lead to a violation of some important invariants of types and instances. On the other hand, it makes sense to allow sharing some common behavior between a group of enumerations, and subclassing empty enumerations is also used to implement `IntEnum`.

这个决定的理由是由Guido在[6]中给出的。允许对定义成员的枚举进行子类化会导致对类型和实例的一些重要不变性的违反。另一方面，允许在一组枚举之间共享一些共同的行为是有意义的，而子类化空枚举也被用来实现`IntEnum`。

## [IntEnum](https://www.python.org/dev/peps/pep-0435/#id32)

A variation of `Enum` is proposed which is also a subclass of `int`. Members of an `IntEnum` can be compared to integers; by extension, integer enumerations of different types can also be compared to each other:

我们提出了一个`Enum`的变体，它也是`int`的一个子类。`IntEnum`的成员可以与整数进行比较；通过扩展，不同类型的整数枚举也可以相互比较。

```python
>>> from enum import IntEnum
>>> class Shape(IntEnum):
...   circle = 1
...   square = 2
...
>>> class Request(IntEnum):
...   post = 1
...   get = 2
...
>>> Shape == 1
False
>>> Shape.circle == 1
True
>>> Shape.circle == Request.post
True
```

However they still can't be compared to `Enum`:

但是他们仍然不能与`Enum`相比：

```python
>>> class Shape(IntEnum):
...   circle = 1
...   square = 2
...
>>> class Color(Enum):
...   red = 1
...   green = 2
...
>>> Shape.circle == Color.red
False
```

`IntEnum` values behave like integers in other ways you'd expect:

`IntEnum`值在其他方面的表现与整数相同，你可以期待：

```python
>>> int(Shape.circle)
1
>>> ['a', 'b', 'c'][Shape.circle]
'b'
>>> [i for i in range(Shape.square)]
[0, 1]
```

For the vast majority of code, `Enum` is strongly recommended, since `IntEnum` breaks some semantic promises of an enumeration (by being comparable to integers, and thus by transitivity to other unrelated enumerations). It should be used only in special cases where there's no other choice; for example, when integer constants are replaced with enumerations and backwards compatibility is required with code that still expects integers.

对于绝大多数代码，强烈建议使用`Enum`，因为`IntEnum`破坏了枚举的一些语义承诺（通过与整数的可比性，以及与其他不相关的枚举的可转换性）。它应该只在没有其他选择的特殊情况下使用；例如，当整数常量被替换成枚举，并且需要向后兼容那些仍然期望使用整数的代码时。

## [Other derived enumerations](https://www.python.org/dev/peps/pep-0435/#id33)

其他衍生枚举

`IntEnum` will be part of the `enum` module. However, it would be very simple to implement independently:

`IntEnum`将是枚举模块的一部分。然而，独立实现它将是非常简单的：

```python
class IntEnum(int, Enum):
    pass
```

This demonstrates how similar derived enumerations can be defined, for example a `StrEnum` that mixes in `str` instead of `int`.

这展示了如何定义类似的派生枚举，例如，一个混合了`str`而不是`int`的`StrEnum`。

Some rules:

一些规则：

1. When subclassing Enum, mix-in types must appear before Enum itself in the sequence of bases, as in the `IntEnum` example above.

   当子类化`Enum`时，混合类型必须在基数序列中出现在`Enum`本身之前，如上面的`IntEnum`例子。

2. While Enum can have members of any type, once you mix in an additional type, all the members must have values of that type, e.g. `int` above. This restriction does not apply to mix-ins which only add methods and don't specify another data type such as `int` or `str`.

   虽然Enum可以有任何类型的成员，但一旦你混入一个额外的类型，所有的成员必须有该类型的值，例如上面的int。这个限制并不适用于只增加方法而不指定另一种数据类型（如`int`或`str`）的混入。

## [Pickling](https://www.python.org/dev/peps/pep-0435/#id34)

序列化

Enumerations can be pickled and unpickled:

枚举可以被序列化和反序列化：

```python
>>> from enum.tests.fruit import Fruit
>>> from pickle import dumps, loads
>>> Fruit.tomato is loads(dumps(Fruit.tomato))
True
```

The usual restrictions for pickling apply: picklable enums must be defined in the top level of a module, since unpickling requires them to be importable from that module.

适用于序列化的通常限制：可腌制的枚举必须定义在一个模块的顶层，因为反序列化要求它们可以从该模块导入。

> 更多关于Python使用pickle模块进行序列化的说明见[**Python pickle模块：实现Python对象的持久化存储**](http://c.biancheng.net/view/5736.html)，译者注。

## [Functional API](https://www.python.org/dev/peps/pep-0435/#id35)

功能性API

The `Enum` class is callable, providing the following functional API:

`Enum`类是可调用的，提供以下功能API：

```python
>>> Animal = Enum('Animal', 'ant bee cat dog')
>>> Animal
<Enum 'Animal'>
>>> Animal.ant
<Animal.ant: 1>
>>> Animal.ant.value
1
>>> list(Animal)
[<Animal.ant: 1>, <Animal.bee: 2>, <Animal.cat: 3>, <Animal.dog: 4>]
```

The semantics of this API resemble `namedtuple`. The first argument of the call to `Enum` is the name of the enumeration. Pickling enums created with the functional API will work on CPython and PyPy, but for IronPython and Jython you may need to specify the module name explicitly as follows:

这个API的语义类似于`namedtuple`。调用`Enum`的第一个参数是枚举的名称。用函数式API创建的枚举可以在CPython和PyPy上使用，但是对于IronPython和Jython，你可能需要明确指定模块名称，如下所示。

```python
>>> Animals = Enum('Animals', 'ant bee cat dog', module=__name__)
```

The second argument is the *source* of enumeration member names. It can be a whitespace-separated string of names, a sequence of names, a sequence of 2-tuples with key/value pairs, or a mapping (e.g. dictionary) of names to values. The last two options enable assigning arbitrary values to enumerations; the others auto-assign increasing integers starting with 1. A new class derived from `Enum` is returned. In other words, the above assignment to `Animal` is equivalent to:

第二个参数是枚举成员名称的来源。它可以是一个以空格分隔的名称字符串，一个名称序列，一个带有键/值对的2元组序列，或者一个名称到值的映射（例如字典）。后两个选项可以为枚举分配任意的值；其他选项自动分配从1开始的递增的整数。 将返回一个从`Enum`派生的新类。换句话说，上面对`Animal`的赋值等同于。

```python
>>> class Animals(Enum):
...   ant = 1
...   bee = 2
...   cat = 3
...   dog = 4
```

The reason for defaulting to `1` as the starting number and not `0` is that `0` is `False` in a boolean sense, but enum members all evaluate to `True`.

默认使用`1`作为起始数字而不是`0`的原因是`0`在布尔意义上是`False`，但枚举成员都评估为`True`。

# [Proposed variations](https://www.python.org/dev/peps/pep-0435/#id36)

建议的演变

Some variations were proposed during the discussions in the mailing list. Here's some of the more popular ones.

在邮件列表的讨论中，有人提出了一些变化。下面是一些比较流行的。

## [flufl.enum](https://www.python.org/dev/peps/pep-0435/#id37)

`flufl.enum` was the reference implementation upon which this PEP was originally based. Eventually, it was decided against the inclusion of `flufl.enum` because its design separated enumeration members from enumerations, so the former are not instances of the latter. Its design also explicitly permits subclassing enumerations for extending them with more members (due to the member/enum separation, the type invariants are not violated in `flufl.enum` with such a scheme).

`flufl.enum` 是本 PEP 最初所依据的参考实现。最终，我们决定不纳入 `flufl.enum`，因为它的设计将枚举成员与枚举分开，所以前者不是后者的实例。它的设计也明确地允许对枚举进行子类化，以便用更多的成员来扩展它们（由于成员/枚举的分离，在 `flufl.enum` 中这样的方案不会违反类型不变性）。

## [Not having to specify values for enums](https://www.python.org/dev/peps/pep-0435/#id38)

不必为枚举指定值

Michael Foord proposed (and Tim Delaney provided a proof-of-concept implementation) to use metaclass magic that makes this possible:

Michael Foord提议（Tim Delaney提供了一个概念验证的实现），使用元类魔法，使这成为可能。

```python
class Color(Enum):
    red, green, blue
```

The values get actually assigned only when first looked up.

这些值只有在第一次查询时才会被实际分配。

Pros: cleaner syntax that requires less typing for a very common task (just listing enumeration names without caring about the values).

优点：更干净的语法，对于一个非常常见的任务（只是列出枚举名称而不关心值）需要更少的输入。

Cons: involves much magic in the implementation, which makes even the definition of such enums baffling when first seen. Besides, explicit is better than implicit.

缺点：在实现中涉及到很多魔法，这使得第一次看到这种枚举的定义时也会感到困惑。此外，显式比隐式好。

## [Using special names or forms to auto-assign enum values](https://www.python.org/dev/peps/pep-0435/#id39)

使用特殊名称或形式来自动分配枚举值

A different approach to avoid specifying enum values is to use a special name or form to auto assign them. For example:

避免指定枚举值的另一种方法是使用一个特殊的名称或形式来自动分配它们。例如：

```python
class Color(Enum):
    red = None          # auto-assigned to 0
    green = None        # auto-assigned to 1
    blue = None         # auto-assigned to 2
```

More flexibly:

更灵活的方式：

```python
class Color(Enum):
    red = 7
    green = None        # auto-assigned to 8
    blue = 19
    purple = None       # auto-assigned to 20
```

Some variations on this theme:

这个主题的一些变化：

1. A special name `auto` imported from the enum package.

   从枚举包中导入一个特殊的名字`auto`。

2. Georg Brandl proposed ellipsis (`...`) instead of `None` to achieve the same effect.

    Georg Brandl提议用省略号（`...`）代替`None`来达到同样的效果。

Pros: no need to manually enter values. Makes it easier to change the enum and extend it, especially for large enumerations.

优点：不需要手动输入数值。使得改变枚举和扩展枚举更加容易，特别是对于大型枚举。

Cons: actually longer to type in many simple cases. The argument of explicit vs. implicit applies here as well.

缺点：在许多简单的情况下，实际上需要更长的时间来输入。显式与隐式的争论也适用于此。

# [Use-cases in the standard library](https://www.python.org/dev/peps/pep-0435/#id40)

标准库中的用例

The Python standard library has many places where the usage of enums would be beneficial to replace other idioms currently used to represent them. Such usages can be divided to two categories: user-code facing constants, and internal constants.

在 Python 标准库中，有许多地方使用枚举将有利于取代目前用于表示枚举的其他习惯。这种使用可以分为两类：面向用户代码的常量和内部常量。

User-code facing constants like `os.SEEK_*`, `socket` module constants, decimal rounding modes and HTML error codes could require backwards compatibility since user code may expect integers. `IntEnum` as described above provides the required semantics; being a subclass of `int`, it does not affect user code that expects integers, while on the other hand allowing printable representations for enumeration values:

面向用户代码的常量，如`os.SEEK_*`，`socket`模块常量，小数四舍五入模式和HTML错误代码，可能需要向后兼容，因为用户代码可能期望得到整数。如上所述的`IntEnum`提供了所需的语义；作为`int`的一个子类，它不影响用户代码对整数的期望，而另一方面，允许枚举值的可打印表示。

```python
>>> import socket
>>> family = socket.AF_INET
>>> family == 2
True
>>> print(family)
SocketFamily.AF_INET
```

Internal constants are not seen by user code but are employed internally by stdlib modules. These can be implemented with `Enum`. Some examples uncovered by a very partial skim through the stdlib: `binhex`, `imaplib`, `http/client`, `urllib/robotparser`, `idlelib`, `concurrent.futures`, `turtledemo`.

内部常量不会被用户代码看到，但会被stdlib模块内部使用。这些可以用`Enum`来实现。通过对stdlib的局部浏览，发现了一些例子：`binhex`,`imaplib`, `http/client`, `urllib/robotparser`, `idlelib`, `concurrent.futures`, `turtledemo`.

In addition, looking at the code of the Twisted library, there are many use cases for replacing internal state constants with enums. The same can be said about a lot of networking code (especially implementation of protocols) and can be seen in test protocols written with the Tulip library as well.

此外，看一下Twisted库的代码，有很多用枚举代替内部状态常量的用例。很多网络代码也是如此（尤其是协议的实现），在用Tulip库编写的测试协议中也可以看到。

# [Acknowledgments](https://www.python.org/dev/peps/pep-0435/#id41)

致谢

This PEP was initially proposing including the `flufl.enum` package [[8\]](https://www.python.org/dev/peps/pep-0435/#id19) by Barry Warsaw into the stdlib, and is inspired in large parts by it. Ben Finney is the author of the earlier enumeration [PEP 354](https://www.python.org/dev/peps/pep-0354).

本PEP最初提议将Barry Warsaw的`flufl.enum`包[[8\]](https://www.python.org/dev/peps/pep-0435/#id19)纳入stdlib，并在很大程度上受到其启发。Ben Finney是早期枚举的作者[PEP 354](https://www.python.org/dev/peps/pep-0354)。

# [References](https://www.python.org/dev/peps/pep-0435/#id42)

参考文献

|                                                        |                                                              |
| ------------------------------------------------------ | ------------------------------------------------------------ |
| [[1\]](https://www.python.org/dev/peps/pep-0435/#id7)  | https://mail.python.org/pipermail/python-dev/2013-May/126112.html |
|                                                        |                                                              |
| [[2\]](https://www.python.org/dev/peps/pep-0435/#id1)  | http://www.python.org/dev/peps/pep-0354/                     |
|                                                        |                                                              |
| [[3\]](https://www.python.org/dev/peps/pep-0435/#id2)  | https://mail.python.org/pipermail/python-ideas/2013-January/019003.html |
|                                                        |                                                              |
| [[4\]](https://www.python.org/dev/peps/pep-0435/#id3)  | https://mail.python.org/pipermail/python-ideas/2013-February/019373.html |
|                                                        |                                                              |
| [[5\]](https://www.python.org/dev/peps/pep-0435/#id4)  | To make enums behave similarly to Python classes like bool, and behave in a more intuitive way. It would be surprising if the type of `Color.red` would not be `Color`. (Discussion in https://mail.python.org/pipermail/python-dev/2013-April/125687.html) |
|                                                        | 为了使枚举的行为类似于像bool这样的Python类，并以更直观的方式表现。如果`Color.red`的类型不是`Color`，那就很奇怪了。（讨论在https://mail.python.org/pipermail/python-dev/2013-April/125687.html） |
| [6]                                                    | *([1](https://www.python.org/dev/peps/pep-0435/#id5), [2](https://www.python.org/dev/peps/pep-0435/#id9), [3](https://www.python.org/dev/peps/pep-0435/#id18))* Subclassing enums and adding new members creates an unresolvable situation; on one hand `MoreColor.red` and `Color.red` should not be the same object, and on the other `isinstance` checks become confusing if they are not. The discussion also links to Stack Overflow discussions that make additional arguments. (https://mail.python.org/pipermail/python-dev/2013-April/125716.html) |
|                                                        | *([1](https://www.python.org/dev/peps/pep-0435/#id5), [2](https://www.python.org/dev/peps/pep-0435/#id9), [3](https://www.python.org/dev/peps/pep-0435/#id18))* 子类化枚举并添加新成员会造成无法解决的情况；一方面`MoreColor.red`和`Color.red`不应该是同一个对象，另一方面如果它们不是，`isinstance`检查会变得很混乱。该讨论还链接到Stack Overflow的讨论，提出了额外的论点。(https://mail.python.org/pipermail/python-dev/2013-April/125716.html) |
| [[7\]](https://www.python.org/dev/peps/pep-0435/#id6)  | It may be useful to have a class defining some behavior (methods, with no actual enumeration members) mixed into an enum, and this would not create the problem discussed in [[6\]](https://www.python.org/dev/peps/pep-0435/#id16). (Discussion in https://mail.python.org/pipermail/python-dev/2013-May/125859.html) |
|                                                        | 将定义一些行为的类（方法，没有实际的枚举成员）混入枚举中可能很有用，这不会产生[[6](https://www.python.org/dev/peps/pep-0435/#id16)]中讨论的问题。(讨论在https://mail.python.org/pipermail/python-dev/2013-May/125859.html) |
| [[8\]](https://www.python.org/dev/peps/pep-0435/#id10) | http://pythonhosted.org/flufl.enum/                          |
|                                                        |                                                              |
| [[9\]](https://www.python.org/dev/peps/pep-0435/#id8)  | http://docs.python.org/3/howto/descriptor.html               |
|                                                        |                                                              |

# [Copyright](https://www.python.org/dev/peps/pep-0435/#id43)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0435.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。

