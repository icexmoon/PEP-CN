# PEP 282 -- A Logging System

PEP 282 -- 一个日志系统

|                 |                                                              |
| :-------------- | ------------------------------------------------------------ |
| PEP:            | 282                                                          |
| Title:          | A Logging System                                             |
| Author:         | vinay_sajip at red-dove.com (Vinay Sajip), trentm at activestate.com (Trent Mick) |
| Status:         | Final                                                        |
| Type:           | Standards Track                                              |
| Created:        | 04-Feb-2002                                                  |
| Python-Version: | 2.3                                                          |
| Post-History:   |                                                              |

------

Contents

- [Abstract](https://www.python.org/dev/peps/pep-0282/#abstract)
- [Motivation](https://www.python.org/dev/peps/pep-0282/#motivation)
- [Influences](https://www.python.org/dev/peps/pep-0282/#influences)
- [Simple Example](https://www.python.org/dev/peps/pep-0282/#simple-example)
- [Control Flow](https://www.python.org/dev/peps/pep-0282/#control-flow)
- [Levels](https://www.python.org/dev/peps/pep-0282/#levels)
- [Loggers](https://www.python.org/dev/peps/pep-0282/#loggers)
- [Handlers](https://www.python.org/dev/peps/pep-0282/#handlers)
- [LogRecords](https://www.python.org/dev/peps/pep-0282/#logrecords)
- [Formatters](https://www.python.org/dev/peps/pep-0282/#formatters)
- [Filters](https://www.python.org/dev/peps/pep-0282/#filters)
- [Configuration](https://www.python.org/dev/peps/pep-0282/#configuration)
- [Thread Safety](https://www.python.org/dev/peps/pep-0282/#thread-safety)
- [Module-Level Functions](https://www.python.org/dev/peps/pep-0282/#module-level-functions)
- [Implementation](https://www.python.org/dev/peps/pep-0282/#implementation)
- [Packaging](https://www.python.org/dev/peps/pep-0282/#packaging)
- [References](https://www.python.org/dev/peps/pep-0282/#references)
- [Copyright](https://www.python.org/dev/peps/pep-0282/#copyright)

# [Abstract](https://www.python.org/dev/peps/pep-0282/#id17)

概述

This PEP describes a proposed logging package for Python's standard library.

本PEP描述了一个为Python标准库提议的日志包。

Basically the system involves the user creating one or more logger objects on which methods are called to log debugging notes, general information, warnings, errors etc. Different logging 'levels' can be used to distinguish important messages from less important ones.

基本上，该系统涉及到用户创建一个或多个日志对象，在这些对象上调用方法来记录调试说明、一般信息、警告、错误等。不同的日志 "级别 "可以用来区分重要的信息和不重要的信息。

A registry of named singleton logger objects is maintained so that

一个命名的单例日志记录器对象的注册表被维护，以便

1. different logical logging streams (or 'channels') exist (say, one for 'zope.zodb' stuff and another for 'mywebsite'-specific stuff)

   存在不同的逻辑日志流（或 "通道"）（例如，一个用于 "zope.zodb "的东西，另一个用于 "mywebsite "的特定东西）。

2. one does not have to pass logger object references around.

   我们不需要到处传递日志对象的引用。

The system is configurable at runtime. This configuration mechanism allows one to tune the level and type of logging done while not touching the application itself.

该系统在运行时是可配置的。这种配置机制允许人们在不触及应用程序本身的情况下调整日志的级别和类型。

# [Motivation](https://www.python.org/dev/peps/pep-0282/#id18)

动机

If a single logging mechanism is enshrined in the standard library, 1) logging is more likely to be done 'well', and 2) multiple libraries will be able to be integrated into larger applications which can be logged reasonably coherently.

如果一个单一的日志机制被载入标准库中，1）日志更有可能被做得很好，2）多个库将能够被集成到更大的应用程序中，从而可以合理地进行日志记录。

# [Influences](https://www.python.org/dev/peps/pep-0282/#id19)

影响因素

This proposal was put together after having studied the following logging packages:

这个建议是在研究了以下日志包之后提出来的：

- java.util.logging in JDK 1.4 (a.k.a. JSR047) [[1\]](https://www.python.org/dev/peps/pep-0282/#id11)
- log4j [[2\]](https://www.python.org/dev/peps/pep-0282/#id12)
- the Syslog package from the Protomatter project [[3\]](https://www.python.org/dev/peps/pep-0282/#id13)
- MAL's mx.Log package [[4\]](https://www.python.org/dev/peps/pep-0282/#id14)

# [Simple Example](https://www.python.org/dev/peps/pep-0282/#id20)

简单例子

This shows a very simple example of how the logging package can be used to generate simple logging output on stderr.

这显示了一个非常简单的例子，说明如何使用logging包在stderr上生成简单的日志输出。

```python
--------- mymodule.py -------------------------------
import logging
log = logging.getLogger("MyModule")

def doIt():
        log.debug("Doin' stuff...")
        #do stuff...
        raise TypeError, "Bogus type error for testing"
-----------------------------------------------------

--------- myapp.py ----------------------------------
import mymodule, logging

logging.basicConfig()

log = logging.getLogger("MyApp")

log.info("Starting my app")
try:
        mymodule.doIt()
except Exception, e:
        log.exception("There was a problem.")
log.info("Ending my app")
-----------------------------------------------------

% python myapp.py

INFO:MyApp: Starting my app
DEBUG:MyModule: Doin' stuff...
ERROR:MyApp: There was a problem.
Traceback (most recent call last):
        File "myapp.py", line 9, in ?
                mymodule.doIt()
        File "mymodule.py", line 7, in doIt
                raise TypeError, "Bogus type error for testing"
TypeError: Bogus type error for testing

INFO:MyApp: Ending my app
```

The above example shows the default output format. All aspects of the output format should be configurable, so that you could have output formatted like this:

上面的例子显示了默认的输出格式。输出格式的所有方面都应该是可配置的，所以你可以有这样的输出格式。

```python
2002-04-19 07:56:58,174 MyModule   DEBUG - Doin' stuff...

or just

Doin' stuff...
```

# [Control Flow](https://www.python.org/dev/peps/pep-0282/#id21)

控制流程

Applications make logging calls on **Logger** objects. Loggers are organized in a hierarchical namespace and child Loggers inherit some logging properties from their parents in the namespace.

应用程序对**记录仪**对象进行日志调用。记录仪被组织在一个分层的命名空间中，子记录仪从命名空间中的父记录仪继承一些记录属性。

Logger names fit into a "dotted name" namespace, with dots (periods) indicating sub-namespaces. The namespace of logger objects therefore corresponds to a single tree data structure.

记录仪名称适合于 "点状名称 "的命名空间，点（句号）表示子命名空间。因此，记录仪对象的命名空间对应于一个单一的树状数据结构。

```python
"" is the root of the namespace
"Zope" would be a child node of the root
"Zope.ZODB" would be a child node of "Zope"
```

These Logger objects create **LogRecord** objects which are passed to **Handler** objects for output. Both Loggers and Handlers may use logging **levels** and (optionally) **Filters** to decide if they are interested in a particular LogRecord. When it is necessary to output a LogRecord externally, a Handler can (optionally) use a **Formatter** to localize and format the message before sending it to an I/O stream.

这些记录器对象创建**LogRecord**对象，这些对象被传递给**处理程序**对象进行输出。记录器和处理程序都可以使用记录**级别**和（可选）**过滤器**来决定它们是否对某个特定的日志记录感兴趣。当需要向外部输出一条日志记录时，处理程序可以（可选）使用**格式化器**，在将消息发送到I/O流之前对其进行本地化和格式化。

Each Logger keeps track of a set of output Handlers. By default all Loggers also send their output to all Handlers of their ancestor Loggers. Loggers may, however, also be configured to ignore Handlers higher up the tree.

每个记录仪都会跟踪一组输出处理程序。默认情况下，所有的记录仪也将其输出发送到其祖先记录仪的所有处理程序。然而，记录器也可以被配置为忽略树上的处理程序。

The APIs are structured so that calls on the Logger APIs can be cheap when logging is disabled. If logging is disabled for a given log level, then the Logger can make a cheap comparison test and return. If logging is enabled for a given log level, the Logger is still careful to minimize costs before passing the LogRecord into the Handlers. In particular, localization and formatting (which are relatively expensive) are deferred until the Handler requests them.

API的结构是这样的：当记录被禁用时，对记录仪API的调用可以是廉价的。如果对某个给定的日志级别禁用了日志记录，那么记录器可以进行廉价的比较测试并返回。如果为给定的日志级别启用了日志记录，那么在将日志记录传递到处理程序之前，记录器仍然会小心翼翼地将成本降到最低。特别是，本地化和格式化（相对昂贵）被推迟到处理程序请求它们。

The overall Logger hierarchy can also have a level associated with it, which takes precedence over the levels of individual Loggers. This is done through a module-level function:

整个记录器层次结构也可以有一个与之相关的级别，它优先于单个记录器的级别。这是通过一个模块级函数完成的。

```python
def disable(lvl):
    """
    Do not generate any LogRecords for requests with a severity less
    than 'lvl'.
    """
    ...
```

# [Levels](https://www.python.org/dev/peps/pep-0282/#id22)

级别

The logging levels, in increasing order of importance, are:

日志级别，按重要性递增的顺序，是：

```python
DEBUG
INFO
WARN
ERROR
CRITICAL
```

The term CRITICAL is used in preference to FATAL, which is used by log4j. The levels are conceptually the same - that of a serious, or very serious, error. However, FATAL implies death, which in Python implies a raised and uncaught exception, traceback, and exit. Since the logging module does not enforce such an outcome from a FATAL-level log entry, it makes sense to use CRITICAL in preference to FATAL.

术语CRITICAL是用来代替FATAL的，后者被log4j使用。这两个级别在概念上是相同的--即严重的或非常严重的错误。然而，FATAL意味着死亡，这在Python中意味着引发和未捕获的异常、跟踪和退出。由于日志模块并不强制要求FATAL级别的日志条目产生这样的结果，所以使用CRITICAL比FATAL更有意义。

These are just integer constants, to allow simple comparison of importance. Experience has shown that too many levels can be confusing, as they lead to subjective interpretation of which level should be applied to any particular log request.

这些只是整数常数，允许简单地比较重要性。经验表明，太多的级别可能会引起混淆，因为它们会导致主观地解释哪个级别应该应用于任何特定的日志请求。

Although the above levels are strongly recommended, the logging system should not be prescriptive. Users may define their own levels, as well as the textual representation of any levels. User defined levels must, however, obey the constraints that they are all positive integers and that they increase in order of increasing severity.

尽管上述级别是强烈推荐的，但是日志系统不应该是规定性的。用户可以定义自己的级别，以及任何级别的文本表示。然而，用户定义的级别必须遵守这样的约束，即它们都是正整数，而且它们的严重程度依次递增。

User-defined logging levels are supported through two module-level functions:

用户定义的日志级别是通过两个模块级函数支持的：

```python
def getLevelName(lvl):
        """Return the text for level 'lvl'."""
        ...

def addLevelName(lvl, lvlName):
        """
        Add the level 'lvl' with associated text 'levelName', or
        set the textual representation of existing level 'lvl' to be
        'lvlName'."""
        ...
```

# [Loggers](https://www.python.org/dev/peps/pep-0282/#id23)

记录器

Each Logger object keeps track of a log level (or threshold) that it is interested in, and discards log requests below that level.

每个记录器对象都会跟踪它感兴趣的日志级别（或阈值），并丢弃低于该级别的日志请求。

A **Manager** class instance maintains the hierarchical namespace of named Logger objects. Generations are denoted with dot-separated names: Logger "foo" is the parent of Loggers "foo.bar" and "foo.baz".

一个**Manager**类实例维护着命名的记录器对象的分层命名空间。世代用点分隔的名字来表示。记录仪 "foo "是记录仪 "foo.bar "和 "foo.baz "的父对象。

The Manager class instance is a singleton and is not directly exposed to users, who interact with it using various module-level functions.

Manager类实例是一个单例，不直接暴露给用户，用户使用各种模块级函数与之交互。

The general logging method is:

一般的日志记录方法是：

```python
class Logger:
    def log(self, lvl, msg, *args, **kwargs):
        """Log 'str(msg) % args' at logging level 'lvl'."""
        ...
```

However, convenience functions are defined for each logging level:

然而，为每个日志级别定义了方便的函数：

```python
class Logger:
    def debug(self, msg, *args, **kwargs): ...
    def info(self, msg, *args, **kwargs): ...
    def warn(self, msg, *args, **kwargs): ...
    def error(self, msg, *args, **kwargs): ...
    def critical(self, msg, *args, **kwargs): ...
```

Only one keyword argument is recognized at present - "exc_info". If true, the caller wants exception information to be provided in the logging output. This mechanism is only needed if exception information needs to be provided at **any** logging level. In the more common case, where exception information needs to be added to the log only when errors occur, i.e. at the ERROR level, then another convenience method is provided:

目前只承认一个关键字参数--"exc_info"。如果为真，调用者希望在日志输出中提供异常信息。只有当异常信息需要在任何日志级别提供时，才需要这种机制。在更常见的情况下，只有当错误发生时才需要将异常信息添加到日志中，即在ERROR级别，那么就提供了另一个方便的方法。

```python
class Logger:
    def exception(self, msg, *args): ...
```

This should only be called in the context of an exception handler, and is the preferred way of indicating a desire for exception information in the log. The other convenience methods are intended to be called with exc_info only in the unusual situation where you might want to provide exception information in the context of an INFO message, for example.

这应该只在异常处理程序的上下文中调用，而且是表示希望在日志中获得异常信息的首选方式。其他的方便方法只有在不寻常的情况下才可以和 exc_info 一起调用，例如，你可能想在 INFO 消息的上下文中提供异常信息。

The "msg" argument shown above will normally be a format string; however, it can be any object x for which `str(x)` returns the format string. This facilitates, for example, the use of an object which fetches a locale- specific message for an internationalized/localized application, perhaps using the standard gettext module. An outline example:

上面显示的 "msg "参数通常是一个格式字符串；但是，它可以是str(x)返回格式字符串的任何对象x。例如，这有利于使用一个对象为一个国际化/本地化的应用程序获取一个本地特定的消息，也许使用标准的gettext模块。一个概要的例子：

```python
class Message:
    """Represents a message"""
    def __init__(self, id):
        """Initialize with the message ID"""

    def __str__(self):
        """Return an appropriate localized message text"""

...

logger.info(Message("abc"), ...)
```

Gathering and formatting data for a log message may be expensive, and a waste if the logger was going to discard the message anyway. To see if a request will be honoured by the logger, the `isEnabledFor()` method can be used:

为一条日志消息收集和格式化数据可能会很昂贵，而且如果记录器无论如何都会丢弃该消息的话，那就是一种浪费。要想知道一个请求是否会被记录仪接受，可以使用isEnabledFor()方法。

```python
class Logger:
    def isEnabledFor(self, lvl):
        """
        Return true if requests at level 'lvl' will NOT be
        discarded.
        """
        ...
```

so instead of this expensive and possibly wasteful DOM to XML conversion:

所以，与其这样昂贵且可能浪费的DOM到XML的转换：

```python
...
hamletStr = hamletDom.toxml()
log.info(hamletStr)
...
```

one can do this:

你可以这样：

```python
if log.isEnabledFor(logging.INFO):
    hamletStr = hamletDom.toxml()
    log.info(hamletStr)
```

When new loggers are created, they are initialized with a level which signifies "no level". A level can be set explicitly using the `setLevel()` method:

当新的日志记录器被创建时，它们被初始化为一个表示 "无级别 "的级别。可以使用`setLevel()`方法来明确设置级别：

```python
class Logger:
    def setLevel(self, lvl): ...
```

If a logger's level is not set, the system consults all its ancestors, walking up the hierarchy until an explicitly set level is found. That is regarded as the "effective level" of the logger, and can be queried via the getEffectiveLevel() method:

如果一个日志记录器的级别没有被设置，系统就会查询其所有的祖先，在层次结构中向上走，直到找到一个明确设置的级别。这被认为是该记录仪的 "有效级别"，可以通过getEffectiveLevel()方法进行查询。

```python
def getEffectiveLevel(self): ...
```

Loggers are never instantiated directly. Instead, a module-level function is used:

记录器从不被直接实例化。取而代之的是使用一个模块级的函数：

```python
def getLogger(name=None): ...
```

If no name is specified, the root logger is returned. Otherwise, if a logger with that name exists, it is returned. If not, a new logger is initialized and returned. Here, "name" is synonymous with "channel name".

如果没有指定名称，将返回根记录器。否则，如果存在一个具有该名称的日志记录器，则会返回。如果没有，就会初始化并返回一个新的日志记录器。这里，"name "是 "channel name "的同义词。

Users can specify a custom subclass of Logger to be used by the system when instantiating new loggers:

用户可以指定一个自定义的记录器子类，以便在实例化新的记录器时被系统使用。

```python
def setLoggerClass(klass): ...
```

The passed class should be a subclass of Logger, and its `__init__` method should call `Logger.__init__`.

被传递的类应该是 Logger 的子类，其`__init__` 方法应该调用 `Logger.__init__`。

> 这点很让我意外，日志包居然还支持从Logger类扩展，这点值得第三方包开发者借鉴，译者注。

# [Handlers](https://www.python.org/dev/peps/pep-0282/#id24)

处理程序

Handlers are responsible for doing something useful with a given `LogRecord`. The following core Handlers will be implemented:

处理程序负责对给定的 LogRecord 做一些有用的事情。下列核心处理程序将被实现：

- `StreamHandler`: A handler for writing to a file-like object.

  `StreamHandler`：一个用于向一个类似文件的对象写入的处理程序。

- `FileHandler`: A handler for writing to a single file or set of rotating files.

  `FileHandler`：一个用于向单个文件或一组轮流的文件写入的处理程序。

- `SocketHandler`: A handler for writing to remote TCP ports.

  `SocketHandler`：一个用于向远程TCP端口写入的处理程序。

- `DatagramHandler`: A handler for writing to UDP sockets, for low-cost logging. Jeff Bauer already had such a system [[5\]](https://www.python.org/dev/peps/pep-0282/#id15).

  `DatagramHandler`：一个写入UDP套接字的处理程序，用于低成本的日志记录。Jeff Bauer已经有这样一个系统[5]。

- `MemoryHandler`: A handler that buffers log records in memory until the buffer is full or a particular condition occurs [[1\]](https://www.python.org/dev/peps/pep-0282/#id11).

  `MemoryHandler`：一个处理程序，在内存中缓冲日志记录，直到缓冲区满了或出现特定条件[1]。

- `SMTPHandler`: A handler for sending to email addresses via SMTP.

  `SMTPHandler`：一个用于通过SMTP向电子邮件地址发送内容的处理程序。

- `SysLogHandler`: A handler for writing to Unix syslog via UDP.

  `SysLogHandler`：一个用于通过UDP向Unix syslog写入的处理程序。

- `NTEventLogHandler`: A handler for writing to event logs on Windows NT, 2000 and XP.

  `NTEventLogHandler`：一个用于向Windows NT, 2000和XP的事件日志写入的处理程序。

- `HTTPHandler`: A handler for writing to a Web server with either GET or POST semantics.

  `HTTPHandler`: 一个处理程序，用于以GET或POST的语义向Web服务器写入信息。

Handlers can also have levels set for them using the `setLevel()` method:

处理程序也可以使用`setLevel()`方法为它们设置级别。

```python
def setLevel(self, lvl): ...
```

The FileHandler can be set up to create a rotating set of log files. In this case, the file name passed to the constructor is taken as a "base" file name. Additional file names for the rotation are created by appending .1, .2, etc. to the base file name, up to a maximum as specified when rollover is requested. The setRollover method is used to specify a maximum size for a log file and a maximum number of backup files in the rotation.

FileHandler可以被设置为创建一组旋转的日志文件。在这种情况下，传递给构造函数的文件名被作为一个 "基本 "文件名。通过在基本文件名上添加.1、.2等来创建用于轮换的额外文件名，最多可达到要求轮换时指定的上限。setRollover方法被用来指定日志文件的最大尺寸和轮换中备份文件的最大数量。

```python
def setRollover(maxBytes, backupCount): ...
```

If maxBytes is specified as zero, no rollover ever occurs and the log file grows indefinitely. If a non-zero size is specified, when that size is about to be exceeded, rollover occurs. The rollover method ensures that the base file name is always the most recent, .1 is the next most recent, .2 the next most recent after that, and so on.

如果maxBytes被指定为零，则永远不会发生翻转，并且日志文件会无限地增长。如果指定了一个非零的大小，当该大小即将被超过时，就会发生翻转。滚动方法确保基本文件名总是最新的，.1是次新的，.2是之后次新的，以此类推。

There are many additional handlers implemented in the test/example scripts provided with [[6\]](https://www.python.org/dev/peps/pep-0282/#id16) - for example, XMLHandler and SOAPHandler.

在[6]提供的测试/示例脚本中实现了许多额外的处理程序--例如，XMLHandler和SOAPHandler。

# [LogRecords](https://www.python.org/dev/peps/pep-0282/#id25)

A LogRecord acts as a receptacle for information about a logging event. It is little more than a dictionary, though it does define a `getMessage` method which merges a message with optional runarguments.

LogRecord作为一个关于日志事件信息的容器。它只不过是一个字典，尽管它确实定义了一个`getMessage`方法，将一个消息与可选的运行参数合并。

# [Formatters](https://www.python.org/dev/peps/pep-0282/#id26)

格式化器

A Formatter is responsible for converting a LogRecord to a string representation. A Handler may call its Formatter before writing a record. The following core Formatters will be implemented:

格式化器负责将 LogRecord 转换为字符串表示。处理程序可以在写入记录之前调用其格式化器。以下核心格式器将被实现。

- `Formatter`: Provide printf-like formatting, using the % operator.

  `Formatter`：提供类似 printf 的格式化，使用 % 操作符。

- `BufferingFormatter`: Provide formatting for multiple messages, with header and trailer formatting support.

  `BufferingFormatter`：为多条信息提供格式化，支持标题和预告片的格式化。

Formatters are associated with Handlers by calling `setFormatter()` on a handler:

通过在处理程序上调用`setFormatter()`将格式化器与处理程序相关联。

```python
def setFormatter(self, form): ...
```

Formatters use the % operator to format the logging message. The format string should contain `%(name)x` and the attribute dictionary of the LogRecord is used to obtain message-specific data. The following attributes are provided:

格式化器使用%操作符来格式化日志消息。格式化字符串应该包含`%(name)x`，LogRecord的属性字典被用来获取消息的具体数据。提供了以下属性：

|                       |                                                              |
| --------------------- | ------------------------------------------------------------ |
| `%(name)s`            | Name of the logger (logging channel)                         |
|                       | 记录器的名称（记录通道）                                     |
| `%(levelno)s`         | Numeric logging level for the message (DEBUG, INFO, WARN, ERROR, CRITICAL) |
|                       | 消息的数字日志级别（DEBUG, INFO, WARN, ERROR, CRITICAL）     |
| `%(levelname)s`       | Text logging level for the message ("DEBUG", "INFO", "WARN", "ERROR", "CRITICAL") |
|                       | 消息的文本日志级别（"DEBUG", "INFO", "WARN", "ERROR", "CRITICAL"） |
| `%(pathname)s`        | Full pathname of the source file where the logging call was issued (if available) |
|                       | 发出日志调用的源文件的完整路径名(如果有的话)                 |
| `%(filename)s`        | Filename portion of pathname                                 |
|                       | 路径名中的文件名部分                                         |
| `%(module)s`          | Module from which logging call was made                      |
|                       | 进行日志调用的模块                                           |
| `%(lineno)d`          | Source line number where the logging call was issued (if available) |
|                       | 发出日志调用的源行号(如果有的话)                             |
| `%(created)f`         | Time when the LogRecord was created (`time.time()` return value) |
|                       | 创建日志记录的时间(`time.time()`返回值)                      |
| `%(asctime)s`         | Textual time when the LogRecord was created                  |
|                       | 创建日志记录的文本时间                                       |
| `%(msecs)d`           | Millisecond portion of the creation time                     |
|                       | 创建时间的毫秒部分                                           |
| `%(relativeCreated)d` | Time in milliseconds when the LogRecord was created, relative to the time the logging module was loaded (typically at application startup time) |
|                       | 创建LogRecord的时间，以毫秒为单位，相对于加载日志模块的时间（通常在应用程序启动时） |
| `%(thread)d`          | Thread ID (if available)                                     |
|                       | 线程ID (如果有的话)                                          |
| `%(message)s`         | The result of record.getMessage(), computed just as the record is emitted |
|                       | record.getMessage()的结果，在记录发出的时候计算              |

If a formatter sees that the format string includes "(asctime)s", the creation time is formatted into the LogRecord's asctime attribute. To allow flexibility in formatting dates, Formatters are initialized with a format string for the message as a whole, and a separate format string for date/time. The date/time format string should be in time.strftime format. The default value for the message format is "%(message)s". The default date/time format is ISO8601.

如果格式化器看到格式字符串包括"(asctime)s"，创建时间就会被格式化为LogRecord的asctime属性。为了允许灵活地格式化日期，格式化器被初始化为整个消息的格式字符串，以及一个单独的日期/时间格式字符串。日期/时间格式字符串应该是time.strftime格式。消息格式的默认值是"%(message)s"。默认的日期/时间格式是ISO8601。

The formatter uses a class attribute, "converter", to indicate how to convert a time from seconds to a tuple. By default, the value of "converter" is "time.localtime". If needed, a different converter (e.g. "time.gmtime") can be set on an individual formatter instance, or the class attribute changed to affect all formatter instances.

格式化器使用一个类属性 "转换器"，以表明如何将时间从秒转换为元组。默认情况下，"转换器 "的值是 "time.localtime"。如果需要，可以在单个格式化器实例上设置一个不同的转换器（例如 "time.gmtime"），或者改变类属性以影响所有格式化器实例。

# [Filters](https://www.python.org/dev/peps/pep-0282/#id27)

过滤器

When level-based filtering is insufficient, a Filter can be called by a Logger or Handler to decide if a LogRecord should be output. Loggers and Handlers can have multiple filters installed, and any one of them can veto a LogRecord being output.

当基于级别的过滤不充分时，记录仪或处理程序可以调用一个过滤器来决定是否应该输出一条日志记录。记录器和处理程序可以安装多个过滤器，它们中的任何一个都可以否决一条LogRecord的输出。

```python
class Filter:
    def filter(self, record):
        """
        Return a value indicating true if the record is to be
        processed.  Possibly modify the record, if deemed
        appropriate by the filter.
        如果记录要被处理，返回一个表示 "true "的值。
        可能会修改记录，如果过滤器认为合适的话。
        """
```

The default behaviour allows a Filter to be initialized with a Logger name. This will only allow through events which are generated using the named logger or any of its children. For example, a filter initialized with "A.B" will allow events logged by loggers "A.B", "A.B.C", "A.B.C.D", "A.B.D" etc. but not "A.BB", "B.A.B" etc. If initialized with the empty string, all events are passed by the Filter. This filter behaviour is useful when it is desired to focus attention on one particular area of an application; the focus can be changed simply by changing a filter attached to the root logger.

默认行为允许用一个记录仪的名字初始化过滤器。这将只允许通过使用命名的记录仪或其任何子记录仪产生的事件。例如，初始化为 "A.B "的过滤器将允许记录仪 "A.B"、"A.B.C"、"A.B.C.D"、"A.B.D "等记录的事件，但不允许 "A.BB"、"B.A.B "等。如果初始化为空字符串，所有的事件都被过滤器传递。当需要将注意力集中在应用程序的一个特定区域时，这种过滤器行为非常有用；可以通过改变连接到根记录器的过滤器来改变注意力。

There are many examples of Filters provided in [[6\]](https://www.python.org/dev/peps/pep-0282/#id16).

[6]中提供了许多过滤器的例子。

# [Configuration](https://www.python.org/dev/peps/pep-0282/#id28)

配置

The main benefit of a logging system like this is that one can control how much and what logging output one gets from an application without changing that application's source code. Therefore, although configuration can be performed through the logging API, it must also be possible to change the logging configuration without changing an application at all. For long-running programs like Zope, it should be possible to change the logging configuration while the program is running.

像这样的日志系统的主要好处是，人们可以在不改变应用的源代码的情况下，控制从应用中获得多少和哪些日志输出。因此，尽管可以通过日志API进行配置，但也必须能够在不改变应用程序的情况下改变日志配置。对于像Zope这样长期运行的程序，应该可以在程序运行时改变日志配置。

Configuration includes the following:

配置包括以下内容：

- What logging level a logger or handler should be interested in.

  记录器或处理程序应该对哪一个日志级别感兴趣。

- What handlers should be attached to which loggers.

  哪些处理程序应该被附加到哪些记录器上。

- What filters should be attached to which handlers and loggers.

  哪些过滤器应附加到哪些处理程序和记录器上。

- Specifying attributes specific to certain handlers and filters.

  为某些处理程序和过滤器指定特定的属性。

In general each application will have its own requirements for how a user may configure logging output. However, each application will specify the required configuration to the logging system through a standard mechanism.

一般来说，每个应用程序都会对用户如何配置日志输出有自己的要求。然而，每个应用程序将通过标准机制向日志系统指定所需的配置。

The most simple configuration is that of a single handler, writing to stderr, attached to the root logger. This configuration is set up by calling the `basicConfig()` function once the logging module has been imported.

最简单的配置是一个单一的处理程序，写到stderr，连接到根记录器。这种配置是在导入日志模块后通过调用`basicConfig()`函数设置的。

```python
def basicConfig(): ...
```

For more sophisticated configurations, this PEP makes no specific proposals, for the following reasons:

对于更复杂的配置，本PEP没有提出具体建议，原因如下：

- A specific proposal may be seen as prescriptive.

  具体的建议可能会被看作是规定性的。

- Without the benefit of wide practical experience in the Python community, there is no way to know whether any given configuration approach is a good one. That practice can't really come until the logging module is used, and that means until **after** Python 2.3 has shipped.

  如果没有Python社区广泛的实践经验，就没有办法知道任何给定的配置方法是否是一个好方法。在使用日志模块之前，这种实践不可能真正到来，这意味着要等到 Python 2.3 发布之后**。

- There is a likelihood that different types of applications may require different configuration approaches, so that no "one size fits all".

  不同类型的应用有可能需要不同的配置方法，所以没有 "一刀切"。

The reference implementation [[6\]](https://www.python.org/dev/peps/pep-0282/#id16) has a working configuration file format, implemented for the purpose of proving the concept and suggesting one possible alternative. It may be that separate extension modules, not part of the core Python distribution, are created for logging configuration and log viewing, supplemental handlers and other features which are not of interest to the bulk of the community.

参考实现 [6] 有一个工作的配置文件格式，实现的目的是为了证明这个概念并提出一个可能的替代方案。可能会有单独的扩展模块，而不是Python核心版本的一部分，用于日志配置和日志查看、补充处理程序和其他对大部分社区不感兴趣的功能。

# [Thread Safety](https://www.python.org/dev/peps/pep-0282/#id29)

线程安全

The logging system should support thread-safe operation without any special action needing to be taken by its users.

日志系统应该支持线程安全的操作，而不需要用户采取任何特殊的行动。

# [Module-Level Functions](https://www.python.org/dev/peps/pep-0282/#id30)

模块级函数

To support use of the logging mechanism in short scripts and small applications, module-level functions `debug()`, `info()`, `warn()`, `error()`, `critical()` and `exception()` are provided. These work in the same way as the correspondingly named methods of Logger - in fact they delegate to the corresponding methods on the root logger. A further convenience provided by these functions is that if no configuration has been done, `basicConfig()` is automatically called.

为了支持在简短的脚本和小型应用程序中使用日志机制，我们提供了模块级函数`debug()`、`info()`、`warning()`、`error()`、`critical()`和`exception()`。这些函数的工作方式与Logger中相应命名的方法相同--事实上，它们委托给根记录器上的相应方法。这些函数提供的另一个便利是，如果没有进行配置，`basicConfig()`会被自动调用。

At application exit, all handlers can be flushed by calling the function:

在应用程序退出时，可以通过调用该函数来刷新所有处理程序：

```python
def shutdown(): ...
```

This will flush and close all handlers.

这将刷新并关闭所有处理程序。

# [Implementation](https://www.python.org/dev/peps/pep-0282/#id31)

实现

The reference implementation is Vinay Sajip's logging module [[6\]](https://www.python.org/dev/peps/pep-0282/#id16).

参考实现是Vinay Sajip的日志模块[[6\]](https://www.python.org/dev/peps/pep-0282/#id16)。

# [Packaging](https://www.python.org/dev/peps/pep-0282/#id32)

打包

The reference implementation is implemented as a single module. This offers the simplest interface - all users have to do is "import logging" and they are in a position to use all the functionality available.

参考实现是作为一个单一的模块实现的。这提供了最简单的接口--用户所要做的就是 "import logging"，他们就可以使用所有可用的功能。

# [References](https://www.python.org/dev/peps/pep-0282/#id33)

|                                                       |                                                              |
| ----------------------------------------------------- | ------------------------------------------------------------ |
| [1]                                                   | *([1](https://www.python.org/dev/peps/pep-0282/#id1), [2](https://www.python.org/dev/peps/pep-0282/#id6))* java.util.logging http://java.sun.com/j2se/1.4/docs/guide/util/logging/ |
|                                                       |                                                              |
| [[2\]](https://www.python.org/dev/peps/pep-0282/#id2) | log4j: a Java logging package http://jakarta.apache.org/log4j/docs/index.html |
|                                                       |                                                              |
| [[3\]](https://www.python.org/dev/peps/pep-0282/#id3) | Protomatter's Syslog http://protomatter.sourceforge.net/1.1.6/index.html http://protomatter.sourceforge.net/1.1.6/javadoc/com/protomatter/syslog/syslog-whitepaper.html |
|                                                       |                                                              |
| [[4\]](https://www.python.org/dev/peps/pep-0282/#id4) | MAL mentions his mx.Log logging module: https://mail.python.org/pipermail/python-dev/2002-February/019767.html |
|                                                       |                                                              |
| [[5\]](https://www.python.org/dev/peps/pep-0282/#id5) | Jeff Bauer's Mr. Creosote http://starship.python.net/crew/jbauer/creosote/ |
|                                                       |                                                              |
| [6]                                                   | *([1](https://www.python.org/dev/peps/pep-0282/#id7), [2](https://www.python.org/dev/peps/pep-0282/#id8), [3](https://www.python.org/dev/peps/pep-0282/#id9), [4](https://www.python.org/dev/peps/pep-0282/#id10))* Vinay Sajip's logging module. http://www.red-dove.com/python_logging.html |
|                                                       |                                                              |

# [Copyright](https://www.python.org/dev/peps/pep-0282/#id34)

This document has been placed in the public domain.

Source: https://github.com/python/peps/blob/master/pep-0282.txt

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。