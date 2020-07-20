# Python 为什么使用缩进来划分代码块？

大家好，这是“Python为什么”系列节目的文字稿（**文末有观看地址**）。

本期话题：Python 为什么使用缩进来划分代码块，而不像其它语言使用花括号 {} 或者 “end” 之类的语法？

Python 的缩进是一个老生常谈的话题，经常有人会提及它，比如 Python 之父在上个月就恰好转发过一篇文章：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej2tpfbsfj20o70dmq4c.jpg)

因为这篇文章，Guido 还受邀在 Python Bytes 播客上录制了一期节目：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gemhl6sns3j20ug0jqtrw.jpg)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gen4u72qktj20v20kwgnm.jpg)



那么，Python 为什么要用缩进来划分代码块呢？

我收集梳理了几份材料，接下来将带大家一起揭晓 Python 这么做的设计原因：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gen4kbernmj20xm0dujtj.jpg)

1. 缩进语法，更加优雅
2. 缩进语法，更加清晰
3. 使用缩进，保持一致性，避免造成误读
4. 使用缩进，代码更紧凑，便于浏览，没有累赘
5. 使用缩进，已足够令解释器执行，没必要使用多余的符号
6. 强制缩进，源自古老的 ABC 语言，Guido 是这门语言的设计者之一
7. 其思想可能出自 Don Knuth（高德纳，著名计算机科学家，经典巨著《计算机程序设计艺术》的作者），他在 1974 年提出，在当时是很时髦和前卫的思想
8. 使用缩进，可以终结大括号放在函数名后面还是再换一行的终极争论（据说此话题能令不同派系的程序员大打出手！）



以上罗列的原因主要来源于 Python 官方文档、官方 wiki 以及 Python 之父的早期访谈。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej3n16gf9j215z0m90wy.jpg)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej3mqctuvj20yj0mf77q.jpg)

总而言之，Python 使用缩进语法，体现了它非常优雅、清晰的设计美学，对开发者、读者以及解释器，都极为友好。

实话说，我本人非常赞许 Python 的缩进，因此才第一眼就迷上了它，如果是使用了花括号之类的语法，那 Python 就顿时黯淡，也就食之无味了！

大家如何看待 Python 的缩进语法呢？请在评论区发表你的看法吧！

### 参考材料：

1、Guido 发的 twitter：[https://twitter.com/gvanrossum/status/1249549091584892928](https://twitter.com/gvanrossum/status/1249549091584892928)

2、Guido 转发的关于缩进的文章：[https://web.archive.org/web/20070922223915/http://www.secnetix.de/~olli/Python/block_indentation.hawk](https://web.archive.org/web/20070922223915/http://www.secnetix.de/~olli/Python/block_indentation.hawk)

3、Guido 作客 Python Bytes 播客节目，谈论缩进话题：[https://pythonbytes.fm/episodes/show/179/guido-van-rossum-drops-in-on-python-bytes](https://pythonbytes.fm/episodes/show/179/guido-van-rossum-drops-in-on-python-bytes)

4、播客节目的视频版：[https://www.youtube.com/watch?v=PlvvVXeU0yw&feature=youtu.be](https://www.youtube.com/watch?v=PlvvVXeU0yw&feature=youtu.be)

5、官方文档对缩进的解释：[https://docs.python.org/3/faq/design.html#why-does-python-use-indentation-for-grouping-of-statements](https://docs.python.org/3/faq/design.html#why-does-python-use-indentation-for-grouping-of-statements)

6、官方早期 wiki 的解释：[https://wiki.python.org/moin/Why%20separate%20sections%20by%20indentation%20instead%20of%20by%20brackets%20or%20%27end%27](https://wiki.python.org/moin/Why%20separate%20sections%20by%20indentation%20instead%20of%20by%20brackets%20or%20%27end%27)

7、池建强老师 MacTalk 文章：[https://mp.weixin.qq.com/s?__biz=MjM5ODQ2MDIyMA==&mid=2650715803&idx=1&sn=94cd823de3689f40d4ed4c541a1ee791](https://mp.weixin.qq.com/s?__biz=MjM5ODQ2MDIyMA==&mid=2650715803&idx=1&sn=94cd823de3689f40d4ed4c541a1ee791)

https://mp.weixin.qq.com/s/byhJnKoKSDnhUNUE9WWopw

### 视频地址：

1、B站：[https://www.bilibili.com/video/BV1UT4y137Yv/](https://www.bilibili.com/video/BV1UT4y137Yv/)

2、知乎：[https://www.zhihu.com/zvideo/1242823762849959936](https://www.zhihu.com/zvideo/1242823762849959936)



写在最后：本文属于“Python为什么”系列（Python猫出品），该系列主要关注 Python 的语法、设计和发展等话题，以一个个“为什么”式的问题为切入点，试着展现 Python 的迷人魅力。若你有其它感兴趣的话题，欢迎填在《[Python的十万个为什么？](https://mp.weixin.qq.com/s/jobdpO7BWWON0ruLNpn31Q) 》里的调查问卷中。部分话题会推出视频版，请在 B 站收看，观看地址：[视频地址](https://space.bilibili.com/97566624/video) 