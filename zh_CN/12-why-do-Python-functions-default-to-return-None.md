# Python 函数为什么会默认返回 None？

Python 有一项默认的做法，很多编程语言都没有——**它的所有函数都会有一个返回值，不管你有没有写 return 语句。** 

在正式开始之前，我们就用之前讨论过的 [pass语句](https://mp.weixin.qq.com/s/GpBOO0MLsc6g_mNq85Zy0A) 和 [...对象](https://mp.weixin.qq.com/s/SOSN_p74eDHv3tJnSJIZfg) 作为例子，看看 Python 的函数是怎样“无中生有”的：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ghrfq5bfplj20ep09h40k.jpg)

可以看出，我们定义的两个函数都没有写任何的 return 语句，但是在函数调用后，都能取到一个返回值。

它们的执行效果跟直接写 return 语句相比，是完全相同的：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ghrfxdjvdsj20ep09hwfm.jpg)

这 4 个例子属于两种类型：一种没有写 return，但是都有隐藏的 return 返回值；一种写了 return，而且实际也有返回值。

**也就是说，后者在语义和行为上表现一致，前者虽然在语义上缺失，但是却有实际的行为和结果；后者的行为是显性的，前者却是隐性的。** 

《Python之禅》中有一句“显性胜于隐性（Explicit is better than implicit）”，但是，出于简洁和便利的考虑（Simple is better than complex），实际上 Python 中有很多行为都是隐性的，会把一些在语法层面的事交给解释器去完成。

上一期的 [真值判断](https://mp.weixin.qq.com/s/g6jZX0IdH9xpM7BMV3-ToQ) 是隐性的行为，本文前两个例子也是如此。

使用`dis` 查看字节码，就可以看到其背后的小动作：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ghrg9pcsycj20ep09hjt4.jpg)

在这个对比图中，可以看出上述 4 个函数的解释器指令一模一样！

不管有没有写 return，它们都会执行 return 的逻辑，而且默认的返回值就是  None。

那么，问题来了：**Python 的函数为什么能默认返回 None 呢？它是如何实现的呢？** 

答案就在解释器中，当 CPython 解释器执行到函数的最后一个代码块时，若发现没有返回值，它就会主动地加上一个 Py_None 值返回（出自：compile.c）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ghrgwcawewj20ep09htag.jpg)

**也就是说，如果定义的函数没有返回值，Python 解释器就会（强行地）默认给我们注入一段返回逻辑！** 

对于解释器的这种附赠的服务，大家是觉得很贴心，还是嫌弃它多事呢？

这样的做法似乎没多少好处，但似乎也没有坏处？

那么，这就会引出新的问题：**Python 为什么要求函数都要有返回值呢？为什么它不像某些语言那样，提供一个 void 关键字，支持定义无返回值的空函数呢？**  

请查看本系列的下一篇文章！