# Python 为什么只需一条语句“a,b=b,a”，就能直接交换两个变量？

从接触 Python 时起，我就觉得 Python 的元组解包（unpacking）挺有意思，非常简洁好用。

最显而易见的例子就是多重赋值，即在一条语句中同时给多个变量赋值：

```python
>>> x, y = 1, 2
>>> print(x, y)  # 结果：1 2
```

在此例中，赋值操作符“=”号的右侧的两个数字会被存入到一个元组中，即变成 (1,2)，然后再被解包，依次赋值给“=”号左侧的两个变量。

如果我们直接写`x = 1,2` ，然后打印出 x，或者在“=”号右侧写成一个元组，就能证实到这一点：

```python
>>> x = 1, 2
>>> print(x)     # 结果：(1, 2)
>>> x, y = (1, 2)
>>> print(x, y)  # 结果：1 2
```

一些博客或公众号文章在介绍到这个特性时，通常会顺着举一个例子，即基于两个变量，直接交换它们的值：

```python
>>> x, y = 1, 2
>>> x, y = y, x
>>> print(x, y) # 结果：2 1
```

一般而言，交换两个变量的操作需要引入第三个变量。道理很简单，如果要交换两个杯子中所装的水，自然会需要第三个容器作为中转。

然而，Python 的写法并不需要借助中间变量，它的形式就跟前面的解包赋值一样。正因为这个形式相似，很多人就误以为 Python 的变量交换操作也是基于解包操作。

但是，事实是否如此呢？

我搜索了一番，发现有人试图回答过这个问题，但是他们的回答基本不够全面。（当然，有不少是错误的答案，还有更多人只是知其然，却从未想过要知其所以然）

先把本文的答案放出来吧：**Python 的交换变量操作不完全基于解包操作，有时候是，有时候不是！** 

有没有觉得这个答案很神奇呢？是不是闻所未闻？！

到底怎么回事呢？先来看看标题中最简单的两个变量的情况，我们上`dis` 大杀器看看编译的字节码：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggpm8nf7soj20y80dvt9q.jpg)

上图开了两个窗口，可以方便比较“a,b=b,a”与“a,b=1,2”的不同：

- “a,b=b,a”操作：两个 LOAD_FAST 是从局部作用域中读取变量的引用，并存入栈中，接着是最关键的 ROT_TWO 操作，它会交换两个变量的引用值，然后两个 STORE_FAST 是将栈中的变量写入局部作用域中。
- “a,b=1,2”操作：第一步 LOAD_CONST 把“=”号右侧的两个数字作为元组放到栈中，第二步 UNPACK_SEQUENCE 是序列解包，接着把解包结果写入局部作用域的变量上。

很明显，形式相似的两种写法实际上完成的操作并不相同。在交换变量的操作中，并没有装包和解包的步骤！

ROT_TWO 指令是 CPython 解释器实现的对于栈顶两个元素的快捷操作，改变它们指向的引用对象。

还有两个类似的指令是 ROT_THREE 和 ROT_FOUR，分别是快捷交换三和四个变量（摘自：ceval.c 文件，最新的 3.9 分支）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggpo6w29pxj20hh0hhq3s.jpg)

预定义的栈顶操作如下：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggpoi0nkscj20l50an3zd.jpg)

查看官方文档中对于这几个指令的解释，其中 ROT_FOUR 是 3.8 版本新加的：

> - `ROT_TWO`
>
>   Swaps the two top-most stack items.
>
>
> - `ROT_THREE`
>
>   Lifts second and third stack item one position up, moves top down to position three.
>
>
> - `ROT_FOUR`
>
>   Lifts second, third and forth stack items one position up, moves top down to position four.
>   New in version 3.8.

CPython 应该是以为这几种变量的交换操作很常见，因此才提供了专门的优化指令。就像 [-5,256] 这些小整数被预先放到了整数池里一样。

对于更多变量的交换操作，实际上则会用到前面说的解包操作：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggppuj44dfj20hy0dhaai.jpg)

截图中的 BUILD_TUPLE 指令会将给定数量的栈顶元素创建成元组，然后被 UNPACK_SEQUENCE 指令解包，再依次赋值。

值得一提的是，此处之所以比前面的“a,b=1,2”多出一个 build 操作，是因为每个变量的 LOAD_FAST 需要先单独入栈，无法直接被组合成 LOAD_CONST 入栈。也就是说，“=”号右侧有变量时，不会出现前文中的  LOAD_CONST 一个元组的情况。

最后还有一个值得一提的细节，那几个指令是跟栈中元素的数量有关，而不是跟赋值语句中实际交换的变量数有关。看一个例子就明白了：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggpr5j60odj20hy090aa7.jpg)

分析至此，你应该明白前文中的结论是怎么回事了吧？

我们稍微总结一下：

- Python 能在一条语句中实现多重赋值，这是利用了序列解包的特性
- Python 能在一条语句中实现变量交换，不需引入中间变量，在变量数少于 4 个时（3.8 版本起是少于 5 个），CPython 是利用了 ROT_* 指令来交换栈中的元素，当变量数超出时，则是利用了序列解包的特性。
- 序列解包是 Python 的一大特性，但是在本文的例子中，CPython 解释器在小小的操作中还提供了几个优化的指令，这绝对会超出大多数人的认知