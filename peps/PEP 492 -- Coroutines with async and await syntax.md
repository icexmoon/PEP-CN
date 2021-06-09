# PEP 492 -- Coroutines with async and await syntax

> 原文地址：<https://www.python.org/dev/peps/pep-0492/>

PEP492--通过async与await语句实现协程

# [Abstract](https://www.python.org/dev/peps/pep-0492/#id50)

概览

The growth of Internet and general connectivity has triggered the proportionate need for responsive and scalable code. This proposal aims to answer that need by making writing explicitly asynchronous, concurrent Python code easier and more Pythonic.

网络和普通连接的增长产生了对响应和扩展代码的强劲需求。这个提案是为了通过以更明确、更简单和更Python的方式编写异步和并发的Python代码以回应这种需求。

It is proposed to make *coroutines* a proper standalone concept in Python, and introduce new supporting syntax. The ultimate goal is to help establish a common, easily approachable, mental model of asynchronous programming in Python and make it as close to synchronous programming as possible.

这里建议在Python中将协程明确为一个更独立的概念，并且为此引入新的关键字。最终目标是在Python中建立一种通用的，容易实现的，异步编程概念模型，并且尽量让它更接近于同步编程。

This PEP assumes that the asynchronous tasks are scheduled and coordinated by an Event Loop similar to that of stdlib module `asyncio.events.AbstractEventLoop`. While the PEP is not tied to any specific Event Loop implementation, it is relevant only to the kind of coroutine that uses `yield` as a signal to the scheduler, indicating that the coroutine will be waiting until an event (such as IO) is completed.

这个PEP假设异步任务通过一个类似于标准模块`asyncio.events.AbstractEventLoop`的事件循环进行调度和协作。尽管这个PEP没有和任何特定的事件循环实现相关联，它仅仅与使用`yield`作为信号与调度程序通信的那一类协程相关，以表明这些协程将等待一个事件（比如IO）以实现响应。

We believe that the changes proposed here will help keep Python relevant and competitive in a quickly growing area of asynchronous programming, as many other languages have adopted, or are planning to adopt, similar features: [[2\]](https://www.python.org/dev/peps/pep-0492/#id31), [[5\]](https://www.python.org/dev/peps/pep-0492/#id34), [[6\]](https://www.python.org/dev/peps/pep-0492/#id35), [[7\]](https://www.python.org/dev/peps/pep-0492/#id36), [[8\]](https://www.python.org/dev/peps/pep-0492/#id37), [[10\]](https://www.python.org/dev/peps/pep-0492/#id39).

我们相信这里提出的这些改变将让Python在快速增长的异步编程领域保持紧密连系和强劲的竞争力，就像其他语言那样，已经采用或者计划采用类似的功能： [[2\]](https://www.python.org/dev/peps/pep-0492/#id31), [[5\]](https://www.python.org/dev/peps/pep-0492/#id34), [[6\]](https://www.python.org/dev/peps/pep-0492/#id35), [[7\]](https://www.python.org/dev/peps/pep-0492/#id36), [[8\]](https://www.python.org/dev/peps/pep-0492/#id37), [[10\]](https://www.python.org/dev/peps/pep-0492/#id39)。

# [API Design and Implementation Revisions](https://www.python.org/dev/peps/pep-0492/#id51)

API设计和实现修订

1. Feedback on the initial beta release of Python 3.5 resulted in a redesign of the object model supporting this PEP to more clearly separate native coroutines from generators - rather than being a new kind of generator, native coroutines are now their own completely distinct type (implemented in [[17\]](https://www.python.org/dev/peps/pep-0492/#id46)).

   关于Python 3.5的初始beta版收集的反馈导致重新设计了支持此PEP的对象模型，以更清楚地将原生协程从生成器中分离 - 而不是作为一种新的生成器，原生协程现在已经拥有了属于它们自己的完全不同的类型（实现于 [[17\]](https://www.python.org/dev/peps/pep-0492/#id46)）。

   This change was implemented based primarily due to problems encountered attempting to integrate support for native coroutines into the Tornado web server (reported in [[18\]](https://www.python.org/dev/peps/pep-0492/#id47)).

   这种改变主要是为了应对在试图将原生协程整合进网络服务Tornado时候遇到的问题(报告于 [[18\]](https://www.python.org/dev/peps/pep-0492/#id47))。

2. In CPython 3.5.2, the `__aiter__` protocol was updated.

   在Cpython3.5.2中，`__aiter__`协议已经被更新。

   Before 3.5.2, `__aiter__` was expected to return an *awaitable* resolving to an *asynchronous iterator*. Starting with 3.5.2, `__aiter__` should return asynchronous iterators directly.

   在3.5.2之前，`__aiter__`被约定为返回一个用于处理异步迭代器的可等待对象。从3.5.2开始，`__aiter__`应该直接返回异步迭代器。

   If the old protocol is used in 3.5.2, Python will raise a `PendingDeprecationWarning`.

   如果在3.5.2中使用旧有协议，Python会产生一个`PendingDeprecationWarning`异常。

   In CPython 3.6, the old `__aiter__` protocol will still be supported with a `DeprecationWarning` being raised.

   在CPython3.6中，旧的`__aiter__`协议依然被支持，不过会抛出`DeprecationWarning`异常。

   In CPython 3.7, the old `__aiter__` protocol will no longer be supported: a `RuntimeError` will be raised if `__aiter__` returns anything but an asynchronous iterator.

   在CPython3.7中，旧的`__aiter__`协议将不会再被支持：如果`__aiter__`返回异步迭代器之外的任何数据，一个`RuntimeError`异常将被抛出。

   See [[19\]](https://www.python.org/dev/peps/pep-0492/#id48) and [[20\]](https://www.python.org/dev/peps/pep-0492/#id49) for more details.

   更多信息见[[19\]](https://www.python.org/dev/peps/pep-0492/#id48) 和[[20\]](https://www.python.org/dev/peps/pep-0492/#id49) 。

# [Rationale and Goals](https://www.python.org/dev/peps/pep-0492/#id52)

目的和目标

Current Python supports implementing coroutines via generators ([PEP 342](https://www.python.org/dev/peps/pep-0342)), further enhanced by the `yield from` syntax introduced in [PEP 380](https://www.python.org/dev/peps/pep-0380). This approach has a number of shortcomings:

目前的Python支持通过生成器（[PEP 342](https://www.python.org/dev/peps/pep-0342)）实现协程，并且通过[PEP 380](https://www.python.org/dev/peps/pep-0380)中引入的`yield from`语句得到进一步增强。这种实现途径具有许多缺点：

- It is easy to confuse coroutines with regular generators, since they share the same syntax; this is especially true for new developers.

  这很容易将协程与常规的生成器混淆，因为它们共享相同的语法;对于新开发人员来说尤其如此。

- Whether or not a function is a coroutine is determined by a presence of `yield` or `yield from` statements in its *body*, which can lead to unobvious errors when such statements appear in or disappear from function body during refactoring.

  一个函数是否是一个协程取决于其函数体中是否存在`yield`或`yield from`语句，这可能导致在重构期间因这些语句从函数体中添加或移除而产生一些难以察觉的错误。

- Support for asynchronous calls is limited to expressions where `yield` is allowed syntactically, limiting the usefulness of syntactic features, such as `with` and `for` statements.

  对异步调用的支持仅限于`yield`语句允许出现的位置，这限制了语法特点的用途，比如`with`和`for`语句。

This proposal makes coroutines a native Python language feature, and clearly separates them from generators. This removes generator/coroutine ambiguity, and makes it possible to reliably define coroutines without reliance on a specific library. This also enables linters and IDEs to improve static code analysis and refactoring.

这个提案将协程定义为一个明确从生成器中分离的原生的Python语言特性。这会消除生成器/协程歧义，并且可以在不依赖特定库的情况下可靠地定义协程。这也使Linters和IDE能够改善静态代码的分析和重构。

Native coroutines and the associated new syntax features make it possible to define context manager and iteration protocols in asynchronous terms. As shown later in this proposal, the new `async with` statement lets Python programs perform asynchronous calls when entering and exiting a runtime context, and the new `async for` statement makes it possible to perform asynchronous calls in iterators.

原生协程和关联的新语法功能使得可以在异步条件下定义上下文管理器和迭代协议。就像稍后在此提案中所示，新的`async with`语句让Python程序在进入和退出运行时上下文的时候实现异步调用，并且新的`async for`语句使得在迭代器中执行异步调用成为可能。

# [Specification](https://www.python.org/dev/peps/pep-0492/#id53)

规范

This proposal introduces new syntax and semantics to enhance coroutine support in Python.

此提议介绍了新的语法和语义，以增强Python中的协程支持。

This specification presumes knowledge of the implementation of coroutines in Python ([PEP 342](https://www.python.org/dev/peps/pep-0342) and [PEP 380](https://www.python.org/dev/peps/pep-0380)). Motivation for the syntax changes proposed here comes from the asyncio framework ([PEP 3156](https://www.python.org/dev/peps/pep-3156)) and the "Cofunctions" proposal ([PEP 3152](https://www.python.org/dev/peps/pep-3152), now rejected in favor of this specification).

本规范假定了你对Python提案（[PEP 342](https://www.python.org/dev/peps/pep-0342)和[PEP 380](https://www.python.org/dev/peps/pep-0380)）中的协程实现知识已经有所了解。这里提出的语法变化的动机来自Asyncio框架（[PEP 3156](https://www.python.org/dev/peps/pep-3156)）和“Cofunctions”提案（[PEP 3152](https://www.python.org/dev/peps/pep-3152)，已经被拒绝，转而赞同本规范）。

From this point in this document we use the word *native coroutine* to refer to functions declared using the new syntax. *generator-based coroutine* is used where necessary to refer to coroutines that are based on generator syntax. *coroutine* is used in contexts where both definitions are applicable.

从本文档中的这一点来看，我们使用单词“原生协程”来指代使用新语法声明的函数。“基于生成器的协程”被用于指代那些基于生成器语法创建的协程。 在两个定义都适用的上下文中将会使用“协程”。

## [New Coroutine Declaration Syntax](https://www.python.org/dev/peps/pep-0492/#id54)

新的协程声明语法

The following new syntax is used to declare a *native coroutine*:

下面的新语法被用于声明一个原生协程：

```python
async def read_data(db):
    pass
```

Key properties of *coroutines*:

 协程函数的关键特征：

- `async def` functions are always coroutines, even if they do not contain `await` expressions.

  所有使用`async def`定义的函数都是协程函数，即使它们没有包含`await`表达式。

- It is a `SyntaxError` to have `yield` or `yield from` expressions in an `async` function.

  在一个`async`函数中使用`yield`或`yield from`表达式会抛出`SyntaxError`异常。

- Internally, two new code object flags were introduced:

  在内部，引入了两个新的代码对象标识：

  - `CO_COROUTINE` is used to mark *native coroutines* (defined with new syntax).

    `CO_COROUTINE`被用于标记原生协程（用新的语句定义）。

  - `CO_ITERABLE_COROUTINE` is used to make *generator-based coroutines* compatible with *native coroutines* (set by [types.coroutine()](https://www.python.org/dev/peps/pep-0492/#types-coroutine) function).

    `CO_ITERABLE_COROUTINE`被用于兼容基于生成器的协程和原生协程（由[types.coroutine()](https://www.python.org/dev/peps/pep-0492/#types-coroutine)函数设置）。

- Regular generators, when called, return a *generator object*; similarly, coroutines return a *coroutine* object.

  对于常规的生成器函数，被当被调用时会返回一个生成器对象;类似的，协程函数也会返回一个协程对象。

- `StopIteration` exceptions are not propagated out of coroutines, and are replaced with a `RuntimeError`. For regular generators such behavior requires a future import (see [PEP 479](https://www.python.org/dev/peps/pep-0479)).

  `StopIteration`异常不会在协程函数中出现，并由`RuntimeError`取代。对于通常的生成器函数，这种行为需要在未来进行考量（见[PEP 479](https://www.python.org/dev/peps/pep-0479)）。

- When a *native coroutine* is garbage collected, a `RuntimeWarning` is raised if it was never awaited on (see also [Debugging Features](https://www.python.org/dev/peps/pep-0492/#debugging-features)).

  当一个原生协程被垃圾回收的时候，如果它从没有处于等待状态，将产生一个`RuntimeWarning`异常（另请参阅[调试功能](https://www.python.org/dev/peps/pep-0492/#debugging-features)）。

- See also [Coroutine objects](https://www.python.org/dev/peps/pep-0492/#coroutine-objects) section.

  另请参见[协程对象](https://www.python.org/dev/peps/pep-0492/#coroutine-objects)部分。

## [types.coroutine()](https://www.python.org/dev/peps/pep-0492/#id55)

A new function `coroutine(fn)` is added to the `types` module. It allows interoperability between existing *generator-based coroutines* in asyncio and *native coroutines* introduced by this PEP:

一个新的函数`coroutine(fn)`被添加到`types`模块中。它允许在此PEP引入的原生协程函数和已存在的基于生成器的协程函数之间进行转换：

```python
@types.coroutine
def process_data(db):
    data = yield from read_data(db)
    ...
```

The function applies `CO_ITERABLE_COROUTINE` flag to generator- function's code object, making it return a *coroutine* object.

该函数将`CO_ITERABLE_COROUTINE`标志应用于生成器函数的代码对象，使其返回一个协程对象。

If `fn` is not a *generator function*, it is wrapped. If it returns a *generator*, it will be wrapped in an *awaitable* proxy object (see below the definition of awaitable objects).

如果`fn`不是一个生成器函数，就会被包装成一个生成器函数。如果它返回一个生成器，它将会被包装成一个可等待的代理对象（请参阅下面的可等待对象定义）。

Note, that the `CO_COROUTINE` flag is not applied by `types.coroutine()` to make it possible to separate *native coroutines* defined with new syntax, from *generator-based coroutines*.

注意，用于将原生协程函数与基于生成器的协程函数进行区分的`CO_COROUTINE`标识并非由`types.coroutine()`提供，而是由新语法所定义。

## [Await Expression](https://www.python.org/dev/peps/pep-0492/#id56)

等待表达式

The following new `await` expression is used to obtain a result of coroutine execution:

以下新的`await`表达式被用于获取协程执行结果：

```python
async def read_data(db):
    data = await db.fetch('SELECT ...')
    ...
```

`await`, similarly to `yield from`, suspends execution of `read_data` coroutine until `db.fetch` *awaitable* completes and returns the result data.

`await`与`yield from`语句类似，它暂停了`read_data`协程的执行，直到可等待对象`db.fetch`执行完毕并返回结果数据。

It uses the `yield from` implementation with an extra step of validating its argument. `await` only accepts an *awaitable*, which can be one of:

它通过使用一个额外的参数验证步骤复用了`yield from`的实现，`await`仅接受一个可等待对象，包括下面的其中之一：

- A *native coroutine* object returned from a *native coroutine function*.

  通过原生协程函数返回的原生协程对象。

- A *generator-based coroutine* object returned from a function decorated with `types.coroutine()`.

  通过使用`types.coroutine()`装饰的函数返回的基于生成器的协程对象。

- An object with an `__await__` method returning an iterator.

  实现了`__await__`方法并返回一个迭代器的对象。

  Any `yield from` chain of calls ends with a `yield`. This is a fundamental mechanism of how *Futures* are implemented. Since, internally, coroutines are a special kind of generators, every `await` is suspended by a `yield` somewhere down the chain of `await` calls (please refer to [PEP 3156](https://www.python.org/dev/peps/pep-3156) for a detailed explanation).

  所有的`yield from`调用链都以`yield`结束，这是一个`Futures`实现的基本机制。在Python内部，协程函数是一种特殊的生成器函数，`wait`调用链上的每一个`await`都是被`yield`暂停（更多详细解释请参考[PEP 3156](https://www.python.org/dev/peps/pep-3156) ）。

  To enable this behavior for coroutines, a new magic method called `__await__` is added. In asyncio, for instance, to enable *Future* objects in `await` statements, the only change is to add `__await__ = __iter__` line to `asyncio.Future` class.

  为了协程函数能实现这种行为，添加了一个新的魔术方法`__await__`。例如，在asyncio中，将`Future`对象用于`await`语句，仅有的修改是添加在`asyncio.Future`类中添加一行代码：`__await__=__iter__`。

  Objects with `__await__` method are called *Future-like* objects in the rest of this PEP.

  在本PEP中接下来的部分，拥有`__await__`方法的对象被称作类Future对象。

  It is a `TypeError` if `__await__` returns anything but an iterator.

  如果`__await__`返回的不是一个迭代器，将产生`TypeError`异常。

- Objects defined with CPython C API with a `tp_as_async.am_await` function, returning an *iterator* (similar to `__await__` method).

  使用带有`tp_as_async.am_await`函数的CPython C API定义的对象，返回一个迭代器（类似于`__await__`方法）。

It is a `SyntaxError` to use `await` outside of an `async def` function (like it is a `SyntaxError` to use `yield` outside of `def` function).

在`async def`定义的函数之外使用`await`将产生`SyntaxError`异常（就像在`def`函数之外使用`yield`会产生`SyntaxError`一样）。

It is a `TypeError` to pass anything other than an *awaitable* object to an `await` expression.

如果将可等待对象之外的数据传给`await`表达式，会产生一个`TypeError`。

### [Updated operator precedence table](https://www.python.org/dev/peps/pep-0492/#id57)

更新的操作符优先表

`await` keyword is defined as follows:

`await`关键字通过下面的方式定义：

```
power ::=  await ["**" u_expr]
await ::=  ["await"] primary
```

where "primary" represents the most tightly bound operations of the language. Its syntax is:

其中“初级”代表语言最紧密的束缚操作。它的语法是：

```
primary ::=  atom | attributeref | subscription | slicing | call
```

See Python Documentation [[12\]](https://www.python.org/dev/peps/pep-0492/#id41) and [Grammar Updates](https://www.python.org/dev/peps/pep-0492/#grammar-updates) section of this proposal for details.

有关详细信息，请参阅本提案的Python文档 [[12\]](https://www.python.org/dev/peps/pep-0492/#id41)和 [Grammar Updates](https://www.python.org/dev/peps/pep-0492/#grammar-updates)部分。

The key `await` difference from `yield` and `yield from` operators is that *await expressions* do not require parentheses around them most of the times.

关键字`await`不同于`yield`和`yield from`操作符的地方在于await表达式大多数时候不需要使用括号包裹。

Also, `yield from` allows any expression as its argument, including expressions like `yield from a() + b()`, that would be parsed as `yield from (a() + b())`, which is almost always a bug. In general, the result of any arithmetic operation is not an *awaitable* object. To avoid this kind of mistakes, it was decided to make `await` precedence lower than `[]`, `()`, and `.`, but higher than `**` operators.

此外，`yield from`允许任何表达式作为参数，包括类似于`yield from a()+b()`这样的表达式，这将被解析为`yield from (a() + b())`，这几乎是一个bug。一般来说，算术运算的结果不会是一个可等待对象。为了避免这种错误，`await`的优先级被定义为低于`[]`、`()`、和`.`，但是高于`**`操作符。

| Operator                                                     | Description                                                  |
| :----------------------------------------------------------- | :----------------------------------------------------------- |
| `yield` `x`, `yield from` `x`                                | Yield expression                                             |
| `lambda`                                                     | Lambda expression                                            |
| `if` -- `else`                                               | Conditional expression                                       |
| `or`                                                         | Boolean OR                                                   |
| `and`                                                        | Boolean AND                                                  |
| `not` `x`                                                    | Boolean NOT                                                  |
| `in`, `not in`, `is`, `is not`, `<`, `<=`, `>`, `>=`, `!=`, `==` | Comparisons, including membership tests and identity tests   |
| `|`                                                          | Bitwise OR                                                   |
| `^`                                                          | Bitwise XOR                                                  |
| `&`                                                          | Bitwise AND                                                  |
| `<<`, `>>`                                                   | Shifts                                                       |
| `+`, `-`                                                     | Addition and subtraction                                     |
| `*`, `@`, `/`, `//`, `%`                                     | Multiplication, matrix multiplication, division, remainder   |
| `+x`, `-x`, `~x`                                             | Positive, negative, bitwise NOT                              |
| `**`                                                         | Exponentiation                                               |
| `await` `x`                                                  | Await expression                                             |
| `x[index]`, `x[index:index]`, `x(arguments...)`, `x.attribute` | Subscription, slicing, call, attribute reference             |
| `(expressions...)`, `[expressions...]`, `{key: value...}`, `{expressions...}` | Binding or tuple display, list display, dictionary display, set display |

### [Examples of "await" expressions](https://www.python.org/dev/peps/pep-0492/#id58)

"await"表达式示例

Valid syntax examples:

合法的语法示例：

| Expression                     | Will be parsed as                 |
| :----------------------------- | :-------------------------------- |
| `if await fut: pass`           | `if (await fut): pass`            |
| `if await fut + 1: pass`       | `if (await fut) + 1: pass`        |
| `pair = await fut, 'spam'`     | `pair = (await fut), 'spam'`      |
| `with await fut, open(): pass` | `with (await fut), open(): pass`  |
| `await foo()['spam'].baz()()`  | `await ( foo()['spam'].baz()() )` |
| `return await coro()`          | `return ( await coro() )`         |
| `res = await coro() ** 2`      | `res = (await coro()) ** 2`       |
| `func(a1=await coro(), a2=0)`  | `func(a1=(await coro()), a2=0)`   |
| `await foo() + await bar()`    | `(await foo()) + (await bar())`   |
| `-await foo()`                 | `-(await foo())`                  |

Invalid syntax examples:

非法的语法示例：

| Expression           | Should be written as   |
| :------------------- | :--------------------- |
| `await await coro()` | `await (await coro())` |
| `await -coro()`      | `await (-coro())`      |

## [Asynchronous Context Managers and "async with"](https://www.python.org/dev/peps/pep-0492/#id59)

异步上下文管理器和"async with"

An *asynchronous context manager* is a context manager that is able to suspend execution in its *enter* and *exit* methods.

一个异步上下文管理器是一个可以在enter和exit函数中将正在执行的代码挂起的上下文管理器。

To make this possible, a new protocol for asynchronous context managers is proposed. Two new magic methods are added: `__aenter__` and `__aexit__`. Both must return an *awaitable*.

为了实现这种可能，一个为异步上下文管理器设计的新协议被提议。添加了两个新的魔术方法：`__aenter__`和`__aexit__`。这两个函数都必须返回一个可等待对象。

一个异步上下文管理器示例：

```python
class AsyncContextManager:
    async def __aenter__(self):
        await log('entering context')

    async def __aexit__(self, exc_type, exc, tb):
        await log('exiting context')
```

### [New Syntax](https://www.python.org/dev/peps/pep-0492/#id60)

新语法

A new statement for asynchronous context managers is proposed:

一个用于异步上下文管理器的新语句被提议：

```python
async with EXPR as VAR:
    BLOCK
```

which is semantically equivalent to:

其语义上等同于：

```python
mgr = (EXPR)
aexit = type(mgr).__aexit__
aenter = type(mgr).__aenter__

VAR = await aenter(mgr)
try:
    BLOCK
except:
    if not await aexit(mgr, *sys.exc_info()):
        raise
else:
    await aexit(mgr, None, None, None)
```

As with regular `with` statements, it is possible to specify multiple context managers in a single `async with` statement.

与常规`with`语句一样，可以使用单个`async with`语句指定多个上下文管理器。

It is an error to pass a regular context manager without `__aenter__` and `__aexit__` methods to `async with`. It is a `SyntaxError` to use `async with` outside of an `async def` function.

将没有实现`__aenter__`和`__aexit__`方法的普通上下文管理器传递给`async with`是一个错误。在`async def`函数之外使用`async with`也会产生一个`SyntaxError`。

### [Example](https://www.python.org/dev/peps/pep-0492/#id61)

示例

With *asynchronous context managers* it is easy to implement proper database transaction managers for coroutines:

使用异步上下文管理器，很容易为协程实现合适的数据库事务管理：

```python
async def commit(session, data):
    ...

    async with session.transaction():
        ...
        await session.update(data)
        ...
```

Code that needs locking also looks lighter:

需要加锁的代码也看起来更简洁：

```python
async with lock:
    ...
```

instead of:

代替：

```python
with (yield from lock):
    ...
```

## [Asynchronous Iterators and "async for"](https://www.python.org/dev/peps/pep-0492/#id62)

异步迭代器和"async for"

An *asynchronous iterable* is able to call asynchronous code in its *iter* implementation, and *asynchronous iterator* can call asynchronous code in its *next* method. To support asynchronous iteration:

 一个异步可迭代对象可以在它的Iter实现中调用异步代码，并且异步迭代器也可以在它的next函数中调用异步代码。为了支持异步迭代：

1. An object must implement an `__aiter__` method (or, if defined with CPython C API, `tp_as_async.am_aiter` slot) returning an *asynchronous iterator object*.

   对象必须实现`__aiter__`方法（或者由使用`tp_as_async.am_aiter`的CPython C API定义）并且返回一个异步迭代器对象。

2. An *asynchronous iterator object* must implement an `__anext__` method (or, if defined with CPython C API, `tp_as_async.am_anext` slot) returning an *awaitable*.

   异步迭代器对象必须实现`__anext__`方法（或者由使用`tp_as_async.am_anext`的CPython C API定义）并返回一个可等待对象。

3. To stop iteration `__anext__` must raise a `StopAsyncIteration` exception.

   为了停止迭代，`__anext__`必须抛出一个`StopAsyncIteration`异常。

An example of asynchronous iterable:

一个异步可迭代对象的示例：

```python
class AsyncIterable:
    def __aiter__(self):
        return self

    async def __anext__(self):
        data = await self.fetch_data()
        if data:
            return data
        else:
            raise StopAsyncIteration

    async def fetch_data(self):
        ...
```

### [New Syntax](https://www.python.org/dev/peps/pep-0492/#id63)

新语法

A new statement for iterating through asynchronous iterators is proposed:

一个用于遍历异步迭代器的新语法被提议：

```python
async for TARGET in ITER:
    BLOCK
else:
    BLOCK2
```

which is semantically equivalent to:

它的等效语法：

```python
iter = (ITER)
iter = type(iter).__aiter__(iter)
running = True
while running:
    try:
        TARGET = await type(iter).__anext__(iter)
    except StopAsyncIteration:
        running = False
    else:
        BLOCK
else:
    BLOCK2
```

It is a `TypeError` to pass a regular iterable without `__aiter__` method to `async for`. It is a `SyntaxError` to use `async for` outside of an `async def` function.

将没有实现`__aiter__`的普通可迭代对象传递给`async for`是一个`TypeError`。在`async def`函数之外使用`async for`是一个`SyntaxError`。

As for with regular `for` statement, `async for` has an optional `else` clause.

和普通的`for`语句一样，`async for`有一个可选的`else`块。

### [Example 1](https://www.python.org/dev/peps/pep-0492/#id64)

示例1

With asynchronous iteration protocol it is possible to asynchronously buffer data during iteration:

使用异步迭代协议，可以在迭代期间异步缓冲数据：

```python
async for data in cursor:
    ...
```

Where `cursor` is an asynchronous iterator that prefetches `N` rows of data from a database after every `N` iterations.

`cursor`是一个异步迭代器，其在每个`N`迭代之后从数据库中预取`N`行。

The following code illustrates new asynchronous iteration protocol:

以下代码说明了新的异步迭代协议：

```python
class Cursor:
    def __init__(self):
        self.buffer = collections.deque()

    async def _prefetch(self):
        ...

    def __aiter__(self):
        return self

    async def __anext__(self):
        if not self.buffer:
            self.buffer = await self._prefetch()
            if not self.buffer:
                raise StopAsyncIteration
        return self.buffer.popleft()
```

then the `Cursor` class can be used as follows:

`Cursor`类可以像下面这样使用：

```python
async for row in Cursor():
    print(row)
```

which would be equivalent to the following code:

其等效语法：

```python
i = Cursor().__aiter__()
while True:
    try:
        row = await i.__anext__()
    except StopAsyncIteration:
        break
    else:
        print(row)
```

### [Example 2](https://www.python.org/dev/peps/pep-0492/#id65)

示例2：

The following is a utility class that transforms a regular iterable to an asynchronous one. While this is not a very useful thing to do, the code illustrates the relationship between regular and asynchronous iterators.

以下是一个实用程序类，它将定期迭代转换为异步。虽然这不是一个非常有用的事情，但代码说明了常规和异步迭代器之间的关系。

```python
class AsyncIteratorWrapper:
    def __init__(self, obj):
        self._it = iter(obj)

    def __aiter__(self):
        return self

    async def __anext__(self):
        try:
            value = next(self._it)
        except StopIteration:
            raise StopAsyncIteration
        return value

async for letter in AsyncIteratorWrapper("abc"):
    print(letter)
```

### [Why StopAsyncIteration?](https://www.python.org/dev/peps/pep-0492/#id66)

Coroutines are still based on generators internally. So, before [PEP 479](https://www.python.org/dev/peps/pep-0479), there was no fundamental difference between

在Python内部，协程函数依然基于生成器函数。所以在[PEP 479](https://www.python.org/dev/peps/pep-0479)之前，下面的两种示例没有本质上的区别

```python
def g1():
    yield from fut
    return 'spam'
```

and

```python
def g2():
    yield from fut
    raise StopIteration('spam')
```

And since [PEP 479](https://www.python.org/dev/peps/pep-0479) is accepted and enabled by default for coroutines, the following example will have its `StopIteration` wrapped into a `RuntimeError`

由于[PEP 479](https://www.python.org/dev/peps/pep-0479)被默认接受并启用了协程函数，因此以下示例将`StopIteration`包裹在`RuntimeError`中

```python
async def a1():
    await fut
    raise StopIteration('spam')
```

The only way to tell the outside code that the iteration has ended is to raise something other than `StopIteration`. Therefore, a new built-in exception class `StopAsyncIteration` was added.

告诉外部代码迭代器已经结束的唯一途径是抛出`StopIteration`之外的东西。因此，添加了一个新的内置异常类`StopAsnciteration`。

Moreover, with semantics from [PEP 479](https://www.python.org/dev/peps/pep-0479), all `StopIteration` exceptions raised in coroutines are wrapped in `RuntimeError`.

此外，通过 [PEP 479](https://www.python.org/dev/peps/pep-0479)的语义，协程函数中抛出的所有`StopIteration`异常都被`RuntimeError`包裹。

## [Coroutine objects](https://www.python.org/dev/peps/pep-0492/#id67)

协程对象

### [Differences from generators](https://www.python.org/dev/peps/pep-0492/#id68)

与生成器函数的区别

This section applies only to *native coroutines* with `CO_COROUTINE` flag, i.e. defined with the new `async def` syntax.

本节仅适用于带有`CO_COROUTINE`标志的原生协程函数，即用新的`async def`语法定义。

**The behavior of existing \*generator-based coroutines\* in asyncio remains unchanged.**

**在asyncio中的\*基于生成器的协程函数\*的已有行为保持不变。**

Great effort has been made to make sure that coroutines and generators are treated as distinct concepts:

已经努力确保协程函数和生成器函数被视为截然不同的概念：

1. *Native coroutine* objects do not implement `__iter__` and `__next__` methods. Therefore, they cannot be iterated over or passed to `iter()`, `list()`, `tuple()` and other built-ins. They also cannot be used in a `for..in` loop.

   原生协程对象不实现`__iter__`和`__next__`方法。因此，他们不能被传递给`iter()`、`list()`、`tuple()`等内建函数，也不能用于`for..in`循环。

   An attempt to use `__iter__` or `__next__` on a *native coroutine* object will result in a `TypeError`.

   试图在一个原生协程对象上使用`__iter__`或`__next__`将产生`TypeError`。

2. *Plain generators* cannot `yield from` *native coroutines*: doing so will result in a `TypeError`.

   普通的生成器函数不能`yiedl from`原生协程函数：这样做会导致`TypeError`。

3. *generator-based coroutines* (for asyncio code must be decorated with `@asyncio.coroutine`) can `yield from` *native coroutine objects*.

   基于生成器的协程函数（对于asyncio代码必须使用`@asyncio.coroutine`进行装饰）可以使用`yield from`关联原生协程对象。

4. `inspect.isgenerator()` and `inspect.isgeneratorfunction()` return `False` for *native coroutine* objects and *native coroutine functions*.

   对原生协程对象和原生协程函数，`inspect.isgenerator()`和`inspect.isgeneratorfunction()`返回`False`。

### [Coroutine object methods](https://www.python.org/dev/peps/pep-0492/#id69)

协程对象函数

Coroutines are based on generators internally, thus they share the implementation. Similarly to generator objects, *coroutines* have `throw()`, `send()` and `close()` methods. `StopIteration` and `GeneratorExit` play the same role for coroutines (although [PEP 479](https://www.python.org/dev/peps/pep-0479) is enabled by default for coroutines). See [PEP 342](https://www.python.org/dev/peps/pep-0342), [PEP 380](https://www.python.org/dev/peps/pep-0380), and Python Documentation [[11\]](https://www.python.org/dev/peps/pep-0492/#id40) for details.

在内部，协程函数基于生成器函数，因此他们共享实现。与生成器对象类似，协程对象具`throw()`，`send()`和`close()`方法。 `StopIteration`和`GeneratorExit`在协程中扮演了相同的角色（尽管在[PEP 479](https://www.python.org/dev/peps/pep-0479)默认为协程启用了）。有关详细信息，请参见 [PEP 342](https://www.python.org/dev/peps/pep-0342)， [PEP 380](https://www.python.org/dev/peps/pep-0380)和Python文档[[11\]](https://www.python.org/dev/peps/pep-0492/#id40)。

`throw()`, `send()` methods for *coroutines* are used to push values and raise errors into *Future-like* objects.

协程中的`throw()`、`send()`方法用于向类期货对象传递数据或抛出异常。

## [Debugging Features](https://www.python.org/dev/peps/pep-0492/#id70)

调试特性

A common beginner mistake is forgetting to use `yield from` on coroutines:

对于协程，通常最先出现的错误是忘记使用`yield from`：

```python
@asyncio.coroutine
def useful():
    asyncio.sleep(1) # this will do nothing without 'yield from'
```

For debugging this kind of mistakes there is a special debug mode in asyncio, in which `@coroutine` decorator wraps all functions with a special object with a destructor logging a warning. Whenever a wrapped generator gets garbage collected, a detailed logging message is generated with information about where exactly the decorator function was defined, stack trace of where it was collected, etc. Wrapper object also provides a convenient `__repr__` function with detailed information about the generator.

为了调试这种错误，在Asyncio中存在特殊调试模式，其中`@coroutine`装饰器用一个特殊对象包裹所有函数，其中析构函数记录警告。每当包装的生成器收集垃圾时，使用有关定义装饰器功能的信息，堆栈迹线的信息生成详细的日志记录消息等。包装器对象还提供了一个方便的`__repr__`函数，其中包含有关生成器的详细信息。

The only problem is how to enable these debug capabilities. Since debug facilities should be a no-op in production mode, `@coroutine` decorator makes the decision of whether to wrap or not to wrap based on an OS environment variable `PYTHONASYNCIODEBUG`. This way it is possible to run asyncio programs with asyncio's own functions instrumented. `EventLoop.set_debug`, a different debug facility, has no impact on `@coroutine` decorator's behavior.

唯一的问题是如何启用这些调试功能。由于调试设施应该是生产模式的NO-OP，因此`@Coroutine`装饰器决定是否基于OS环境变量`PYTHONASYNCIODEBUG`包裹或不包裹。这样就可以使用Asyncio自己的函数来运行Asyncio程序。 `EventLoop.set_debug`，一个不同的调试设施，对`@coroutine`装饰器的行为没有影响。

With this proposal, coroutines is a native, distinct from generators, concept. *In addition* to a `RuntimeWarning` being raised on coroutines that were never awaited, it is proposed to add two new functions to the `sys` module: `set_coroutine_wrapper` and `get_coroutine_wrapper`. This is to enable advanced debugging facilities in asyncio and other frameworks (such as displaying where exactly coroutine was created, and a more detailed stack trace of where it was garbage collected).

通过这一提议，协程是一个原生，不同于生成器的概念。除了在协程从没被等待时抛出`RuntimeWarning`以外，建议向`sys`模块添加两个新函数：`set_coroutine_wrapper`和`get_coroutine_wrapper`。这是为了使Asyncio和其他框架中的高级调试设施（例如在创建协程，以及被垃圾回收时候显示更详细的堆栈跟踪）。

## [New Standard Library Functions](https://www.python.org/dev/peps/pep-0492/#id71)

 新的标准库函数

- `types.coroutine(gen)`. See [types.coroutine()](https://www.python.org/dev/peps/pep-0492/#types-coroutine) section for details.

- `inspect.iscoroutine(obj)` returns `True` if `obj` is a *native coroutine* object.

  `inspect.iscoroutine(obj)` 如果`obj`是一个原生协程对象，返回`True`。

- `inspect.iscoroutinefunction(obj)` returns `True` if `obj` is a *native coroutine function*.

  `inspect.iscoroutinefunction(obj)` 如果`obj`是一个原生协程函数返回`True`。

- `inspect.isawaitable(obj)` returns `True` if `obj` is an *awaitable*.

  `inspect.isawaitable(obj)`如果`obj`是一个可等待对象，返回`True`。

- `inspect.getcoroutinestate(coro)` returns the current state of a *native coroutine object* (mirrors `inspect.getfgeneratorstate(gen)`).

  `inspect.getcoroutinestate(coro)` 返回原生协程对象的当前状态（`inspect.getfgeneratorstate(gen)`的镜像方法）。

- `inspect.getcoroutinelocals(coro)` returns the mapping of a *native coroutine object's* local variables to their values (mirrors `inspect.getgeneratorlocals(gen)`).

  `inspect.getcoroutinelocals(coro)` 返回原生协程对象的局部变量对其值的映射（`inspect.getgeneratorlocals(gen)`的镜像方法）。

- `sys.set_coroutine_wrapper(wrapper)` allows to intercept creation of *native coroutine* objects. `wrapper` must be either a callable that accepts one argument (a *coroutine* object), or `None`. `None` resets the wrapper. If called twice, the new wrapper replaces the previous one. The function is thread-specific. See [Debugging Features](https://www.python.org/dev/peps/pep-0492/#debugging-features) for more details.

  `sys.set_coroutine_wrapper(wrapper)`允许拦截原生协程对象的创建。`wrapper`必须是一个接受一个参数（协程对象）的可调用对象，或者`None`。`None`会重置这个包装器。如果调用两次，新的包装器会取代之前的那一个。这个函数是线程特定的。更多详情参考[Debugging Features](https://www.python.org/dev/peps/pep-0492/#debugging-features) 。

- `sys.get_coroutine_wrapper()` returns the current wrapper object. Returns `None` if no wrapper was set. The function is thread-specific. See [Debugging Features](https://www.python.org/dev/peps/pep-0492/#debugging-features) for more details.

  `sys.get_coroutine_wrapper()`返回当前包装器对象。如果没有设置包装器对象，返回`None`。这个函数是线程特定的。更多详情参考 [Debugging Features](https://www.python.org/dev/peps/pep-0492/#debugging-features)。

## [New Abstract Base Classes](https://www.python.org/dev/peps/pep-0492/#id72)

新的抽象基类

In order to allow better integration with existing frameworks (such as Tornado, see [[13\]](https://www.python.org/dev/peps/pep-0492/#id42)) and compilers (such as Cython, see [[16\]](https://www.python.org/dev/peps/pep-0492/#id45)), two new Abstract Base Classes (ABC) are added:

为了允许与现有框架（例如Tornado，见 [[13\]](https://www.python.org/dev/peps/pep-0492/#id42)）和编译器（如Cython，请参阅 [[16\]](https://www.python.org/dev/peps/pep-0492/#id45)）更好地集成，添加了两个新的抽象基类（ABC）：

- `collections.abc.Awaitable` ABC for *Future-like* classes, that implement `__await__` method.

  `collections.abc.Awaitable`抽象基类用于类期货类，实现了`__await__`方法。

- `collections.abc.Coroutine` ABC for *coroutine* objects, that implement `send(value)`, `throw(type, exc, tb)`, `close()` and `__await__()` methods.

  `collections.abc.Coroutine`抽象基类用于协程对象，实现了`send(value)`、`throw(type,exc,tb)`、`close()`和`__await__()`方法。

Note that generator-based coroutines with `CO_ITERABLE_COROUTINE` flag do not implement `__await__` method, and therefore are not instances of `collections.abc.Coroutine` and `collections.abc.Awaitable` ABCs:

注意，使用`CO_ITERABLE_COROUTINE`标记的基于生成器的协程并没有实现`__await__`方法，所以因此其并不是`collections.abc.Coroutine`和`collections.abc.Awaitable`抽象基类的实例。

```python
@types.coroutine
def gencoro():
    yield

assert not isinstance(gencoro(), collections.abc.Coroutine)

# however:
assert inspect.isawaitable(gencoro())
```

To allow easy testing if objects support asynchronous iteration, two more ABCs are added:

 要允许轻松测试，如果对象支持异步迭代，则添加两个ABC：

- `collections.abc.AsyncIterable` -- tests for `__aiter__` method.
- `collections.abc.AsyncIterator` -- tests for `__aiter__` and `__anext__` methods.

# [Glossary](https://www.python.org/dev/peps/pep-0492/#id73)

词汇表

Native coroutine function

原生协程函数

A coroutine function is declared with `async def`. It uses `await` and `return value`; see [New Coroutine Declaration Syntax](https://www.python.org/dev/peps/pep-0492/#new-coroutine-declaration-syntax) for details.

使用`async def`定义的协程函数。使用`await`和`return value`；更多详情参考 [New Coroutine Declaration Syntax](https://www.python.org/dev/peps/pep-0492/#new-coroutine-declaration-syntax) 。

Native coroutine

原生协程

Returned from a native coroutine function. See [Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression) for details.

从原生协程函数中返回。更多详情参考 [Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression)。

Generator-based coroutine function

基于生成器的协程函数

Coroutines based on generator syntax. Most common example are functions decorated with `@asyncio.coroutine`.

基于生成器语法的协程函数。最常见的示例是由`@asyncio.coroutine`装饰的函数。

Generator-based coroutine

基于生成器的协程

Returned from a generator-based coroutine function.

由基于生成器的协程函数返回。

Coroutine

协程

Either *native coroutine* or *generator-based coroutine*.

包括原生协程和基于生成器的协程。

Coroutine object

协程对象

Either *native coroutine* object or *generator-based coroutine* object.

包括原生协程对象和基于生成器的协程对象。

Future-like object

类期货对象

An object with an `__await__` method, or a C object with `tp_as_async->am_await` function, returning an *iterator*. Can be consumed by an `await` expression in a coroutine. A coroutine waiting for a Future-like object is suspended until the Future-like object's `__await__` completes, and returns the result. See [Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression) for details.

实现了`__await__`方法的对象，或实现了`tp_as_async->am_await`方法的C对象，并通过该方法返回一个迭代器。可以被用于协程中的`await`表达式。协程将会在等待类期货对象的时候暂停，直到类期货对象的`__await__`执行完毕，并返回结果。更多详情见[Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression)。

Awaitable

可等待对象

A *Future-like* object or a *coroutine* object. See [Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression) for details.

类期货对象或协程对象。更多详情见 [Await Expression](https://www.python.org/dev/peps/pep-0492/#await-expression)。

Asynchronous context manager

异步上下文管理器

An asynchronous context manager has `__aenter__` and `__aexit__` methods and can be used with `async with`. See [Asynchronous Context Managers and "async with"](https://www.python.org/dev/peps/pep-0492/#asynchronous-context-managers-and-async-with) for details.

异步上下文管理器拥有`__aenter__`和`__aexit__`方法，并且可以和`async with`一起使用。更多详情见[Asynchronous Context Managers and "async with"](https://www.python.org/dev/peps/pep-0492/#asynchronous-context-managers-and-async-with)。

Asynchronous iterable

异步可迭代对象

An object with an `__aiter__` method, which must return an *asynchronous iterator* object. Can be used with `async for`. See [Asynchronous Iterators and "async for"](https://www.python.org/dev/peps/pep-0492/#asynchronous-iterators-and-async-for) for details.

实现了`__aiter__`的对象，该方法必须返回一个异步迭代器对象。可以和`async for`一起使用。更多信息见[Asynchronous Iterators and "async for"](https://www.python.org/dev/peps/pep-0492/#asynchronous-iterators-and-async-for) 。

Asynchronous iterator

异步迭代器

An asynchronous iterator has an `__anext__` method. See [Asynchronous Iterators and "async for"](https://www.python.org/dev/peps/pep-0492/#asynchronous-iterators-and-async-for) for details.

异步迭代器拥有`__anext__`方法。更多信息见 [Asynchronous Iterators and "async for"](https://www.python.org/dev/peps/pep-0492/#asynchronous-iterators-and-async-for) 。

> - 原文的剩余部分是为了向前兼容或者阐述为何使用这样的关键字命名，对于协程实现没有更多帮助，所以这里就不继续翻译了。
> - 翻译水平有限，请多包涵，有任何纠错或者交流可以在下面留言。
> - 翻译花了大半天，如果支持我的劳动可以在[**我的CSDN**](https://blog.csdn.net/hy6533)关注或点赞。

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。