# PEP 7 -- c 代码风格指导

> 原文地址：https://www.python.org/dev/peps/pep-0007/

| PEP:              | 7                                                            |
| ----------------- | ------------------------------------------------------------ |
| **Title:**        | C 代码风格指导                                               |
| **Author:**       | Guido van Rossum <guido at python.org>, Barry Warsaw <barry at python.org> |
| **Status:**       | 活跃                                                         |
| **Type:**         | 进行中                                                       |
| **Created:**      | 05-Jul-2001                                                  |
| **Post-History:** |                                                              |

---

内容

- 介绍

- c 方言

- 代码布局

- 命名约定

- 文档字符串


### *Introduction*

介绍

*This document gives coding conventions for the C code comprising the C implementation of Python. Please see the companion informational PEP describing style guidelines for Python code [[1\]](https://www.python.org/dev/peps/pep-0007/#id2).*

本文档给出了 Python 的 C 实现代码编码约定(CPython) , 请参阅描述 Python 代码风格指南的配套信息 PEP 1

*Note, rules are there to be broken. Two good reasons to break a particular rule:*

请注意，规则是可以打破的。打破特定规则的两个充分理由：

1. *When applying the rule would make the code less readable, even for someone who is used to reading code that follows the rules.*

   应用规则会降低代码的可读性，即使对于已经熟悉了遵循这些代码规则的人也是如此。

2. *To be consistent with surrounding code that also breaks it (maybe for historic reasons) -- although this is also an opportunity to clean up someone else's mess (in true XP style).*

   会破坏它的周围代码保持一致（可能出于历史原因）——尽管这也是清理其他人的烂摊子的机会（以真正的 XP 风格）。

   

### *C dialect*

C方言

- *Python versions before 3.6 use ANSI/ISO standard C (the 1989 version of the standard). This means (amongst many other things) that all declarations must be at the top of a block (not necessarily at the top of function).*

  Python 3.6 之前的版本使用 `ANSI/ISO C`( C89标准) . 这意味着所有声明（除特殊情况）都必须位于代码块的顶部（不一定位于函数的顶部）

- *Python versions greater than or equal to 3.6 use C89 with several select C99 features:*

  Python 3.6 及之后的版本使用了C89 并选用了以下几个C99特性：

  - *Standard integer types in `<stdint.h>` and `<inttypes.h>`. We require the fixed width integer types.*

    `<stdint.h>`和`<inttypes.h> ` 中的标准整数类型。我们需要固定宽度的整数类型。

  - *`static inline` functions*

    `静态内联 / static inline`函数

  - *designated initializers (especially nice for type declarations)*

    指定初始值设定项（特别适用于类型声明）

  - *intermingled declarations*

    混合声明

  - *booleans*

    布尔值

  - *C++-style line comments*

    C++ 风格的行注释( `//` )

  *Future C99 features may be added to this list in the future depending on compiler support (mostly significantly MSVC).*

  未来的 C99 特性可能会在将来添加到此列表中，具体取决于编译器支持（主要是 `MSVC`）

- *Don't use GCC extensions (e.g. don't write multi-line strings without trailing backslashes).*

  不要使用 GCC 扩展（例如不要写没有跟反斜杠的多行字符串）。

- *All function declarations and definitions must use full prototypes (i.e. specify the types of all arguments).*

  所有函数声明和定义必须使用完整的原型（即指定所有参数的类型）

- *Only use C++ style // one-line comments in Python 3.6 or later.*

  仅在 Python 3.6 或更高版本中使用 C++ 样式 // 单行注释。

- *No compiler warnings with major compilers (gcc, VC++, a few others).*

  主要编译器（gcc、VC++、其它）没有编译器警告。
  
  

### *Code lay-out*

代码布局

- *Use 4-space indents and no tabs at all.*

  使用 4 个空格缩进，任何时候都不要使用制表符。

- *No line should be longer than 79 characters. If this and the previous rule together don't give you enough room to code, your code is too complicated -- consider using subroutines.*

  任何行都不应该超过 79 个字符。如果这条规则和之前的规则一起让你没有足够的编码空间，那么你的代码就太复杂了——考虑使用子例程（这里的子例程我认为是将部分功能包装成新的函数）。

- *Function definition style: function name in column 1, outermost curly braces in column 1, blank line after local variable declarations.*

  函数定义样式：函数名占一行，最外面的大括号占一行（`{`、 `}`都是单独占一行 ），局部变量声明后有一行空行。

  >  (译者备注：返回值类型单独占一行，所有的代码都是这样做的)

  ```C
  static int
  extra_ivars(PyTypeObject *type, PyTypeObject *base)
  {
      int t_size = PyType_BASICSIZE(type);
      int b_size = PyType_BASICSIZE(base);
  
      assert(t_size >= b_size); /* type smaller than base! */
      ...
      return 1;
  }
  ```

- *Code structure: one space between keywords like `if`, `for` and the following left paren; no spaces inside the paren; braces are required everywhere, even where C permits them to be omitted, but do not add them to code you are not otherwise modifying. All new C code requires braces. Braces should be formatted as shown:*

  代码结构：关键字诸如`if`、`for`和后面的左括号之间要有一个空格；括号内没有空格；大括号在任何地方都是必需的，即使 C 允许省略它们，大括号的格式应如下所示：

  ```C
  if (mro != NULL) { 
      ... 
  } 
  else { 
      ... 
  }
  ```

  >  (译者备注：函数定义的两个大括号各自单独占一行，这与`if`, `for` 后的大扩号是不同的)

- The return statement should *not* get redundant parentheses:

  `return` 语句的返回值不应该加多余的小括号：

  ```C
  return albatross; /* 正确 */
  return (albatross); /* 错误 */
  ```

- *Function and macro call style: `foo(a, b, c)` -- no space before the open paren, no spaces inside the parens, no spaces before commas, one space after each comma.*

  函数调用和宏调用样式：`foo(a, b, c)` -- 左括号前没有空格，括号内没有空格，逗号前没有空格，每个逗号后一个空格。

- *Always put spaces around assignment, Boolean and comparison operators. In expressions using a lot of operators, add spaces around the outermost (lowest-priority) operators.*

  始终在赋值、布尔和比较运算符周围放置空格。在使用大量运算符的表达式中，在最外层（最低优先级）运算符周围添加空格。

- *Breaking long lines: if you can, break after commas in the outermost argument list. Always indent continuation lines appropriately, e.g.:*

  长语句分行：如果可以，在最外面的参数列表中的逗号之后分行。始终适当地缩进连续行，例如：

  ```C
  PyErr_Format(PyExc_TypeError, 
               "cannot create '%.100s' instances", 
               type->tp_name);
  ```

- *When you break a long expression at a binary operator, the operator goes at the end of the previous line, and braces should be formatted as shown. E.g.:*

  当您在长表达式的二元运算符处分行时，运算符应放到上一行的末尾，并且大括号的格式应下：

  ```C
  if (type->tp_dictoffset != 0 && base->tp_dictoffset == 0 && 
      type->tp_dictoffset == b_size && 
      (size_t)t_size == b_size + sizeof(PyObject *)) 
  { 
      return 0; /* 只添加一个 __dict__ */ 
  }
  ```

  >  (译者备注：一般`if`, `for` 后的开始大扩号是不单独占一行的，而这里长表达式后的大括号是单独占一行的)

- *Put blank lines around functions, structure definitions, and major sections inside functions.*

  在函数、结构体定义和函数内部的主要部分周围放置空行（合理换行）。

- *Comments go before the code they describe.*

  注释放在描述的代码之前。

  > （译者备注：注释放在代码之前或者放在同一行 ，python3.6及之后的 CPython 支持 `//` 行内注释）

- *All functions and global variables should be declared static unless they are to be part of a published interface*

  所有内部函数和全局变量都应声明为静态 `static`，除非它们是已发布接口的一部分

- *For external functions and variables, we always have a declaration in an appropriate header file in the "Include" directory, which uses the `PyAPI_FUNC()` macro, like this:*

  对于外部函数和变量，我们总是在`“Include”`目录下的适当头文件中有一个声明，它使用`PyAPI_FUNC()`宏，如下所示：

```C
PyAPI_FUNC(PyObject *) PyObject_Repr(PyObject *);
```

> （以下为译者备注：CPython 中，所有的函数都有两套实现，一套称为内部函数，函数名为小写命名，一套称为外部函数，函数名为大写命名；它们的区别是内部函数是提供给用户使用， 接收的都是`*PyObject`, 也就是纯Python对象，而外部函数是提供给CPython的接口调用，可以接收C类型）
>
> 如下，`list` 在 CPython 的`insert`实现有两套：
>
> ```C
> // 外部函数，接收的位置(where) 与返回值都是C类型
> int
> PyList_Insert(PyObject *op, Py_ssize_t where, PyObject *newitem)
> {
>     if (!PyList_Check(op)) {
>         PyErr_BadInternalCall();
>         return -1;
>     }
>     return ins1((PyListObject *)op, where, newitem);
> }
> 
> // 内部函数，这个才是真正的 list.insert 
> static PyObject *
> listinsert(PyListObject *self, PyObject *args)
> {
>     Py_ssize_t i;
>     PyObject *v;
>     if (!PyArg_ParseTuple(args, "nO:insert", &i, &v))
>         return NULL;
>     if (ins1(self, i, v) == 0)
>         Py_RETURN_NONE;
>     return NULL;
> }
> ```



### *Naming conventions*

命名约定

- *Use a `Py` prefix for public functions; never for static functions. The `Py_` prefix is reserved for global service routines like `Py_FatalError`; specific groups of routines (e.g. specific object type APIs) use a longer prefix, e.g. `PyString_` for string functions.*

  对公共/外部函数使用`Py_`前缀；并且从不用于静态函数（备注）。如`Py_FatalError` ;  特定的函数组（例如特定的对象类型 API）使用更长的前缀，例如对于字符串函数使用`PyString_`前缀。

  > （译者备注：在 c/c++ 中， static 修饰的函数/变量只能文件内使用， 可以将简单的将静态函数理解为私有函数，`Py_`前缀提供的是公共部分的功能，显然不能添加 static）

- *Public functions and variables use MixedCase with underscores, like this: `PyObject_GetAttr`, `Py_BuildValue`, `PyExc_TypeError`.*

  公共函数和变量使用带下划线的 MixedCase，例如：`PyObject_GetAttr`、`Py_BuildValue`、`PyExc_TypeError`。

- *Occasionally an "internal" function has to be visible to the loader; we use the `_Py` prefix for this, e.g.: `_PyObject_Dump`.*

  有时，加载程序必须看到“内部”函数；我们为此使用`_Py`前缀，例如：`_PyObject_Dump`。

- *Macros should have a MixedCase prefix and then use upper case, for example: `PyString_AS_STRING`, `Py_PRINT_RAW`.*

  宏应该有一个混合词的前缀，然后用大写，例如：`PyString_AS_STRING`，`Py_PRINT_RAW`。

### Documentation Strings

字符串文档

- *Use a `Py` prefix for public functions; never for static functions. The `Py_` prefix is reserved for global service routines like `Py_FatalError`; specific groups of routines (e.g. specific object type APIs) use a longer prefix, e.g. `PyString_` for string functions.*

  将`PyDoc_STR()`或`PyDoc_STRVAR()`宏用于字符串文档以支持构建没有字符串文档的 Python ( `./configure --without-doc-strings` )。

  *For C code that needs to support versions of Python older than 2.3, you can include this after including `Python.h`:*

  如果需要支持Python2.3 之前的 C 代码，您可以在包含`Python.h`之后包含再包含这些：

  ```C
  #ifndef PyDoc_STR 
  #define PyDoc_VAR(name) static char name[] 
  #define PyDoc_STR(str) (str) 
  #define PyDoc_STRVAR(name, str) PyDoc_VAR(name) = PyDoc_STR(str) 
  #endif
  ```

- *The first line of each function docstring should be a "signature line" that gives a brief synopsis of the arguments and return value. For example:*

  每个函数的字符串文档的第一行应该是一个“签名行”，它给出了参数和返回值的简要概要。例如：

  ```C
  PyDoc_STRVAR(myfunction__doc__,
  "myfunction(name, value) -> bool\n\n\
  Determine whether name and value make a valid pair.");
  ```

  > (译者备注：这里 `\n\n\` 前两个 `\n` 分别表示换行符，最后的 `\` 表示分隔的两个字符串是连续的)

  *Always include a blank line between the signature line and the text of the description.*

  始终在签名行和描述文本之间包含一个空行。

  *If the return value for the function is always None (because there is no meaningful return value), do not include the indication of the return type.*

  如果函数的返回值为 None（因为没有有意义的返回值），则不包括返回类型的指示。

- *When writing multi-line docstrings, be sure to always use backslash continuations, as in the example above, or string literal concatenation:*

  在编写多行字符串文档时，请确保始终使用反斜杠延续，如上例所示，或字符串文字连接：

  ```C
  PyDoc_STRVAR(myfunction__doc__,
  "myfunction(name, value) -> bool\n\n"
  "Determine whether name and value make a valid pair.");
  ```

- *Though some C compilers accept string literals without either:  not all do; the MSVC compiler is known to complain about this.*

- 尽管一些 C 编译器接受字符串文字, 但并非所有都是这样，`MSVC` 编译器就不是很支持这样。

  ```C
  /* 错误的 -- don't do this! */
  PyDoc_STRVAR(myfunction__doc__,
  "myfunction(name, value) -> bool\n\n
  Determine whether name and value make a valid pair.");
  ```


# References

| [[1\]](https://www.python.org/dev/peps/pep-0007/#id1) | [PEP 8](https://www.python.org/dev/peps/pep-0008), "Style Guide for Python Code", van Rossum, Warsaw (http://www.python.org/dev/peps/pep-0008) |
| ----------------------------------------------------- | ------------------------------------------------------------ |

# Copyright

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0007.txt

> 本翻译基于Google机翻加本人修改审阅完成。

版权声明：本文由 [**yy-Sun**](https://github.com/yy-Sun) 翻译，遵循CC 4.0 BY-SA版权协议。

