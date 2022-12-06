# Python 为什么不用分号作终止符？

一般而言，编程语言中使用分号“;”来实现两种目的：

1. 作为语句分隔符：使用分号来分隔语句（statement），这样就能在一行代码中书写多条语句（一行多句）
2. 作为语句终止符：使用分号来终止语句，这样就能把多行代码识别为一条语句（一句多行）

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gf69zg92e2j211q0dqdi0.jpg)

单纯看“分隔符”与“终止符”，它们都是必须的，然而是否都要用分号来表示呢？这个问题没有达成一致的标准。

**Python 中使用了分号作为语句分隔符，但是不用分号作为终止符， 而是用了换行作为终止符。** 

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gf6a07qti2j20z50edwg7.jpg)

若在一句完整的语句末尾加了分号，然后换行，那么 IDE 一般会提示“Trailing semicolon in the statement”，提示着这个“尾随分号”是多余的。

按我粗浅的理解，尾随分号实际会被当成分隔符，只不过它后面是“空语句”，然后是换行（即终止符）。分隔空语句是无必要的，所以尾随分号就成了多余的。

![](http://ww1.sinaimg.cn/large/68b02e3bgy1gf6a0o259xj217k0f7wiw.jpg)

Python 不用分号作为终止符，大概有如下的原因：

1. 它把缩进和换行当成文法的有效部分，可以表达出完整的语义，不会导致编译时的歧义。这是最主要的原因，是跟“分号党”的根本区别
2. 不用分号与花括号，但是使用缩进和冒号，这是一脉相承的思路，总体上形成了更高的可读性、简洁性和标准化。这体现了局部语法与整体规则的关系，1 + 1 > 2
3. 可以少写字符，也避免了在某些键盘上要敲“shift”键的麻烦
4. 分号主要是给机器看的，但 Python 更注重于人性化。早期硬件有所限制，加分号可以提升解析/编译的速度，但如今障碍已除，某些分号党语言只是在延续 B/C 语言的老传统
5. 对于需要换行的语句，Python 中使用反斜杠（\）来连接，可以理解成它把换行给转义了，能够解决一句多行的问题

![Python猫后台回复"0527"领取](http://ww1.sinaimg.cn/large/68b02e3bgy1gf67ygo2bcj217b0ovtdu.jpg)

### 关联阅读

1、[A Brief History of the Semicolon in Programming](https://medium.com/better-programming/a-brief-history-of-the-8efda9dde2b8)

2、[The End of the Semicolon Era](https://medium.com/@elizarov/the-end-of-the-semicolon-era-60ab95e669ab)
