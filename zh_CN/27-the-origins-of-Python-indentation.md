# Python 缩进语法的起源：上世纪 60-70 年代的大胆创意

上个月，Python 之父 Guido van Rossum 在推特上转发了一篇文章《[The Origins of Python](https://inference-review.com/article/the-origins-of-python)》，引起了我的强烈兴趣。

![Guido的推文](http://tva1.sinaimg.cn/large/68b02e3bgy1h8zp59nxw6j20ge05stab.jpg)

众所周知，Guido 在 1989 年圣诞节期间开始创造 Python，当时他就职于荷兰数学和计算机科学研究学会（简称 CWI），曾参与设计与实现了一门用于教学的 ABC 语言。这段工作经历以及 ABC 语言的某些设计思想对 Python 有着重要的影响。

文章标题是“Python 的起源”，文章作者 Lambert Meertens 是 Guido 在 CWI 时的导师，以同事亲历者的视角，讲述 Python 从无到有的起源过程。这样的文章我还未曾读过，因此饶有兴趣。

文章内容跟 Python 直接相关的部分并不多，作者花了较大篇幅介绍 ABC 项目的演变，讨论了编程语言的设计（特别强调的是简洁性 **Simplicity**）。

**最引起我兴趣的内容是：缩进语法的设计！**

> More striking is the use of indentation. Although it was common in programs written in ALGOL 60 or its descendants, such as Pascal, to use indentation as a typographical layout feature for clarifying the grouping of commands, this was an entirely optional presentation choice, made purely for the benefit of the human reader. In an article by P. J. Plauger entitled “**Signal and Noise in Programming Languages**,”[16](https://inference-review.com/article/the-origins-of-python#endnote-16) we found the (then) radical idea to “**have the compiler read the same signal as we human beings, and let the indenting control grouping**,” a suggestion we followed with enthusiasm. Indentation to indicate that a suite of commands belong together subsequently became mandatory in B0 programs, a design choice that has been maintained throughout all iterations.[17](https://inference-review.com/article/the-origins-of-python#endnote-17)
> 
> ——节选自《The Origins of Python》

简单概括：当时在设计新的编程语言时，他们受到了一篇文章的强烈影响，决定仅采用缩进语法来控制代码块的分组。核心思想是“have the compiler read the same signal as we human beings”，为了代码简洁性及理解一致性，舍弃了其它的代码分组方案。

我极为推崇 Python 的强制缩进语法，曾写过一篇《[Python为什么使用缩进来划分代码块？](https://mp.weixin.qq.com/s/byhJnKoKSDnhUNUE9WWopw)》介绍了这种设计的 8 个原因，但是，该文收到了大量的反对声，因此，我又补写了一篇《[Python 的缩进是不是反人类的设计？](https://mp.weixin.qq.com/s/pi1x6lT88dMmfUUqcVet-A)》。

我知道自己的两篇文章不足以说服那些讨厌 Python 缩进的人，但是，如果有更多资料介绍这项设计的原因及思想来源的话，或许就能稍微地改观某些人的看法，同时也提供给那些喜欢这项设计的人一些信心~

作为 ABC 语言的继承者，Python 的缩进语法应该主要来源于它。因此，我决定沿着前文的线索，继续挖掘它们设计缩进语法的起源。

上文提到的文章标题为《[编程语言中的信号与噪声](https://dl.acm.org/doi/10.1145/800181.810322)》（Signal and Noise in Programming Languages），发表于 1975 年的 ACM 年会论文集，作者 P.J. Plauger 是全球知名的计算机科学家、C/ C++技术专家以及 The Standard C Library、Standard C : A Reference 和 The Standard Template Library 等图书的作者。

该篇文章想要区分编程语言的哪些语法是对读者有用的信号、哪些仅是无用的噪声。文中提到了一个编程理论：“**常说的东西应该言简意赅（things which get said a lot should be concise）**”。

![常说的东西应该言简意赅](http://tva1.sinaimg.cn/large/68b02e3bgy1h902jpp61zj20e9049abc.jpg)

由于代码经常要分组分块，因此，“信号与噪声”一文将`begin...end` 及`do...end` 这两种当时常见的代码分组语法批评为糟糕的设计。它不反对花括号“{...}”的语法设计，但是提出了一种更为激进的设计，也就是仅用缩进来控制代码分组（let the indenting control grouping）。

按我的理解，P.J. Plauger 建议我们移除编程语言中的噪声。人们在阅读代码时，可以直观地根据代码的缩进层级将它们分组，因此缩进本身就是一种有意义的信号，如果激进地让机器也做到“所见即所得”的话，那甚至连“{...}”这种足够言简意赅的设计也不需要了。

P.J. Plauger 是个擅于总结编程风格/原则的人，他曾合作编写过一本《The Elements of Programming Style》（译本：编程格调），全书介绍了 70 多条最佳实践和编程规则。

![编程格调的豆瓣条目](http://tva1.sinaimg.cn/large/68b02e3bgy1h902xwk1hdj20j60fetbo.jpg)

只不过，相比于他提出的那些经典的编程规则，“使用缩进来分组代码块”不仅在 40 多年前是一条激进而少人接受的风格，它直到今天依然令某些人无法认同。

CWI 的团队当初在设计 ABC 语言时，激进地采用了缩进作分组的设计。通过溯源那篇“古老的”文章，我们知道了这种设计不是他们突然蹦出的，而是有着某种设计思想的指导，同时这也意味着，Python 的缩进设计除了有“终身仁慈独裁者（BDFL）”的个人偏好外，还隐含了这一层思想脉络的渊源。

另外，《The Origins of Python》中还提供了两个比《编程语言中的信号与噪声》更早的起源：

- 1965 年的 ISWIM 编程语言（“**I**f you **S**ee **W**hat **I** **M**ean”的首字母缩写）。它可能是有据可考最早使用缩进分组代码块的语言（尽管它没有实现），其设计者在《[The Next 700 Programming Languages](https://web.archive.org/web/20220323111045/https://www.cs.cmu.edu/~crary/819-f09/Landin66.pdf)》中称之为“[Off-side rule](https://en.wikipedia.org/wiki/Off-side_rule)”（越位规则）
- 1974 年唐纳德·克努特（Donald Knuth，著名计算机科学家、图灵奖获得者，经典巨著《计算机程序设计艺术》的作者）发表在 ACM 通讯的文章《 [Structured Programming with Go To Statements](https://doi.org/10.1145/356635.356640)》，他在畅想未来的编程语言时说：We will perhaps eventually be writing only small modules which are identified by name as they are used to build larger ones, so that devices like indentation, rather than delimiters, might become feasible for expressing local structure in the source language。

![唐纳德的文章节选](http://tva1.sinaimg.cn/large/68b02e3bgy1h907s87e8vj20dj090q5i.jpg)

值得注意的是，唐纳德提供的参考材料正是 1965 年 ISWIM 之父的文章《[The Next 700 Programming Languages](https://web.archive.org/web/20220323111045/https://www.cs.cmu.edu/~crary/819-f09/Landin66.pdf)》，里面收录了多位大佬对于缩进的讨论观点。

受限于当时的计算机硬件及编辑器工具，以及考虑到印刷对代码排版的现实性影响，纯缩进分组的代码确实可能会带来一些麻烦。因此，这些编程界的先驱们仅仅是在大胆畅想未来的编程语言的语法，当时并没有编程语言作出了实现。

从 1965 年的 ISWIM，到 1974 年唐纳德的畅想，再到 1975 年 P.J. Plauger 激进的想法，再到 1980 年代 ABC 及 Python 的落地实现。20 多年的时间，说长确实是挺长了。

如今 2022 年即将过去，Python 已经度过了它的“而立之年”， 受这种设计思想影响的编程语言也遍地开花：据维基百科统计，有近 30 门语言使用“Off-side rule”。

![采用 Off-side rule 的编程语言](http://tva1.sinaimg.cn/large/68b02e3bgy1h909ob1v0yj20le0lin2t.jpg)

尽管某些语言（如 Scala、Nemerle、Haskell）只是可选性或部分性支持，但这份列表意味着在花括号占据统治地位的时代，缩进的星星之火依然迸发着顽强的生命力。畅想未来，我相信这份列表会加进更多语言，但愿那时可以打破 Python 一枝独秀的局面。

现在作一下总结吧。本文最先关注的是 Python 之父年轻时的导师的文章“Python 的起源”，但是我发现最吸引人的还是老生常谈的缩进话题，于是文章主题转向了“Python 的缩进语法的起源”。

**不可否认，Python 的缩进语法属于是较为大胆的编程风格，但换个角度，你也可以认为它很前卫，因为它本就起源于计算机科学家们在畅想未来的编程语言时的一种创意。**

缩进语法简洁、紧凑、清晰，它是营造出 Python 之美的最大功臣之一。人生苦短，我用 Python！

最后，如果你对 Python 其它语法的起源、为什么这样设计、跟其它语言的区别等等话题感兴趣，欢迎关注“[Python 为什么](https://github.com/chinesehuazhou/python-whydo)”系列（请在 Github 上给颗小星星吧，喵~）

