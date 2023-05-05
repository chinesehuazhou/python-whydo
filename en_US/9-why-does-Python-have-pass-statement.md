# Why does Python have the "pass" statement?

The pass statement in Python is a simple concept that even beginners with no programming experience can quickly grasp. 

The official [documentation](https://docs.python.org/3/tutorial/controlflow.html#pass-statements) provides a very simple introduction, and the following three examples allow us to quickly understand how to use it.

![](https://img.pythoncat.top/68b02e3bgy1gh2f7sovw9j20y80g60u7.jpg)

It is essentially a null operation that does nothing but allows the interpreter to check if the syntax is valid before skipping over it. 

Unlike other non-empty operations like return, break, continue, and yield, pass does not change the program's execution order. It is like a comment that takes up a line of code but does not affect the scope it is in.

However, if you have experience with other programming languages, you may wonder why Python has this unique pass statement while other languages do not. 

What was the reason behind Python's design? Was it to address common problems faced by most programming languages or was it because of its own new discoveries, resulting in the creation of a new feature?

In other words, why does Python have the "pass" statement, what problems (benefits) does it solve, and what problems (drawbacks) would arise without it?

This article analyzes the reasons behind Python's design of the pass statement from two perspectives.

Firstly, from a human perspective, **pass can be used as a space placeholder**. It is a concise way of saying "reserve a position here and come back to fill in the specific code implementation later." 

For example, in a multi-layered if-elif-else structure, we can write the conditional statements first and then write pass in the corresponding block to be completed later. Pass can also be used in class or function definitions to write the name and parameters first and then skip the body code to be filled in later. 

Pass is easy to write, and since it is a keyword, IDEs will highlight it in a noticeable color, making it more convenient than writing comment content.

The "pass" statement serves as a placeholder in space and mainly facilitates our conceptualization of local code structure, providing some helpful reminders.

However, as a way of commenting, pass is too thin and cannot compare to writing "# todo: xxxx," which is more explicit and can also be highlighted by IDEs. Although pass is easy to write, it introduces a seemingly redundant keyword.

Therefore, from the perspective of a space placeholder, pass is not a necessary design element in programming languages. It can express the semantics of "something is here, but temporarily skipped," but it can be replaced by comment content if it is not available.

Secondly, from a machine perspective, **pass is used for syntax completeness**. When we use pass, we usually place it on the line below the colon and in the code block with the same indentation, and there is only this one statement. 

We can imagine what would happen if we did not write it. The answer is that it would report an indentation error: "IndentationError: expected an indented block." 

```python
# Removing the "pass" statement from the function body will result in an error.
def func():

func()
```

This is because Python uses indentation to divide code blocks, and the colon indicates that a new indented code block is about to appear. Therefore, this example will report a lack of indented code blocks.

If we replace pass with a comment, it will also report an error: "IndentationError: expected an indented block." 

```python
# Replacing the "pass" statement in the function body with a comment.
def func():
    # todo:There is something here that will be filled in later
func()
```

This is because the comment is not valid syntax content, and it will be ignored by the Python interpreter, unlike the pass statement, which is "valid syntax content but skipped."

In other words, the indented code block must contain syntax-meaningful content. The following examples are all valid.

```python
def func():
    """This is a string"""

def func2():
    123456
```

Python must include the function body when defining a function, which means that it must include both declaration and definition semantics. It cannot define an empty function like some languages, such as writing "void test(){}." 

Therefore, when defining an empty function in Python, it must have a valid function body, so it designed the pass statement to represent a null operation. It is to supplement the completeness of syntax, and together with the colon, it is equivalent to a pair of empty curly braces in other languages.

From the perspective of maintaining syntax integrity, it is a necessary design element. If it is not present, a similar empty statement or special symbol must be used as a replacement.

In summary, pass can represent the meaning of "temporarily skipped" as a temporary placeholder for humans, which will eventually be replaced by actual code implementation. For machines, it can represent "skipped directly," only to complete the syntax logic and will not be replaced by other code. 

From the perspective of syntax completeness, it is a necessary design element, and if it is not available, it must be replaced by similar empty statements or special symbols.

Other programming languages do not have a specific statement or symbol to represent this kind of placeholder (i.e., semantics are lacking), but they also do not need to spend effort on designing a keyword specifically for syntax integrity (i.e., syntax completeness).

Returning to the question at the beginning of this article: why does Python have the "pass" statement, what problems (benefits) does it solve, and what problems (drawbacks) would arise without it?

Python uses the "pass" statement to support code blocks with purely empty operations (empty functions, empty classes, empty loop control blocks, etc.). With it, an additional semantic of a placeholder can be expressed.

The former is necessary for the machine and is equivalent to the use of empty braces in other programming languages. The latter is not necessary for humans and can be expressed through comments. However, because Python has designed this statement, this usage is sometimes quite convenient.