# 为什么 range() 生成的不是迭代器？range 到底是什么类型？

迭代器是 23 种设计模式中最常用的一种之一，在 Python 中随处可见它的身影，我们经常用到它，但是却不一定意识到它的存在。在关于迭代器的[系列文章](https://mp.weixin.qq.com/s/7MbRCn37fIIN42rLm6ho3g)中，我至少提到了 23 种生成迭代器的方法。有些方法是专门用于生成迭代器的，还有一些方法则是为了解决别的问题而“暗中”使用到迭代器。

在系统学习迭代器之前，我一直以为 range() 方法也是用于生成迭代器的，现在却突然发现，**它生成的只是可迭代对象，而并不是迭代器**！ （PS：Python2 中 range() 生成的是列表，本文基于Python3，生成的是可迭代对象）

于是，我有了这样的疑问：为什么 range() 不生成迭代器呢？在查找答案的过程中，我发现自己对 range 类型的认识存在一些误区。因此，本文将和大家全面地认识一下 range ，期待与你共同学习进步。

## 1、range() 是什么？

它的语法：range(start, stop [,step]) ；start 指的是计数起始值，默认是 0；stop 指的是计数结束值，但不包括 stop ；step 是步长，默认为 1，不可以为 0 。range() 方法生成一段左闭右开的整数范围。

```python
>>> a = range(5)  # 即 range(0,5)  
>>> a  
range(0, 5)  
>>> len(a)  
5  
>>> for x in a:  
>>>     print(x,end=" ")  
0 1 2 3 4
```

对于 range() 函数，有几个注意点：

- （1）它表示的是左闭右开区间；
- （2）它接收的参数必须是整数，可以是负数，但不能是浮点数等其它类型；
- （3）它是不可变的序列类型，可以进行判断元素、查找元素、切片等操作，但不能修改元素；
- （4）它是可迭代对象，却不是迭代器。

```python
# （1）左闭右开  
>>> for i in range(3, 6):  
>>>     print(i,end=" ")  
3 4 5  
  
# （2）参数类型  
>>> for i in range(-8, -2, 2):  
>>>     print(i,end=" ")  
-8 -6 -4  
>>> range(2.2)  
----------------------------  
TypeError    Traceback (most recent call last)  
...  
TypeError: 'float' object cannot be interpreted as an integer  
  
# （3）序列操作  
>>> b = range(1,10)  
>>> b[0]  
1  
>>> b[:-3]  
range(1, 7)  
>>> b[0] = 2  
TypeError  Traceback (most recent call last)  
...  
TypeError: 'range' object does not support item assignment  
  
# （4）不是迭代器  
>>> hasattr(range(3),'__iter__')  
True  
>>> hasattr(range(3),'__next__')  
False  
>>> hasattr(iter(range(3)),'__next__')  
True
```

## 2、 为什么range()不生产迭代器？

可以获得迭代器的内置方法很多，例如 zip() 、enumerate()、map()、filter() 和 reversed() 等等，但是像 range() 这样仅仅得到的是可迭代对象的方法就绝无仅有了（若有反例，欢迎告知）。这就是我存在知识误区的地方。

在 for-循环 遍历时，可迭代对象与迭代器的性能是一样的，即它们都是惰性求值的，在空间复杂度与时间复杂度上并无差异。我曾概括过两者的差别是“一同两不同”：相同的是都可惰性迭代，不同的是可迭代对象不支持自遍历（即next()方法），而迭代器本身不支持切片（即`__getitem__()` 方法）。

虽然有这些差别，但很难得出结论说它们哪个更优。现在微妙之处就在于，为什么给 5 种内置方法都设计了迭代器，偏偏给 range() 方法设计的就是可迭代对象呢？把它们都统一起来，不是更好么？

事实上，Pyhton 为了规范性就干过不少这种事，例如，Python2 中有 range() 和 xrange() 两种方法，而 Python3 就干掉了其中一种，还用了“李代桃僵”法。为什么不更规范点，令 range() 生成的是迭代器呢？

关于这个问题，我没找到官方解释，以下纯属**个人观点** 。

zip() 等方法都需要接收确定的可迭代对象的参数，是对它们的一种再加工的过程，因此也希望马上产出确定的结果来，所以 Python 开发者就设计了这个结果是迭代器。这样还有一个好处，即当作为参数的可迭代对象发生变化的时候，作为结果的迭代器因为是消耗型的，不会被错误地使用。

而 range() 方法就不同了，它接收的参数不是可迭代对象，本身是一种初次加工的过程，所以设计它为可迭代对象，既可以直接使用，也可以用于其它再加工用途。例如，zip() 等方法就完全可以接收 range 类型的参数。

```python
>>> for i in zip(range(1,6,2), range(2,7,2)):  
>>>    print(i, end="")  
(1, 2)(3, 4)(5, 6)
```

也就是说，range() 方法作为一种初级生产者，它生产的原料本身就有很大用途，早早把它变为迭代器的话，无疑是一种画蛇添足的行为。

对于这种解读，你是否觉得有道理呢？欢迎就这个话题与我探讨。

## 3、range 类型是什么？

以上是我对“为什么range()不产生迭代器”的一种解答。顺着这个思路，我研究了一下它产生的 range 对象，一研究就发现，这个 range 对象也并不简单。

首先奇怪的一点就是，它竟然是不可变序列！我从未注意过这一点。虽然说，我从未想过修改 range() 的值，但这一不可修改的特性还是令我惊讶。

翻看文档，官方是这样明确划分的——**有三种基本的序列类型：列表、元组和范围（range）对象。（There are three basic sequence types: lists, tuples, and range objects.）**

这我倒一直没注意，原来 range 类型居然跟列表和元组是一样地位的基础序列！我一直记挂着字符串是不可变的序列类型，不曾想，这里还有一位不可变的序列类型呢。

**那 range 序列跟其它序列类型有什么差异呢？**

普通序列都支持的操作有 12 种，在《[你真的知道Python的字符串是什么吗？](https://mp.weixin.qq.com/s/z-gSW6XJrAvUE5rS5XrAig)》这篇文章里提到过。range 序列只支持其中的 10 种，不支持进行加法拼接与乘法重复。

```python
>>> range(2) + range(3)  
-----------------------------------------  
TypeError  Traceback (most recent call last)  
...  
TypeError: unsupported operand type(s) for +: 'range' and 'range'  
  
>>> range(2)*2  
-----------------------------------------  
TypeError  Traceback (most recent call last)  
...  
TypeError: unsupported operand type(s) for *: 'range' and 'int'
```

那么问题来了：同样是不可变序列，为什么字符串和元组就支持上述两种操作，而偏偏 range 序列不支持呢？虽然不能直接修改不可变序列，但我们可以将它们拷贝到新的序列上进行操作啊，为何 range 对象连这都不支持呢？

且看官方文档的解释：

> …due to the fact that range objects can only represent sequences that follow a strict pattern and repetition and concatenation will usually violate that pattern.
> 
> 原因是 range 对象仅仅表示一个遵循着严格模式的序列，而重复与拼接通常会破坏这种模式…

问题的关键就在于 range 序列的 pattern，仔细想想，其实它表示的就是一个**等差数列**啊（喵，高中数学知识没忘…），拼接两个等差数列，或者重复拼接一个等差数列，想想确实不妥，这就是为啥 range 类型不支持这两个操作的原因了。由此推论，其它修改动作也会破坏等差数列结构，所以统统不给修改就是了。

## 4、小结

回顾全文，我得到了两个偏冷门的结论：**range 是可迭代对象而不是迭代器；range 对象是不可变的等差序列。**

若单纯看结论的话，你也许没有感触，或许还会说这没啥了不得啊。但如果我追问，为什么 range 不是迭代器呢，为什么 range 是不可变序列呢？对这俩问题，你是否还能答出个自圆其说的设计思想呢？（PS：我决定了，若有机会面试别人，我必要问这两个问题的嘿~）

由于 range 对象这细微而有意思的特性，我觉得这篇文章写得值了。