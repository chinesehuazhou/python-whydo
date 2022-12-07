# 为什么说 Python 最会变魔术的魔术方法是它？

在[上篇文章中](https://mp.weixin.qq.com/s/FtlVNTYq60KSPti2xD4psA)，我有一个核心的发现：**Python 内置类型的特殊方法（含魔术方法与其它方法）由 C 语言独立实现，在 Python 层面不存在调用关系。**

但是，文中也提到了一个例外：一个非常神秘的魔术方法。

这个方法非常不起眼，用途狭窄，我几乎从未注意过它，然而，当发现它可能是上述“定律”的唯一例外情况时，我认为值得再写一篇文章来详细审视一下它。

本文主要关注的问题有：

(1) \_\_missing\_\_()到底是何方神圣？

(2) \_\_missing\_\_()有什么特别之处？擅长“大变活人”魔术？

(3) \_\_missing\_\_()是否真的是上述发现的例外？如果是的话，为什么会有这种特例？

## 1、有点价值的__missing__()

从普通的字典中取值时，可能会出现 key 不存在的情况：

```python
dd = {'name':'PythonCat'}  
dd.get('age')        # 结果：None  
dd.get('age', 18)    # 结果：18  
dd['age']            # 报错 KeyError  
dd.__getitem__('age')  # 等同于 dd['age']
```

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK3hwrJBYfS3iad7M51RpQj6DNiabTN2r2PHVG5xqUC3vlT3jslpibia0v5ibA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

对于 get() 方法，它是有返回值的，而且可以传入第二个参数，作为 key 不存在时的返回内容，因此还可以接受。但是，另外两种写法都会报错。

为了解决后两种写法的问题，就可以用到 \_\_missing\_\_() 魔术方法。

现在，假设我们有一个这样的诉求：从字典中取某个 key 对应的 value，如果有值则返回值，如果没有值则插入 key，并且给它一个默认值（例如一个空列表）。

如果用原生的 dict，并不太好实现，但是，Python 提供了一个非常好用的扩展类`collections.defaultdict`：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK3E2SPfgibpf8MjVKJkdsJkFWT6HFhhgSklhz86oS9fhnhjI4qA3oeUIA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

如图所示，当取不存在的 key 时，没有再报 KeyError，而是默认存入到字典中。

为什么 defaultdict 可以做到这一点呢？

原因是 defaultdict 在继承了内置类型 dict 之后，还定义了一个 \_\_missing\_\_() 方法，当 \_\_getitem\_\_取不存在的值时，它就会调用入参中传入的工厂函数（上例是调用 list()，创建空列表）。

作为最典型的示例，defaultdict 在文档注释中写到：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK3SVZMgvtcY1rJGBTbKMYq2iaicWbWwAjD72q7rGPtnmebsWXR23Mj6qaQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

简而言之，**__missing__()的主要作用就是由__getitem__在缺失 key 时调用，从而避免出现 KeyError。**

另外一个典型的使用例子是`collections.Counter`，它也是 dict 的子类，在取未被统计的 key 时，返回计数 0：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK36n4Nnbw6TbU3SvBOxG8fK8BydqHzo6SddGOBENInoDPhRCiaTVeqjIQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

## 2、神出鬼没的__missing__()

由上可知，\_\_missing\_\_() 在 \_\_getitem\_\_()取不到值时会被调用，但是，我不经意间还发现了一个细节：**__getitem__()在取不到值时，并不一定会调用__missing__()。**

这是因为它并非内置类型的必要属性，并没有在字典基类中被预先定义。

如果你直接从 dict 类型中取该属性值，会报属性不存在：`AttributeError: type object 'object' has no attribute '__missing__'`。

使用 dir() 查看，发现确实不存在该属性：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK30E5cZWoCSMJT9kAPk8cEIb8Lu0xgWVRenzT6QruxgBcaDWOSho7XUw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

如果从 dict 的父类即 object 中查看，也会发现同样的结果。

这是怎么回事呢？为什么在 dict 和 object 中都没有__missing__属性呢？

然而，查阅最新的官方文档，object 中分明包含这个属性：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK34P2VoOE1YKC1dpUwM1Vrn3apak30uJfF98WNJqRDr2Vpqsh2NiaCicxA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

出处：https://docs.python.org/3/reference/datamodel.html?highlight=__missing__#object.__missing__

也就是说，理论上 object 类中会预定义__missing__，其文档证明了这一点，然而实际上它并没有被定义！文档与现实出现了偏差！

如此一来，当 dict 的子类（例如 defaultdict 和 Counter）在定义__missing__ 时，这个魔术方法事实上只属于该子类，也就是说，**它是一个诞生于子类中的魔术方法！**

据此，我有一个不成熟的猜想：\_\_getitem\_\_()会判断当前对象是否是 dict 的子类，且是否拥有__missing__()，然后才会去调用它（如果父类中也有该方法，则不会先作判断，而是直接就调用了）。

我在交流群里说出了这个猜想，有同学很快在 CPython 源码中找到验证：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK3Mgxbx17G3icJsibzTm0wtEpfQYwbwtkLSssb13umBicULgQ9CTWKIGrlg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1 "Python猫_20201201_230325.jpg")

而这就有意思了，**在内置类型的子类上才存在的魔术方法，** 纵观整个 Python 世界，恐怕再难以找出第二例。

我突然有一个联想：这神出鬼没的__missing__()，就像是一个擅长玩“大变活人”的魔术师，先让观众在外面透过玻璃看到他（即官方文档），然而揭开门时，他并不在里面（即内置类型），再变换一下道具，他又完好无损就出现了（即 dict 的子类）。

## 3、被施魔法的__missing__()

\_\_missing\_\_() 的神奇之处，除了它本身会变“魔术”之外，它还需要一股强大的“魔法”才能驱动。

在[为什么继承 Python 内置类型会出问题？！](https://github.com/chinesehuazhou/python-whydo/blob/master/zh_CN/19-why-is-inheriting-Python-built-in-types-a-problem.md)，我发现原生的魔术方法间相互独立，它们在 C 语言界面可能有相同的核心逻辑，但是在 Python 语言界面，却并不存在着调用关系：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK36d8NrjoxV7ySHRiaSaPicIHANxLROD5UsYCG2ZZxp3O4HycUgjCLYw0w/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

魔术方法的这种“老死不相往来”的表现，违背了一般的代码复用原则，也是导致内置类型的子类会出现某些奇怪表现的原因。

官方 Python 宁肯提供新的 UserString、UserList、UserDict 子类，也不愿意复用魔术方法，唯一合理的解释似乎是令魔术方法相互调用的代价太大。

但是，对于特例__missing__()，Python 却不得不妥协，不得不付出这种代价！

\_\_missing\_\_() 是魔术方法的“**二等公民**”，它没有独立的调用入口，只能被动地由 \_\_getitem\_\_() 调用，即__missing__() 依赖于__getitem__()。

不同于那些“**一等公民**”，例如 \_\_init\_\_()、\_\_enter\_\_()、\_\_len\_\_()、\_\_eq\_\_() 等等，它们要么是在对象生命周期或执行过程的某个节点被触发，要么由某个内置函数或操作符触发，这些都是相对独立的事件，无所依赖。

**__missing__() 依赖于__getitem__()，才能实现方法调用；而 __getitem__() 也要依赖 __missing__()，才能实现完整功能。**

为了实现这一点，\_\_getitem\_\_()在解释器代码中开了个后门，从 C 语言界面折返回 Python 界面，去调用那个名为“\_\_missing\_\_”的特定方法。

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTOTABakJZpyBQBuibH8n1fK3SvdPl8gVnKUmFQ12Znhvf8XVEIohAiaRNEx8L9qlM076J6bzsvMncIQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

而这就是真正的“魔法”了，目前为止，\_\_missing\_\_()似乎是唯一一个享受了此等待遇的魔术方法！

## 4、小结

Python 的字典提供了两种取值的内置方法，即__getitem__() 和 get()，当取值不存在时，它们的处理策略是不一样的：**前者会报错`KeyError`，而后者会返回 None。**

为什么 Python 要提供两个不同的方法呢？或者应该问，为什么 Python 要令这两个方法做出不一样的处理呢？

这可能有一个很复杂（也可能是很简单）的解释，本文暂不深究了。

不过有一点是可以确定的：即原生 dict 类型简单粗暴地抛`KeyError`的做法有所不足。

为了让字典类型有更强大的表现（或者说让__getitem__()作出 get() 那样的表现），Python 让字典的子类可以定义__missing__()，供__getitem__()查找调用。

本文梳理了__missing__()的实现原理，从而揭示出它并非是一个毫不起眼的存在，恰恰相反，**它是唯一一个打破了魔术方法间壁垒，支持被其它魔术方法调用的特例！** 

Python 为了维持魔术方法的独立性，不惜煞费苦心地引入了 UserString、UserList、UserDict 这些派生类，但是对于 \_\_missing\_\_()，它却选择了妥协。

本文揭示出了这个魔术方法的神秘之处，不知你读后有何感想呢？欢迎留言讨论。