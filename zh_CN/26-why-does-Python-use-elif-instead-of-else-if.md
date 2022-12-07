# Python 为什么用”elif“，而不是“else if”？

2019 年 6 月 22 日，Python 之父 Guido 发了一条推特，说了 Python 的一则历史故事，他说 elif 是从 C 语言中偷过来的：

![图片](https://mmbiz.qpic.cn/mmbiz_jpg/LLRiaS9YfFTN9XnM9iaW7ca9nbDetjcdwqx2ibJxq9T0oxCkjSOPpVfO069FEouQicItLOlq4ArPMwbibqdNRbtAZkg/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

elif 是“else if”的简写，用于条件判断。当只有两个分支时，我们会写成“if…else…”，当出现更多分支时，我们会写成如下格式：

```python
if 判断条件1：  
    做事情1  
elif 判断条件2：  
    做事情2  
else：  
    做其它事
```

简写而成的 elif 不仅是减少了几个字符，而且由于单一而清晰的用途，它还不会给我们带来理解或使用上的困惑。

但是，简写法并不是主流，完整写法才是主流，C 语言中就是采用完整的写法：

```python
if(判断条件1)  
{  
   做事情1  
}  
else if(判断条件2)  
{  
   做事情2  
}  
else   
{  
   做其它事  
}
```

没错，C 语言使用的是全拼写法，但是在它的预处理/预编译语句中，还有一个 elif 指令，Guido 所说的“偷”，就是从这来的：

```python
#if 常量表达式1  
// 编译1  
#elif 常量表达式2  
// 编译2  
#else  
// 编译3  
#endif
```

Python 没有预编译，所以所谓的偷，跟预编译没有关系，只是在对比两种写法后，借用了更简洁的写法而已。

为什么 C 语言不把两种写法统一起来呢？这我不得而知了，而 Guido 在两种写法中，选择了后一种非主流却更好用的写法。我想对他说，你“偷”得好啊！

实际上，留言区里的人也有同感，纷纷表示：不介意、很 okay、非常喜欢，还有人说“不是偷，而是收获（harvested）”、“不是偷，而是把它提升了一些高度”……