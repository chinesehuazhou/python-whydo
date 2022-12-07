# Python 为什么不设计 do-while 循环结构？

在某些编程语言中，例如 C/C++、C#、PHP、Java、JavaScript 等等，do-while 是一种基本的循环结构。

它的核心语义是：先执行一遍**循环体代码**，然后执行一遍**条件语句**，若条件语句判断为真，则继续执行循环体代码，并再次执行条件语句；直到条件语句判断为假，则跳出循环结构。

流程图如下（Java 示例）：

![](http://tva1.sinaimg.cn/large/68b02e3bgy1gy7jzggaebj207p097glk.jpg)

```java
// 打印小于 20 的数字
public class Test {
   public static void main(String[] args){
      int x = 10;
      do {
         System.out.print("value of x : " + x );
         x++;
         System.out.print("\n");
      } while(x < 20);
   }
}
```

Python 并不支持 do-while 结构，“do”并不是一个有效的关键字。

**那么，为什么 Python 不提供这种语法结构呢，这种现状的背后有何种设计考量因素呢？**

在回答这个问题之前，让我们再仔细思考一下 do-while 语法可以解决什么问题，看看使用这种结构能带来什么好处？

最显而易见的好处是：do-while 语法保证了会先执行一遍循环体代码。

它的使用场景也许不多，但是，跟普通的 while 循环或者 for 循环语法的“**条件前置**”思想不同，它体现的是一种“**条件后置**”的编程逻辑，也是一种控制循环的常见方式。

它们的关系似乎有点像 C/C++ 这些语言中的`i++`与`++i`操作的区别，在某些特殊场合中，也许会更为高效。

除了这一特点，这种结构最大的应用场景其实是在 C/C++ 中特殊的`do {...} while (0)` 用法。这在很多开源项目的源码中都能找到踪迹，例如 Linux、Redis 以及 CPython 解释器，等等。

这里面的数字 0 表示布尔值 False，意味着循环只会执行一遍，然后就跳出。

这样的写法是不是很诡异？所谓“循环”，一般就意味着程序体会被反复执行多次，但是，`do {...} while (0)` 却偏偏只需要它执行一遍，这初看起来是有点多余啊。 

这种写法主要用在宏函数的定义中，可以解决宏代码块的编译问题，使代码按照我们的意图而合理分块。

另外，`do {...} while (0)` 结合 break 使用，还可以实现很优雅的跳转控制效果。

在下面的示例中，步骤 1、4 和 5 要求必须执行，而步骤 2 取决于步骤 1 的执行结果，步骤 3 则取决于步骤 2 的执行结果。

```c
do {
  // 执行步骤 1 
  if (条件1失败) {
    break;
  }
  // 执行步骤 2 
  if (条件2失败) {
    break;
  }
  // 执行步骤 3 
  if (条件3失败) {
    break;
  }
} while(0);
// 执行步骤 4
// 执行步骤 5
```

在这种场景中，我们确实只需要按照顺序执行一遍。do-while 结构很清晰，避免造成多层条件嵌套或者设置诸多额外标记的局面。

最后还有一点，在汇编层面，do-while 比 while 更接近汇编语言的逻辑，可以节省使用指令，在过去的低内存时代，算得上是一种优化写法。 

分析完 do-while 的好处后，让我们回到主题：Python 为什么不需要设计 do-while 循环语法呢？

首先，Python 离底层应用编程太远了，就不用考虑汇编指令的优化了，同时，它也不涉及宏的使用。

至于“条件前置”和“条件后置”的区别，其实并没有太大影响，而且，由于 Python 使用简洁优雅的缩进加冒号语法来划分代码块，导致直译过来的 do-while 语法看起来会很怪异（注意，直译的 while 的条件后没有其它内容）：

```python
do:
    pass
while False
```

想要引入新的语法特性，必然要遵守既定的风格习惯。其它语言的 do-while 结构直译成 Python 的话，肯定不合适。

事实上，在 2003 年时，有一个 PEP 提议给 Python 加上 do-while 语法支持：

![](http://tva1.sinaimg.cn/large/68b02e3bgy1gyb7bui5evj20ii07amyq.jpg)

PEP-315 Enhanced While Loop

该 PEP 提议增加一个可选的 do 子句，支持将 while 循环扩展成这样子：

```python
do:
    <setup code>
while <condition>:
    <loop body>
```

这不是简单地从其它语言翻译成 Python，它的 while 语句后保留了 Python 的缩进用法，并不会造成直译形式的突兀结果。

加上 while 循环本身已支持的可选的 else 子句，因此，while 完整的语法结构是这样的：

```Python
while_stmt : ["do" ":" suite]
            "while" expression ":" suite
            ["else" ":" suite]
```

也就是说，在保持原 while 循环语法不变的情况下，PEP-315 提议支持在 while 前面使用一个可选的 do 子句。

do 子句只会执行一遍，当它里面出现 break 时，则跳出整个 do-while 循环；当 do 子句中出现 continue 时，则跳出 do 子句，进到 while 的条件判断中。

有了 do 子句后，很容易就能实现 `do {...} while (0)` 的跳转控制效果。

但是，这个 PEP 遭到了一些核心开发者的反对。

反对的理由是，不需要引入新的关键字和语法，仅使用现有语法就能很好地实现同样的功能：

```python
while True:
    <setup code>
    if not <condition>:
        break
    <loop body>
```

Python 之父 Guido van Rossum 也持反对意见，他的原话是：

![Guido的回复](http://tva1.sinaimg.cn/large/68b02e3bgy1gygyfagfpxj20ex087djb.jpg)

> Please reject the PEP. More variations along these lines won't make the
> language more elegant or easier to learn. They'd just save a few hasty
> folks some typing while making others who have to read/maintain their code wonder what it means.

简单翻译一下，这种 do-while 语法并不会使 Python 更优雅好用，反而会产生阅读/维护代码的理解负担。

就个人的感觉而言，我也不赞成引入 PEP-315 那种可选的 do-while 语法，虽然它比固定形式的 do-while 结构更为灵活和优雅一点。

最后稍微总结一下，do-while 作为一种常见的循环结构，在其它语言中有所发挥，它甚至还发展出了 `do {...} while (0)` 的典型用法，但是，do-while 能够解决的几个问题要么在 Python 中并不存在（宏定义、汇编指令），要么就是已经有更为合适而低成本的实现（跳转控制）。

看完这篇文章，你是否还有其它补充的内容呢？欢迎交流讨论。
