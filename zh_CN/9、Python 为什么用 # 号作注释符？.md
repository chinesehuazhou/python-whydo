# Python 为什么用 # 号作注释符？

关于编程语言中的注释，其重要性基本上已为大家所共识。

然而关于注释的规范，这个话题就像我们之前聊过的缩进、终止符和命名方式一样，众口难调。

注释符通常可分为两种，即行注释与块注释（inline/block），它们在不同的编程语言中的符号可谓让人眼花缭乱。

比如行注释符，它至少有以下的 17 种之多（出自[维基百科](https://en.wikipedia.org/wiki/Comparison_of_programming_languages_(syntax))）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggvjzlowlzj212j0jt406.jpg)

其中两个最大的阵营分别是“//”与“#”号：

- // 注释符：ActionScript, C (C99), C++, C#, D, F#, Go, Java, JavaScript, Kotlin, Object Pascal (Delphi), Objective-C, PHP, Rust, Scala, SASS, Swift, Xojo
- \# 注释符：Bourne shell and other UNIX shells, Cobra, Perl, Python, Ruby, Seed7, Windows PowerShell, PHP, R, Make, Maple, Elixir, Nim


那么，**Python 为什么用“#”号作注释符，而不是“//”呢？ **  

这个问题恐怕没办法从解析的效率、符号的辨识度和输入的便利性等方面回答，因为它们基本上没有区别。

我没有找到官方的解释，但是从这些注释符的阵营中，已经不难得出一个较为合理的解释：

- // 注释符基本上被 C 语言家族所用

- \# 注释符则基本上是被 Shell 和其它脚本语言所用


Python 在创造之初，从 C 和 Shell 语言中借鉴了不少东西，但它是一种脚本语言，因此在注释符这个最为基础的语言要素上，就偏向了脚本语言的传统。

在某些“类脚本语言”中，比如 yaml、conf 和 ini 等格式的配置文件，它们大多也是采用脚本语言的“#”号作为注释符。

所以，**Python 行内注释符的选择，大概可以归结为一种历史原因，即借鉴了 Shell 脚本语言的写法。** 

相比于行注释符的多样，块注释符更加是让人眼花缭乱：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggvk1aknzsj216q0or40d.jpg)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggw5jn8dmhj213f0e3t9f.jpg)

大多数写法是我从未见过的，有些甚至是难以忍受的，槽点太多！

在这份表格里，我们看不到 Python，因为从严格意义上讲，**Python 并没有块注释符！** 

一般而言，我们在连续的每行内容前面加“#”号，达到块注释的效果。块注释被看作是多个行注释。

PEP-8 中是这么建议的：

> Each line of a block comment starts with a `#` and a single space (unless it is indented text inside the comment).

有人曾在 Twitter 上发问，为什么 Python 没有块注释符？

Guido 回复称，可以将多行字符串用作块注释：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1ggw2sja89hj20oc0bgq4a.jpg)

Python 的多行字符串用三对单引号或双引号表示，它还可以用作文档字符串（即Documentation Strings，简写docstrings）。

但是，将它当做多行注释符使用，在语义上则有点怪怪的——它表示的是一段字符串，虽然没有赋值给变量，不会生成代码，但是它并非语义上的注释。

由于脚本语言的特性，它允许我们写一段“无根的字符串”，在语法上没有问题，也没有负作用（negative effects），但是，如果把它作为注释使用，这就是一种副作用（side effects）了。

从这点上考虑，我虽然不反对有人把多行字符串写法用作块注释，但是我会更推荐大家使用“#”号作注释。

另外，对于无用的代码，最好的做法就是直接删除，如果后续发现有需要，再回退修改。详细的多行注释尽量放在文档字符串中，这样在核心代码中就会很少出现多行注释的情况了。

对于 Python 的注释符用法，大家是怎么想的呢？欢迎留言交流。



写在最后：本文属于“Python为什么”系列（Python猫出品），该系列主要关注 Python 的语法、设计和发展等话题，以一个个“为什么”式的问题为切入点，试着展现 Python 的迷人魅力。若你有其它感兴趣的话题，欢迎填在《[Python的十万个为什么？](https://mp.weixin.qq.com/s/jobdpO7BWWON0ruLNpn31Q) 》里的调查问卷中。部分话题会推出视频版，请在 B 站收看，观看地址：[视频地址](https://space.bilibili.com/97566624/video) 
