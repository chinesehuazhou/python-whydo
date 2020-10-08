# Python 为什么不支持 switch 语句？

在这篇文章里，我们会聊一聊为什么 Python 决定不支持 switch 语句。

为什么想要聊这个话题呢？

主要是因为 switch 在其它语言中太常见了，而 Python 却不支持，这样的独特性本身就值得关注，而回答这个问题，也能更加看清 Python 在程序设计上的理念，了解 Python 在语法设计中的决策过程。

本文除了会详细分析 PEP-275 和 PEP-3103，还会介绍到 Python 最新的发展动态（PEP-622），即可能要引入的模式匹配（pattern matching）语法，相信这个话题会开阔大家的眼界，从而对 switch 语法有更为全面的认识。

## 1、switch 是什么？

在开始正题之前，我们需要先聊聊 switch 是什么？

有些同学可能会第一时间想到它……

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gjfnkxodc3j208c04m748.jpg)

喂~喂~，麻烦收收心，别总想着游戏啦，我们要说的是编程语言中的 switch 语句。

一般而言，switch 的语法格式如下：

```java
switch(expression){
    case value1:
       // 语句
       break; // 可选
    case value2:
       // 语句
       break; // 可选
    default: // 可选
       // 语句
}
```

使用流程图来表示，大概是这样的：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1giyo4rps4uj208e0b9gln.jpg)

它的用法不难理解：switch 语句的值满足哪一个 case 情况，就会执行对应的代码块，执行时遇到 break 就跳出，否则就继续执行下一个 case 分支；一般会在最后放一个 default 分支，作为兜底。

大多数语言都提供了 switch 语句或者极其相似的东西，例如，在 C/C++/Java /Go 等静态语言中，它们都支持 switch-case 结构；在 Ruby 中有类似的 case-when 结构，在 Shell 语言中，有相似的 case-in 结构，在 Perl 中，有 switch-case-else……

switch 语句的好处是支持“单条件多分支”的选择结构，相比 if-else 的二分选择结构，在某些时候会更为简洁清晰。

但是，在 Python 中，我们看不到 switch-case 或者相近的语法结构，这是为什么呢？

## 2、Python 为什么不支持 switch?

官方文档中有一篇 FAQ 包含了这个问题：[Why isn’t there a switch or case statement in Python?](https://docs.python.org/3.8/faq/design.html#why-isn-t-there-a-switch-or-case-statement-in-python)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gjfp61puo6j20zs0opgos.jpg)

> FAQ 即 Frequently Asked Questions 的缩写，表示常见问题，官方列了 27 个常见问题，完整清单在此：[https://mp.weixin.qq.com/s/zabIvt4dfu_rf7SmGZXqXg](https://mp.weixin.qq.com/s/zabIvt4dfu_rf7SmGZXqXg)

该文档给出了几个建议，告诉了我们几个 switch/case 的替代方案：

- 使用 if-elif-else 条件判断语句
- 使用字典，将 case 值与调用的函数映射起来
- 使用内置 getattr() 检索特定的对象调用方法

曾有人提出过一些提案（即 PEP-275 和 PEP-3103），想给 Python 引入 switch 语法，然而，对于“**是否以及如何进行靶场测试**”，大家没有达成一致的共识。

靶场测试，即 range test，指的是对武器弹药的技术性能作各种测试验证，与药物的临床试验一样，都是在最终产品交付前的一项关键性测试。

官方文档对于“为什么 Python 不引入 switch”的解释，实际上来源于 Python 之父 Guido van Rossum 在 PEP-3103 中的意见：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gjfz25bpkbj20sx0fuq47.jpg)

出处：[https://www.python.org/dev/peps/pep-3103/](https://www.python.org/dev/peps/pep-3103/)

> A quick poll during my keynote presentation at PyCon 2007 shows this proposal has no popular support. I therefore reject it.
>
> 我在 PyCon 2007 的主题演讲中做了一个快速的民意调查，结果表明这个提案没有得到广泛的支持。因此，我拒绝了它。

简而言之，**PEP 提案有了，语法实现也有了雏形，但是核心开发者们似乎没有达成一致意见，最终导致提案流产了。** 

## 3、PEP-275 与 PEP-3103 说了什么？

PEP-3103 是在 2006 年提出的，PEP-275 则是在 2001 年提出的，它们的共同之处是提出了引入 switch 语句的某种必要性、分析了好几种备选的实现方案，然而，结局是都被拒绝了。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gjfzggnuidj20rz0fjq47.jpg)

出处：[https://www.python.org/dev/peps/pep-0275/](https://www.python.org/dev/peps/pep-0275/)

那么，我们就先来回顾一下核心开发者们都做出了哪些讨论，看一看如果 Python 要实现 switch 结构，会是怎么样子的？（PS：PEP 里还涉及其它内容，本文只摘取与 switch 直接相关的部分）

PEP-275 提出的语法结构如下：

```python
switch EXPR:
    case CONSTANT:
        SUITE
    case CONSTANT:
        SUITE
    ...
    else:
        SUITE
```

其中 else 分支是可选的，如果没有它，并且前面的分支都不满足的话，就什么也不做。另外 case 值 constant 支持不同类型，因为 expr 表达式的类型是动态的。

PEP-275 还提出让 switch 不支持掉落（fall-through）行为，即每个 case 分支相互独立而完整，不用像 C 语言那样需要写 break。

该 PEP 还列举了一些其它的 issue：

- 重用现有关键字，不引入“switch”和“case”
- 使用新的关键字，避免与 C 的 switch 概念混淆
- 支持单分支多值选择（例如：case 'a', 'b', 'c': ...）
- 还有建议支持范围取值判断（例如：case 10..14: ...）

除了首选方案，该 PEP 还记录了几种风格各异的语法方案：

```python
case EXPR:
    of CONSTANT:
        SUITE
    of CONSTANT:
        SUITE
    else:
        SUITE

case EXPR:
    if CONSTANT:
         SUITE
    if CONSTANT:
        SUITE
    else:
        SUITE

when EXPR:
    in CONSTANT_TUPLE:
        SUITE
    in CONSTANT_TUPLE:
        SUITE
    ...
else:
     SUITE
```

PEP-275 记录下了不少重要的思路和问题，为 PEP-3103 的出现做了很好的铺垫。

那么，我们再来看看由 Guido 编写的 PEP-3103 说了些什么吧。

它首先认可了 PEP-275 中的两个基础设定，例如，实现“隐式的 break”，不让 case 分支出现 fall-through 这种转移控制权的情况（其它语言似乎都要求显式地写 break）；else 分支是可选的，复用 else 关键字，而不用引入“default”。

对于 PEP-275 提倡的那种风格，Guido 比较认可，但也认为它的问题是缩进层次太多，因此建议减少代码分支缩进的空格数，例如本来缩进 4 空格，改为缩进 2 空格。

PEP-3103 还列举了另外三种实现方案，分析了它们的差异以及问题，具体内容从略，这里只给大家看看它们的风格：

```python
# case 分支不缩进
switch EXPR:
case EXPR:
    SUITE
case EXPR:
    SUITE
....
else:
    SUITE

# switch 语句后不加冒号
switch EXPR
case EXPR:
    SUITE
case EXPR:
    SUITE
....
else:
    SUITE

# 省略 case 关键字
switch EXPR:
    EXPR:
        SUITE
    EXPR:
        SUITE
    ...
    else:
        SUITE
```

在基础语法之外，Guido 花了很多篇幅来讨论扩展语法（Extended Syntax），即在一个 case 分支中实现匹配多个值的复杂情况：

```python
case EXPR, EXPR, ...:

# Guido 优选的
case in EXPR_LIST:

case *EXPR:

case [*]EXPR, [*]EXPR, ...:

case *(EXPR, EXPR, ...):
```

他重点考虑到的问题包括：switch 中表达式的结果是元组或可迭代对象的情况、case 的值被看成元组解包的情况、在 case 分支作“*”星号操作……

接着，Guido 又用了非常非常多的篇幅来分析该如何实现 switch，其中讨论到的主要思路有：

- 使用等价的 if-elif 链来定义 switch 语句（可能会做些优化）
- 同上，另外所有表达式都必须是可哈希的（hashable）
- 看作是预先计算的字典的分派（dispatch）

PEP 中这部分的内容非常多，因为在每个思路上，Guido 还考虑到了好几种实现路径，这导致了他在复杂分析后的结论是：It is too early to decide（ 现在做决定为时尚早）。

阅读完 PEP-3103 后，我总体的感觉是：Guido 的思路非常发散、层次丰富，但是，缺少了他在面对其它问题时那“快刀斩乱麻”式的洞察力。

也就是说，在诸多的可能性方案中，他力求面面俱到，最终无法说服自己做出一个独裁的决定。阻力主要来自于他自己，而不是其他人。

不过，之所以会出现这种情况，也许跟他的预设立场有关：他似乎认为“Python is fine without a switch statement”，因此尽管写了很长的 PEP，但只是在把问题复杂化，把议题搁置起来。

最后，他在 PyCon 上做了一个小范围调查，借此“名正言顺”地拒绝了自己发起的 PEP，试图堵住众人的悠悠之口……

## 4、未来会有 switch 语句么？

归结起来，之所以 Python 没有 switch 语句，原因有：**switch 的实现细节/功能点未经敲定、没有 switch 也挺好的、有其它不错的方法替代 switch、Guido 的小任性……** 

但是，我们还是要追问一句：**未来会有 switch 语句么？或者类似的多分支选择结构？** 

为什么要有此一问呢？原因是有太多语言自带 switch 语句，而且也有很多人尝试编写提供 switch 功能的库（我记得在 *PyCoder's Weekly* 里曾见到过两次）。

我（Python猫）本人自始至终并不喜欢 switch，几乎可以肯定地说，Python 未来也不会有 switch，但是，它很可能会引入一个类似于 switch 且更为复杂的语法结构！

2020 年 6 月，PEP-622 被提出了，它建议引入在 Scala、Erlang 和 Rust 等语言中的模式匹配语法（**pattern matching**）。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gjhph1cvtvj20wt0ksdhm.jpg)

截至 2020 年 10 月，该 PEP 已被分解成另外三个 PEP（634-636），目前都处于草案阶段。考虑到核心开发者们的参与情况以及话题讨论的情况，这些提案极有可能会在未来版本（比如正在开发中的 3.10）中实现。

以一个求平均数的函数为例，模式匹配语法可以实现成这样：

```python
def average(*args):
    match args:
        case [x, y]:           # captures the two elements of a sequence
            return (x + y) / 2
        case [x]:              # captures the only element of a sequence
            return x
        case []:
            return 0
        case x:                # captures the entire sequence
            return sum(x) / len(x)
```

match-case 结构神似于 switch-case 结构，然而它基于模式（pattern）而非表达式（expression），因此有更多待考虑的细节问题，也有更为广阔的应用空间。 

对此话题感兴趣的读者，建议去查阅这几个新的 PEP。

最后，让我们回到标题中的问题：**Python 为什么不支持 switch 语句？** 

官方文档的 FAQ 对此问题有一个解答，告诉我们有几个不错的替代写法，同时也留下了一条线索：曾有 PEP 提议引入 switch，只是没有成功实现。

沿着这条线索，本文拆解了 PEP-275 和 PEP-3103 这两篇文档，带大家看到了 Python 社区里提出过的风格各异的 switch 方案，以及诸多的悬而未决的问题。

最后，我们还关注到了最新的 PEP-622 的动态，看起来 switch 的“孪生兄弟” match 语法有望引入到 Python 中！switch 话题的讨论似乎要终止了，但是另一个更大的话题正在进行中！

本文属于“Python为什么”系列（Python猫出品），该系列主要关注 Python 的语法、设计和发展等话题，以一个个“为什么”式的问题为切入点，试着展现 Python 的迷人魅力。所有文章将会归档在 Github 上，欢迎大家给颗小星星，项目地址：https://github.com/chinesehuazhou/python-whydo