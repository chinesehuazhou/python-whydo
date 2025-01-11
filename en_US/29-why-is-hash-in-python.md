# Why is hash(-1) == hash(-2) in Python?

Source: https://omairmajid.com/posts/2021-07-16-why-is-hash-in-python

Author: Omair Majid

Published Time: 2021-07-16

While ~browsing Reddit the other day~ [waiting for my code to compile](https://xkcd.com/303/) , I ran across this question on r/Python:

> [is hash(-1) == hash(-2) an easter egg?](https://www.reddit.com/r/Python/comments/oks5km/is_hash_1hash2_an_easter_egg/)

Wait, is that really true?

```
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

Yes, it is. How surprising!

Let’s check some other common hash values:

```
>>> hash(1)
1
>>> hash(0)
0
>>> hash(3)
3
>>> hash(-4)
-4
```

All small numbers seem to hash to themselves, except `-1`…

Now I was completely intrigued. I tried to find the answer myself. In the rest of this post, I will walk you through how you can also find this answer yourself.

How can we get started? What would have an authoritative answer? Let’s use the source! The actual python implementation source code!

Let’s get the source code!
--------------------------

I am hoping that, like myself when I began this exercise, you have no idea where the source code of python is.

So how can we (assuming we have _never_ looked at the source code of python) get the source code to answer the original question?

Maybe we can use google? A search for “python implementation” brings up a set of interesting results.

The first result for me, [https://wiki.python.org/moin/PythonImplementations](https://wiki.python.org/moin/PythonImplementations) calls out “the Cpython reference implementation”.

[https://github.com/python](https://github.com/python) has “cpython” as the number 2 repo. That looks legit. How can we be extra sure?

We can check python.org. Let’s go to source downloads. I eventually ended up at [https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz](https://www.python.org/ftp/python/3.9.6/Python-3.9.6.tgz) . Extracting that, the `README.rst` also points to [https://github.com/python/cpython](https://github.com/python/cpython)

Okay, that’s where we will start. Let’s get this code so we can later search through it:

```
git clone https://github.com/python/cpython --depth 1
```

The `--depth 1` argument makes `git` fetch a limited amount of history. This makes the clone operation much faster. If we need the complete history, we can get it later.

Let’s walk through it
---------------------

We need to find a starting point as we begin to dig through the code. Something easy to search for. Something like a simple string without too many misleading matches.

Maybe we can use the documentation of the `hash` function? We can use `help(hash)` to see the documentation text:

```
>>> hash
<built-in function hash>
>>> help(hash)
Help on built-in function hash in module builtins:

hash(obj, /)
    Return the hash value for the given object.

    Two objects that compare equal must also have the same hash value, but the
    reverse is not necessarily true.
```

Now, we can use it to find the implementation of `hash()`:

```
$ grep -r 'Return the hash value'
Python/clinic/bltinmodule.c.h:"Return the hash value for the given object.\n"
Python/bltinmodule.c:Return the hash value for the given object.
Doc/library/functions.rst:   Return the hash value of the object (if it has one).  Hash values are
Lib/hmac.py:        """Return the hash value of this hashing object.
```

The `hmac` is probably related to the crypto HMAC implementation. So we can probably ignore that. The `functions.rst` is a documentation file. So, let’s ignore that too.

`Python/bltinmodule.c` looks interesting. If we look through that, we find this snippet of code:

```
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

A search for `PyLong` brings me [here](https://docs.python.org/3/c-api/long.html) . Looks like `PyLongObject` is the native representation of a python integer (this will come in handy later too). After skimming the `PyLongObject` doc and then re-reading this code, it looks like:

1.  We call `PyObject_Hash` to find the hash of an object
2.  If the computed hash value is -1, that’s an error.
    *   It looks like we use -1 to indicate an error, so no hash function will ever compute -1 for a real object
3.  We convert `Py_Ssize_t` to `PyLongObject` (which the documentation calls out as: “This subtype of PyObject represents a Python integer object).

Aha! that answers why `hash(0)` is `0`, `hash(1)` is `1`, `hash(-2)` is `-2`, but `hash(-1)` is not `-1`. It’s because `-1` is internally used to indicate an error.

But why is `hash(-1)` `-2`? What sets it to that value?

Lets see if we can find out why.

We can start by looking for `PyObject_Hash`. Let’s search for it.

```
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

There’s a lot of noise, but the only implementation appears to be in `Objects/object.c`:

```
Py_hash_t
PyObject_Hash(PyObject *v)
{
    PyTypeObject *tp = Py_TYPE(v);
    if (tp->tp_hash != NULL)
        return (*tp->tp_hash)(v);
    /* To keep to the general practice that inheriting
     * solely from object in C code should work without
     * an explicit call to PyType_Ready, we implicitly call
     * PyType_Ready here and then check the tp_hash slot again
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

This is quite a confusing piece of code. Thankfully, the comment is illuminating. After reading through it a few times, it seems like the code - accounting for some lazy loading of the type (?) - finds the type of the object (using `Py_TYPE`). Then looks for a function `tp_hash` of that type and calls that function on v: `(*tp->tp_hash)(v)`

Where would we find the `tp_hash` of `-1`? Let’s do another search for `tp_hash`:

```
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

That’s a huge list. Recalling what the documentation said about `PyLongObject` (“This … represents a Python integer object”), I started by looking at `Objects/longobject.c`:

```
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

So `tp_hash` is `long_hash` of objects of type `PyLongObject`. Let’s look at that.

```
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

Note that I have removed a big chunk of the implementation details. But the ending of this function matches exactly what we are expecting: `-1` is reserved for an error signal, so the code converts that return value explicitly to `-2`!

And that explains the entire reason why `hash(-1)` ends up being the same as `hash(-2)`. Not an easter egg, just working around the unavailability of `-1` as the possible result of a `hash()` method.

Is this the right/complete answer?
----------------------------------

As I said earlier, I have never looked through the python code base at all. I _think_ I have an answer. But is it right? I might be completely wrong.

Thankfully, [/u/ExoticMandibles provided an answer on the Reddit post itself](https://www.reddit.com/r/Python/comments/oks5km/is_hash_1hash2_an_easter_egg/h5a7ylc/) :

> The reference implementation of Python is “CPython”, which is almost certainly the Python you’re using. CPython is written in C, and unlike Python, C doesn’t have exceptions. So, in C, when you design a function, and you want your function to be able to indicate “there was an error”, it has to return that error as its return value.
> 
> The hash() function in CPython can return an error, so it defines a return code of -1 to mean “there was an error”. But this could be confusing if the hash worked correctly, and the actual hash of an object was -1. So the convention is: if hashing worked, and you got a -1, return -2.
> 
> There’s special code in the hash function for ints (“long objects”) in CPython that does exactly that:
> 
> [https://github.com/python/cpython/blob/main/Objects/longobject.c#L2967](https://github.com/python/cpython/blob/main/Objects/longobject.c#L2967)

This is exactly what I had guessed by reading the code.

Conclusion
----------

I started with a question that I thought might be difficult to answer. But a few minutes of digging through code - and python’s clean codebase makes looking through it much easier than some other codebases I have seen - and the answer is quite easy to discover and understand! Which shouldn’t be surprising if you have been around computers. There’s no magic, just layers and layers of abstractions and code.

If there’s a take away here, let it be this: [Use the source!](https://wiki.c2.com/?UseTheSourceLuke)