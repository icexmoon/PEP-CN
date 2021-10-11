# PEP 205 -- Weak References

PEP 205 -- 弱引用

|                 |                                         |
| :-------------- | --------------------------------------- |
| PEP:            | 205                                     |
| Title:          | Weak References                         |
| Author:         | Fred L. Drake, Jr. <fred at fdrake.net> |
| Status:         | Final                                   |
| Type:           | Standards Track                         |
| Created:        | 14-Jul-2000                             |
| Python-Version: | 2.1                                     |
| Post-History:   | 11-Jan-2001                             |

------

Contents

- Motivation
  - [Caches (weak dictionaries)](https://www.python.org/dev/peps/pep-0205/#caches-weak-dictionaries)
  - [Circular references](https://www.python.org/dev/peps/pep-0205/#circular-references)
- Aspects of the Solution Space
  - [Invalidation](https://www.python.org/dev/peps/pep-0205/#invalidation)
  - [Presentation](https://www.python.org/dev/peps/pep-0205/#presentation)
- [Proposed Solution](https://www.python.org/dev/peps/pep-0205/#proposed-solution)
- [Implementation Strategy](https://www.python.org/dev/peps/pep-0205/#implementation-strategy)
- [Possible Applications](https://www.python.org/dev/peps/pep-0205/#possible-applications)
- [Previous Weak Reference Work in Python](https://www.python.org/dev/peps/pep-0205/#previous-weak-reference-work-in-python)
- [Weak References in Java](https://www.python.org/dev/peps/pep-0205/#weak-references-in-java)
- Appendix -- Dianne Hackborn's vref proposal (1995)
  - [Proposal: Virtual References](https://www.python.org/dev/peps/pep-0205/#proposal-virtual-references)
  - [Pointer semantics](https://www.python.org/dev/peps/pep-0205/#pointer-semantics)
  - [Proxy semantics](https://www.python.org/dev/peps/pep-0205/#proxy-semantics)
- [Copyright](https://www.python.org/dev/peps/pep-0205/#copyright)

# [Motivation](https://www.python.org/dev/peps/pep-0205/#id1)

动机

There are two basic applications for weak references which have been noted by Python programmers: object caches and reduction of pain from circular references.

Python程序员已经注意到弱引用的两个基本应用：对象缓存和减少循环引用的痛苦。

## [Caches (weak dictionaries)](https://www.python.org/dev/peps/pep-0205/#id2)

缓存（弱字典）

There is a need to allow objects to be maintained that represent external state, mapping a single instance to the external reality, where allowing multiple instances to be mapped to the same external resource would create unnecessary difficulty maintaining synchronization among instances. In these cases, a common idiom is to support a cache of instances; a factory function is used to return either a new or existing instance.

有一种需求是维持若干个达标外部状态的对象，即将单个实例映射到外部现实，在这种情况下，允许多个实例映射到同一个外部资源会给维护实例间的同步带来不必要的困难。在这些情况下，一个常见的习惯是支持实例的缓存；一个工厂函数被用来返回一个新的或现有的实例。

The difficulty in this approach is that one of two things must be tolerated: either the cache grows without bound, or there needs to be explicit management of the cache elsewhere in the application. The later can be very tedious and leads to more code than is really necessary to solve the problem at hand, and the former can be unacceptable for long-running processes or even relatively short processes with substantial memory requirements.

这种方法的困难在于，必须容忍两种情况之一：要么缓存无限制地增长，要么需要在应用程序的其他地方对缓存进行明确的管理。后者是非常繁琐的，并且会导致更多的代码超出解决手头问题的实际需要，而前者对于长期运行的进程或者甚至是有大量内存需求的相对较短的进程来说，是不可接受的。

- External objects that need to be represented by a single instance, no matter how many internal users there are. This can be useful for representing files that need to be written back to disk in whole rather than locked & modified for every use.

  无论有多少内部用户，都需要用一个实例来表示的外部对象。这对于代表需要整体写回磁盘而不是每次使用都要锁定和修改的文件是很有用的。

- Objects that are expensive to create, but may be needed by multiple internal consumers. Similar to the first case, but not necessarily bound to external resources, and possibly not an issue for shared state. Weak references are only useful in this case if there is some flavor of "soft" references or if there is a high likelihood that users of individual objects will overlap in lifespan.

  创建对象的成本很高，但可能被多个内部消费者所需要。与第一种情况类似，但不一定与外部资源绑定，而且对于共享状态来说可能不是一个问题。在这种情况下，弱引用只有在存在某种 "软 "引用的情况下才有用，或者个别对象的用户很有可能在寿命上有重叠。

## [Circular references](https://www.python.org/dev/peps/pep-0205/#id3)

循环引用

- DOMs require a huge amount of circular (to parent & document nodes) references, but these could be eliminated using a weak dictionary mapping from each node to its parent. This might be especially useful in the context of something like `xml.dom.pulldom`, allowing the `.unlink()` operation to become a no-op.

  DOM需要大量的循环引用（对父节点和文档节点），但这些可以通过使用每个节点对其父节点的弱字典映射来消除。这在像 `xml.dom.pulldom` 这样的情况下可能特别有用，允许 `.unlink()` 操作成为一个无操作。

This proposal is divided into the following sections:

这个建议分为以下几个部分：

- Proposed Solution

  建议的解决方案

- Implementation Strategy

  实施策略

- Possible Applications

  可能的应用

- Previous Weak Reference Work in Python

  先前在 Python 中的弱引用工作

- Weak References in Java

  Java中的弱引用

The full text of one early proposal is included as an appendix since it does not appear to be available on the net.

一个早期提案的全文被列为附录，因为网上似乎没有这个提案。

# [Aspects of the Solution Space](https://www.python.org/dev/peps/pep-0205/#id4)

解空间的各个方面

There are two distinct aspects to the weak references problem:

弱引用问题有两个不同的方面：

- Invalidation of weak references

  弱引用的无效化

- Presentation of weak references to Python code

  对Python代码的弱引用的展示

## [Invalidation](https://www.python.org/dev/peps/pep-0205/#id5)

无效化

Past approaches to weak reference invalidation have often hinged on storing a strong reference and being able to examine all the instances of weak reference objects, and invalidating them when the reference count of their referent goes to one (indicating that the reference stored by the weak reference is the last remaining reference). This has the advantage that the memory management machinery in Python need not change, and that any type can be weakly referenced.

过去的弱引用无效的方法往往依赖于存储一个强引用，并能够检查弱引用对象的所有实例，当其引用对象的引用计数为1时(表明弱引用所存储的引用是最后剩下的引用)，将其无效。这样做的好处是，Python 的内存管理机制不需要改变，而且任何类型都可以被弱引用。

The disadvantage of this approach to invalidation is that it assumes that the management of the weak references is called sufficiently frequently that weakly-referenced objects are noticed within a reasonably short time frame; since this means a scan over some data structure to invalidate references, an operation which is O(N) on the number of weakly referenced objects, this is not effectively amortized for any single object which is weakly referenced. This also assumes that the application is calling into code which handles weakly-referenced objects with some frequency, which makes weak-references less attractive for library code.

这种无效化方法的缺点是，它假定弱引用的管理被足够频繁地调用，以至于弱引用对象在相当短的时间内被注意到；因为这意味着对一些数据结构进行扫描以使引用无效，这种操作对弱引用对象的数量来说是O(N)，这对任何一个被弱引用的对象来说都是无法有效摊薄的。这也假定应用程序调用的代码是以某种频率处理弱引用对象的，这使得弱引用对库代码的吸引力降低。

An alternate approach to invalidation is that the de-allocation code to be aware of the possibility of weak references and make a specific call into the weak-reference management code to all invalidation whenever an object is deallocated. This requires a change in the tp_dealloc handler for weakly-referencable objects; an additional call is needed at the "top" of the handler for objects which support weak-referencing, and an efficient way to map from an object to a chain of weak references for that object is needed as well.

另一种无效化的方法是，去分配代码要意识到弱引用的可能性，并对弱引用管理代码进行特定的调用，以便在对象被去分配时进行全部无效化。这需要改变tp_dealloc处理程序中的弱引用对象；对于支持弱引用的对象，需要在处理程序的 "顶部 "进行额外的调用，同时还需要一种有效的方法来将对象映射到该对象的弱引用链。

## [Presentation](https://www.python.org/dev/peps/pep-0205/#id6)

呈现

Two ways that weak references are presented to the Python layer have been as explicit reference objects upon which some operation is required in order to retrieve a usable reference to the underlying object, and proxy objects which masquerade as the original objects as much as possible.

弱引用呈现给 Python 层的两种方式是：显式引用对象和代理对象，前者需要对其进行一些操作才能检索到底层对象的可用引用，后者则尽可能地伪装成原始对象的样子。

> 这里意味两种不同的呈现效果，译者注。

Reference objects are easy to work with when some additional layer of object management is being added in Python; references can be checked for liveness explicitly, without having to invoke operations on the referents and catching some special exception raised when an invalid weak reference is used.

当在Python中添加一些额外的对象管理层时，引用对象很容易操作；可以显式地检查引用的有效性，而不需要在引用上调用操作，并在使用无效的弱引用时捕捉一些特殊的异常。

However, a number of users favor the proxy approach simply because the weak reference looks so much like the original object.

然而，一些用户喜欢代理的方法，仅仅是因为弱引用看起来很像原始对象。

# [Proposed Solution](https://www.python.org/dev/peps/pep-0205/#id7)

建议的解决方案

Weak references should be able to point to any Python object that may have substantial memory size (directly or indirectly), or hold references to external resources (database connections, open files, etc.).

弱引用应该能够指向任何Python对象，这些对象可能有大量的内存大小(直接或间接)，或者持有对外部资源(数据库连接、打开的文件等)的引用。

A new module, weakref, will contain new functions used to create weak references. `weakref.ref()` will create a "weak reference object" and optionally attach a callback which will be called when the object is about to be finalized. `weakref.mapping()` will create a "weak dictionary". A third function, `weakref.proxy()`, will create a proxy object that behaves somewhat like the original object.

一个新的模块，weakref，将包含用于创建弱引用的新函数。`weakref.ref()`将创建一个 "弱引用对象"，并可选择附加一个回调，当该对象即将被销毁时将被调用。`weakref.mapping()`将创建一个 "弱字典"。第三个函数，`weakref.proxy()`，将创建一个代理对象，其行为有点像原始对象。

A weak reference object will allow access to the referenced object if it hasn't been collected and to determine if the object still exists in memory. Retrieving the referent is done by calling the reference object. If the referent is no longer alive, this will return None instead.

一个弱引用对象将允许访问被引用的对象，如果它还没有被回收，并确定该对象是否仍然存在于内存中。对引用的检索是通过调用引用对象来完成的。如果引用对象不再存在，这将返回 None。

A weak dictionary maps arbitrary keys to values, but does not own a reference to the values. When the values are finalized, the (key, value) pairs for which it is a value are removed from all the mappings containing such pairs. Like dictionaries, weak dictionaries are not hashable.

一个弱字典将任意的键映射到值，但不拥有对值的引用。当值被销毁时，它作为值的（键，值）对被从所有包含这种对的映射中删除。像字典一样，弱字典也是不可哈希的。

Proxy objects are weak references that attempt to behave like the object they proxy, as much as they can. Regardless of the underlying type, proxies are not hashable since their ability to act as a weak reference relies on a fundamental mutability that will cause failures when used as dictionary keys -- even if the proper hash value is computed before the referent dies, the resulting proxy cannot be used as a dictionary key since it cannot be compared once the referent has expired, and comparability is necessary for dictionary keys. Operations on proxy objects after the referent dies cause weakref.ReferenceError to be raised in most cases. "is" comparisons, `type()`, and `id()` will continue to work, but always refer to the proxy and not the referent.

代理对象是弱引用，试图尽可能地表现得像它们所代理的对象。无论底层类型如何，代理对象都是不可哈希的，因为它们作为弱引用的能力依赖于一种基本的可变性，这种可变性在用作字典键时将导致失败--即使在引用者死亡之前计算出适当的哈希值，产生的代理对象也不能用作字典键，因为一旦引用者过期，它就不能被比较，而比较性对于字典键是必要的。在引用者死亡后对代理对象的操作，在大多数情况下会引起 weakref.ReferenceError。"is "比较、`type()`和`id()`将继续工作，但总是引用代理而不是引用者。

> 关于更多的“可哈希”的内容可以阅读[**Python学习笔记20：字典与集合**](https://blog.icexmoon.xyz/archives/93.html)，译者注。

The callbacks registered with weak references must accept a single parameter, which will be the weak reference or proxy object itself. The object cannot be accessed or resurrected in the callback.

用弱引用注册的回调必须接受一个参数，这将是弱引用或代理对象本身。该对象不能在回调中被访问或复活。

# [Implementation Strategy](https://www.python.org/dev/peps/pep-0205/#id8)

实施策略

The implementation of weak references will include a list of reference containers that must be cleared for each weakly- referencable object. If the reference is from a weak dictionary, the dictionary entry is cleared first. Then, any associated callback is called with the object passed as a parameter. Once all callbacks have been called, the object is finalized and deallocated.

弱引用的实现将包括一个必须为每个弱引用对象清除的引用容器的列表。如果引用来自一个弱字典，那么字典条目首先被清除。然后，任何相关的回调被调用，对象被作为一个参数传递。一旦所有的回调被调用，该对象就被销毁和去分配。

Many built-in types will participate in the weak-reference management, and any extension type can elect to do so. The type structure will contain an additional field which provides an offset into the instance structure which contains a list of weak reference structures. If the value of the field is <= 0, the object does not participate. In this case, `weakref.ref()`, `<weakdict>.__setitem__()` and `.setdefault()`, and item assignment will raise `TypeError`. If the value of the field is > 0, a new weak reference can be generated and added to the list.

许多内置类型将参与弱引用管理，任何扩展类型也可以选择这样做。类型结构将包含一个额外的字段，它提供了一个进入实例结构的偏移量，该实例结构包含一个弱引用结构的列表。如果该字段的值<=0，则该对象不参与。在这种情况下，`weakref.ref()`、`<weakdict>.__setitem__()`和`.setdefault()`，以及项目赋值将引发`TypeError`。如果字段的值>0，可以生成一个新的弱引用并添加到列表中。

This approach is taken to allow arbitrary extension types to participate, without taking a memory hit for numbers or other small types.

采取这种方法是为了允许任意的扩展类型参与，而不会对数字或其他小类型的内存造成冲击。

Standard types which support weak references include instances, functions, and bound & unbound methods. With the addition of class types ("new-style classes") in Python 2.2, types grew support for weak references. Instances of class types are weakly referencable if they have a base type which is weakly referencable, the class not specify `__slots__`, or a slot is named `__weakref__`. Generators also support weak references.

支持弱引用的标准类型包括实例、函数、绑定和非绑定方法。在 Python 2.2 中增加了类类型 ("新式类")，类型对弱引用的支持增加了。如果类类型的实例有一个可弱引用的基类型，该类没有指定 `__slots__`，或者一个槽被命名为 `__weakref__`，那么它就是可弱引用的。生成器也支持弱引用。

# [Possible Applications](https://www.python.org/dev/peps/pep-0205/#id9)

可能的应用

PyGTK+ bindings?

PyGTK+ 绑定？

Tkinter -- could avoid circular references by using weak references from widgets to their parents. Objects won't be discarded any sooner in the typical case, but there won't be so much dependence on the programmer calling `.destroy()` before releasing a reference. This would mostly benefit long-running applications.

Tkinter -- 可以通过使用从widget到其父辈的弱引用来避免循环引用。在典型的情况下，对象不会很快被丢弃，但在释放引用之前，程序员就不会那么依赖调用`.destroy()`。这主要有利于长期运行的应用程序。

DOM trees.

DOM树。

# [Previous Weak Reference Work in Python](https://www.python.org/dev/peps/pep-0205/#id10)

Python中以前的弱引用工作

Dianne Hackborn has proposed something called "virtual references". 'vref' objects are very similar to java.lang.ref.WeakReference objects, except there is no equivalent to the invalidation queues. Implementing a "weak dictionary" would be just as difficult as using only weak references (without the invalidation queue) in Java. Information on this has disappeared from the Web, but is included below as an Appendix.

Dianne Hackborn 提出了一个叫做 "虚拟引用 "的东西。vref "对象与 java.lang.ref.WeakReference 对象非常相似，只是没有相当于无效队列的东西。实现一个 "弱字典 "就像在Java中只使用弱引用（没有无效队列）一样困难。这方面的信息已经从网上消失了，但作为附录收录在下面。

Marc-André Lemburg's mx.Proxy package:

Marc-André Lemburg的mx.Proxy包：

> http://www.lemburg.com/files/python/mxProxy.html

The weakdict module by Dieter Maurer is implemented in C and Python. It appears that the Web pages have not been updated since Python 1.5.2a, so I'm not yet sure if the implementation is compatible with Python 2.0.

Dieter Maurer的weakdict模块是用C和Python实现的。似乎网页在 Python 1.5.2a 之后没有更新，所以我还不确定这个实现是否与 Python 2.0 兼容。

> http://www.handshake.de/~dieter/weakdict.html

PyWeakReference by Alex Shindich:

> http://sourceforge.net/projects/pyweakreference/

Eric Tiedemann has a weak dictionary implementation:

> http://www.hyperreal.org/~est/python/weak/

# [Weak References in Java](https://www.python.org/dev/peps/pep-0205/#id11)

Java中的弱引用

http://java.sun.com/j2se/1.3/docs/api/java/lang/ref/package-summary.html

Java provides three forms of weak references, and one interesting helper class. The three forms are called "weak", "soft", and "phantom" references. The relevant classes are defined in the java.lang.ref package.

Java提供了三种形式的弱引用，以及一个有趣的辅助类。这三种形式被称为 "弱"、"软 "和 "幻影 "引用。相关的类被定义在java.lang.ref包中。

For each of the reference types, there is an option to add the reference to a queue when it is invalidated by the memory allocator. The primary purpose of this facility seems to be that it allows larger structures to be composed to incorporate weak-reference semantics without having to impose substantial additional locking requirements. For instance, it would not be difficult to use this facility to create a "weak" hash table which removes keys and referents when a reference is no longer used elsewhere. Using weak references for the objects without some sort of notification queue for invalidations leads to much more tedious implementation of the various operations required on hash tables. This can be a performance bottleneck if deallocations of the stored objects are infrequent.

对于每一种引用类型，都有一个选项，当引用被内存分配器废止时，可以将其添加到一个队列中。这一设施的主要目的似乎是它允许更大的结构被组成，以纳入弱引用语义，而不需要施加大量的额外锁定要求。例如，使用这个工具来创建一个 "弱 "哈希表并不困难，当一个引用不再用于其他地方时，就会删除键和引用。在没有某种无效通知队列的情况下，为对象使用弱引用会导致对哈希表所需的各种操作进行更繁琐的实现。如果存储对象的删除不频繁，这可能是一个性能瓶颈。

Java's "weak" references are most like Dianne Hackborn's old vref proposal: a reference object refers to a single Python object, but does not own a reference to that object. When that object is deallocated, the reference object is invalidated. Users of the reference object can easily determine that the reference has been invalidated, or a NullObjectDereferenceError can be raised when an attempt is made to use the referred-to object.

Java 的 "弱 "引用最像 Dianne Hackborn 的老 vref 建议：一个引用对象指向一个 Python 对象，但并不拥有对该对象的引用。当那个对象被解配时，引用对象就失效了。引用对象的用户可以很容易地确定引用已经失效，或者当试图使用被引用的对象时，可以引发一个 NullObjectDereferenceError。

The "soft" references are similar, but are not invalidated as soon as all other references to the referred-to object have been released. The "soft" reference does own a reference, but allows the memory allocator to free the referent if the memory is needed elsewhere. It is not clear whether this means soft references are released before the `malloc()` implementation calls `sbrk()` or its equivalent, or if soft references are only cleared when `malloc()` returns `NULL`.

"软 "引用是类似的，但是一旦所有其他对被引用对象的引用被释放，就不会被宣告无效。软 "引用确实拥有一个引用，但允许内存分配器在其他地方需要内存时释放引用者。目前还不清楚这是否意味着软引用在malloc()实现调用sbrk()或其等价物之前被释放，或者软引用只在malloc()返回NULL时被清除。

"Phantom" references are a little different; unlike weak and soft references, the referent is not cleared when the reference is added to its queue. When all phantom references for an object are dequeued, the object is cleared. This can be used to keep an object alive until some additional cleanup is performed which needs to happen before the objects `.finalize()` method is called.

"幻影 "引用有点不同；与弱引用和软引用不同，当引用被添加到其队列时，引用者不会被清除。当一个对象的所有幻影引用都被取消后，该对象就被清除了。这可以用来保持一个对象的活力，直到进行一些额外的清理，这需要在调用对象的.finalize()方法之前发生。

Unlike the other two reference types, "phantom" references must be associated with an invalidation queue.

与其他两种引用类型不同，"幻影 "引用必须与一个无效队列相关联。

# [Appendix -- Dianne Hackborn's vref proposal (1995)](https://www.python.org/dev/peps/pep-0205/#id12)

附录--Dianne Hackborn的vref建议（1995）

[This has been indented and paragraphs reflowed, but there have be no content changes. --Fred]

[这篇文章已经缩进，段落也重新排版，但内容没有变化。--Fred]

> 这部分我就不翻译了，译者注。

## [Proposal: Virtual References](https://www.python.org/dev/peps/pep-0205/#id13)

In an attempt to partly address the recurring discussion concerning reference counting vs. garbage collection, I would like to propose an extension to Python which should help in the creation of "well structured" cyclic graphs. In particular, it should allow at least trees with parent back-pointers and doubly-linked lists to be created without worry about cycles.

The basic mechanism I'd like to propose is that of a "virtual reference," or a "vref" from here on out. A vref is essentially a handle on an object that does not increment the object's reference count. This means that holding a vref on an object will not keep the object from being destroyed. This would allow the Python programmer, for example, to create the aforementioned tree structure tree structure, which is automatically destroyed when it is no longer in use -- by making all of the parent back-references into vrefs, they no longer create reference cycles which keep the tree from being destroyed.

In order to implement this mechanism, the Python core must ensure that no -real- pointers are ever left referencing objects that no longer exist. The implementation I would like to propose involves two basic additions to the current Python system:

1. A new "vref" type, through which the Python programmer creates and manipulates virtual references. Internally, it is basically a C-level Python object with a pointer to the Python object it is a reference to. Unlike all other Python code, however, it does not change the reference count of this object. In addition, it includes two pointers to implement a doubly-linked list, which is used below.
2. The addition of a new field to the basic Python object [`PyObject_Head` in object.h], which is either `NULL`, or points to the head of a list of all vref objects that reference it. When a vref object attaches itself to another object, it adds itself to this linked list. Then, if an object with any vrefs on it is deallocated, it may walk this list and ensure that all of the vrefs on it point to some safe value, e.g. Nothing.

This implementation should hopefully have a minimal impact on the current Python core -- when no vrefs exist, it should only add one pointer to all objects, and a check for a `NULL` pointer every time an object is deallocated.

Back at the Python language level, I have considered two possible semantics for the vref object --

## [Pointer semantics](https://www.python.org/dev/peps/pep-0205/#id14)

In this model, a vref behaves essentially like a Python-level pointer; the Python program must explicitly dereference the vref to manipulate the actual object it references.

An example vref module using this model could include the function "new"; When used as 'MyVref = vref.new(MyObject)', it returns a new vref object such that `MyVref.object == MyObject`. `MyVref.object` would then change to Nothing if `MyObject` is ever deallocated.

For a concrete example, we may introduce some new C-style syntax:

- `&` -- unary operator, creates a vref on an object, same as `vref.new()`.
- `*` -- unary operator, dereference a vref, same as `VrefObject.object`.

We can then define:

```
1.     type(&MyObject) == vref.VrefType
2.        *(&MyObject) == MyObject
3. (*(&MyObject)).attr == MyObject.attr
4.          &&MyObject == Nothing
5.           *MyObject -> exception
```

Rule #4 is subtle, but comes about because we have made a vref to (a vref with no real references). Thus the outer vref is cleared to Nothing when the inner one inevitably disappears.

## [Proxy semantics](https://www.python.org/dev/peps/pep-0205/#id15)

In this model, the Python programmer manipulates vref objects just as if she were manipulating the object it is a reference of. This is accomplished by implementing the vref so that all operations on it are redirected to its referenced object. With this model, the dereference operator (*) no longer makes sense; instead, we have only the reference operator (&), and define:

```
1.  type(&MyObject) == type(MyObject)
2.        &MyObject == MyObject
3. (&MyObject).attr == MyObject.attr
4.       &&MyObject == MyObject
```

Again, rule #4 is important -- here, the outer vref is in fact a reference to the original object, and -not- the inner vref. This is because all operations applied to a vref actually apply to its object, so that creating a vref of a vref actually results in creating a vref of the latter's object.

The first, pointer semantics, has the advantage that it would be very easy to implement; the vref type is extremely simple, requiring at minimum a single attribute, object, and a function to create a reference.

However, I really like the proxy semantics. Not only does it put less of a burden on the Python programmer, but it allows you to do nice things like use a vref anywhere you would use the actual object. Unfortunately, it would probably an extreme pain, if not practically impossible, to implement in the current Python implementation. I do have some thoughts, though, on how to do this, if it seems interesting; one possibility is to introduce new type-checking functions which handle the vref. This would hopefully older C modules which don't expect vrefs to simply return a type error, until they can be fixed.

Finally, there are some other additional capabilities that this system could provide. One that seems particularly interesting to me involves allowing the Python programmer to add "destructor" function to a vref -- this Python function would be called immediately prior to the referenced object being deallocated, allowing a Python program to invisibly attach itself to another object and watch for it to disappear. This seems neat, though I haven't actually come up with any practical uses for it, yet... :)

-- Dianne

# [Copyright](https://www.python.org/dev/peps/pep-0205/#id16)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0205.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。