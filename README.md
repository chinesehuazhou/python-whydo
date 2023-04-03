# Python 为什么？

自从接触到 Python 优雅的语法、流畅的编程体验以及活泼的开源社区后，我就成为了这门语言的忠实粉丝！我阅读了大量的书籍和文章，收获了很多知识，同时也积累了很多自己的思考。

“Python为什么”是一系列文章的合集（含翻译），主要关注 Python 的语法、设计、发展、与其它语言的差别等话题，以一个个“为什么”式的问题为切入点，试着展现 Python 的迷人魅力。

大多数人似乎比较关心“怎么做”（HOW-TO）以及“是什么”（WHAT-IS），因此这类的文章随处可见，同质化极为严重。但是，我更关注的话题是“为什么”（WHY-DO or WHY-NOT-DO），这体现了一种不同的思维方式，它可能偏于务虚，有时候对工作内容不会有实质性的帮助。

回答“为什么”的问题，往往也并非易事，因为一个“为什么”问题的答案通常会引出一个新的“为什么”，最后的答案有可能会走到哲学与美学的范畴，从而出现一种“认同的人完全认同，反对的人坚决反对”的对立局面。最典型的例子就是“为什么 Python 强制使用缩进”，赞同的人（比如我）认为这是 Python 最吸引人的特性之一，而反对的人则会深恶痛绝。

但是，我相信这类问题及回答，有着不可磨灭的重要价值！但愿本仓库收录的话题，能对所有喜爱 Python 和编程的人带来新颖的思考角度，增进自己的编程技艺。

如果你有其它话题建议，欢迎[提交 Issue](../../issues/new)。目前的文章主要是中文，欢迎你来翻译成其它语言的版本，同时，欢迎将翻译后的文件提交 pull request 到本仓库。

## 文章列表

- [Python 设计和历史的常见问题](./zh_CN/0-design-and-history-FAQ.md)
  - Python 官方提供了约 30 个常见问题的 FAQ，你可以从中快速得到“权威”的解释
- [Python 为什么用 len() 函数，不用 x.len() 风格？](./zh_CN/1-why-design-len()-and-not-x.len().md)
  - 介绍了《流畅的Python》及 Guido 的解释
  - 我本人认为这体现了 Python 对世界本质的洞察
  - 文章顺便回答了：**为什么 Python 的索引从 0 开始计数？**
- [Python 为什么使用缩进来划分代码块？](./zh_CN/2-why-use-indentation-to-divide-code-block.md)
  - 这是个经典的问题，总会被提起，我总结了 8 个原因
  - 有不少人对上述 8 个原因并不买账，因此我补充了一个回复：Python 的缩进绝不是反人类的设计！
  - Guido 在一次采访中说：严格要求代码缩进确实有点夸张，改用花括号，也不是不可以
  - Python 的缩进起源于 ABC，而 ABC 的缩进起源于 60-70 年代的编程畅想
- [Python 为什么不用分号作语句终止符？](./zh_CN/3-why-not-use-semicolons-as-terminators.md)
  - 分号一般有分隔符与终止符两种作用，但 Python 只用分号作为分隔符，却不用它作为终止符， 而是改用换行作为终止符。本文精炼总结了 5 个原因
- [Python 为什么没有 main 函数？为什么我不推荐写 main 函数？](./zh_CN/4-why-doesn't-Python-have-main-function.md)
  - main 函数作为某些编程语言的执行入口是强制必要的，然而 Python 这门脚本语言有着自己更为灵活的执行方式
  - 在我的编程习惯中，我反感那些不假思索的`if __name__ == '__main__'` 写法，文中给出了我的编程建议
- [Python 为什么推荐蛇形命名法？](./zh_CN/5-why-recommend-snake-case.md)
  - 编程语言中有好几种变量命名风格，最为流行的两种分别是驼峰命名法和蛇形命名法。本文从编程语言的历史发展过程和语言内部的使用习惯角度，解释了为什么 Python 更偏好于蛇形命名法
- [Python 为什么不支持 i++ 自增语法，不提供 ++ 操作符？](./zh_CN/6-why-doesn't-Python-support-i++-increment-syntax.md)
  - 有过 C/C++/Java 等语言的编程经验的开发者会疑惑，为什么 Python 中没有 i++ 这样的语法
  - 这个问题反映出 Python 中的数字对象跟其它语言中的数字有着根本性的差异；另外，Python   的可迭代对象特性，也深刻影响着语言的诸多设计方面
- [Python 为什么只需一条语句“a,b=b,a”，就能直接交换两个变量？](./zh_CN/7-why-can-Python-use-'a,b=b,a'-to-directly-swap-variable-values.md)
  - 很多人以为“a,b=b,a”（交换变量操作）跟“a,b=1,2”（多变量赋值）一样，都是基于元组解包的特性，然而 CPython 的实现并非如此
  - CPython 使用专门的优化指令（即 ROT_TWO、ROT_THREE 和 ROT_FOUR）实现栈顶元素的快捷交换
  - 当同时交换的元素数量大于 4 个时，解释器才会跟“a,b=1,2”（多变量赋值）一样，基于解包实现变量赋值
- [Python 为什么用 # 号作注释符？](./zh_CN/8-why-use-%23-as-a-notation-for-comments.md)
  - 注释符是编程语言中最基础的要素之一，Python 属于“# 号注释符阵营”，原因或许是它遵循着 Shell 等脚本语言的传统
  - Python 中不存在“块注释符”，Guido 曾建议使用多行字符串（multi-line strings）来达到块注释的效果，但这种方案在语义上有点怪异
- [Python 为什么要有 pass 语句？](./zh_CN/9-why-does-Python-have-pass-statement.md)
  - pass 是 Python 独有的一种空操作，其它语言并没有这样的设计
  - pass 可以作为一种空间占位符，辅助程序员快速编程，然而这点小用途并非至关重要的
  - 由于 Python 不使用花括号之类的手段来划分代码块，因此在定义空函数时，pass 就成了一种补齐语法逻辑的方案
- [Python 为什么会有个奇怪的“...”对象？](./zh_CN/10-why-does-Python-have-a-weird-'...'-object.md)
  - ... 是 Python3 在 PEP-3100 中引入的一个内置常量，与 Ellipsis 表示同一个对象
  - 官方说它们是单例的，然而这有违事实。要么是文档错了，要么这是一个 Bug ？
  - ... 有什么用处，能够解决什么问题？文中介绍了 4 个用途：扩展切片语法、表达“未完成的代码”语义、Type Hint 用法、表示无限循环
- [Python 为什么能支持任意的真值判断？](./zh_CN/11-why-does-Python-support-arbitrary-truth-value-testing.md)
  - 这也是 Python 与众不同的一个特性，它将其它语言中仅限于布尔类型的操作（if 或 while 或布尔操作 and、or、not），扩展到了任意对象，带来了极大的灵活性
  - 真值判断的结果取决于\_\_bool\_\_() 和 \_\_len\_\_() 这两个魔术方法的返回值
  - Python 甚至可以对数字对象作真值判断（表示 0 的数为 False，其它数为 True）
- [Python 函数为什么会默认返回 None？](./zh_CN/12-why-do-Python-functions-default-to-return-None.md)
  - Python 隐性地为没有带 return 的函数添加一个 return 操作，即默认返回 None 值，这是由解释器强行注入的逻辑。这意味着：Python 中不存在无返回值的函数
  - 为什么 Python 要强制令所有函数都有一个返回值呢？为什么它不支持无返回值的空函数呢？
- [Python 为什么没有 void 关键字？](./zh_CN/13-why-doesn't-Python-have-the-void-keyword.md)
  - void 通常指的是一种类型（type），但是它没有具体的值（value）。文中介绍了其它语言需要使用 void 关键字实现的两种功能
  - Python 舍弃了表示“没有值的类型”的 void，统一使用表示“仅有一个值的类型” None，配合前一篇“所有函数必然有返回值”的设计，实现了简单好用的效果 
- [Python 为什么是强类型语言，不是弱类型语言？](./zh_CN/14-why-is-Python-strongly-typed.md)
  - 动静类型与强弱类型是两组不同维度的概念，不应混为一谈。在编程语言发展的早期，当强弱类型的概念还未提出时，一些大佬使用动静类型来笼统地描述语言的特性，这是历史原因
  - 如今主流观点以“隐式类型转换”来划分强弱类型，Python 毫无疑问是强类型语言。文中针对几个易混淆的问题，详细解释了为什么 Python 中不存在“隐式类型转换”
- [Python 之父为什么嫌弃 lambda 匿名函数？](./zh_CN/15-why-does-Guido-dislike-lambda-anonymous-functions.md)
  - lambda 语法借鉴自 lisp 语言，却遭到 Python 之父的嫌弃，然而它竟从他的屠刀下幸存，这段故事充满戏剧性
  - Python 的 lambda 只支持单行表达式，功能不完备。曾有人提议增强 lambda 语法，Python 之父认为那不是好的设计，因而否决了
  - Guido 提出要一次性移除 reduce()、map()、filter() 以及 lambda，但最后他妥协了
- [Python 为什么不支持 switch 语句？](./zh_CN/16-why-doesn't-Python-support-the-switch-statement.md)
  - 大多数语言都提供了 switch 语句或者极其相似的东西，但在 Python 之父的裁决下，Python 不提供 switch 语句
  - 文章介绍了试图引入 switch 语句的 PEP-275 与 PEP-3103，总结了这两个提案的要点以及被否决的原因
- [Python 疑难问题：[] 与 list() 哪个快？为什么快？快多少呢？](./zh_CN/17-why-is-[]-faster-than-list().md)
  - 两种创建列表的 [] 与 list() 写法，哪一个更快呢，为什么它会更快呢？
  - 文章通过字节码与执行过程的分析，解释了两者执行速度的差异
- [为什么说 Python 内置函数并不是万能的？](./zh_CN/18-why-aren't-Python-built-in-functions-a-panacea.md)
  - 内置函数的名称并不是关键字，而内置作用域位于名称查找的最低优先级，因此在调用时，某些内置函数/类型的执行速度就明显慢于它们对应的字面量表示法
- [为什么继承 Python 内置类型会出问题？！](./zh_CN/19-why-is-inheriting-Python-built-in-types-a-problem.md)
  - 由《流畅的Python》中的例子，引出 Python 在内置类型子类化时不合常理的话题
  - 分析魔术方法的底层实现逻辑及调用关系，解释内置类型存在的问题
  - 介绍了内置类型子类化的最佳实践
- [为什么 Python 的 f-string 可以拼接字符串与数字？](./zh_CN/20-why-can-Python's-f-string-concatenate-strings-and-numbers.md)
  - Python 是强类型语言，在不经过强制类型转换的情况下，字符串无法拼接数字
  - 介绍了 PEP-498 实现 f-string 的原理
- [Python 的切片为什么不会索引越界？](./zh_CN/21-why-don't-Python-slices-index-out-of-bounds.md)
  - 切片是不少编程语言的特性，Python 的切片不仅功能完善，而且在使用上更为灵活
  - 索引越界是一个常见的问题，Python 切片使用了几条规则，屏蔽了可能导致出错的情况
  - 文章介绍了 Python 的解决方案，但是也留下了一个疑问：为什么 Python 的切片语法要允许索引超出边界呢，为什么不设计成抛出索引错误？
- [为什么 range() 生成的不是迭代器？](./zh_CN/22-why-doesn't-range()-generate-an-iterator.md)
  - 有很多内置方法可以生成迭代器，然而似乎只有 range() 生成的是可迭代对象，这个 range() 显得非常独特。文中给出了我对此的猜想
  - 我还注意到 range 是一种不可变序列，然而它跟字符串这种不可变序列相比，也有着独特的表现
- [Python 为什么要保留显式的 self ？](./zh_CN/23-why-does-Python-keep-explicit-self.md)
  - 这也是一个常见问题。这里给出了官方文档的解释，另外附了 Guido 的一篇博客全文
- [Python 为什么不设计 do-while 循环结构？](./zh_CN/24-why-doesn't-Python-design-a-do-while-loop.md)
  - 在 C/C++、C#、PHP、Java、JavaScript 等语言中，do-while 是一种基本结构。Python 为什么不沿袭它们的传统呢？有什么特殊的考虑？
  - 文章列举了其它语言中 do-while 语法的主要使用场景，解释了为什么 Python 可以不用这种结构
  - 介绍了 PEP-315 试图引入 do-while 结构的尝试，以及 Guido 的反对意见
- [为什么 Python 3 把 print 改为函数？](https://github.com/chinesehuazhou/peps-cn/blob/master/StandardsTrack/3105--改%20print%20为函数.md)
  - Python3 与 Python2 最显眼的一个区别就是：print 语句变成了 print() 函数
  - PEP-3105 Make print a function 是对这个问题最好的回答
- [为什么说 Python 最会变魔术的魔术方法是它？](./zh_CN/25-why-is-it-the-most-magical-magic-method-in-Python.md)
  - \_\_missing\_\_() 是仅在内置类型的子类上才存在的魔术方法，似乎是唯一的特例
  - \_\_missing\_\_() 极为特殊，Python 解释器为它开了后门，实现了最为罕见的“魔术方法间调用”逻辑
- [Python 为什么用”elif“，而不是“else if”？](./zh_CN/26-why-does-Python-use-elif-instead-of-else-if.md)
  - elif 写法相比于“else if”更为简洁，这种写法并非 Python 首创。Guido 发推特解释了这种写法的来源
- [为什么 Python、Go 和 Rust 都不支持三元运算符？](./zh_CN/28-why-not-support-ternary-operator.md)
  - Python 经历了一个波折的过程才引入条件表达式语法，本文梳理了这段设计背后的故事
  - Go 和 Rust 也不支持三元运算符，但原因各不相同，与 Python 一起比较思考，你会明白更多东西

## 相关资料

- [Python文档](https://docs.python.org/)：关于语言本身的很多问题，都能在文档中找到解答 
- [Python增强提案](https://peps.python.org/)：即 PEP，可查找到语言特性的设计、语法及功能的取舍等内容，中文的部分翻译在 [PEP中文翻译计划](https://github.com/chinesehuazhou/peps-cn)
- [Discussions on Python.org](https://discuss.python.org)：从邮件组升级而来的更好用的论坛，了解社区最新动态
- [Guido博客](https://python-history.blogspot.com/)：Python 之父的博客， 介绍了 Python 语言发展历史的部分话题
- [Brett Cannon 的系列文章](https://snarky.ca/tag/syntactic-sugar/)：Python 核心开发者关于语法糖的系列解析，对了解 Python 语法及其实现原理有一定的帮助

## 版权声明

全部文章将首发在微信公众号「Python猫」里，欢迎关注：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gfffh3g28lj2076076q3e.jpg)

授权协议：[CC BY-NC-SA 4.0](https://creativecommons.org/licenses/by-nc-sa/4.0/)
