# Python 到底是强类型语言，还是弱类型语言？

## 0、前言

我在上一篇文章中分析了 [为什么 Python 没有 void 类型](https://mp.weixin.qq.com/s/wWCgwMofSvKBbi3gZLxsAQ) 的话题，在文章发布后，有读者跟我讨论起了另一个关于类型的问题，但是，我们很快就出现了重大分歧。

我们主要的分歧就在于：**Python 到底是不是强类型语言？** 我认为是，而他认为不是。

他写了一篇很长的文章《[谁告诉的你们Python是强类型语言！站出来，保证不打你！](https://blog.csdn.net/nokiaguy/article/details/108218260)》，专门重申了他的观点，但可惜错漏百出。

我曾有想法要写写关于 Python 类型的问题，现在借着这个机会，就来系统地梳理一下吧。

（PS：在我写作进行到差不多一半的时候，微信读者群里恰好也讨论到“强弱类型”的话题！在与大家讨论时，我的一些想法得到了验证，同时我也学到了很多新知识，所以本文的部分内容有群友们的功劳，特此鸣谢！）

## 1、动静类型与强弱类型

很多读者应该都熟悉`动态类型` 与`静态类型` ，但是很多人也会把它们跟`强弱类型` 混为一谈，所以我们有必要先作一下概念上的澄清。

这两组类型都是针对于编程语言而言的，但关注的核心问题不同。

**对于“动静类型”概念，它的核心问题是“什么时候知道一个变量是哪种类型”？**

**一般而言，在编译期就确定变量类型的是静态类型语言，在运行期才确定变量类型的则是动态类型语言。** 

例如，某些语言中定义函数“int func(int a){...}”，在编译时就能确定知道它的参数和返回值是 int 类型，所以是静态类型；而典型如 Python，定义函数时写“def func(a):...”，并不知道参数和返回值的类型，只有到运行时调用函数，才最终确定参数和返回值的类型，所以是动态类型

**对于“强弱类型”概念，它的核心问题是“不同类型的变量是否允许隐式转化”？** 

**一般而言，编译器有很少（合理）隐式类型转化的是强类型语言，有较多（过分）隐式类型转化的是弱类型语言。**  

例如，Javascript 中的 `"1000"+1` 会得到字符串“10001”，而 `"1000"-1` 则会得到数字 999，也就是说，编译器根据使用场合，对两种不同类型的对象分别做了隐式的类型转化，但是相似的写法，在强类型语言中则会报类型出错。（数字与字符串的转化属于过分的转化，下文会再提到一些合理的转化。）

按照以上的定义，有人将常见的编程语言画了一张分类图：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gi5r2xkihgj20dw09dwec.jpg)

按强弱类型维度的划分，可以归纳出：

- 强类型：Java、C#、Python、Ruby、Erlang（再加GO、Rust）……
- 弱类型：C、C++、Javascript、Perl、PHP、VB……

## 2、过去的强弱类型概念

动静类型的概念基本上被大家所认可，然而，强弱类型的概念在问答社区、技术论坛和学术讨论上却有很多的争议。此处就不作罗列了。

为什么会有那么多争议呢？

**最主要的原因之一是有人把它与动静类型混用了。** 

最明显的一个例子就是 Guido van Rossum 在 2003 年参加的一个访谈，它的话题恰好是关于强弱类型的（[Strong versus Weak Typing](https://www.artima.com/intv/strongweak.html)）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gi5r1xokqgj20cs09ggnr.jpg)

但是，他们谈论的明显只是动静类型的区别。

访谈中还引述了 Java 之父 James Gosling 的话，从他的表述中也能看出，他说的“强弱类型”其实也是动静类型的区分。

另外还有一个经典的例子，C 语言之父 Dennis Ritchie 曾经说 C 语言是一种“强类型但是弱检查”**的语言。如果对照成前文的定义，那他其实指的是“静态类型弱类型”。

为什么这些大佬们会有混淆呢？

其实原因也很简单，**那就是在当时还没有明确的动静类型与强弱类型的概念之分！或者说，那时候的强弱类型指的就是动静类型。** 

维基百科上给出了 1970 年代对强类型的定义，基本可以还原成前文提到的静态类型：

> In 1974, Liskov and Zilles defined a strongly-typed language as one in which "whenever an object is passed from a calling function to a called function, its type must be compatible with the type declared in the called function."[[3\]](https://en.wikipedia.org/wiki/Strong_and_weak_typing#cite_note-2) In 1977, Jackson wrote, "In a strongly typed language each data area will have a distinct type and each process will state its communication requirements in terms of these types."[[4\]](https://en.wikipedia.org/wiki/Strong_and_weak_typing#cite_note-3)

前面几位编程语言之父应该就是持有类似的观念。

不过，大佬们也意识到了当时的“强弱类型”概念并不充分准确，所以 Dennis Ritchie 才会说成“**强类型但是弱检查**”，而且在访谈中，Guido 也特别强调了 Python 不应该被称为弱类型，而应该说是**运行时类型（runtime typing）** 。

但是在那个早期年代，基本上强弱类型就等同于动静类型，而这样的想法至今仍在影响着很多人。

## 3、现在的强弱类型概念

早期对于编程语言的分类其实是混杂了动静与强弱两个维度，但是，它们并不是一一对应重合的关系，并不足以表达编程语言间的区别，因此就需要有更为明确/丰富的定义。

有人提出了“type safety”、“memory safety”等区分维度，也出现了静态检查类型和动态检查类型，与强弱类型存在一定的交集。

直到出现 2004 年的一篇集大成的学术论文《[Type Systems](http://lucacardelli.name/Papers/TypeSystems.pdf)》（出自微软研究院，作者 Luca Cardelli），专门研究编程语言的不同类型系统：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gi5r2jaof0j20cs09gmze.jpg)

论文中对于强弱检查（也即强弱类型）有一个简短的归纳如下：

- Strongly checked language: A language where no forbidden errors can occur at run time (depending on the definition of forbidden error).
- Weakly checked language: A language that is statically checked but provides no clear guarantee of absence of execution errors.

其关键则是程序对于 *untrapped errors* 的检查强度，在某些实际已出错的地方，弱类型程序并不作捕获处理，例如 C 语言的一些指针计算和转换，而《[C 程序员十诫](http://doc.cat-v.org/henry_spencer/ten-commandments)》的前几个都是弱类型导致的问题。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gi7jc5jq0kj20cs09gjt0.jpg)

论文对于这些概念的定义还是比较抽象的，由于未捕获的错误（untrapped errors）大多是由于隐式类型转换所致，所以又演化出了第一节中的定义，以隐式类型转换作为判断标准。

如今将“**对隐式类型转换的容忍度**”**作为强弱类型的分类标准，已经是很多人的共识（虽然不够全面，而且有一些不同的声音）。

例如，维基百科就把隐式类型转换作为弱类型的主要特点之一：

> A weakly typed language has looser typing rules and may produce unpredictable results or may perform implicit type conversion at runtime.

例如，以 Python 为例，**社区的主流看法认为它是强类型语言，而判断的标准也是看隐式类型转换。** 

例子有很多，比如 Python 官方的 wiki，它专门回答了[Why is Python a dynamic language and also a strongly typed language](https://wiki.python.org/moin/Why%20is%20Python%20a%20dynamic%20language%20and%20also%20a%20strongly%20typed%20language) ，给出了 4 个答案，为 Python 的“动态强类型”定性：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gi7jdo43yuj20cs09gdib.jpg)

再比如，在《流畅的Python》第11章的杂谈中，也专门提到了强弱类型的分类。（它的用语是“很少隐式类型转换”，算是比较严谨的，但是也错误地把 C++ 归为了强类型。）

## 4、Python 是不是强类型语言？

关于“Python 是否属于强类型”话题，在主流观点之外，还存在着不少误解的看法。

一方面的原因有些人混用了强弱类型与动静类型，这有历史的原因，前面已经分析了。

另外还有一个同样重要的原因，即有人把弱类型等同于“完全没有隐式类型转换”了，这种想法并不对。

**事实上，强弱类型的概念中包含着部分相对主义的含义，强类型语言中也可能有隐式类型转换。** 

比如，Rust 语言为了实现“内存安全”的设计哲学，设计了很强大的类型系统，但是它里面也有隐式类型转换（自动解引用）。

问题在于：**怎么样的隐式类型转换是在合理范围内的？以及，某些表面的隐式类型转换，是否真的是隐式类型转换？** 

回到 Python 的例子，我们可以分析几种典型的用法。

比如，`"test"*3` 这种字符串“乘法”运算，虽然是两种类型的操作，但是并不涉及隐式类型转换转化。

比如，`x=10; x="test"` 先后给一个变量不同类型的赋值，表面上看 x 的类型变化了，用 type(x) 可以判断出不同，但是，Python 中的类型是跟值绑定的（右值绑定），并不是跟变量绑定的。

变量 x 准确地说只是变量名，是绑定到实际变量上的一个标签，它没有类型。type(x) 判断出的并不是 x 本身的类型，而是 x 指向的对象的类型，就像内置函数 id(x) 算出的也不是 x 本身的地址，而是实际的对象的地址。

比如，`1 + True` 这种数字与布尔类型的加法运算，也没有发生隐式类型转换。因为 Python 中的布尔类型其实是整型的子类，是同一种类型！（如果有疑问，可查阅 [PEP-285](https://github.com/chinesehuazhou/peps-cn/blob/master/StandardsTrack/285--%E6%B7%BB%E5%8A%A0%E5%B8%83%E5%B0%94%E7%B1%BB%E5%9E%8B.md)）

再比如，整数/布尔值与浮点数相加，在 Python 中也不需要作显式类型转换。但是，它的实现过程其实是用了数字的`__add__()` 方法，Python 中一切皆对象，数字对象也有自己的方法。（其它语言可不一定）

也就是说，**数字间的算术运算操作，其实是一个函数调用的过程，跟其它语言中的算术运算有着本质的区别。** 

**另外，不同的数字类型虽然在计算机存储层面有很大差异，但在人类眼中，它们是同一种类型（宽泛地分），所以就算发生了隐式类型转换，在逻辑上也是可以接受的。** 

最后，还有一个例子，即 Python 在 if/while 之后的真值判断，我之前分析过它的[实现原理](https://mp.weixin.qq.com/s/g6jZX0IdH9xpM7BMV3-ToQ) ，它会把其它类型的对象转化成布尔类型的值。

但是，它实际上也只是函数调用的结果（\_\_bool\_\_() 和 \_\_len\_\_()），是通过计算而得出的合理结果，并不属于隐式的强制类型转换，不在 untrapped errors 的范畴里。

**所以，严格来说，前面 5 个例子中都没有发生类型转换。** 浮点数和真值判断的例子，直观上看是发生了类型转换，但它们其实是 Python 的特性，是可控的、符合预期的、并没有对原有类型造成破坏。

退一步讲，若放宽“隐式类型转换”的含义，认为后两个例子发生了隐式类型转换，但是，它们是通过严谨的函数调用过程实现的，也不会出现 forbidden errors，所以还是属于强检查类型。

## 5、其它相关的问题

前文对概念的含义以及 Python 中的表现，作了细致的分析。接下来，为了逻辑与话题的完整性，我们还需要回答几个小问题：

（1）能否以“隐式类型转换”作为强弱类型的分类依据？

明确的分类定义应该以《[Type Systems](http://lucacardelli.name/Papers/TypeSystems.pdf)》为准，它有一套针对不同 error 的分类，强弱类型其实是对于 forbidden errors 的处理分类。隐式类型转换是其明显的特征，但并不是全部，也不是唯一的判断依据。

本文为了方便理解，使用这个主要特征来划分强弱类型，但是要强调，强类型不是没有隐式类型转换，而是可能有很少且合理的隐式类型转换。

（2）假如有其它解释器令 Python 支持广泛的隐式类型转换，那 Python 还是强类型语言么？

语言的标准规范就像是法律，而解释器是执法者。如果有错误的执法解释，那法律还是那个法律，应该改掉错误的执法行为；如果是法律本身有问题（造成了解释歧义和矛盾，或者该废弃），那就应该修改法律，保证它的确定性（要么是强类型，要么是弱类型）。

（3）为什么说 Javascript 是弱类型？

因为它的隐式类型转换非常多、非常复杂、非常过分！比如，Javascript 中`123 + null` 结果为 123，`123 + {} ` 结果为字符串“123[object Object]”。

另外，它的双等号“\=\=”除了有基本的比较操作，还可能发生多重的隐式类型转换，例如`true==['2']` 判断出的结果为 false，而`true==['1']` 的结果是 true，还有`[]==![]` 和`[undefined]==false` 的结果都为 true……

（4）C++ 是不是弱类型语言？

前文提到《流畅的Python》中将 C++ 归为强类型，但实际上它应该被归为弱类型。C++ 的类型转换是个非常复杂的话题，@樱雨楼 小姐姐曾写过一个系列文章做了系统论述，文章地址：[如何攻克 C++ 中复杂的类型转换？](https://mp.weixin.qq.com/s/lJiva3BUJXUV0cpX1dOe2Q) 、[详解 C++ 的隐式类型转换与函数重载！](https://mp.weixin.qq.com/s/S_1KPn_GWJ7hmLH19Dajfg)  、[谁说 C++ 的强制类型转换很难懂？](https://mp.weixin.qq.com/s/q3iwtvqMSp6lNC_ZR_SP6A) 

## 6、小结

强弱类型概念在网上有比较多的争议，不仅在 Python 是如此，在 C/C++ 之类的语言更甚。

**其实在学术上，这个概念早已有明确的定义，而且事实上也被很多人所接纳。** 

那些反对的声音大多是因为概念混用，因为他们忽略了另一种对语言进行分类的维度；同时，还有一部分值得注意的原因，即不能认为强类型等于“完全无隐式类型转换”或“只要没有xxx隐式类型转换”。

本文介绍了社区中对 Python 的主流分类，同时对几类疑似隐式类型转换的用法进行了分析，论证出它是一种强类型语言。

文章体现了作者一贯的刨根问底精神，这是“[Python为什么](https://github.com/chinesehuazhou/python-whydo)”系列文章的风格，如果你喜欢本文，欢迎订阅关注！

## 相关链接

[1] 谁告诉的你们Python是强类型语言！站出来，保证不打你！: https://blog.csdn.net/nokiaguy/article/details/108218260

[2] Strong versus Weak Typing: https://www.artima.com/intv/strongweak.html

[3] https://en.wikipedia.org/wiki/Strong_and_weak_typing#cite_note-2

[4] https://en.wikipedia.org/wiki/Strong_and_weak_typing#cite_note-3

[5] Type Systems: http://lucacardelli.name/Papers/TypeSystems.pdf

[6] C 程序员十诫: http://doc.cat-v.org/henry_spencer/ten-commandments

[7] Why is Python a dynamic language and also a strongly typed language: https://wiki.python.org/moin/Why%20is%20Python%20a%20dynamic%20language%20and%20also%20a%20strongly%20typed%20language

[8] PEP-285: https://github.com/chinesehuazhou/peps-cn/blob/master/StandardsTrack/285--%E6%B7%BB%E5%8A%A0%E5%B8%83%E5%B0%94%E7%B1%BB%E5%9E%8B.md

[9] Type Systems: http://lucacardelli.name/Papers/TypeSystems.pdf

[10] Python为什么: https://github.com/chinesehuazhou/python-whydo