# PEP-CN

# PEP中文翻译项目

## 项目地址

Github：<https://github.com/icexmoon/PEP-CN>

个人博客：<https://blog.icexmoon.xyz/archives/171.html>

## 目的

最近学习Python，翻译了几篇PEP，遂突发奇想有没有PEP的系统中文翻译项目，翻了一下Github，有一个现成的项目[**PEP中文翻译计划**](https://github.com/chinesehuazhou/peps-cn)，但一来这个项目已经长时间没更新了，二来这个项目有以下缺陷：

1. 没有版权声明，比如某个PEP链接的文章是微信公众号文章，而且明确说了不许转载。
2. 翻译内容是各种外部链接，已经有部分链接失效。

所以我在Github创建了这个项目，目的是进行系统性翻译中文版PEP，为Python学习者做出一些贡献。

当然，我个人的力量远远不够，这里还是希望Python爱好者能广泛加入这个项目。

想加入项目或者有任何建议，可以直接在本项目的[**issue页面**](https://github.com/chinesehuazhou/peps-cn/issues)发帖。

> 如果你不了解什么是PEP，可以阅读[**学习Python，经常见到PEP，那么PEP是什么呢？**](https://blog.icexmoon.xyz/archives/164.html)

## 规范

为了避免项目[**PEP中文翻译计划**](https://github.com/chinesehuazhou/peps-cn)中的不足，这里暂时规定一下规范：

### 必须遵守

1. 翻译文章必须使用markdown文本作为载体。

   > 如果不会使用markdown，可以阅读[**从今天开始markdown**](https://www.cnblogs.com/Moon-Face/p/14449146.html)，相信我，和学习一门编程语言相比，这完全是轻而易举。

2. 在文章开头必须注明PEP原文地址。

3. 在文章末尾必须注明创作者和版权声明，例如：

   ```
   版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 翻译，遵循CC 4.0 BY-SA版权协议。
   ```

   > 建议使用 CC 4.0 版权声明，CC 4.0 BY-SA 的意思是可以转载和改编，但必须注明原作者和来源，关于更多 CC 协议的说明可以阅读[**“知识共享”（CC协议）简单介绍**](https://zhuanlan.zhihu.com/p/20641764)。

 4. 所有翻译成果使用本项目的Github仓库进行托管，在目录中直接进行引用，不使用第三方外链。

 5. 译文中如果需要插入图片，必须将图片上传到可靠图床后添加进文本。

	> [**Markdown-img**](https://github.com/icexmoon/markdown-img)是我编写的对此有帮助的小工具。

6. 待翻译列表以未链接的形式在目录中展示，主要由其它网络文章推荐和个人口味形成，如果有其它想加入的P待翻译PEP请在issue页面告知。

7. 可以从带翻译列表认领或者自行确认翻译目标。为了避免重复劳动，选定后请提交一个空的markdown文件到分支合并请求进行占位。

### 建议遵守

1. 建议在译文中保留原始英文段落，采用中英文对照方式，这样更便于纠错和改进。
2. 建议进行全文翻译，如果时间精力有限，可以将未翻译部分以原文方式呈现，只翻译核心内容。
3. 译文使用中文标点，原文中使用md代码块方式包裹的内容也应该依然使用此方式。
4. 译文之外的译者注释部分应当使用md中的注释语法`>注释`，例如：`>linter指语法检查工具，可以参考[**程序开发中的linter是什么意思？**](https://www.zhihu.com/question/28421865)中的回答，译者注。`
5. 译文之外添加的链接可以使用`**加粗**`的方式强化显示。
6. 为了尽可能提高翻译内容的一致性，添加了[**词汇对照表**](https://github.com/icexmoon/PEP-CN/blob/main/%E8%AF%8D%E6%B1%87%E5%AF%B9%E7%85%A7%E8%A1%A8.md)作为专有名词的翻译依据，如果表中没有，可以选择合适的翻译后维护该表。

### 可选事项

1. [**Typora**](https://typora.io/)是个优秀的多平台开源免费markdown编辑器，唯一缺点是如果内容较多，反应会变慢（至少我的落伍小破笔记本如此）。
2. [**DeepL**](https://www.deepl.com/translator)是最近出现的德国产优秀翻译器，据说使用了深度学习，实际使用也的确比Google优秀一些，至少在翻译计算机专业内容时候是这样。推荐下载桌面版进行使用，效率更高。

### 未来计划

如果有其他Python爱好者加入，打算组织实现以下计划：

- <del>筛选并确定翻译的PEP列表。</del>
- <del>编写PEP中的常见专用词汇对照表。</del>
- 进行翻译条目的认领和分配。
- 对已翻译条目进行修订。

## 目录

所有已翻译文章列表会展示在这里：

- PEP 0 -- Index of Python Enhancement Proposals，Python增强提案索引
- [**PEP 7 -- Style Guide for C Code**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%207%20--%20Style%20Guide%20for%20C%20Code.md)，C扩展规范
- [**PEP 8 -- Style Guide for Python Code**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%208%20--%20Style%20Guide%20for%20Python%20Code.md)，Python编码规范
- [**PEP 20 -- The Zen of Python**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%2020%20--%20The%20Zen%20of%20Python.md)，Python之禅
- PEP 202 -- List Comprehensions，列表推导式
- PEP 205 -- Weak References，弱引用
- PEP 234 -- Iterators，迭代器
- [**PEP 257 -- Docstring Conventions**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20257%20--%20Docstring%20Conventions.md)，文档字符串约定
- PEP 274 -- Dict Comprehensions，字典推导式
- PEP 279 -- The enumerate() built-in function，内建函数enumerate()
- PEP 282 -- A Logging System，一个日志系统
- PEP 285 -- Adding a bool type，添加一个 bool 类型
- PEP 289 -- Generator Expressions，生成器表达式
- PEP 309 -- Partial Function Application，部分函数应用
- PEP 318 -- Decorators for Functions and Methods，函数和方法装饰器
- PEP 333 -- Python Web Server Gateway Interface v1.0，Python Web 服务网关接口 v1.0
- PEP 342 -- Coroutines via Enhanced Generators，通过增强生成器实现协程
- PEP 343 -- The "with" Statement，with语句
- PEP 380 -- Syntax for Delegating to a Subgenerator，委托给子生成器的语法
- [**PEP 405 -- Python Virtual Environments**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20405%20--%20Python%20Virtual%20Environments.md)，Python虚拟环境
- [**PEP 435 -- Adding an Enum type to the Python standard library.md**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20435%20--%20Adding%20an%20Enum%20type%20to%20the%20Python%20standard%20library.md)，在Python标准库中添加一个枚举类型
- PEP 443 -- Single-dispatch generic functions，单分派泛函数
- PEP 471 -- os.scandir() function，遍历目录
- [**PEP 484 -- Type Hints**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md)，类型提示
- [**PEP 492 -- Coroutines with async and await syntax**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20492%20--%20Coroutines%20with%20async%20and%20await%20syntax.md)，通过async与await语句实现协程
- PEP 498 -- Literal String Interpolation，字符串字面量插值法
- PEP 525 -- Asynchronous Generators，异步生成器
- [**PEP 526 -- Syntax for Variable Annotations**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md)，变量注解语法
- PEP 563 -- Postponed Evaluation of Annotations，推迟的注解评估
- PEP 557 -- Data Classes，数据类
- PEP 572 -- Assignment Expressions，赋值表达式
- [**PEP 584 -- Add Union Operators To dict**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20584%20--%20Add%20Union%20Operators%20To%20dict.md)，给dict添加合并操作符
- [**PEP 585 -- Type Hinting Generics In Standard Collections**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20585%20--%20Type%20Hinting%20Generics%20In%20Standard%20Collections.md)，标准集合中的类型提示泛型
- PEP 3101 -- Advanced String Formatting，高级字符串格式化
- PEP 3105 -- Make print a function，将print变为函数
- PEP 3115 -- Metaclasses in Python 3000，Python3中的元类
- [**PEP 3119 -- Introducing Abstract Base Classes**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%203119%20--%20Introducing%20Abstract%20Base%20Classes.md)，介绍抽象基类
- PEP 3120 -- Using UTF-8 as the default source encoding，使用UTF-8作为默认的源编码
- PEP 3135 -- New Super，新的 super
- PEP 3148 -- futures - execute computations asynchronously，futures - 异步执行计算
- PEP 3156 -- Asynchronous IO Support Rebooted: the "asyncio" Module，重新启动的异步IO支持："asyncio "模块
- PEP 3333 -- Python Web Server Gateway Interface v1.0.1 Python Web 服务网关接口 v1.0.1
- PEP 8000 -- Python Language Governance Proposal Overview，Python 语言治理提案概述

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 原创，遵循CC 4.0 BY-SA版权协议。