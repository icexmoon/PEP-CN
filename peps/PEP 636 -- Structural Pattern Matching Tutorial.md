# PEP 636 -- Structural Pattern Matching: Tutorial

PEP 636 -- 结构化模式匹配：教程

| PEP:            | 636                                                          |
| :-------------- | ------------------------------------------------------------ |
| Title:          | Structural Pattern Matching: Tutorial                        |
| Author:         | Daniel F Moisset <dfmoisset at gmail.com>                    |
| Sponsor:        | Guido van Rossum <guido at python.org>                       |
| BDFL-Delegate:  |                                                              |
| Discussions-To: | Python-Dev <[python-dev at python.org](mailto:python-dev@python.org?subject=PEP 636)> |
| Status:         | Final                                                        |
| Type:           | Informational                                                |
| Created:        | 12-Sep-2020                                                  |
| Python-Version: | 3.10                                                         |
| Post-History:   | 22-Oct-2020, 08-Feb-2021                                     |
| Resolution:     | https://mail.python.org/archives/list/python-committers@python.org/message/SQC2FTLFV5A7DV7RCEAR2I2IKJKGK7W3 |

------

Contents

- [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#abstract)，摘要
- Tutorial，教程
  - [Matching sequences](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-sequences)，匹配序列
  - [Matching multiple patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-multiple-patterns)，匹配多个模式
  - [Matching specific values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-specific-values)，匹配特定的值
  - [Matching multiple values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-multiple-values)，匹配多个值
  - [Adding a wildcard](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#adding-a-wildcard)，添加一个通配符
  - [Composing patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#composing-patterns)，构成模式
  - [Or patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#or-patterns)，或模式
  - [Capturing matched sub-patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#capturing-matched-sub-patterns)，捕获匹配的子模式
  - [Adding conditions to patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#adding-conditions-to-patterns)，为模式添加条件
  - [Adding a UI: Matching objects](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#adding-a-ui-matching-objects)，添加一个用户界面：匹配对象
  - [Matching positional attributes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-positional-attributes)，匹配位置属性
  - [Matching against constants and enums](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-against-constants-and-enums)，与常量和枚举相匹配
  - [Going to the cloud: Mappings](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#going-to-the-cloud-mappings)，走向云端：映射
  - [Matching builtin classes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#matching-builtin-classes)，匹配内建类
- [Appendix A -- Quick Intro](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#appendix-a-quick-intro)，附录 A -- 快速介绍
- [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#copyright)，版权声明

# [Abstract](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id2)

摘要

This PEP is a tutorial for the pattern matching introduced by [PEP 634](https://www.python.org/dev/peps/pep-0634).

本PEP是对[PEP 634](https://www.python.org/dev/peps/pep-0634)所介绍的模式匹配的教程。

[PEP 622](https://www.python.org/dev/peps/pep-0622) proposed syntax for pattern matching, which received detailed discussion both from the community and the Steering Council. A frequent concern was about how easy it would be to explain (and learn) this feature. This PEP addresses that concern providing the kind of document which developers could use to learn about pattern matching in Python.

[PEP 622](https://www.python.org/dev/peps/pep-0622)提出了模式匹配的语法，并得到了社区和指导委员会的详细讨论。一个经常被关注的问题是能否容易地解释（和学习）这个功能。这个PEP解决了这个问题，提供了开发者可以用来学习Python中模式匹配的那种文档。

This is considered supporting material for [PEP 634](https://www.python.org/dev/peps/pep-0634) (the technical specification for pattern matching) and [PEP 635](https://www.python.org/dev/peps/pep-0635) (the motivation and rationale for having pattern matching and design considerations).

这被认为是 [PEP 634](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md) (模式匹配的技术规范) 和 [PEP 635](https://www.python.org/dev/peps/pep-0635) (拥有模式匹配的动机和理由以及设计考虑) 的辅助材料。

> 已完成对[PEP 634](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20634%20--%20Structural%20Pattern%20Matching%20Specification.md)的翻译工作，建议在阅读完本文后前往阅读，译者注。

For readers who are looking more for a quick review than for a tutorial, see [Appendix A](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#appendix-a).

对于那些想快速回顾而不是寻找教程的读者来说，请参见[附录A](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#appendix-a)。

# [Tutorial](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id3)

教程

As an example to motivate this tutorial, you will be writing a text adventure. That is a form of interactive fiction where the user enters text commands to interact with a fictional world and receives text descriptions of what happens. Commands will be simplified forms of natural language like `get sword`, `attack dragon`, `go north`, `enter shop` or `buy cheese`.

作为激励本教程的一个例子，你将写一个文本冒险。这是一种交互式小说的形式，用户输入文本命令与一个虚构的世界进行交互，并收到关于所发生情况的文本描述。命令将是自然语言的简化形式，如拿剑、攻击龙、去北方、进入商店或购买奶酪。

## [Matching sequences](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id4)

匹配序列

Your main loop will need to get input from the user and split it into words, let's say a list of strings like this:

你的主循环将需要从用户那里获得输入并将其分割成单词，比方说像这样的字符串列表：

```python
command = input("What are you doing next? ")
# analyze the result of command.split()
```

The next step is to interpret the words. Most of our commands will have two words: an action and an object. So you may be tempted to do the following:

下一步是解释这些词。我们的大多数命令会有两个词：一个动作和一个对象。因此，你可能会被诱惑做以下的事情：

```python
[action, obj] = command.split()
... # interpret action, obj
```

The problem with that line of code is that it's missing something: what if the user types more or fewer than 2 words? To prevent this problem you can either check the length of the list of words, or capture the `ValueError` that the statement above would raise.

这行代码的问题是它缺少了一些东西：如果用户输入的字数多于或少于2个，怎么办？为了防止这个问题，你可以检查单词列表的长度，或者捕获上面的语句会引发的`ValueError`。

You can use a matching statement instead:

你可以用一个匹配语句来代替：

```python
match command.split():
    case [action, obj]:
        ... # interpret action, obj
```

The match statement evaluates the **"subject"** (the value after the `match` keyword), and checks it against the **pattern** (the code next to `case`). A pattern is able to do two different things:

匹配语句评估**"主题 "**（`match` 关键字后面的值），并与**模式**（`case` 旁边的代码）进行检查。一个模式能够做两件不同的事情。

- Verify that the subject has certain structure. In your case, the `[action, obj]` pattern matches any sequence of exactly two elements. This is called **matching**

  验证主题是否具有某些结构。在你的例子中，`[action, obj]`模式匹配任何正好是两个元素的序列。这被称为**匹配**

- It will bind some names in the pattern to component elements of your subject. In this case, if the list has two elements, it will bind `action = subject[0]` and `obj = subject[1]`.

  它将把模式中的一些名字与你的主题的组件元素绑定。在这种情况下，如果列表有两个元素，它将绑定`action = subject[0]`和`obj = subject[1]`。

If there's a match, the statements inside the case block will be executed with the bound variables. If there's no match, nothing happens and the statement after `match` is executed next.

如果匹配，case块内的语句将与绑定的变量一起被执行。如果没有匹配，则不会发生任何事情，接下来会执行`match`后面的语句。

Note that, in a similar way to unpacking assignments, you can use either parenthesis, brackets, or just comma separation as synonyms. So you could write `case action, obj` or `case (action, obj)` with the same meaning. All forms will match any sequence (for example lists or tuples).

注意，与解包赋值类似，你可以使用小括号、大括号，或者仅仅是逗号分隔作为同义词。所以你可以写`case action, obj`或者`case (action, obj)`，意思都一样。所有的形式都可以匹配任何序列（例如，列表或元组）。

## [Matching multiple patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id5)

匹配多个模式

Even if most commands have the action/object form, you might want to have user commands of different lengths. For example, you might want to add single verbs with no object like `look` or `quit`. A match statement can (and is likely to) have more than one `case`:

即使大多数命令都是动词/宾语形式，你可能希望有不同长度的用户命令。例如，你可能想增加没有宾语的单动词，如看或退出。一个匹配语句可以（而且很可能）有一个以上的`case`：

```python
match command.split():
    case [action]:
        ... # interpret single-verb action
    case [action, obj]:
        ... # interpret action, obj
```

The match statement will check patterns from top to bottom. If the pattern doesn't match the subject, the next pattern will be tried. However, once the *first* matching pattern is found, the body of that case is executed, and all further cases are ignored. This is similar to the way that an `if/elif/elif/...` statement works.

匹配语句将从上到下检查模式。如果模式与主题不匹配，将尝试下一个模式。然而，一旦找到第一个匹配的模式，该案例的主体将被执行，所有下面的案例将被忽略。这类似于`if/elif/elif/...`语句的工作方式。

## [Matching specific values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id6)

匹配特定的值

Your code still needs to look at the specific actions and conditionally execute different logic depending on the specific action (e.g., `quit`, `attack`, or `buy`). You could do that using a chain of `if/elif/elif/...`, or using a dictionary of functions, but here we'll leverage pattern matching to solve that task. Instead of a variable, you can use literal values in patterns (like `"quit"`, `42`, or `None`). This allows you to write:

你的代码仍然需要查看具体的动作，并根据具体的动作（例如，退出、攻击或购买）有条件地执行不同的逻辑。你可以用一连串的`if/elif/elif/...`，或者用一个函数字典来完成这个任务，但在这里我们将利用模式匹配来解决这个问题。你可以在模式中使用字面量（如 `"quit"`、`42`或`None`），而不是变量。这使你可以这样写：

```python
match command.split():
    case ["quit"]:
        print("Goodbye!")
        quit_game()
    case ["look"]:
        current_room.describe()
    case ["get", obj]:
        character.get(obj, current_room)
    case ["go", direction]:
        current_room = current_room.neighbor(direction)
    # The rest of your commands go here
```

A pattern like `["get", obj]` will match only 2-element sequences that have a first element equal to `"get"`. It will also bind `obj = subject[1]`.

像`["get", obj]`这样的模式将只匹配第一个元素等于 `"get "`的2元素序列。它也会绑定`obj = subject[1]`。

As you can see in the `go` case, we also can use different variable names in different patterns.

正如你在`go`案例中看到的，我们也可以在不同的模式中使用不同的变量名。

Literal values are compared with the `==` operator except for the constants `True`, `False` and `None` which are compared with the `is` operator.

除了常量`True`、`False`和`None`是用`is`操作符进行比较外，其他的文字值都是用`==`操作符进行比较。

## [Matching multiple values](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id7)

匹配多个值

A player may be able to drop multiple items by using a series of commands `drop key`, `drop sword`, `drop cheese`. This interface might be cumbersome, and you might like to allow dropping multiple items in a single command, like `drop key sword cheese`. In this case you don't know beforehand how many words will be in the command, but you can use extended unpacking in patterns in the same way that they are allowed in assignments:

玩家可以通过使用一系列的命令`drop key`, `drop sword`, `drop cheese`来投放多个物品。这种界面可能很麻烦，你可能希望在一个命令中允许投放多个物品，比如投放钥匙剑奶酪。在这种情况下，你事先不知道命令中会有多少个词，但你可以在模式中使用扩展解包，就像在赋值中允许的那样：

> 这里意为可以像赋值语句中解包那样使用星号来捕获变长子序列，译者注。

```python
match command.split():
    case ["drop", *objects]:
        for obj in objects:
            character.drop(obj, current_room)
    # The rest of your commands go here
```

This will match any sequences having "drop" as its first elements. All remaining elements will be captured in a `list` object which will be bound to the `objects` variable.

这将匹配任何以 `drop` 为第一元素的序列。所有剩余的元素将被捕获到一个`list`对象中，该对象将被绑定到`objects`变量中。

This syntax has similar restrictions as sequence unpacking: you can not have more than one starred name in a pattern.

这个语法与序列解包有类似的限制：你不能在一个模式中拥有一个以上的星号（变量）名称。

## [Adding a wildcard](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id8)

添加一个通配符

You may want to print an error message saying that the command wasn't recognized when all the patterns fail. You could use the feature we just learned and write `case [*ignored_words]` as your last pattern. There's however a much simpler way:

你可能想打印一条错误信息，说当所有的模式都失败时，该命令没有被识别。你可以使用我们刚刚学到的功能，将`case [*ignored_words]`作为你的最后一个模式。不过还有一个更简单的方法：

```python
match command.split():
    case ["quit"]: ... # Code omitted for brevity
    case ["go", direction]: ...
    case ["drop", *objects]: ...
    ... # Other cases
    case _:
        print(f"Sorry, I couldn't understand {command!r}")
```

This special pattern which is written `_` (and called wildcard) always matches but it doesn't bind any variables.

这个特殊的模式被写成`_`（被称为通配符），总是匹配，但它不绑定任何变量。

Note that this will match any object, not just sequences. As such, it only makes sense to have it by itself as the last pattern (to prevent errors, Python will stop you from using it before).

请注意，这将匹配任何对象，而不仅仅是序列。因此，只有让它本身作为最后一个模式才有意义 (为了防止错误，Python 会阻止你在前面使用它)。

> `case _`相当于其他语言中`switch...case`语句中的`default`，不同的是这里延续了解包的风格，巧妙的使用`_`而避免了引入一个额外的`default`关键字，但我个人认为缺点会是其不像`default`那样明显，可能会导致误用，比如说没有放在末尾，译者注。

## [Composing patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id9)

构成模式

This is a good moment to step back from the examples and understand how the patterns that you have been using are built. Patterns can be nested within each other, and we have been doing that implicitly in the examples above.

这是一个很好的时机，让我们从例子中抽身出来，了解你所使用的模式是如何构建的。模式可以相互嵌套，我们在上面的例子中已经隐含了这一点。

There are some "simple" patterns ("simple" here meaning that they do not contain other patterns) that we've seen:

有一些我们见过的 "简单 "模式（这里的 "简单 "是指它们不包含其他模式）：

- **Capture patterns** (stand-alone names like `direction`, `action`, `objects`). We never discussed these separately, but used them as part of other patterns.

  捕获模式（独立的名称，如`direction`、`action`、`objects`）。我们从未单独讨论过这些，而是把它们作为其他模式的一部分。

- **Literal patterns** (string literals, number literals, `True`, `False`, and `None`)

  字面模式（字符串字面、数字字面、`True`、`False`、`None`）。

- The **wildcard pattern** `_`

  通配符模式`_`

Until now, the only non-simple pattern we have experimented with is the sequence pattern. Each element in a sequence pattern can in fact be any other pattern. This means that you could write a pattern like `["first", (left, right), _, *rest]`. This will match subjects which are a sequence of at least three elements, where the first one is equal to `"first"` and the second one is in turn a sequence of two elements. It will also bind `left=subject[1][0]`, `right=subject[1][1]`, and `rest = subject[3:]`

到目前为止，我们唯一试验过的非简单模式是序列模式。序列模式中的每个元素事实上可以是任何其他模式。这意味着你可以写一个像`["first"，(left，right)，_，*rest]`的模式。这将匹配至少有三个元素的序列的主题，其中第一个元素等于 `"first"`，第二个元素又是两个元素的序列。它还将绑定`left=subject[1][0]`, `right=subject[1][1]`, 和 `rest = subject[3:]`

## [Or patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id10)

或模式

Going back to the adventure game example, you may find that you'd like to have several patterns resulting in the same outcome. For example, you might want the commands `north` and `go north` to be equivalent. You may also desire to have aliases for `get X`, `pick up X` and `pick X up` for any X.

回到冒险游戏的例子，你可能会发现，你希望有几个模式能产生相同的结果。例如，你可能希望`north`和`go north`的命令是等同的。你也可能希望对任何一个X都有`get X`、`pick up X`和`pick X up`的别名。

The `|` symbol in patterns combines them as alternatives. You could for example write:

模式中的`|`符号将它们结合起来作为替代。例如，你可以这样写：

```python
match command.split():
    ... # Other cases
    case ["north"] | ["go", "north"]:
        current_room = current_room.neighbor("north")
    case ["get", obj] | ["pick", "up", obj] | ["pick", obj, "up"]:
        ... # Code for picking up the given object
```

This is called an **or pattern** and will produce the expected result. Patterns are tried from left to right; this may be relevant to know what is bound if more than one alternative matches. An important restriction when writing or patterns is that all alternatives should bind the same variables. So a pattern `[1, x] | [2, y]` is not allowed because it would make unclear which variable would be bound after a successful match. `[1, x] | [2, x]` is perfectly fine and will always bind `x` if successful.

这被称为**或模式**，将产生预期的结果。模式是从左到右尝试的；如果有一个以上的替代方案匹配，这可能与知道什么被绑定有关。在编写或模式时，一个重要的限制条件是，所有的选择都应该绑定相同的变量。因此，一个模式`[1, x] | [2, y]`是不允许的，因为它不清楚在成功匹配后哪个变量会被绑定。`[1, x] | [2, x]`是完全可以的，如果成功的话，总是会绑定`x`。

> 在翻译PEP 634时我曾经疑惑过为什么有或模式但没有且模式，现在我明白了，模式匹配本质上其核心目的在于匹配，而非是bool表达式那样以逻辑运算为核心目的，而匹配从逻辑上讲，对于一个给定的主题，只能是匹配成A或者B，而不会存在匹配成A而且B这种逻辑，译者注。

## [Capturing matched sub-patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id11)

捕获匹配的子模式

The first version of our "go" command was written with a `["go", direction]` pattern. The change we did in our last version using the pattern `["north"] | ["go", "north"]` has some benefits but also some drawbacks in comparison: the latest version allows the alias, but also has the direction hardcoded, which will force us to actually have separate patterns for north/south/east/west. This leads to some code duplication, but at the same time we get better input validation, and we will not be getting into that branch if the command entered by the user is `"go figure!"` instead of a direction.

我们的 "go "命令的第一个版本是用`["go", direction]`模式编写的。我们在上一版本中使用`["north"]|["go"，"north"]`模式所做的改变有一些好处，但相比之下也有一些缺点：最新版本允许别名，但也有硬编码的方向，这将迫使我们实际上为北/南/东/西有单独的模式。这导致了一些代码的重复，但同时我们得到了更好的输入验证，如果用户输入的命令是 "go figure!"而不是一个方向，我们将不会进入那个分支。

We could try to get the best of both worlds doing the following (I'll omit the aliased version without "go" for brevity):

我们可以尝试用下面的方法来获得两方面的好处（为了简洁起见，我省略了没有 "go "的别名版本）：

```python
match command.split():
    case ["go", ("north" | "south" | "east" | "west")]:
        current_room = current_room.neighbor(...)
        # how do I know which direction to go?
```

This code is a single branch, and it verifies that the word after "go" is really a direction. But the code moving the player around needs to know which one was chosen and has no way to do so. What we need is a pattern that behaves like the or pattern but at the same time does a capture. We can do so with an **as pattern**:

这段代码是一个单一的分支，它验证了 "go "后面的词确实是一个方向。但是移动玩家的代码需要知道哪一个被选中，但没有办法做到这一点。我们需要的是一个行为类似于或模式的模式，但同时又能进行捕获。我们可以用一个**as模式**来做这件事。

```python
match command.split():
    case ["go", ("north" | "south" | "east" | "west") as direction]:
        current_room = current_room.neighbor(direction)
```

The as-pattern matches whatever pattern is on its left-hand side, but also binds the value to a name.

as模式匹配其左侧的任何模式，此外还会将值绑定到一个名称。

> as模式中`as`的用法实质上与上下文语句`with`中的`as`是一致的，译者注。

## [Adding conditions to patterns](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id12)

为模式添加条件

The patterns we have explored above can do some powerful data filtering, but sometimes you may wish for the full power of a boolean expression. Let's say that you would actually like to allow a "go" command only in a restricted set of directions based on the possible exits from the current_room. We can achieve that by adding a **guard** to our case. Guards consist of the `if` keyword followed by any expression:

我们在上面探讨的模式可以做一些强大的数据过滤，但有时你可能希望得到布尔表达式的全部功能。比方说，你实际上希望根据当前房间的可能出口，只允许在一组有限的方向上执行 "去 "的命令。我们可以通过在我们的案例中添加一个**守卫**来实现这一点。守卫由 `if` 关键字和任何表达式组成。

```python
match command.split():
    case ["go", direction] if direction in current_room.exits:
        current_room = current_room.neighbor(direction)
    case ["go", _]:
        print("Sorry, you can't go that way")
```

The guard is not part of the pattern, it's part of the case. It's only checked if the pattern matches, and after all the pattern variables have been bound (that's why the condition can use the `direction` variable in the example above). If the pattern matches and the condition is truthy, the body of the case executes normally. If the pattern matches but the condition is falsy, the match statement proceeds to check the next case as if the pattern hadn't matched (with the possible side-effect of having already bound some variables).

守卫不是模式的一部分，它是case的一部分。只有在模式匹配的情况下，并且在所有的模式变量都被绑定之后，才会被检查（这就是为什么在上面的例子中，条件可以使用`direction`变量）。如果模式匹配并且条件是真实的，那么case的主体将正常执行。如果模式匹配，但条件是假的，那么匹配语句就会继续检查下一个案例，就像模式没有匹配一样（可能的副作用是已经绑定了一些变量）。

## [Adding a UI: Matching objects](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id13)

添加一个用户界面：匹配对象

Your adventure is becoming a success and you have been asked to implement a graphical interface. Your UI toolkit of choice allows you to write an event loop where you can get a new event object by calling `event.get()`. The resulting object can have different type and attributes according to the user action, for example:

你的冒险正在变得成功，你被要求实现一个图形界面。你选择的UI工具包允许你写一个事件循环，你可以通过调用`event.get()`来获得一个新的事件对象。得到的对象可以根据用户的动作有不同的类型和属性，例如：

- A `KeyPress` object is generated when the user presses a key. It has a `key_name` attribute with the name of the key pressed, and some other attributes regarding modifiers.

  当用户按下一个键时，会产生一个`KeyPress`对象。它有一个`key_name`属性，包含被按下的键的名称，以及其他一些关于修改器的属性。

- A `Click` object is generated when the user clicks the mouse. It has an attribute `position` with the coordinates of the pointer.

  当用户点击鼠标时，会生成一个`Click`对象。它有一个属性`position`，是指针的坐标。

- A `Quit` object is generated when the user clicks on the close button for the game window.

  当用户点击游戏窗口的关闭按钮时，会生成一个`Quit`对象。

Rather than writing multiple `isinstance()` checks, you can use patterns to recognize different kinds of objects, and also apply patterns to its attributes:

与其写多个`isinstance()`检查，你可以使用模式来识别不同种类的对象，并且可以对其属性应用模式：

```python
match event.get():
    case Click(position=(x, y)):
        handle_click_at(x, y)
    case KeyPress(key_name="Q") | Quit():
        game.quit()
    case KeyPress(key_name="up arrow"):
        game.go_north()
    ...
    case KeyPress():
        pass # Ignore other keystrokes
    case other_event:
        raise ValueError(f"Unrecognized event: {other_event}")
```

A pattern like `Click(position=(x, y))` only matches if the type of the event is a subclass of the `Click` class. It will also require that the event has a `position` attribute that matches the `(x, y)` pattern. If there's a match, the locals `x` and `y` will get the expected values.

像`Click(position=(x, y))`这样的模式只有在事件的类型是`Click`类的一个子类时才会匹配。它还要求该事件有一个与`(x, y)`模式相匹配的位置属性。如果有匹配的，局部（变量）`x`和`y`将得到预期的值。

A pattern like `KeyPress()`, with no arguments will match any object which is an instance of the `KeyPress` class. Only the attributes you specify in the pattern are matched, and any other attributes are ignored.

像`KeyPress()`这样没有参数的模式将匹配任何属于`KeyPress`类的实例的对象。只有你在模式中指定的属性被匹配，任何其他属性都被忽略。

## [Matching positional attributes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id14)

匹配位置属性

The previous section described how to match named attributes when doing an object match. For some objects it could be convenient to describe the matched arguments by position (especially if there are only a few attributes and they have a "standard" ordering). If the classes that you are using are named tuples or dataclasses, you can do that by following the same order that you'd use when constructing an object. For example, if the UI framework above defines their class like this:

上一节描述了在进行对象匹配时如何匹配命名的属性。对于一些对象来说，通过位置来描述匹配的参数可能是很方便的（特别是如果只有几个属性并且它们有一个 "标准 "的顺序）。如果你使用的类是具名数组或数据类，你可以按照构造对象时使用的相同顺序来做。例如，如果上面的UI框架是这样定义他们的类：

> 关于数据类的内容可以阅读PEP 557 -- Data Classes，遗憾的是还没有完成翻译工作，译者注。

```python
from dataclasses import dataclass

@dataclass
class Click:
    position: tuple
    button: Button
```

then you can rewrite your match statement above as:

那么你可以使用下面的匹配语句重写相关代码：

```python
match event.get():
    case Click((x, y)):
        handle_click_at(x, y)
```

The `(x, y)` pattern will be automatically matched against the `position` attribute, because the first argument in the pattern corresponds to the first attribute in your dataclass definition.

`(x, y)`模式将自动与`position`属性匹配，因为模式中的第一个参数对应于你的数据类定义中的第一个属性。

Other classes don't have a natural ordering of their attributes so you're required to use explicit names in your pattern to match with their attributes. However, it's possible to manually specify the ordering of the attributes allowing positional matching, like in this alternative definition:

其他类的属性没有一个自然的顺序，所以你需要在模式中使用明确的名称来与它们的属性匹配。然而，可以手动指定属性的顺序，允许位置匹配，就像在这个替代定义中：

> 这里潜在的原理是，一般类定义中的属性都是以字典形式保存在`__dict__`中的，而Python中的字典结构出于效率方面的考虑，是不会保证轮询时的访问顺序的，具体到不同的Python解释器可能表现形式不同，但在理论上应当认为是无序的。所以是不能通过位置来匹配或者捕获一般类的属性的。而具名元组或者数据类其属性的组织形式有所不同，是以元组形式保存，所以是可以使用位置模式的。我比较好奇如果使用`__slots__`来优化存储后的类能否满足位置模式匹配的要求，这里并没有说明，译者注。

```python
class Click:
    __match_args__ = ("position", "button")
    def __init__(self, position, button):
        ...
```

The `__match_args__` special attribute defines an explicit order for your attributes that can be used in patterns like `case Click((x,y))`.

`__match_args__`特殊属性为你的属性定义了一个明确的顺序，可以用于`case Click((x,y))`这样的模式。

> 这里引入了一个新的特殊属性`__match_args__`，而非`__slots__`，或许是考虑在不改变原有类的行为的情况下使得位置模式成为可能，这的确是个兼容旧代码的好设计，译者注。

## [Matching against constants and enums](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id15)

与常量和枚举相匹配

Your pattern above treats all mouse buttons the same, and you have decided that you want to accept left-clicks, and ignore other buttons. While doing so, you notice that the `button` attribute is typed as a `Button` which is an enumeration built with `enum.Enum`. You can in fact match against enumeration values like this:

你上面的模式对所有的鼠标按钮都是一样的，你已经决定要接受左键点击，而忽略其他按钮。在这样做的时候，你注意到按钮属性被看做是`Button`，它是一个用`enum.Enum`构建的枚举。事实上，你可以像这样与枚举值进行匹配：

```python
match event.get():
    case Click((x, y), button=Button.LEFT):  # This is a left click
        handle_click_at(x, y)
    case Click():
        pass  # ignore other clicks
```

This will work with any dotted name (like `math.pi`). However an unqualified name (i.e. a bare name with no dots) will be always interpreted as a capture pattern, so avoid that ambiguity by always using qualified constants in patterns.

这将与任何带点的名称（如`math.pi`）一起工作。然而，一个不合格的名字（即一个没有点的纯（变量）名）将总是被解释为一个捕获模式，所以通过在模式中总是使用合格的常数来避免这种模糊性。

## [Going to the cloud: Mappings](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id16)

走向云端：映射

You have decided to make an online version of your game. All of your logic will be in a server, and the UI in a client which will communicate using JSON messages. Via the `json` module, those will be mapped to Python dictionaries, lists and other builtin objects.

你已经决定为你的游戏制作一个在线版本。你所有的逻辑都在一个服务器中，用户界面在一个客户端中，它将使用JSON消息进行通信。通过`json`模块，这些信息将被映射到Python字典、列表和其他内置对象。

Our client will receive a list of dictionaries (parsed from JSON) of actions to take, each element looking for example like these:

我们的客户端将收到一个行动字典的列表（从JSON解析而来），每个元素看起来像这样。

- `{"text": "The shop keeper says 'Ah! We have Camembert, yes sir'", "color": "blue"}`

- If the client should make a pause `{"sleep": 3}`

  如果客户应该暂停`{"sleep": 3}`

- To play a sound `{"sound": "filename.ogg", "format": "ogg"}`

  播放声音`{"sound": "filename.ogg", "format": "ogg"}`

Until now, our patterns have processed sequences, but there are patterns to match mappings based on their present keys. In this case you could use:

到目前为止，我们的模式都是处理序列的，但也有一些模式是根据它们现在的键来匹配映射的。在这种情况下，你可以使用：

```python
for action in actions:
    match action:
        case {"text": message, "color": c}:
            ui.set_text_color(c)
            ui.display(message)
        case {"sleep": duration}:
            ui.wait(duration)
        case {"sound": url, "format": "ogg"}:
            ui.play(url)
        case {"sound": _, "format": _}:
            warning("Unsupported audio format")
```

The keys in your mapping pattern need to be literals, but the values can be any pattern. As in sequence patterns, all subpatterns have to match for the general pattern to match.

你的映射模式中的键需要是字面量的，但是值可以是任何模式。就像序列模式一样，所有的子模式都必须与一般模式相匹配。

You can use `**rest` within a mapping pattern to capture additional keys in the subject. Note that if you omit this, extra keys in the subject will be ignored while matching, i.e. the message `{"text": "foo", "color": "red", "style": "bold"}` will match the first pattern in the example above.

你可以在映射模式中使用`**rest`来捕获主题中的其他键。注意，如果你省略了这一点，在匹配时，主题中的额外键将被忽略，即信息`{"text": "foo", "color": "red", "style": "bold"}`将匹配上面例子中的第一个模式。

> 在处理序列和映射时，似乎模式匹配的行为并不一致，对于序列，如果长度不相同就不会被匹配，而映射只要满足模式中给定的键名即会被匹配，剩余键名会被忽略，译者注。

## [Matching builtin classes](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id17)

匹配内建类

The code above could use some validation. Given that messages came from an external source, the types of the field could be wrong, leading to bugs or security issues.

上面的代码可以使用一些验证。鉴于消息来自于外部来源，字段的类型可能是错误的，导致错误或安全问题。

Any class is a valid match target, and that includes built-in classes like `bool` `str` or `int`. That allows us to combine the code above with a class pattern. So instead of writing `{"text": message, "color": c}` we can use `{"text": str() as message, "color": str() as c}` to ensure that `message` and `c` are both strings. For many builtin classes (see PEP-634 for the whole list), you can use a positional parameter as a shorthand, writing `str(c)` rather than `str() as c`. The fully rewritten version looks like this:

任何类都是一个有效的匹配目标，这包括像`bool` `str`或`int`这样的内建类。这使得我们可以将上面的代码与类模式结合起来。因此，我们可以用`{"text": message, "color": c}`代替写`{"text": str() as message, "color": str() as c}`来确保message和c都是字符串。对于许多内建类（整个列表见PEP-634），你可以使用一个位置参数作为速记，写成`str(c)`而不是`str() as c`。完全重写的版本看起来像这样：

```python
for action in actions:
    match action:
        case {"text": str(message), "color": str(c)}:
            ui.set_text_color(c)
            ui.display(message)
        case {"sleep": float(duration)}:
            ui.wait(duration)
        case {"sound": str(url), "format": "ogg"}:
            ui.play(url)
        case {"sound": _, "format": _}:
            warning("Unsupported audio format")
```

# [Appendix A -- Quick Intro](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id18)

附录A -- 快速介绍

A match statement takes an expression and compares its value to successive patterns given as one or more case blocks. This is superficially similar to a switch statement in C, Java or JavaScript (and many other languages), but much more powerful.

匹配语句接收一个表达式，并将其值与作为一个或多个case块的连续模式进行比较。这在表面上类似于C、Java或JavaScript（以及许多其他语言）中的switch语句，但功能更强大。

The simplest form compares a subject value against one or more literals:

最简单的形式是将一个主题值与一个或多个字面量进行比较：

```python
def http_error(status):
    match status:
        case 400:
            return "Bad request"
        case 404:
            return "Not found"
        case 418:
            return "I'm a teapot"
        case _:
            return "Something's wrong with the Internet"
```

Note the last block: the "variable name" `_` acts as a *wildcard* and never fails to match.

注意最后一个区块："变量名"`_`作为一个*通配符*，永远不会匹配失败。

You can combine several literals in a single pattern using `|` ("or"):

你可以使用`|`（"或"）在一个模式中组合多个字面量：

```python
case 401 | 403 | 404:
    return "Not allowed"
```

Patterns can look like unpacking assignments, and can be used to bind variables:

模式可以看起来像解包赋值，并可以用来绑定变量：

```python
# point is an (x, y) tuple
match point:
    case (0, 0):
        print("Origin")
    case (0, y):
        print(f"Y={y}")
    case (x, 0):
        print(f"X={x}")
    case (x, y):
        print(f"X={x}, Y={y}")
    case _:
        raise ValueError("Not a point")
```

Study that one carefully! The first pattern has two literals, and can be thought of as an extension of the literal pattern shown above. But the next two patterns combine a literal and a variable, and the variable *binds* a value from the subject (`point`). The fourth pattern captures two values, which makes it conceptually similar to the unpacking assignment `(x, y) = point`.

仔细研究一下这个! 第一个模式有两个字面，可以认为是上面所示的字面模式的扩展。但是接下来的两个模式结合了一个字面量和一个变量，而这个变量*绑定*了一个来自主体（`point`）的值。第四种模式捕获了两个值，这使得它在概念上类似于解包赋值`(x, y) = point`。

If you are using classes to structure your data you can use the class name followed by an argument list resembling a constructor, but with the ability to capture attributes into variables:

如果你使用类来组织你的数据，你可以使用类名，后面跟一个类似于构造函数的参数列表，但有能力将属性捕捉到变量中。

```python
from dataclasses import dataclass

@dataclass
class Point:
    x: int
    y: int

def where_is(point):
    match point:
        case Point(x=0, y=0):
            print("Origin")
        case Point(x=0, y=y):
            print(f"Y={y}")
        case Point(x=x, y=0):
            print(f"X={x}")
        case Point():
            print("Somewhere else")
        case _:
            print("Not a point")
```

You can use positional parameters with some builtin classes that provide an ordering for their attributes (e.g. dataclasses). You can also define a specific position for attributes in patterns by setting the `__match_args__` special attribute in your classes. If it's set to ("x", "y"), the following patterns are all equivalent (and all bind the `y` attribute to the `var` variable):

你可以在一些为其属性提供排序的内置类中使用位置参数（例如，数据类）。你也可以通过在你的类中设置`__match_args__`特殊属性来为模式中的属性定义一个特定位置。如果它被设置为 ("x", "y")，下面的模式都是等价的 (并且都将`y`属性绑定到`var`变量上)。

```python
Point(1, var)
Point(1, y=var)
Point(x=1, y=var)
Point(y=var, x=1)
```

Patterns can be arbitrarily nested. For example, if we have a short list of points, we could match it like this:

模式可以任意嵌套。例如，如果我们有一个简短的点的列表，我们可以这样匹配它：

```python
match points:
    case []:
        print("No points")
    case [Point(0, 0)]:
        print("The origin")
    case [Point(x, y)]:
        print(f"Single point {x}, {y}")
    case [Point(0, y1), Point(0, y2)]:
        print(f"Two on the Y axis at {y1}, {y2}")
    case _:
        print("Something else")
```

We can add an `if` clause to a pattern, known as a "guard". If the guard is false, `match` goes on to try the next case block. Note that value capture happens before the guard is evaluated:

我们可以在模式中添加一个`if`子句，即所谓的 "守卫"。如果保护句是假的，匹配就会继续尝试下一个case块。请注意，值捕获发生在守卫被评估之前：

> 即先处理模式（包含其中可能的捕获行为），再执行守卫，译者注。

```python
match point:
    case Point(x, y) if x == y:
        print(f"Y=X at {x}")
    case Point(x, y):
        print(f"Not on the diagonal")
```

Several other key features:

其他几个关键特征：

- Like unpacking assignments, tuple and list patterns have exactly the same meaning and actually match arbitrary sequences. An important exception is that they don't match iterators or strings. (Technically, the subject must be an instance of `collections.abc.Sequence`.)

  像解包赋值一样，元组和列表模式具有完全相同的含义，实际上可以匹配任意的序列。一个重要的例外是，它们不匹配迭代器或字符串。(技术上讲，主体必须是 `collections.abc.Sequence` 的实例）。)

- Sequence patterns support wildcards: `[x, y, *rest]` and `(x, y, *rest)` work similar to wildcards in unpacking assignments. The name after `*` may also be `_`, so `(x, y, *_)` matches a sequence of at least two items without binding the remaining items.

  序列模式支持通配符。`[x, y, *rest]`和`(x, y, *rest)`的工作方式类似于解包赋值中的通配符。*后面的名字也可以是_，所以`(x, y, *_)`匹配至少两个项目的序列而不绑定其余项目。

- Mapping patterns: `{"bandwidth": b, "latency": l}` captures the `"bandwidth"` and `"latency"` values from a dict. Unlike sequence patterns, extra keys are ignored. A wildcard `**rest` is also supported. (But `**_` would be redundant, so it is not allowed.)

  映射模式：`{"bandwidth": b, "latency": l}`从dict中抓取 `"bandwidth "`和 `"latency "`值。与序列模式不同，额外的键会被忽略。也支持通配符`**rest`。(但`**_`是多余的，所以不允许使用）。)

- Subpatterns may be captured using the `as` keyword:

  子模式可以使用as关键字来捕获：

  ```python
  case (Point(x1, y1), Point(x2, y2) as p2): ...
  ```

- Most literals are compared by equality, however the singletons `True`, `False` and `None` are compared by identity.

  大多数字面量是通过等价比较的，但是单例`True`、`False`和`None`是通过身份比较的。

- Patterns may use named constants. These must be dotted names to prevent them from being interpreted as capture variable:

  模式可以使用命名的常量。这些名称必须是带点的，以防止它们被解释为捕获变量：

  ```python
  from enum import Enum
  class Color(Enum):
      RED = 0
      GREEN = 1
      BLUE = 2
  
  match color:
      case Color.RED:
          print("I see red!")
      case Color.GREEN:
          print("Grass is green")
      case Color.BLUE:
          print("I'm feeling the blues :(")
  ```

# [Copyright](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20636%20--%20Structural%20Pattern%20Matching%20Tutorial.md#id19)

版权声明

This document is placed in the public domain or under the CC0-1.0-Universal license, whichever is more permissive.

Source: https://github.com/python/peps/blob/master/pep-0636.rst

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。