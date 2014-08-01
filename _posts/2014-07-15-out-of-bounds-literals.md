---
layout: post
title: Out-of-Bounds literals in Java
categories: [programming]
tags:
- programming
- java
---

A few days ago I answered [this](http://stackoverflow.com/questions/24676375) Java question on Stack Overflow, and since then it seems to have garnered a lot of attention, so I thought I'd write a quick post on the topic touched by the question.

To start, note that the `int` type in Java can hold integral values from `-2147483648` to `2147483647`, inclusive. Now, The question can be reformulated as follows:

How come the Java compiler rejects

{% highlight java %}
int n = 2147483648;
{% endhighlight %}

but accepts

{% highlight java %}
int n = 2147483647 + 1;
{% endhighlight %}

Isn't `2147483647 + 1` a constant expression that "evaluates" to `2147483648` and, therefore, subject to rejection just like the literal `2147483648` in the first snippet? Or, shouldn't the fact that we are unable to assign an out-of-range value in the first snippet apply also for the second? The answers of course are "no".

The *real* issue here is that the `2147483648` in the first snippet is simply an invalid literal; it isn't syntactically correct. From [JLS §3.10.1](http://docs.oracle.com/javase/specs/jls/se8/html/jls-3.html#jls-3.10.1):

> **It is a compile-time error if a decimal literal of type `int` is larger than `2147483648` (2<sup>31</sup>), or if the decimal literal `2147483648` appears anywhere other than as the operand of the unary minus operator ([§15.15.4](http://docs.oracle.com/javase/specs/jls/se8/html/jls-15.html#jls-15.15.4)).**

(It's really the bit after the "or" that applies here.) `2147483647 + 1`, on the other hand, is perfectly valid; we're just adding two numbers and there just happens to be an integer overflow.

I appreciate that the answers to these types of questions could all potentially be *"because the language specification says so"*, but hopefully this has provided *some* insight beyond that.


