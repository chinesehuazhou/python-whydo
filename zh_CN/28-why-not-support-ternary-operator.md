# 为什么 Python、Go 和 Rust 都不支持三元运算符？

在编程时，我们经常要作条件判断，并根据条件的结果选择执行不同的语句块。在许多编程语言中，最常见的写法是三元运算符，但是，Python 并不支持三元运算符，无独有偶，两个最热门的新兴语言 Go 和 Rust 也不支持！

为什么 Python 不支持三元运算符呢？本文将主要分析 Python 在设计**条件选择语法**时的过程，科普为什么它会采用现今的与众不同的实现方案，同时，我们也将考察为什么其它语言也要抛弃传统的三元运算符。

在开篇之前，我再声明一下：就像“[Python为什么](https://github.com/chinesehuazhou/python-whydo)”系列的大部分文章一样，本文关注的仅是一个很小的语法点，但它并不是“茴香豆有几种写法”那种毫无意义的话题。因为，**细微之处见真功夫，深入研究语言设计背后的原因、历史和哲学，可以让我们在编程时有更加清晰和自由的思维。**

## 什么是三元运算符？

三元运算符通常指的是“?:”，其语法形式为：`condition ? expression1 : expression2`，如果 condition 为真，则取 expression1，若不为真，则取 expression2。

语法简化形式“a ? b : c”，可以读成“如果 a 条件成立，则为 b，否则为 c”。

三元运算符是对普通一重 if-else 结构的简化，常用于在一条语句中同时实现条件判断和取值操作。

```C
// 常规 if-else 
if (a > b) {
    result = x;
} else {
    result = y;
}

// 简化后的写法
result = a > b ? x : y;
```

采用了这种语法设计的编程语言有很多，比如 C、C#、C++、Java、JavaScript、PHP、Perl、Ruby、Swift 等等。毫无争议，它就是编程语言界的主流设计方案（至今仍是）。

这种语法非常简洁高效，代码的可读性也很强（如果你不是第一次接触的话），深得很多人的喜欢。

但是，它并非毫无缺点。Python 是这种语法设计的最著名的挑战者，接下来，我们将看看为什么 Python 要另辟蹊径。

## Python 社区的投票

Python 发布于 1991 年，但在接下来的 15 年里，除了 if-else 语法外，它并不支持三元运算符和其它条件表达式。而且，在 2006 年引入条件表达式前，社区对此进行了漫长而曲折的争论，可以说这是一个设计得很艰难的语法了。

最初，由于时常有人请求添加 if-then-else（三元）表达式，因此在 2003 年 2 月，[PEP 308 – Conditional Expressions](https://peps.python.org/pep-0308) 被提了出来，目的是让社区选出一个让多数人支持的方案。

![PEP-308](https://img.pythoncat.top/20230330231943.png)

很快，除了少部分人希望啥也不做外，社区里出现了好几种方案：

（1）使用标点符号构建的三元运算符

即常规的三元运算符，跟前文介绍的语法一样：

```Python
<condition> ? <expression1> : <expression2>
```

这个方案的呼声挺高，有开发者甚至已提交了实现代码。但是，Guido 给出了两个反对的理由：冒号在 Python 中已经有许多用途（即使它实际上不会产生歧义，因为问号需要匹配冒号）；对于不习惯 C 衍生语言的人来说，理解起来很困难。

（2）使用现有和新的关键字构建

引入新的“then”关键字，结合现有的“else”关键字：

```Python
<condition> then <expression1> else <expression2>
```

它的优点是简单明了、不需要括号、不改变现有关键字的语义，不大可能与语句混淆，而且不需要重载冒号。缺点是引入新关键字的实现成本较高。

（3）其它思路

跟上一种方案的思路相似，但没有上述两类方案的支持度高。

```Python
(if <condition>: <expression1> else: <expression2>)
<condition> and <expression1> else <expression2>
<expression1> if <condition> else <expression2>
cond(<condition>, <expression1>, <expression2>)
```

值得一提的是`(if <condition>: <expression1> else: <expression2>)` ，它是常规 if-else 语法的扁平化，容易理解，但缺点是需要使用圆括号，容易跟生成器表达式混淆，而且需要解释器对冒号做特殊化处理。

另外值得一提的是`<expression1> if <condition> else <expression2>`，它是 PEP-308 最早版本的推荐方案，但是这种不将条件放在首位的风格让一些人感觉不舒服，而且，当“expression1”很长的时候，很容易就忽略掉它的条件。

当时参与投票的全部设计方案：

![](https://img.pythoncat.top/20230331221553.png)

总体上，开发者们希望引入某种形式的 if-then-else 表达式，但投票后却没有哪种方案能取得绝对的优势。概括起来，分歧的问题主要有：是否用标点符号、是否复用关键字、是否复用圆括号、是否引入新关键字、是否引入新语法……

由于得票太分散，因此，这个 PEP 在当时被拒绝了。PEP 中写道：“**Python 的一个设计原则是在不确定采取哪条路线时，则保持现状。**”

## and-or 用于条件选择的问题

以上的投票事件发生在 2004 年 3 月，但是，在 PEP 被拒绝后，相关话题的讨论并未平息，因为大家总想找一种简洁的方式来替换“if-else“。

时间到了 2005 年 9 月，邮件组中有人提议[在  Py3.0 中变更"and"与"or"操作符的逻辑](https://mail.python.org/pipermail/python-dev/2005-September/056510.html)，提议将"and" 和 "or" 运算符简化成始终返回布尔值，而不是返回最后一个被求值的参数。

之所以发起这个提议，原因是他使用了`<condition> and <expression1> or <expression2>`的方式来实现条件判断与选择。但是这种写法在 Python 中的行为跟有些语言并不一样，使用不严谨的话，可能会酿成 Bug！

看看下面的两个例子，你觉得它们会得到什么结果呢？

```Python
a = True and True or "Python猫"

b = True and False or "Python猫"
```

对于`<condition> and <expression1> or <expression2>` ，若 condition 为假，则会直接对 expression2 求值并返回结果；若 condition 为真，则先对 expression1 求值，若也为真，则不会继续对 expression2 求值，若 expression1 不为真，则对 expression2 求值。

因此，上述例子得到的 a 是“True”，而 b 会得到“Python猫”。

本系列的《[Python 为什么能支持任意的真值判断？](https://mp.weixin.qq.com/s/g6jZX0IdH9xpM7BMV3-ToQ) 》介绍过 Python 在真值判断的特殊之处，运用到以上结构中，将出现更不易察觉的问题。比如，该邮件的作者就是遇到了“expression1”为复数“0+4i”，这个数的真值判断为 False，因此导致最后返回的不是预期的“expression1”，而是“expression2”！

在没有更好的方案前，“and-or”是比较常见的条件选择写法，PEP-308 也提及了它，也指出了当“expression1”为假的情况，还认为这种方案是丑陋和令人费解的。

这封邮件再次引发了社区对条件选择语法的讨论，大佬们纷纷登场。

以我现在的视角分析，其实就是开发者们不满足于“if-else”的现状，但是当时流行的“and-or”写法并不够好，因此，大家期望 Python 设计出新的规范性语法，来解决这个痛点。

## 与众不同的条件表达式

在经过 10 天的邮件讨论后，Guido van Rossum 最终决定添加一个**条件表达式**，语法形式为`X if C else Y` 。因此，PEP-308 被重开和更新，并很快就在次年的 [2.5 版本](https://docs.python.org/3/faq/programming.html#is-there-an-equivalent-of-c-s-ternary-operator)中实现了。

前文已提到过这个让一些人感觉不舒服的方案了，因为它没有将条件判断逻辑放在最前面。

那么，为什么最后的胜者会是它呢？这是不是最优的设计呢？

不可否认，起到决定性作用的原因是 Guido。由于社区在一年半前投票时没有形成多数意见，因此他行使 BDFL （终身仁慈独裁者）的决策权力，裁定出一个他认为是最佳的方案。

`X if C else Y` 非常易于理解，可读性高。它延续了“**明确优于隐式**”的风格，使用了直观口语化的“if-else”，而不是引入可能引起混淆的标点符号，就像 Python 选择“and”和“or”两个单词，而不是“&&”和“||”两个符号，它们有着异曲同工之妙。

虽然调整后的语法顺序让人不太习惯，但其实这样的实现却大有好处。首先，它只需复用“if-else”两个关键字，而不需要引入“then”、“when”和其它语法要素，也不像`(if <condition>: <expression1> else: <expression2>)` 那样的繁琐。

其次，为了验证`X if C else Y` 的有效性，Guido 排查了标准库中所有“and-or”组合的写法，发现那些`C and X or Y` 写法都可以被`X if C else Y` 替换掉。[标准库的情况](https://mail.python.org/pipermail/python-dev/2005-September/056803.html)，证明了这新的语法是可行的。

最后，在 PEP-308 提及的原因外，我还想补充一点。据观察，我发现很多时候我们有一个已初始化的变量，然后需要在出现某个条件时，更新变量的值。在这种情况下，“else”部分可以被省略，非常便捷。

```python
my_str = ""
# 中间存在其它代码逻辑
# 当 condition 为真时，变量会被重新赋值
my_str = "Python猫" if condition
```

回顾这段历史，我们可以梳理出一条线索：**Python 没有设计三元运算符“?:”，主要是因为它不符合 Python 明确直观的设计风格。最后采用`X if C else Y` 这种设计，主要的意图其实是消除“and-or”写法的隐患，这种设计简明易读，而且还有`<expression> if <condition>` 简化写法的妙用。**

总体而言，**Python 设计者非常看重可读性与可维护性，不采用三元运算符而创造条件表达式语法，这是一个经过了开放讨论、谨慎评估与权衡取舍的结果。**

## Go、Rust 为什么不支持三元运算符？

考察完 Python 的设计原因后，我们再来考察“反派阵营”中两门最热门的语言。

首先是 Go 语言，官网的 [FAQ](https://go.dev/doc/faq#Does_Go_have_a_ternary_form) 专门列出了一个问题：“Why does Go not have the `?:` operator?”。

Go 语言不支持“?:”运算符，而是推荐使用原生的“if-else”写法。文档的解释很简短，只有一段话：

> Go 语言没有 ?: 运算符，因为语言的设计者们经常看到它被用来创建难以理解的复杂表达式。虽然 if-else 形式比较长，但是它无疑更清晰易懂。**一个语言只需要一个条件控制流结构**。

接着是 Rust 语言，它的[官方文档](https://doc.rust-lang.org/stable/reference/introduction.html)中似乎没有任何关于不支持三元运算符的解释。但在查阅资料后，我发现它也有一段特殊的故事，非常有意思：在 2011 年 6 月时，Rust 曾经引入过三元运算符（[#565](https://github.com/rust-lang/rust/commit/05c0216654999e3d33373914b10aebf9dd7d4907)），然而半年后，设计者意识到这个特性是多余的，因此又把它移除了（[#1698](https://github.com/rust-lang/rust/issues/1698)、[#4632](https://github.com/rust-lang/rust/issues/4632)）!

为什么三元运算符在 Rust 是多余的呢？因为它的 if 语法并不像其它语言是“语句（**statement**）”，而是一个“表达式（**expression**）”，这意味着你可以直接将 if 表达式赋值给变量：

```rust
// 若条件为真，得到 5，否则 6
let number = if condition { 5 } else { 6 };
```

这种语法形式足够简单明了，不就是将大家都熟悉的“if-else”直接用于赋值么，太方便了，替换成三元运算符的话，确实有点画蛇添足之感。

另外，Rust 使用花括号划分代码块，因此上例的花括号内可以包含多条表达式，也支持换行，例如这个例子：

```rust
let x = 42;
let result = if x > 50 {
    println!("x is greater than 50");
    x * 2 // 这是一个表达式，将返回的值赋给 result
} else {
    println!("x is less than or equal to 50");
    x / 2 // 也是一个表达式，将返回的值赋给 result
};
```

这种用法，Python 是不可能做到的。最关键的区别在于，Rust 的 if 是表达式而不是语句。

这两个概念的区别是：

- 表达式（expression）通常指的是由变量、常量、运算符等组成的一个**可求值的代码片段**，它的求值结果可以用到其它表达式或语句中。
- 语句（statement）通常指的是完成某个任务的**单个指令或一组指令**，例如赋值语句、条件语句、循环语句等，它没有返回值（或者为空），不能用于赋值操作。

除了 Rust 外，还有一些编程语言中的 if 是表达式而不是语句，例如 Kotlin、Scala、F#、Swift，它们在理论上也不需要使用三元运算符。（题外话：Swift 是个例外，它也有三元运算符。Kotlin 有“?:”运算符，注意两个符号是连在一起的，`val result = a ?: b` 表示：如果 `a` 不为 `null`，则赋值给 `result` ；否则将 `b` 赋给 `result`）

由于有这种语言设计层面的区别，因此在面对“是否要支持三元运算符”这个问题时，Rust 和 Python/Go 的思考角度有着天然不同的起点。知道了这种区别后，我们对编程语言会有更明晰地认知。

回到本文的问题：为什么有些编程语言不采用主流的三元运算符语法呢？

不可否认，“?:”确实是一种简洁好用的设计，然而，标点符号的负面影响是过于抽象，可读性并不及“if-else”那样强。另外，不同语言的设计风格与使用习惯，也会导致不同的选择。

Python 在经过一番波折后，最后设计出了与众不同的条件表达式。Go 语言明确表示不支持三元运算符。Rust 先设计后舍去，主要的原因在于 if 表达式的语言基础。

考察完这三个热门语言后，我相信你已收获了一个满意的答案。如果是这样，请点赞支持一下本文吧！

最后，本文出自“[Python为什么](https://github.com/chinesehuazhou/python-whydo)”系列，全部文章已归档在 Github 上，欢迎 star 和提 issue。

