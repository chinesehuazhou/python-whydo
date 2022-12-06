# Python 为什么会有个奇怪的“...”对象？

在写上一篇《[Python 为什么要有 pass 语句？](https://mp.weixin.qq.com/s/GpBOO0MLsc6g_mNq85Zy0A)》时，我想到一种特别的写法，很多人会把它当成 pass 语句的替代。在文章发布后，果然有三条留言提及了它。

所谓特别的写法就是下面这个：

```python
# 用 ... 替代 pass
def foo():
	...
```

它是中文标点符号中的半个省略号，也即由英文的 3 个点组成。如果你是第一次看到，很可能会觉得奇怪：这玩意是怎么回事？（PS：如果你知道它，仔细看过本文后，你同样可能会觉得奇怪！）

## 1、认识一下“...”内置常量

事实上，它是 Python 3 中的一个内置对象，有个正式的名字叫作——Ellipsis，翻译成中文就是“省略号”。

更准确地说，它是一个[内置常量](https://docs.python.org/3/library/constants.html)（Built-in Constant），是 6 大内置常量之一（另外几个是 None、False、True、NotImplemented、\_\_debug\_\_）。

关于这个对象的基础性质，下面给出了一张截图，你们应该能明白我的意思：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gh889qm0mnj20n00klwf5.jpg)

“...“并不神秘，它只是一个可能不多见的符号型对象而已。用它替换 pass，在语法上并不会报错，因为 Python 允许一个对象不被赋值引用。

**严格来说，** 这是旁门左道，在语义上站不住脚——把“...”或其它常量或已被赋值的变量放在一个空的缩进代码块中，它们是与动作无关的，只能表达出“这有个没用的对象，不用管它”。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gh9ej0zr8yj20gt0fdjrl.jpg)

Python 允许这些不被实际使用的对象存在，然而聪明的 IDE 应该会有所提示（我用的是 Pycharm），比如告诉你：`Statement seems to have no effect ` 。

但是“...”这个常量似乎受到了特殊对待，我的 IDE 上没有作提示。

很多人已经习惯上把它当成 pass 那样的空操作来用了（在最早引入它的邮件组讨论中，就是举了这种用法的例子）。但我本人还是倾向于使用 pass，不知道你是怎么想的呢？

## 2、奇怪的 Ellipsis 和 ... 

... 在 [PEP-3100](https://www.python.org/dev/peps/pep-3100/) 中被引入，最早合入在 Python 3.0 版本，而 Ellipsis 则在更早的版本中就已包含。

虽然官方说它们是同一个对象的两种写法，而且说成是单例的（singleton），但我还发现一个非常奇怪的现象，与文档的描述是冲突的：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gh89fp92x2j20j40e0aad.jpg)

如你所见，赋值给 ... 时会报错`SyntaxError: cannot assign to Ellipsis` ，然而 Ellipsis 却可以被赋值，它们的行为根本就不同嘛！被赋值之后，Ellipsis 的内存地址以及类型属性都改变了，它成了一个“变量”，不再是常量。

作为对比，给 True 或 None 之类的常量赋值时，会报错`SyntaxError: cannot assign to XXX`，但是给 NotImplemented 常量赋值时不会报错。

众所周知，在 Python 2 中也可以给布尔对象（True/False）赋值，然而 Python 3 已经把它们改造成不可修改的。

所以有一种可能的解释：**Ellipsis 和 NotImplemented 是 Python 2 时代的遗留产物，为了兼容性或者只是因为核心开发者遗漏了，所以它们在当前版本（3.8）中还可以被赋值修改。** 

... 出生在 Python 3 的时代，或许在将来会完全取代 Ellipsis。目前两者共存，它们不一致的行为值得我们注意。我的建议：只使用"..."吧，就当 Ellipsis 已经被淘汰了。

## 3、为什么要使用“...”对象？

接下来，让我们回到标题的问题：**Python 为什么要使用“...”对象？** 

这里就只聚焦于 Python 3 的“...”了，不去追溯 Ellipsis 的历史和现状。

之所以会问这个问题，我的意图是想知道：**它有什么用处，能够解决什么问题？从而窥探到 Python 语言设计中的更多细节。**  

大概有如下的几种答案：

### （1）扩展切片语法

官方文档中给出了这样的说明：

> Special value used mostly in conjunction with extended slicing syntax for user-defined container data types.
>
> 这是个特殊的值，通常跟扩展的切片语法相结合，用在自定义的数据类型容器上。

文档中没有给出具体实现的例子，但用它结合\_\_getitem\_\_() 和 slice() 内置函数，可以实现类似于 [1, ..., 7] 取出 7 个数字的切片片段的效果。

由于它主要用在数据操作上，可能大部分人很少接触。听说 Numpy 把它用在了一些语法糖用法上，如果你在用 Numpy 的话，可以探索一下都有哪些玩法？

### （2）表达“未完成的代码”语义

... 可以被用作占位符，也就是我在《[Python 为什么要有 pass 语句？](https://mp.weixin.qq.com/s/GpBOO0MLsc6g_mNq85Zy0A)》中提到 pass 的作用。前文中对此已有部分分析。

有人觉得这样很 cute，这种想法获得了 Python 之父 Guido 的[支持](https://mail.python.org/pipermail/python-3000/2008-January/011793.html) ：

![](http://ww1.sinaimg.cn/large/68b02e3bly1ghb5rjgtc3j20lz0aydg8.jpg)

### （3）Type Hint 用法

Python 3.5 引入的 Type Hint 是“...”的主要使用场合。

它可以表示不定长的参数，比如`Tuple[int, ...]` 表示一个元组，其元素是 int 类型，但数量不限。

它还可以表示不确定的变量类型，比如文档中给出的这个例子：

```python
from typing import TypeVar, Generic

T = TypeVar('T')

def fun_1(x: T) -> T: ...  # T here
def fun_2(x: T) -> T: ...  # and here could be different

fun_1(1)                   # This is OK, T is inferred to be int
fun_2('a')                 # This is also OK, now T is str
```

T 在函数定义时无法确定，当函数被调用时，T 的实际类型才被确定。

在 .pyi 格式的文件中，... 随处可见。这是一种存根文件（stub file），主要用于存放 Python 模块的类型提示信息，给 mypy、pytype 之类的[类型检查工具](https://mp.weixin.qq.com/s/l8FyTp_oxdBEcg95fgWrDA) 以及 IDE 来作静态代码检查。

### （4）表示无限循环

最后，我认为有一个非常终极的原因，除了引入“...”来表示，没有更好的方法。

先看看两个例子：

![](http://ww1.sinaimg.cn/large/68b02e3bly1ghbg6ap2fbj20ie0az74g.jpg)

两个例子的结果中都出现了“...”，它表示的是什么东西呢？

对于列表和字典这样的容器，如果其内部元素是可变对象的话，则存储的是对可变对象的引用。那么，当其内部元素又引用容器自身时，就会递归地出现无限循环引用。

无限循环是无法穷尽地表示出来的，Python 中用 ... 来表示，比较形象易懂，除了它，恐怕没有更好的选择。

最后，我们来总结一下本文的内容：

- ... 是 Python 3 中的一个内置常量，它是一个单例对象，虽然是 Python 2 中就有的 Ellipsis 的别称，但它的性质已经跟旧对象分道扬镳
- ... 可以替代 pass 语句作为占位符使用，但是它作为一个常量对象，在占位符语义上并不严谨。很多人已经在习惯上接受它了，不妨一用
- ... 在 Python 中不少的使用场景，除了占位符用法，还可以支持扩展切片语法、丰富 Type Hint 类型检查，以及表示容器对象的无限循环
- ... 对大多数人来说，可能并不多见（有人还可能因为它是一种符号特例而排斥它），但它的存在，有些时候能够带来便利。

希望本文能让更多人认识它，那么文章的目的也就达成了~
