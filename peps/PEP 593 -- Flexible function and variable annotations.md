# PEP 593 -- Flexible function and variable annotations

PEP 593 -- 灵活的函数和变量注解

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 593                                                          |
| Title:          | Flexible function and variable annotations                   |
| Author:         | Till Varoquaux <till at fb.com>, Konstantin Kashin <kkashin at fb.com> |
| Sponsor:        | Ivan Levkivskyi <levkivskyi at gmail.com>                    |
| Discussions-To: | [typing-sig at python.org](mailto:typing-sig@python.org?subject=PEP 593) |
| Status:         | Accepted                                                     |
| Type:           | Standards Track                                              |
| Created:        | 26-Apr-2019                                                  |
| Python-Version: | 3.9                                                          |
| Post-History:   | 20-May-2019                                                  |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0593/#abstract)
- [Motivation](https://www.python.org/dev/peps/pep-0593/#motivation)
- [Rationale](https://www.python.org/dev/peps/pep-0593/#rationale)
- Motivating examples
  - [Combining runtime and static uses of annotations](https://www.python.org/dev/peps/pep-0593/#combining-runtime-and-static-uses-of-annotations)
  - [Lowering barriers to developing new typing constructs](https://www.python.org/dev/peps/pep-0593/#lowering-barriers-to-developing-new-typing-constructs)
- Specification
  - [Syntax](https://www.python.org/dev/peps/pep-0593/#syntax)
  - [Consuming annotations](https://www.python.org/dev/peps/pep-0593/#consuming-annotations)
  - [Interaction with `get_type_hints()`](https://www.python.org/dev/peps/pep-0593/#interaction-with-get-type-hints)
  - [Aliases & Concerns over verbosity](https://www.python.org/dev/peps/pep-0593/#aliases-concerns-over-verbosity)
- [Rejected ideas](https://www.python.org/dev/peps/pep-0593/#rejected-ideas)
- [References](https://www.python.org/dev/peps/pep-0593/#references)
- [Copyright](https://www.python.org/dev/peps/pep-0593/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0593/#id11)

概述

This PEP introduces a mechanism to extend the type annotations from [PEP 484](https://www.python.org/dev/peps/pep-0484) with arbitrary metadata.

这个PEP引入了一种机制，用任意的元数据来扩展PEP 484的类型注解。

# [Motivation](https://www.python.org/dev/peps/pep-0593/#id12)

动机

[PEP 484](https://www.python.org/dev/peps/pep-0484) provides a standard semantic for the annotations introduced in [PEP 3107](https://www.python.org/dev/peps/pep-3107). [PEP 484](https://www.python.org/dev/peps/pep-0484) is prescriptive but it is the de facto standard for most of the consumers of annotations; in many statically checked code bases, where type annotations are widely used, they have effectively crowded out any other form of annotation. Some of the use cases for annotations described in [PEP 3107](https://www.python.org/dev/peps/pep-3107) (database mapping, foreign languages bridge) are not currently realistic given the prevalence of type annotations. Furthermore, the standardisation of type annotations rules out advanced features only supported by specific type checkers.

PEP 484为PEP 3107中引入的注解提供了一个标准语义。PEP 484是规定性的，但对于大多数注解的用户来说，它是事实上的标准；在许多静态检查的代码库中，类型注解被广泛使用，它们已经有效地排挤了任何其他形式的注释。鉴于类型注解的盛行，PEP 3107中描述的一些注解用例（数据库映射、外语桥）目前并不现实。此外，类型注解的标准化排除了仅由特定类型检查器支持的高级功能。

# [Rationale](https://www.python.org/dev/peps/pep-0593/#id13)

理由

This PEP adds an `Annotated` type to the typing module to decorate existing types with context-specific metadata. Specifically, a type `T` can be annotated with metadata `x` via the typehint `Annotated[T, x]`. This metadata can be used for either static analysis or at runtime. If a library (or tool) encounters a typehint `Annotated[T, x]` and has no special logic for metadata `x`, it should ignore it and simply treat the type as `T`. Unlike the `no_type_check` functionality that currently exists in the `typing` module which completely disables typechecking annotations on a function or a class, the `Annotated` type allows for both static typechecking of `T` (e.g., via mypy [[mypy\]](https://www.python.org/dev/peps/pep-0593/#mypy) or Pyre [[pyre\]](https://www.python.org/dev/peps/pep-0593/#pyre), which can safely ignore `x`) together with runtime access to `x` within a specific application. The introduction of this type would address a diverse set of use cases of interest to the broader Python community.

本PEP为`typing`模块添加了一个`Annotated`类型，以用特定上下文的元数据来装饰现有类型。具体来说，一个类型`T`可以通过类型注释 `Annotated[T, x]`用元数据`x`进行注解。这个元数据可以用于静态分析或在运行时使用。如果一个库（或工具）遇到一个类型提示`Annotated[T, x]`，并且对元数据`x`没有特殊的逻辑，它应该忽略它，并且简单地将该类型视为`T`。通过`mypy[mypy]`或`Pyre[pyre]`，它可以安全地忽略`x`），同时在一个特定的应用程序中对`x`进行运行时访问。这种类型的引入将解决更广泛的Python社区感兴趣的各种用例。

This was originally brought up as issue 600 [[issue-600\]](https://www.python.org/dev/peps/pep-0593/#issue-600) in the typing github and then discussed in Python ideas [[python-ideas\]](https://www.python.org/dev/peps/pep-0593/#python-ideas).

这最初是作为600号问题[issue-600]在打字的github中提出的，然后在Python ideas[python-ideas]中讨论。

# [Motivating examples](https://www.python.org/dev/peps/pep-0593/#id14)

## [Combining runtime and static uses of annotations](https://www.python.org/dev/peps/pep-0593/#id15)

There's an emerging trend of libraries leveraging the typing annotations at runtime (e.g.: dataclasses); having the ability to extend the typing annotations with external data would be a great boon for those libraries.

Here's an example of how a hypothetical module could leverage annotations to read c structs:

```
UnsignedShort = Annotated[int, struct2.ctype('H')]
SignedChar = Annotated[int, struct2.ctype('b')]

class Student(struct2.Packed):
    # mypy typechecks 'name' field as 'str'
    name: Annotated[str, struct2.ctype("<10s")]
    serialnum: UnsignedShort
    school: SignedChar

# 'unpack' only uses the metadata within the type annotations
Student.unpack(record)
# Student(name=b'raymond   ', serialnum=4658, school=264)
```

## [Lowering barriers to developing new typing constructs](https://www.python.org/dev/peps/pep-0593/#id16)

Typically when adding a new type, a developer need to upstream that type to the typing module and change mypy, PyCharm [[pycharm\]](https://www.python.org/dev/peps/pep-0593/#pycharm), Pyre, pytype [[pytype\]](https://www.python.org/dev/peps/pep-0593/#pytype), etc... This is particularly important when working on open-source code that makes use of these types, seeing as the code would not be immediately transportable to other developers' tools without additional logic. As a result, there is a high cost to developing and trying out new types in a codebase. Ideally, authors should be able to introduce new types in a manner that allows for graceful degradation (e.g.: when clients do not have a custom mypy plugin [[mypy-plugin\]](https://www.python.org/dev/peps/pep-0593/#mypy-plugin)), which would lower the barrier to development and ensure some degree of backward compatibility.

For example, suppose that an author wanted to add support for tagged unions [[tagged-union\]](https://www.python.org/dev/peps/pep-0593/#tagged-union) to Python. One way to accomplish would be to annotate `TypedDict` [[typed-dict\]](https://www.python.org/dev/peps/pep-0593/#typed-dict) in Python such that only one field is allowed to be set:

```
Currency = Annotated[
    TypedDict('Currency', {'dollars': float, 'pounds': float}, total=False),
    TaggedUnion,
]
```

This is a somewhat cumbersome syntax but it allows us to iterate on this proof-of-concept and have people with type checkers (or other tools) that don't yet support this feature work in a codebase with tagged unions. The author could easily test this proposal and iron out the kinks before trying to upstream tagged union to `typing`, mypy, etc. Moreover, tools that do not have support for parsing the `TaggedUnion` annotation would still be able to treat `Currency` as a `TypedDict`, which is still a close approximation (slightly less strict).

# [Specification](https://www.python.org/dev/peps/pep-0593/#id17)

## [Syntax](https://www.python.org/dev/peps/pep-0593/#id18)

`Annotated` is parameterized with a type and an arbitrary list of Python values that represent the annotations. Here are the specific details of the syntax:

- The first argument to `Annotated` must be a valid type

- Multiple type annotations are supported (`Annotated` supports variadic arguments):

  ```
  Annotated[int, ValueRange(3, 10), ctype("char")]
  ```

- `Annotated` must be called with at least two arguments ( `Annotated[int]` is not valid)

- The order of the annotations is preserved and matters for equality checks:

  ```
  Annotated[int, ValueRange(3, 10), ctype("char")] != Annotated[
      int, ctype("char"), ValueRange(3, 10)
  ]
  ```

- Nested `Annotated` types are flattened, with metadata ordered starting with the innermost annotation:

  ```
  Annotated[Annotated[int, ValueRange(3, 10)], ctype("char")] == Annotated[
      int, ValueRange(3, 10), ctype("char")
  ]
  ```

- Duplicated annotations are not removed:

  ```
  Annotated[int, ValueRange(3, 10)] != Annotated[
      int, ValueRange(3, 10), ValueRange(3, 10)
  ]
  ```

- `Annotated` can be used with nested and generic aliases:

  ```
  Typevar T = ...
  Vec = Annotated[List[Tuple[T, T]], MaxLen(10)]
  V = Vec[int]
  
  V == Annotated[List[Tuple[int, int]], MaxLen(10)]
  ```

## [Consuming annotations](https://www.python.org/dev/peps/pep-0593/#id19)

Ultimately, the responsibility of how to interpret the annotations (if at all) is the responsibility of the tool or library encountering the `Annotated` type. A tool or library encountering an `Annotated` type can scan through the annotations to determine if they are of interest (e.g., using `isinstance()`).

**Unknown annotations:** When a tool or a library does not support annotations or encounters an unknown annotation it should just ignore it and treat annotated type as the underlying type. For example, when encountering an annotation that is not an instance of `struct2.ctype` to the annotations for name (e.g., `Annotated[str, 'foo', struct2.ctype("<10s")]`), the unpack method should ignore it.

**Namespacing annotations:** Namespaces are not needed for annotations since the class used by the annotations acts as a namespace.

**Multiple annotations:** It's up to the tool consuming the annotations to decide whether the client is allowed to have several annotations on one type and how to merge those annotations.

Since the `Annotated` type allows you to put several annotations of the same (or different) type(s) on any node, the tools or libraries consuming those annotations are in charge of dealing with potential duplicates. For example, if you are doing value range analysis you might allow this:

```
T1 = Annotated[int, ValueRange(-10, 5)]
T2 = Annotated[T1, ValueRange(-20, 3)]
```

Flattening nested annotations, this translates to:

```
T2 = Annotated[int, ValueRange(-10, 5), ValueRange(-20, 3)]
```

## [Interaction with `get_type_hints()`](https://www.python.org/dev/peps/pep-0593/#id20)

`typing.get_type_hints()` will take a new argument `include_extras` that defaults to `False` to preserve backward compatibility. When `include_extras` is `False`, the extra annotations will be stripped out of the returned value. Otherwise, the annotations will be returned unchanged:

```
@struct2.packed
class Student(NamedTuple):
    name: Annotated[str, struct.ctype("<10s")]

get_type_hints(Student) == {'name': str}
get_type_hints(Student, include_extras=False) == {'name': str}
get_type_hints(Student, include_extras=True) == {
    'name': Annotated[str, struct.ctype("<10s")]
}
```

## [Aliases & Concerns over verbosity](https://www.python.org/dev/peps/pep-0593/#id21)

Writing `typing.Annotated` everywhere can be quite verbose; fortunately, the ability to alias annotations means that in practice we don't expect clients to have to write lots of boilerplate code:

```
T = TypeVar('T')
Const = Annotated[T, my_annotations.CONST]

Class C:
    def const_method(self: Const[List[int]]) -> int:
        ...
```

# [Rejected ideas](https://www.python.org/dev/peps/pep-0593/#id22)

Some of the proposed ideas were rejected from this PEP because they would cause `Annotated` to not integrate cleanly with the other typing annotations:

- `Annotated` cannot infer the decorated type. You could imagine that `Annotated[..., Immutable]` could be used to mark a value as immutable while still inferring its type. Typing does not support using the inferred type anywhere else [[issue-276\]](https://www.python.org/dev/peps/pep-0593/#issue-276); it's best to not add this as a special case.
- Using `(Type, Ann1, Ann2, ...)` instead of `Annotated[Type, Ann1, Ann2, ...]`. This would cause confusion when annotations appear in nested positions (`Callable[[A, B], C]` is too similar to `Callable[[(A, B)], C]`) and would make it impossible for constructors to be passthrough (`T(5) == C(5)` when `C = Annotation[T, Ann]`).

This feature was left out to keep the design simple:

- `Annotated` cannot be called with a single argument. Annotated could support returning the underlying value when called with a single argument (e.g.: `Annotated[int] == int`). This complicates the specifications and adds little benefit.

# [References](https://www.python.org/dev/peps/pep-0593/#id23)

| [[issue-600\]](https://www.python.org/dev/peps/pep-0593/#id3) | https://github.com/python/typing/issues/600 |
| ------------------------------------------------------------ | ------------------------------------------- |
|                                                              |                                             |

| [[python-ideas\]](https://www.python.org/dev/peps/pep-0593/#id4) | https://mail.python.org/pipermail/python-ideas/2019-January/054908.html |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

| [struct-doc] | https://docs.python.org/3/library/struct.html#examples |
| ------------ | ------------------------------------------------------ |
|              |                                                        |

| [[mypy\]](https://www.python.org/dev/peps/pep-0593/#id1) | http://www.mypy-lang.org/ |
| -------------------------------------------------------- | ------------------------- |
|                                                          |                           |

| [[pyre\]](https://www.python.org/dev/peps/pep-0593/#id2) | https://pyre-check.org/ |
| -------------------------------------------------------- | ----------------------- |
|                                                          |                         |

| [[pycharm\]](https://www.python.org/dev/peps/pep-0593/#id5) | https://www.jetbrains.com/pycharm/ |
| ----------------------------------------------------------- | ---------------------------------- |
|                                                             |                                    |

| [[pytype\]](https://www.python.org/dev/peps/pep-0593/#id6) | https://github.com/google/pytype |
| ---------------------------------------------------------- | -------------------------------- |
|                                                            |                                  |

| [[mypy-plugin\]](https://www.python.org/dev/peps/pep-0593/#id7) | https://github.com/python/mypy_extensions |
| ------------------------------------------------------------ | ----------------------------------------- |
|                                                              |                                           |

| [[tagged-union\]](https://www.python.org/dev/peps/pep-0593/#id8) | https://en.wikipedia.org/wiki/Tagged_union |
| ------------------------------------------------------------ | ------------------------------------------ |
|                                                              |                                            |

| [[typed-dict\]](https://www.python.org/dev/peps/pep-0593/#id9) | https://mypy.readthedocs.io/en/latest/more_types.html#typeddict |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
|                                                              |                                                              |

| [[issue-276\]](https://www.python.org/dev/peps/pep-0593/#id10) | https://github.com/python/typing/issues/276 |
| ------------------------------------------------------------ | ------------------------------------------- |
|                                                              |                                             |

# [Copyright](https://www.python.org/dev/peps/pep-0593/#id24)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0593.rst