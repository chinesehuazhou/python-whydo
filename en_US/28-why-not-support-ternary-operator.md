# Why don't Python, Go and Rust have the ternary conditional operator?

The ternary operator is a common syntax shortcut used in many programming languages to simplify conditional logic by allowing developers to execute different blocks of code based on specific conditions. However, Python, as well as two emerging popular languages, Go and Rust, do not have a ternary conditional operator.

In this article, we will analyze the process of Python designing its conditional expression syntax and explain why it adopted its current unique implementation. We will also examine why other languages have abandoned ternary traditional operator.

It is worth noting that, like most articles in the "[Python Whydo](https://github.com/chinesehuazhou/python-whydo)" series, this article focuses on a seemingly small syntax point. However, understanding the rationale, history and philosophy behind language design can enhance our programming skills and lead to clearer and more creative thinking.

## What is the ternary operator?

Ternary operator commonly represented as "?:", is a syntax shortcut that simplify conditional logic by allowing developers to execute different blocks of code based on specific conditions. The syntax takes the form of `condition ? expression1 : expression2`, where `expression1` is taken if `condition` is true, and `expression2` is taken if `condition` is false.

The simplified syntax form of `a ? b : c` can be read as "if the condition `a` is true, then `b` is returned; otherwise, `c` is returned."

Ternary operator serves as a shorthand for the common if-else structure, and is frequently used to perform conditional checks and value assignments in a single statement.

```C
// use if-else 
if (a > b) {
    result = x;
} else {
    result = y;
}

// use the ternary operator
result = a > b ? x : y;
```

There are many programming languages that have adopted this syntax, including C, C#, C++, Java, JavaScript, PHP, Perl, Ruby, Swift, and many others. Undoubtedly, it is the mainstream design solution in the programming language community and remains so to this day.

This syntax is highly concise and efficient and has strong code readability (if you are not encountering it for the first time), making it a favorite of many developers.

However, it is not without its drawbacks. Python is the most famous challenger to this syntax design, and in the following section, we will explore why Python has taken a different approach.

## Vote from the Python community

Python was first released in 1991, but for the next 15 years, apart from the if-else syntax, it did not support ternary operator or any other conditional expression. Even after the introduction of conditional expression in 2006, there was a long and winding debate within the community, making it a highly challenging syntax to design.

In February 2003,  [PEP 308 - Conditional Expressions](https://peps.python.org/pep-0308) was proposed in response to frequent requests for the addition of if-then-else (ternary) expressions. The goal was to choose a solution that would be widely supported by the community.

![PEP-308](https://img.pythoncat.top/20230330231943.png)

Soon, several proposals emerged within the community, with only a few people preferring to do nothing. 

One proposal was to use punctuation to construct ternary operator, which is the same as the syntax introduced earlier:

```
<condition> ? <expression1> : <expression2>
```

This proposal received significant support, and Eric Raymond even implemented this. However, Guido gave two reasons for his opposition: the colon already has many uses in Python (even though it would actually not be ambiguous, because the question mark requires a matching colon); for people not used to C-derived language, it is hard to understand.

Another proposal suggested using a combination of existing and new keywords by introducing a new 'then' keyword in conjunction with the existing 'else' keyword:

```
<condition> then <expression1> else <expression2>
```

This proposal offers several benefits, such as readability, no need for parentheses, no impact on the meaning of existing keywords, lower risk of confusion with statement syntax, and avoidance of additional burden on the colon operator. However, a drawback is that it entails implementation costs associated with introducing a new keyword.

There were other proposals that were similar to the previous one in concept, but did not receive as much support as the first two proposals.

```
(if <condition>: <expression1> else: <expression2>)
<condition> and <expression1> else <expression2>
<expression1> if <condition> else <expression2>
cond(<condition>, <expression1>, <expression2>)
```

It's worth mentioning `(if <condition>: <expression1> else: <expression2>)`, which is a flattened version of the conventional if-else syntax that is easy to understand. However, the downside is that it requires parentheses, which can be confused with generator expressions, and requires special treatment of the colon by the interpreter.

Another noteworthy proposal is `<expression1> if <condition> else <expression2>`, which was the recommended solution in the earliest version of PEP-308. However, some people find the style of not placing the condition first uncomfortable, and when "expression1" is long, it is easy to overlook its condition.

Here are all the design proposals that were voted on at the time:

![](https://img.pythoncat.top/20230331221553.png)

Overall, developers wanted to introduce some form of if-then-else expression, but no proposal gained an absolute advantage in the vote. The main points of disagreement were whether to use punctuation, whether to reuse keywords, whether to reuse parentheses, whether to introduce new keywords, and whether to introduce new syntax.

Due to the scattered votes, the PEP was rejected at the time. The PEP stated, "a Python design principle has been to prefer the status quo whenever there are doubts about which path to take."

## The problem of using and-or for conditional selection

The above voting event occurred in March 2004, but the discussion on the related topic did not die down after the PEP was rejected, as people were always looking for a concise way to replace "if-else".

In September 2005, someone in the mailing list proposed [changing the logic of the "and" and "or" operators in Py3.0](https://mail.python.org/pipermail/python-dev/2005-September/056510.html), suggesting that "and" and "or" operators be simplified to always return a boolean value instead of returning the last evaluated argument.

The reason for this proposal was that he used the `<condition> and <expression1> or <expression2>` syntax to implement conditional selection. However, this syntax behaves differently in Python than in some other languages, and if used improperly, it can result in bugs!

Take a look at the following two examples. What do you think their results will be?

```Python
a = True and True or "Python cat"

b = True and False or "Python cat"
```

For `<condition> and <expression1> or <expression2>`, if the condition is false, expression2 is evaluated and returned directly. If the condition is true, expression1 is evaluated first. If it is also true, expression2 will not be evaluated further. If expression1 is false, expression2 will be evaluated.

Therefore, the variable a in the above example will be "True", while b will be "Python cat".

In the previous article "[Why Python Supports Arbitrary Truth Value Testing?](https://mp.weixin.qq.com/s/g6jZX0IdH9xpM7BMV3-ToQ)", we discussed the special nature of truth value testing in Python. When applied to the above structure, more subtle issues may arise. For example, the author of the email encountered a complex number "0+4i" as "expression1", which is evaluated as false, leading to the unexpected return of "expression2" instead of "expression1"!

Before a better solution was found, "and-or" was a common conditional selection syntax, which was also mentioned in PEP-308. It was also considered ugly and require much more effort to understand.

This email once again sparked a discussion in the community about conditional selection syntax, with many experts joining the conversation.

From my current perspective, developers were not satisfied with the status quo of "if-else", but the popular "and-or" syntax at the time was not good enough. Therefore, people hoped that Python could design a new standardized syntax to solve this pain point.

## Unusual conditional expression

After 10 days of email discussion, Guido van Rossum ultimately decided to add a conditional expression with the syntax `X if C else Y`. As a result, PEP-308 was reopened and updated, and it was soon implemented in the [2.5 version](https://docs.python.org/3/faq/programming.html#is-there-an-equivalent-of-c-s-ternary-operator) the following year.

As mentioned earlier, this solution made some people uncomfortable because it did not put conditional logic at the forefront.

So why did it end up being the winner? Is it the optimal design?

Undeniably, the decisive factor was Guido. Since the community did not form a majority opinion when voting a year and a half ago, he exercised his BDFL (Benevolent Dictator For Life) decision-making power to decide on what he believed to be the best solution.

`X if C else Y` is very easy to understand and highly readable. It continues the style of "explicit is better than implicit" by using the intuitive and conversational "if-else" instead of introducing potentially confusing punctuation, just like how Python chooses the words "and" and "or" instead of the symbols "&&" and "||".

Although the adjusted syntax order may require some adjustment, it has many advantages. First, it only needs to reuse the two keywords "if-else" without introducing other syntax elements like "then" and "when", or the cumbersome `(if <condition>: <expression1> else: <expression2>)`.

Secondly, in order to verify the effectiveness of `X if C else Y`, Guido examined all the "and-or" combinations in the standard library and found that those written as `C and X or Y` could be replaced by `X if C else Y`. The situation in the standard library proved that this new syntax is feasible.

Looking back at this history, we can trace a clue: Python did not design the ternary operator "?:" mainly because it did not fit the clear and intuitive design style of Python. The main intention behind using the `X if C else Y` design was actually to eliminate the pitfalls of the "and-or" syntax. This design is concise and easy to read.

Overall, Python designers highly value readability and maintainability. The decision to create a conditional expression syntax instead of adopting the ternary operator was the result of open discussion, careful evaluation, and trade-offs.

## Why don't Go and Rust have the ternary operator?

After examining the design reasons for Python, let's now look at two other most popular languages.

First is the Go language, which has a specific question in its [FAQ](https://go.dev/doc/faq#Does_Go_have_a_ternary_form): "Why does Go not have the `?:` operator?".

Go language does not support the `?:` operator and instead recommends using the native "if-else" syntax. The explanation in the documentation is brief, with only one sentence:

>The reason `?:` is absent from Go is that the language's designers had seen the operation used too often to create overly complex expressions. The `if-else` form, although longer, is unquestionably clearer. A language needs only one conditional control flow construct.

Moving on to the Rust language, there doesn't seem to be any explanation in its [official documentation](https://doc.rust-lang.org/stable/reference/introduction.html) about the lack of support for the ternary operator. However, after doing some research, I found a fascinating story: in June 2011, Rust once introduced the ternary operator ([#565](https://github.com/rust-lang/rust/commit/05c0216654999e3d33373914b10aebf9dd7d4907)), but six months later, the designers realized that this feature was redundant and removed it ([#1698](https://github.com/rust-lang/rust/issues/1698), [#4632](https://github.com/rust-lang/rust/issues/4632))!

Why was the ternary operator redundant in Rust? Because its `if` syntax is not a "statement" like in other languages, but an "expression", which means that you can directly assign the `if` expression to a variable:

```rust
// Gets 5 if the condition is true, otherwise 6
let number = if condition { 5 } else { 6 };
```

This syntax is simple enough - it's just taking the familiar "if-else" and using it directly for assignment. It's so convenient that replacing it with the ternary operator would feel like unnecessary complication.

Additionally, Rust uses curly braces to delimit code blocks, so the curly braces in the above example can contain multiple expressions and support line breaks, as shown in this example:

```rust
let x = 42;
let result = if x > 50 {
    println!("x is greater than 50");
    x * 2 // This is an expression that will assign its returned value to the variable 'result'
} else {
    println!("x is less than or equal to 50");
    x / 2
};
```

This usage is impossible in Python. The key difference is that in Rust, if is an expression rather than a statement.

The difference between these two concepts is:

-   An expression is an **evaluatable code snippet** composed of variables, constants, operators, etc., whose evaluation result can be used in other expressions or statements.
-   A statement is typically a **single or group of instructions** that perform a task, such as assignment statements, conditional statements, loop statements, etc. It has no return value (or is empty) and cannot be used for assignment operations.

In addition to Rust, there are also other programming languages in which `if` is an expression instead of a statement, such as Kotlin, Scala, F#, and Swift. They theoretically do not need to use the ternary operator. (As an aside, Swift is an exception, and it also has a ternary operator. Kotlin has the "?: " operator, note that the two symbols are connected together. `val result = a ?: b` means: if `a` is not `null`, assign it to `result`; otherwise, assign `b` to `result`.)

Due to this language design difference, Rust and Python/Go have naturally different starting points when facing the question of whether to support the ternary operator. Knowing this difference will give us a clearer understanding of programming languages.

Returning to the question of this article: Why do some programming languages not adopt the mainstream syntax of the ternary operator?

Undeniably, the "?: " syntax is indeed a concise and useful design. However, the potential drawbacks of using punctuation are that they are too abstract, and their readability is not as strong as "if-else". Additionally, different language design styles and usage habits can lead to different choices.

After some twists and turns, Python ultimately designed a unique conditional expression. The Go language explicitly states that it does not support the ternary operator. Rust designed it initially but later abandoned it, mainly due to the language foundation of the if expression.

After examining these three popular languages, I believe you have found a satisfying answer. 

## Author's Note

This article is based on a [Chinese translation](https://pythoncat.top/posts/2023-04-03-condition) and may contain inaccuracies or errors in expression. The author welcomes any feedback or corrections from readers.

Finally, this article is part of the "[Python whydo](https://github.com/chinesehuazhou/python-whydo)" series and all articles have been archived on GitHub. Feel free to star and raise any issues.

