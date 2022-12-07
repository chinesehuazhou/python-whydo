# Python 为什么使用缩进来划分代码块？

Python 为什么使用缩进来划分代码块，而不像其它语言使用花括号 {} 或者 “end” 之类的语法？

Python 的缩进是一个老生常谈的话题，经常有人会提及它，比如 Python 之父 2020 年 4 月曾在 Twitter 上转发过一篇文章[《Python: Myths about Indentation》](https://web.archive.org/web/20070922223915/http://www.secnetix.de/~olli/Python/block_indentation.hawk) 讨论这个话题。

出处：[Guido 发的 twitter](https://twitter.com/gvanrossum/status/1249549091584892928)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej2tpfbsfj20o70dmq4c.jpg)

因为这篇文章，Guido 还受邀在 Python Bytes 播客上录制了一期节目：[179: Guido van Rossum drops in on Python Bytes](https://pythonbytes.fm/episodes/show/179/guido-van-rossum-drops-in-on-python-bytes) 

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gemhl6sns3j20ug0jqtrw.jpg)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gen4u72qktj20v20kwgnm.jpg)

可在 Youtube 上观看[播客节目的视频版](https://www.youtube.com/watch?v=PlvvVXeU0yw&feature=youtu.be)

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

以上罗列的原因主要来源于 Python [官方文档对缩进的解释](https://docs.python.org/3/faq/design.html#why-does-python-use-indentation-for-grouping-of-statements)、[官方早期 wiki 的解释](https://wiki.python.org/moin/Why%20separate%20sections%20by%20indentation%20instead%20of%20by%20brackets%20or%20%27end%27)。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej3n16gf9j215z0m90wy.jpg)

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gej3mqctuvj20yj0mf77q.jpg)

以及 Python 之父的早期访谈：

> **问：为什么你在设计 Python 语言的时候采用了强制缩进的方式来划分程序域？**
> 
> 答：这种强制缩进，并不是什么新概念。当年我在 CWI 使用 ABC 语言编程的时候，人家就这么干的。我从 ABC 语言中继承了这个概念。不过 occam 这种很古老的语言也是用了这种方式，我不知道他们是谁先采用的，也许都是独创。这种思想也可能出自 Don Knuth（高德纳，著名计算机科学家，经典巨著《计算机程序设计艺术》的作者），他早在 1974 年就提出过这种做法。

总而言之，Python 使用缩进语法，体现了它非常优雅、清晰的设计美学，对开发者、读者以及解释器，都极为友好。

实话说，我本人非常赞许 Python 的缩进，因此才第一眼就迷上了它，如果是使用了花括号之类的语法，那 Python 就顿时黯淡，也就食之无味了！

---

附注：我在中文的技术圈里发了文章后，收到了大量的反对意见，这非常出乎意料，因此又写了下面这篇文章作进一步解释。

## 《Python 的缩进是不是反人类的设计？》

前些天，我写了《[Python为什么使用缩进来划分代码块？](https://mp.weixin.qq.com/s/byhJnKoKSDnhUNUE9WWopw)》，文中详细梳理了 Python 采用缩进语法的 8 大原因。我极其喜欢这种简洁优雅的风格，所以对它赞美有加。

然而文章发出去后，非常意外，竟收到了大量的反对意见！！（以往文章的互动不多，此次创下了记录）

我就不截图了，先摘录几则最刺眼的评论：

> 最大的缺陷就是这个缩进机制
>
> 去掉花括号是最愚蠢的设计
>
> 绝对是过度设计了，缺陷很大
>
> 最大的缺点就是缩进，太反人类了
>
> ……

对于这一类的评论，我认为他们是“睁着眼睛说瞎话”，颠倒是非黑白。Python 的缩进语法如此简洁好用，怎么就“过度设计/愚蠢/缺陷/反人类”了？

常言道众口难调，有人爱甜粽子有人爱咸粽子，但是对于咸甜味道，大家是有所共识的，不至于感官紊乱，大放厥词。

还有比较多的评论，认为缩进容易造成混乱：

> 代码多了，自己看着累，别人更难懂
>
> 眼花了，还是括号好些
>
> 还是{}或end更清晰
>
> 没有花括号老觉得没有安全感
>
> 缩进层次看不清楚
>
> 没有大括号不便于阅读
>
> 层次一多看起来很乱，不知哪层是哪层，要缩多少。到底退出循环没有。
>
> 看着明明缩进是对的，但运行时总是报错
>
> 用python写上十万行试试，到时候你就知道，什么叫混乱看不下去
>
> ……

现在主流的 IDE 工具都很强大，应该善于使用其基本功能，例如：设置显示空格字符、设置 tab 自动转化为空格、设置 tab 键为 4 个空格……同一层级的缩进还会有浅浅的竖线，在视觉上辅助阅读。

至于说层次过多、代码很长的情况，这本身就是一种代码坏味道！当出现过长的函数或者类时，**优秀的程序员** 第一时间该考虑的就是重构。推荐一本书《重构：改善既有代码的设计》，里面有**正道的价值观和详尽的方法论。** 

还有说点击右括号，可以看到匹配的左括号，会清晰。有这东西确实不错，但没有，我并不诉求。本身紧凑简洁的代码，缩进阅读会很快。

除了以上两大类的评论，我还收到以下几种比较有代表性的评论：

- 有人说“取消花括号会大大降低运行速度”、“这个特性鲁棒性太低了”。——这纯粹是臆想，让他们给出论证和例子，无果。别以为在哪里看到有人说 Python 慢，就想当然把锅扣到缩进的头上。
- 有人说“多人协同编辑时，有人用tab，有人用空格”。——我说开发团队应该统一规范，然后用 autopep8 之类的辅助工具。他说规范要不停花精力维护，要花成本。拜托！这年头还有人不重视代码规范，直接开除“猿籍”。
- 有人说“缩进没办法自动格式化代码”。——这在复制移动代码，或者要改变代码层级时，有此诉求。我一直用比较笨的方法调节（tab、shift+tab、加减空格），确实是比较笨，但是会比较有把握。刚在 PyCharm 里研究了一下，我发现它是支持自动格式化的，只是有个小小的问题要注意！

关于缩进的自动格式化，这里有两个例子，给大家演示一下：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gethpu5pcfj20gi07k0st.jpg)

上述例子，删除掉那行 if 条件语句，然后直接”ctrl+alt+l“作全局格式化，格式会出错。我们希望两句 print 向左缩进 4 格，但是 return 那句也会向左缩进。

在删除 if 那行后，如果我们只选中两行 print，作局部”ctrl+alt+l“格式化，那只有这两行会缩进，就没问题。

再看第二个例子，我们复制了一段新代码，但是它的缩进不对：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gethqzdjp2j20gc09uglv.jpg)

这时候，若直接“ctrl+alt+l”全局格式化，或者选中那三行再格式化，结果都不对！原因是第二个 if 的缩进格数小于 4 个，所以 PyCharm 认为它属于一级缩进（即不该有空格），所以自动格式化时就把它左移了。

如果选中它们，先按 tab 键右移（即新代码变成缩进大于 4 格，小于 8 格）：

![](http://ww1.sinaimg.cn/large/68b02e3bgy1geti2bt2rqj20fq09a0sz.jpg)

此时再作格式化的话，它们的缩进就跟第一层的 if 一致了（两层 if 是兄弟关系）。

同理，如果你想把新代码缩进到第一层 if 的内部（变为父子关系），那只需选中上图三行代码再 tab 键右移 4 格，之后格式化就可以了！

![](http://ww1.sinaimg.cn/large/68b02e3bgy1getibm3zl9j20g109cjro.jpg)

建议大家在编辑器里实操一下。等空了我会录制一期小视频（B 站搜“Python猫”），敬请留意。

除了上面的评论/观点之外，我们在微信交流群里也讨论了这个话题。@樱雨楼（[https://github.com/yingyulou](https://github.com/yingyulou)） 小姐姐的观点对我挺有启发。

![](https://raw.githubusercontent.com/chinesehuazhou/python-whydo/master/zh_CN/img/3-wechat-1.jpg)

![](https://raw.githubusercontent.com/chinesehuazhou/python-whydo/master/zh_CN/img/3-wechat-2.jpg)

![](https://raw.githubusercontent.com/chinesehuazhou/python-whydo/master/zh_CN/img/3-wechat-3.jpg)

![](https://raw.githubusercontent.com/chinesehuazhou/python-whydo/master/zh_CN/img/3-wechat-4.jpg)

![](https://raw.githubusercontent.com/chinesehuazhou/python-whydo/master/zh_CN/img/3-wechat-5.jpg)

群聊截图已记录如上，其中她提到了编程语言在设计上的“比较抽象和哲学”的两点：

- 缩进使得代码失去了形式语言里所谓的“上下文无关文法”，从而使得空格+数量的组合变得不再是可有可无的。
- block 作为一个“语法组分”，需要一个定界符，而空格一般不作为语法组分，所以就觉得少了些什么。

三言两语没法转述清楚，但她谈论缩进话题的视角确实令人耳目一新！

上次的文章发出后，也有不少小伙伴表示很喜欢 Python 的缩进。我本以为会听到很多这类的声音，没想到却是负面的评论更多。（也许更多认同的声音没有表现出来）

本文对几类典型的评论作出了回应，再次表达了我在这个话题上的关注和理解（以及情绪的抒发），希望也能给读者们带来一些思考和收获吧。