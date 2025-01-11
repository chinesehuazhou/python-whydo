# 为什么在 Python 中 hash(-1) == hash(-2)?

英文：https://omairmajid.com/posts/2021-07-16-why-is-hash-in-python

作者：Omair Majid

译者：豌豆花下猫&Claude-3.5-Sonnet

时间：原文发布于 2021.07.16，翻译于 2025.01.11

当我在[等待代码编译](https://xkcd.com/303/)的时候，我在 Reddit 的 r/Python 上看到了这个问题：

> [hash(-1) == hash(-2) 是个彩蛋吗？](https://www.reddit.com/r/Python/comments/oks5km/is_hash_1hash2_an_easter_egg/)

等等，这是真的吗？

```python
$ python
Python 3.9.6 (default, Jun 29 2021, 00:00:00)
[GCC 11.1.1 20210531 (Red Hat 11.1.1-3)] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> hash(-1)
-2
>>> hash(-2)
-2
>>> hash(-1) == hash(-2)
True
```

是的，确实如此。真让人惊讶！

让我们看看其它一些常见的哈希值：

```python
>>> hash(1)
1
>>> hash(0)
0
>>> hash(3)
3
>>> hash(-4)
-4
```

看起来所有小整数的哈希值都等于它们自身，除了 `-1`...

现在我完全被这个问题吸引住了。我试图自己找出答案。在接下来的内容中，我将带你了解如何自己寻找这个答案。

如何开始呢？什么能给我们一个权威的答案？

让我们看看源代码！Python 的实际实现代码！

获取源代码
--------------------------

我假设你和我一样，对 Python 的源代码在哪里完全没有概念。

那么，我们（假设从未看过 Python 的源代码）如何获取源代码来回答最初的问题呢？

也许我们可以用 Google？搜索 "python implementation" 会带来一些有趣的结果。

我搜索的 [第一个结果](https://wiki.python.org/moin/PythonImplementations) 提到了 "CPython 参考实现"。

Github 上 [Python 组织](https://github.com/python) 的第二个仓库就是 "cpython"。这看起来很靠谱。我们如何确保万无一失呢？

我们可以访问 python.org。让我们去到源码下载页面。最终我找到了 [Python 3.9.6 的压缩包 ](https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz)。解压后，`README.rst` 也指向了 Github 上的 CPython。

好的，这就是我们的起点。让我们获取这些代码，以便后续搜索：

```shell
git clone https://github.com/python/cpython --depth 1
```

`--depth 1` 参数使 `git` 只获取有限的历史记录。这样可以让克隆操作快很多。如果之后需要完整的历史记录，我们可以再获取。

让我们深入研究
---------------------

在研究代码时，我们需要找到一个起点。最好是容易搜索的东西，比如一个简单的字符串，不会有太多误导性的匹配。

也许我们可以使用 `hash` 函数的文档？我们可以用 `help(hash)` 来查看文档内容：

```python
>>> hash
<built-in function hash>
>>> help(hash)
Help on built-in function hash in module builtins:

hash(obj, /)
    Return the hash value for the given object.

    Two objects that compare equal must also have the same hash value, but the
    reverse is not necessarily true.
```

现在，我们可以用它来找到 `hash()` 的实现：

```python
$ grep -r 'Return the hash value'
Python/clinic/bltinmodule.c.h:"Return the hash value for the given object.\n"
Python/bltinmodule.c:Return the hash value for the given object.
Doc/library/functions.rst:   Return the hash value of the object (if it has one).  Hash values are
Lib/hmac.py:        """Return the hash value of this hashing object.
```

`hmac` 可能与加密的 HMAC 实现有关，所以我们可以忽略它。`functions.rst` 是一个文档文件，所以也可以忽略。

`Python/bltinmodule.c` 看起来很有趣。如果我们查看这个文件，会找到这样一段代码：

```python
/*
...
Return the hash value for the given object.

Two objects that compare equal must also have the same hash value, but the
reverse is not necessarily true.
[clinic start generated code]*/

static PyObject *
builtin_hash(PyObject *module, PyObject *obj)
/*[clinic end generated code: output=237668e9d7688db7 input=58c48be822bf9c54]*/
{
    Py_hash_t x;

    x = PyObject_Hash(obj);
    if (x == -1)
        return NULL;
    return PyLong_FromSsize_t(x);
}
```

搜索 `PyLong` 带我来到[这里](https://docs.python.org/3/c-api/long.html)。看起来 `PyLongObject` 是 Python 整数的原生表示（这在稍后会派上用场）。在浏览了 `PyLongObject` 文档并重读这段代码后，看起来是这样的：

1. 我们调用 `PyObject_Hash` 来获得一个对象的哈希值
2. 如果计算出的哈希值是 -1，那表示是一个错误
   * 看起来我们用 -1 来表示错误，所以没有哈希函数会为真实对象计算出 -1
3. 我们将 `Py_Ssize_t` 转换为 `PyLongObject`（文档中称之为："这是 PyObject 的子类型，表示一个 Python 整数对象"）

啊哈！这就解释了为什么 `hash(0)` 是 `0`，`hash(1)` 是 `1`，`hash(-2)` 是 `-2`，但 `hash(-1)` 不是 `-1`。这是因为 `-1` 在内部被用来表示错误。

但为什么 `hash(-1)` 是 `-2` 呢？是什么将它设置成了这个值？

让我们看看能否找出原因。

我们可以先查找 `PyObject_Hash` 。让我们搜索一下。

```python
$ ag PyObject_Hash
...
Objects/rangeobject.c
552:    result = PyObject_Hash(t);

Objects/object.c
777:PyObject_HashNotImplemented(PyObject *v)
785:PyObject_Hash(PyObject *v)
802:    return PyObject_HashNotImplemented(v);

Objects/classobject.c
307:    y = PyObject_Hash(a->im_func);
538:    y = PyObject_Hash(PyInstanceMethod_GET_FUNCTION(self));
...
```

虽然有很多干扰，但唯一的实现似乎在 `Objects/object.c` 中：

```python
Py_hash_t
PyObject_Hash(PyObject *v)
{
    PyTypeObject *tp = Py_TYPE(v);
    if (tp->tp_hash != NULL)
        return (*tp->tp_hash)(v);
    /* 为了保持通用做法：在 C 代码中仅从 object 继承的类型，应该无需显式调用 PyType_Ready 就能工作，
     * 我们在这里隐式调用 PyType_Ready，然后再次检查 tp_hash 槽
     */
    if (tp->tp_dict == NULL) {
        if (PyType_Ready(tp) < 0)
            return -1;
        if (tp->tp_hash != NULL)
            return (*tp->tp_hash)(v);
    }
    /* Otherwise, the object can't be hashed */
    return PyObject_HashNotImplemented(v);
}
```

这段代码相当令人困惑。幸运的是，注释很清晰。在多次阅读后，似乎这段代码——考虑到类型的一些延迟加载（？）——先找到对象的类型（使用 `Py_TYPE`）。然后寻找该类型的 `tp_hash` 函数，并在 v 上调用该函数：`(*tp->tp_hash)(v)`

我们在哪里能找到 `-1` 的 `tp_hash` 呢？让我们再次搜索 `tp_hash`：

```python
$ ag tp_hash -l
...
Modules/_multiprocessing/semaphore.c
Objects/sliceobject.c
Objects/moduleobject.c
Objects/exceptions.c
Modules/_pickle.c
Objects/frameobject.c
Objects/setobject.c
Objects/rangeobject.c
Objects/longobject.c
Objects/object.c
Objects/methodobject.c
Objects/classobject.c
Objects/enumobject.c
Objects/odictobject.c
Objects/complexobject.c
...
```

这是一个很长的列表。回想一下文档中关于 `PyLongObject` 的说明（"这个...表示一个 Python 整数对象"），我先查看下 `Objects/longobject.c` ：

```python
PyTypeObject PyLong_Type = {
    PyVarObject_HEAD_INIT(&PyType_Type, 0)
    "int",                                      /* tp_name */
    offsetof(PyLongObject, ob_digit),           /* tp_basicsize */
    sizeof(digit),                              /* tp_itemsize */
    0,                                          /* tp_dealloc */
    0,                                          /* tp_vectorcall_offset */
    0,                                          /* tp_getattr */
    0,                                          /* tp_setattr */
    0,                                          /* tp_as_async */
    long_to_decimal_string,                     /* tp_repr */
    &long_as_number,                            /* tp_as_number */
    0,                                          /* tp_as_sequence */
    0,                                          /* tp_as_mapping */
    (hashfunc)long_hash,                        /* tp_hash */
    ...
```

所以 `PyLongObject` 类型对象的 `tp_hash` 是 `long_hash`。让我们看看这个函数。

```python
static Py_hash_t
long_hash(PyLongObject *v)
{
    Py_uhash_t x;
    Py_ssize_t i;
    int sign;

    ...

    if (x == (Py_uhash_t)-1)
        x = (Py_uhash_t)-2;
    return (Py_hash_t)x;
}
```

注意我删除了大部分实现细节。但这个函数的结尾正好符合我们的预期：`-1` 被保留用作错误信号，所以代码明确地将该返回值转换为 `-2`！

这就解释了为什么 `hash(-1)` 最终与 `hash(-2)` 相同。这不是一个彩蛋，只是为了避免使用 `-1` 作为 `hash()` 方法的返回值，因此采取的变通方法。

这是正确/完整的答案吗？
----------------------------------

如前所述，我从未看过 Python 代码库。我认为自己找到了答案。但这是对的吗？我可能完全错了。

幸运的是，[/u/ExoticMandibles 在 Reddit 帖子中提供了答案](https://www.reddit.com/r/Python/comments/oks5km/is_hash_1hash2_an_easter_egg/h5a7ylc/)：

> Python 的参考实现是 "CPython"，这很可能就是你正在使用的 Python。CPython 是用 C 语言编写的，与 Python 不同，C 语言没有异常处理。所以，在 C 语言中，当你设计一个函数，并且想要表示"发生了错误"时，必须通过返回值来表示这个错误。
> 
> CPython 中的 hash() 函数可能返回错误，所以它定义返回值 -1 表示"发生了错误"。但如果哈希计算正确，而对象的实际哈希值恰好是 -1，这可能会造成混淆。所以约定是：如果哈希计算成功，并得到值是 -1，就返回 -2。
> 
> 在 CPython 中，整数（"长整型对象"）的哈希函数中有专门的代码来处理这种情况：
> 
> [https://github.com/python/cpython/blob/main/Objects/longobject.c#L2967](https://github.com/python/cpython/blob/main/Objects/longobject.c#L2967)

这正是我通过阅读代码推测出的结果。

结论
----------

我从一个看似难以回答的问题开始。但是通过几分钟的代码探索——Python 整洁的代码库使得查看它的代码比我见过的其它代码库要容易得多——很容易就发现和理解了答案！如果你接触过计算机，这应该不足为奇。这里没有魔法，只有层层的抽象和代码。

如果本文有什么启示的话，那就是：[查看源代码！](https://wiki.c2.com/?UseTheSourceLuke) （文档可能会过时，注释可能不准确，但源码是永恒的。）

