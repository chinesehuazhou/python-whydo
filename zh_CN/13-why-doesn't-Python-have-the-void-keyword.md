# Python 为什么没有 void 关键字？

`void` 是编程语言中最常见的关键字之一，从字面上理解，它是“空的、空集、空白”的意思，**最常用于** 表示函数的一种返回值类型。

维基百科上有一个定义：

> The void type, in several programming languages derived from C and Algol68, is the type for the result of a function that returns normally, but does not provide a result value to its caller. 
>
> 在 C、Algol68 及它们所派生的几种编程语言中，void 类型是函数正常返回的一种类型，但是不会给调用者返回一个值。

**简单来说，void 是一种类型（type），但是没有具体的值（value）。** 

这到底是什么意思呢？

以 Python 的几种常见类型为例，我们可以从对比中看出规律：int 是一种表示整数的类型，它有无限个可能的整数值；bool 是一种布尔类型，它有两个可能的值（True 和 False）；NoneType 是一种表示 None 的类型，它只有一个值（None）。

至于 void，它是一种更为抽象的特殊类型，但是不包含任何值。

介绍完概念上的含义，我们就可以进入正题了。标题中的问题可以进一步分解成两个：

- **其它语言为什么要使用 void 关键字？** 
- **Python 为什么不设计出 void 关键字？** 

对于第一个问题，我们以 C/C++ 为例，先看看 void 的两种使用场景（PS：此处只考虑函数的用法，不考虑指针的用法，因为 Python 没有指针）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ghxnocqx7uj20w0066mxe.jpg)

**当 void 用在函数的参数位置时，它表示该函数不需要传参。** 

最初 C 语言的`f()` 表示参数数量不确定，为了另外表达“不需要参数”的语义，所以引入`f(void)` 作为限定。后来的语言（包括 Python）基本不在参数中使用 void，而是直接用`f()` 表示不需传参。C++ 为了兼容 C，所以才同时支持这两种语法。

**当 void 用在函数前作修饰时，它表示该函数没有返回值。** 

在 C 语言中，若不声明返回类型，则`f()` 函数在编译后会返回整型的值。为了避免混乱，当不需要返回值时，就使用`void f()` 来作限定。

同时，更主要的是，它还起到了占位符的作用，表明一个函数的类型是已知的，这对代码可读性和编译都有所帮助。

void 作为函数的空返回值类型，这种用法在 C++/Java 中也被继承了。另外，在 Javascript 中也有 void 的身影，只不过它成了一种操作符，起到了完全不同的作用，此处不表。

但是，**Python 从头到尾都没有 void 关键字。** 

为什么会这样？难道是因为在 Python 中不存在其它语言所面对的问题么？还是说，Python 中有自己的一套解决方案？

仍以跟函数相关的两种用法为例作分析吧。

在表示函数不需传参时，`f(void)` 这种写法根本就是多余的，所以 Python 使用了最简单明了的无参式写法`f()` 。

至于返回值类型的用法，在我们定义出一个函数时，例如最简单的`def func():pass` ，为了让它的调用结果`func()` 是一个合法的对象，那它必须具有一个有效的类型（type）。

这应该是以类型为基的编程语言都会遇到的共性问题，Python 也不例外。

这个时候，如果函数本身没有显式地 return 出一个对象的话，就有两种可能的解决办法：

- 方法一，即声明该函数为 void 类型，像 C 和其它语言所做的那样，只要能通过类型检查即可
- 方法二，则是 Python 所用的方法，即令解释器隐式地返回一个 None 对象，也就是令函数默认得到一个 NoneType 类型，再用于类型检查（PS：Javascript 也类似，只不过它默认返回的是 undefined，它不是一个对象，而是一种表示“未定义”的类型，类似于 void）

**简单而言，Python 的设计思路是直接复用已有的 NoneType 类型，并让解释器来填补缺失掉的函数类型。** 

关于 Python 解释器的这个隐式填补过程，我已在上一篇《[Python 函数为什么会默认返回 None？](https://mp.weixin.qq.com/s/e31XEz7z2Wl2jZaaYYGgbA)》文章详细分析过，感兴趣的同学可去查阅。

这样做的好处至少有两点：**一是没有引入新的 void 类型和关键字；二是不需要程序员在函数前声明返回类型，这就跟有显式返回值的写法保持了一致。** 

试想一下，如果 Python 不让函数默认有返回值的话，就可能要写成 `void def func():...` 这样的形式，那它就变成了函数定义时的一种特例。与另一种特例函数相比，即异步函数`asyc def func():... ` ，就可能引起混乱。

总体而言，Python 似乎认为 void 空类型不是那么有存在的必要，似乎 NoneType 类型就足够了，而当缺少返回值时，让解释器统一注入是极为方便的，因此才出现了我们看到的现状。

至此，文章标题的问题算是圆满回答了。

最后，让我们开始进入 ending 吧：本文明面上是以“Python 为什么没有 void 关键字”为切入点，然而，它实际上瞄准的却是“Python 为什么需要返回 None”的问题。

在《[Python 函数为什么会默认返回 None？](https://mp.weixin.qq.com/s/e31XEz7z2Wl2jZaaYYGgbA)》这篇文章中，我介绍了  Python 中函数默认返回 None 的机制，它是属于“how can”的内容。但是为什么要默认返回 None 呢？这则是属于“why need”或者“why should”的问题，而它需要从 void 关键字的缺失开始谈起……

那么，为什么 Python 没有 void 关键字呢？请往上翻，重新阅读本文……

