# 为什么 Python 的 f-string 可以连接字符串与数字？

> 本文出自“Python为什么”系列，归档在 Github 上：[https://github.com/chinesehuazhou/python-whydo](https://github.com/chinesehuazhou/python-whydo)

毫无疑问，Python 是一门**强类型**语言。**强类型**语言。**强类型**语言！（关于强弱类型话题，推荐阅读这篇 [技术科普文](https://mp.weixin.qq.com/s/GRq8Hg4jqIzCglHRdrWtww)）

这就意味着，不同类型的对象通常需要先做**显式地类型转化，** 然后才能进行某些操作。

下面以字符串和数字为例，看看强行操作会产生什么结果：

```python
>>> "Python猫" + 666
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate str (not "int") to str
```

它报类型错误了（TypeError），**说字符串只能连接（concatenate）字符串，不能连接 int 类型。** 这正是强类型语言的基本约束。

但是，如果我们先把数字“转化”成字符串类型，再执行“+”操作，就不会报错了：

```python
>>> "Python猫" + str(666)
'Python猫666'
```

上面的这个例子，对读者们来说，应该并不难理解。

由此，我们要引出一个问题：**如何在不作显式类型转化的情况下，进行字符串与数字类型的拼接呢？** 

在《[详解Python拼接字符串的七种方式](https://mp.weixin.qq.com/s/Whrd6NiD4Y2Z-YSCy4XJ1w)》这篇文章中，它梳理了七种拼接字符串的写法，我们可以逐个来试验一下。

> 几种字符串拼接方式：
>
> 1、格式化类：%、format()、template
>
> 2、拼接类：+、()、join()
>
> 3、插值类：f-string

为了节省篇幅，此处直接把可以顺利拼接的 4 种写法罗列如下：

```python
>>> "%s %d" % ("Python猫", 666)
'Python猫 666'

>>> from string import Template
>>> s = Template('${s1}${s2}')
>>> s.safe_substitute(s1='Python猫',s2=666)
'Python猫666'

>>> "Python猫{}".format(666)
'Python猫666'

>>> num = 666
>>> f"Python猫{num}"
'Python猫666'
```

第一种写法（即 % 格式化）来自古老的 C 语言，其中的“%d”是一个占位符，表示它将要接收一个整数，并格式化成字符串。

第二和第三种写法，它们是第一种写法的升级版，不同的是，它们的占位符是通用型的，不必指定“%s”、“%d”等等明确的类型。这两种写法中，数字类型的参数被传给特定的格式化方法（即 safe_substitute 与 format），在这些方法的内部，它们会作类型转化处理。

可以说，上述三种写法都不难理解，它们的意图都有迹可循。

但是，现在再看看最后一种写法，也就是 f-string 写法，似乎就不是那么明显了。

首先，在字符串内部，它并没有像“%格式化”那样指定占位符的类型；其次，所要拼接的数字并没有作为任何函数的参数来传递。

也就是说，在明面上根本看不出任何要作类型转化的意图。但是，由于我们已知 Python 是强类型语言，已知数字类型绝对不可能直接拼接到字符串里，因此，**只能说明 f-string 语法在底层作了某种类型转化的操作！** 

那么，我们就可以再提出一个新的问题：**f-string 语法在处理字符串与数字时，是如何实现数字的类型转化的呢？** 

也许有的读者会猜想它是调用了内置的 str() 或 repr()（或它们对应的魔术方法\_\_str\_\_() 与 \_\_repr\_\_()），从而实现类型转化，但是，答案并没有如此简单！

f-string 语法是在 Python 3.6 版本引入的。为了省事，我们直接找到 PEP-498 文档，在里面查阅看是否有关于实现原理的线索。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gns2a4h8rgj20q80dl3zl.jpg)

文档地址：https://www.python.org/dev/peps/pep-0498

PEP 里提到，f-string 的语法格式是这样的：

```python
f'<text> { <expression> <optional !s, !r, or !a> <optional : format specifier> } <text> ...'
```

其中，花括号里的内容就是要作格式化的内容，除去可选的“optional”部分后，“expression”部分就是真正要处理的内容。对应前文的例子，数字 666 就是一个 expression。

expression 会按 \_\_format\_\_ 协议进行格式化，但是并不会直接调用 \_\_format\_\_() 这个方法。

文档上指出，**实际的执行过程等效于`type(value).__format__(value, format_spec)` 或者  `format(value, format_spec)` 。** 

事实上，字符串对象的 foramt() 方法跟 Python 内置的 foramt() 函数，它们都会调用_\_format\_\_() 魔术方法，所以，**f-string 其实是前文中 format() 格式化写法的升级版。** 

在默认情况下，`format_spec` 是一个空字符串，而`format(value, "")` 的效果等同于`str(value)` ，因此，在不指定其它 format_spec 的情况下，**可以简单地认为 f-string 就是调用了 str() 来作的类型转化……** 

至此，我们看到了 f-string 的实现原理，明白了它在拼接字符串与数字时，效果等效于前文的 format() 格式化方法，也等效于使用 str() 进行类型转化。

写在最后：本文属于“[Python为什么](https://github.com/chinesehuazhou/python-whydo)”系列（Python猫出品），该系列主要关注 Python 的语法、设计和发展等话题，以一个个“为什么”式的问题为切入点，试着展现 Python 的迷人魅力。更多精彩文章，请移步 Github 查看，项目地址：[https://github.com/chinesehuazhou/python-whydo](https://github.com/chinesehuazhou/python-whydo)

