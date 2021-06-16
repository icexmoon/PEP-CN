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

### 建议遵守

1. 建议在译文中保留原始英文段落，采用中英文对照方式，这样更便于纠错和改进。
2. 建议进行全文翻译，如果时间精力有限，可以将未翻译部分以原文方式呈现，只翻译核心内容。
3. 译文使用中文标点，原文中使用md代码块方式包裹的内容也应该依然使用此方式。
4. 译文之外的译者注释部分应当使用md中的注释语法`>注释`。
5. 译文之外添加的链接可以使用`**加粗**`的方式强化显示。

### 可选事项

1. [**Typora**](https://typora.io/)是个优秀的多平台开源免费markdown编辑器，唯一缺点是如果内容较多，反应会变慢（至少我的落伍小破笔记本如此）。
2. [**DeepL**](https://www.deepl.com/translator)是最近出现的德国产优秀翻译器，据说使用了深度学习，实际使用也的确比Google优秀一些，至少在翻译计算机专业内容时候是这样。推荐下载桌面版进行使用，效率更高。

### 未来计划

如果有其他Python爱好者加入，打算组织实现以下计划：

- 筛选并确定翻译的PEP列表。
- 编写PEP中的常见专用词汇对照表。
- 进行翻译条目的认领和分配。
- 对已翻译条目进行修订。

## 目录

所有已翻译文章列表会展示在这里：

- [**PEP 8 -- Style Guide for Python Code**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%208%20--%20Style%20Guide%20for%20Python%20Code.md)，Python编码规范
- [**PEP 20 -- The Zen of Python**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%2020%20--%20The%20Zen%20of%20Python.md)，Python之禅
- [**PEP 257 -- Docstring Conventions**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20257%20--%20Docstring%20Conventions.md)，文档字符串约定
- [**PEP 405 -- Python Virtual Environments**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20405%20--%20Python%20Virtual%20Environments.md)，Python虚拟环境
- [**PEP 435 -- Adding an Enum type to the Python standard library.md**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20435%20--%20Adding%20an%20Enum%20type%20to%20the%20Python%20standard%20library.md)，在Python标准库中添加一个枚举类型
- [**PEP 484 -- Type Hints**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20484%20--%20Type%20Hints.md)，类型提示
- [**PEP 492 -- Coroutines with async and await syntax**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20492%20--%20Coroutines%20with%20async%20and%20await%20syntax.md)，通过async与await语句实现协程
- [**PEP 526 -- Syntax for Variable Annotations**](https://github.com/icexmoon/PEP-CN/blob/main/peps/PEP%20526%20--%20Syntax%20for%20Variable%20Annotations.md)，变量注解语法

版权声明：本文由 [**icexmoon**](https://github.com/icexmoon/) 原创，遵循CC 4.0 BY-SA版权协议。