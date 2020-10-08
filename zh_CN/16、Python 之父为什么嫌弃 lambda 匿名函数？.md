# Python 之父为什么嫌弃 lambda 匿名函数？

Python 支持 lambda 匿名函数，其扩展的 BNF 表示法是`lambda_expr ::= "lambda" [parameter_list] ":" expression` ，也就是 `lambda 参数序列:表达式`。

这是一种便捷的函数定义方式，若翻译成我们熟知的函数形式，会是这个样子：

```Python
def <lambda>(parameter_list):
    return expression
```

也就是说，**Python 中的 lambda 函数是一种可接收多个参数的函数，返回值是一个表达式。** 

它最大的好处是单行简洁，不需要函数命名与换行缩进。

不得不说，匿名函数有时候是挺好用的，比如下文会介绍到的一些常见用法，它因此受到了不少人的推崇。

但是，匿名函数通常也会造成代码难以阅读，容易被人滥用，再加上 Python 只提供了对它的“残疾的”支持，所以又有一些观点不建议使用匿名函数。

事实上，**Python 之父 Guido van Rossum 就属于“不推荐使用派”，他甚至曾经（2005年）想要移除 lambda，只不过最后妥协了。** 

![](http://ww1.sinaimg.cn/large/68b02e3bgy1giwcu579v2j20v60gmacb.jpg)

出处：[https://www.artima.com/weblogs/viewpost.jsp?thread=98196](https://www.artima.com/weblogs/viewpost.jsp?thread=98196)

lambda 这一个由其他开发者贡献进来的特性（借鉴自 lisp 语言），存在了十多年，但是却被这门语言的创造者（兼首席设计师）所嫌弃，最后竟然还奇迹般地幸存了下来，对于这个故事，大家是否觉得挺有戏剧性的？

接下来，本文就仔细聊一聊这个处境尴尬却生命力顽强的 lambda 匿名函数吧！

## 1、lambda 怎么使用？

lambda 函数通常的用法是结合 map()、reduce()、filter()、sorted() 等函数一起使用，这些函数的共性是：**都可以接收其它函数作为参数。** 

例如下面的几个例子：

```python
my_list = [3, 1, 5, 4, 10]

# 元素全加1，结果：[4, 2, 6, 5, 11]
list(map(lambda i:i+1, my_list)) 

# 过滤小于10的元素，结果：[3, 1, 5, 4]
list(filter(lambda i:i<10, my_list)) 

# 元素累加，结果：33
from functools import reduce
reduce(lambda i,j:i+j, my_list, 10)

# 字典按值排序，结果：[('b', 1), ('a', 3), ('d', 4), ('c', 5)]
my_dict = {'a':3, 'b':1, 'c':5, 'd':4}
sorted(my_dict.items(), key=lambda item:item[1])
```

初学者也许会觉得代码读不懂，但是只要记住“**Python中的函数是一等公民**”，知道一个函数可以被作为另一个函数的参数或者返回值，就容易理解了。

比如对于 map() 函数的例子，你可以理解成这个形式：

```python
my_func = lambda i:i+1
list(map(my_func, my_list)) 
```

甚至可以还原成普通的函数：

```python
def add_one(i):
	return i+1

list(map(add_one, my_list)) 
```

map() 函数的第一个参数是一个函数，第二个参数是一个可迭代对象。这第一个参数会迭代地调用第二个参数中的元素，调用的结果以迭代器的形式返回。

这个例子使用了 list()，是为了方便一次性取出迭代器中的元素，直观地展示出来，在实际使用中，很可能会是基于迭代器的形式。

由这几种用法，我们可以总结出 lambda 函数的使用规律：

- 它出现在需要使用函数的地方
- 它适合实现简单的功能
- 它是一次性的用途，不能在其它地方复用
- 它一般不会被独立使用，总是作为其它函数的一部分

## 2、lambda 有什么问题？

由上面的用法可以看出，使用 lambda 函数的代码比较紧凑简洁，所以有人称它体现了“Pythonic”的优雅思想。

但是，lambda 函数有没有什么缺陷呢？

有！当前的 lambda 函数有一个最大的问题，即只支持单行表达式，无法实现丰富的功能，例如无法在函数创建时使用语句（statement），无法使用 if-else 的判断条件，也无法使用 try-except 的异常捕获机制，等等。

这极大地限制了它的能力，导致了它被人诟病为“残疾的”。

**从技术实现的角度上看，** 这个问题可以通过语法层面的设计来解决。

在当年的邮件组讨论中，有人提出过一些解决思路，比如这封邮件：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1giwct19tfpj20q30bsjsi.jpg)

出处：[https://mail.python.org/pipermail/python-dev/2006-February/060654.html](https://mail.python.org/pipermail/python-dev/2006-February/060654.html)

它提出了一个`lambda args::suite` 的想法，支持写成这样的形式：

```python
ss = sorted(seq, key=(lambda x::
            try: return abs(x)
            except TypeError: return 0))
```

但是，Guido 很快就否决了这个思路。

他写了一篇文章《Language Design Is Not Just Solving Puzzles》来回应：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1giwcr4klfnj20yo0ii777.jpg)

出处：[https://www.artima.com/weblogs/viewpost.jsp?thread=147358](https://www.artima.com/weblogs/viewpost.jsp?thread=147358)

其基本观点是：**不能光顾着解决一个问题/实现某种功能，就引入缺乏“Pythonicity”的语言设计。** 

那么，为什么 Guido 会认为这是一种不好的设计呢？

我试着概括一下，理由是：

- 双冒号“::”凭空在此引入，但是跟切片语法中的“::”完全不同，而且跟 C++/Perl 中的作用域操作符用法也不同
- 即使不用双冒号，用其它符号表示（比如单冒号），还是难以接受，因为都会在一个表达式中嵌入缩进代码块。这就跟使用花括号和 begin/end 关键字来作语句分组（statement grouping）一样，都令人难以接受
- 在 lambda 中实现其它功能并不重要，这还会让解析器变得复杂（需区分是否有缩进、记录缩进级别），显得小题大做了

简而言之，**他认为简洁友好的用户体验更为重要，如果简洁的语法无法满足需求，就应该写成具名函数的形式，而非设计出复杂的匿名函数。** 

## 3、为什么 Guido 想移除 lambda？

上文提到的多行 lambda 语句（multi-statement lambda）事件发生在 2006 年，我们看到了 Guido 不想给 lambda 引入复杂设计的原因。

但是，早在 2005 年，Guido 就曾经想要从 Python 移除 lambda，他对它的“嫌弃”是一个“历史悠久”的传统……

在《The fate of reduce() in Python 3000》这篇短文中，Guido 提出要一次性移除 reduce()、map()、filter() 以及 lambda。

移除 lambda 的理由如下：

- 对于不熟悉 Lisp 或 Scheme 的用户，lambda 这名字容易造成混淆
- 很多人误以为匿名函数能做嵌套函数不能做的事，但其实并无区别；存在lambda，就会造成不必要的选择，减少选择，可以简化思维
- 移除 reduce()、map() 和 filter() 后，就没必要写简短的局部函数了

回顾一下我们在前文中总结出的 lambda 的 4 条使用规律，可以发现它跟几个高阶函数（可以接收其它函数作为参数的函数）有较强的“寄生关系”，如果它们能移除了的话，lambda 确实就没有什么独立存留的意义了。

那么，为什么 Guido 觉得应该移除那几个高阶函数呢？

主要的理由有：

- 可以替换成更加清晰的列表解析式或者生成器表达式，例如 filter(P,S) 可以写成 [x for x in S if P(x)]，map(F, S) 写成 [F(x) for x in S]
- 至于 reduce()，他说这是最讨厌的，除了涉及 + 和 * 的少数用法，其它时候他总要拿出纸笔来画图解才能搞清楚。除了显式地写循环，他还针对 reduce() 的几种用法而提出了几个替代用法，包括引入新的 any() 和 all() 函数

总体而言，Guido 的想法暗合了《The Zen of Python》中的这一条：**There should be one-- and preferably only one --obvious way to do it。** 

但是回到现实，为了照顾某些人的习惯，以及对兼容性的考虑，Guido 最后保守地放弃了“清理异端”的计划。

因此，lambda 得以从 Python 最高独裁者的手上死里逃生。直到一年后，它试图兴风作浪（多行表达式），却惨遭镇压。

我仿佛听到了 Guido 的内心 OS：当初我想删除东西的时候，你们百般阻挠，现在你们想添加东西，哼，没门！……

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gixfz6d2qjj20rs0m8dgr.jpg)

哈哈，开了个玩笑。

Guido 的所有决定都体现了他的 Pythonic 设计美学、自恰的逻辑一致性以及对社区声音的权衡。

对于 lambda，我认可他的观点，而通过回溯语法发展的历史，我觉得自己对于 Python 的理解变得更为丰富了。不知道你可有同感？
