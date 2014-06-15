---
layout: post
title: (FAQ) Comparing strings in Java
categories: [programming]
tags:
- programming
- java
- faq
---

### Q: How come comparing strings in Java with `==` produces unexpected results?

### A:

**TL;DR:** Use the `equals` method for comparing strings; i.e. `s1.equals(s2)` *as opposed to* `s1 == s2`. The former compares values whereas the latter compares references.

---

This is the source of a common problem encountered by many Java beginners; they try to compare two strings via `==` and are surprised to find that the result is false for two equivalent strings.

The appropriate way to compare string `s1` with string `s2` is via [`equals`](http://docs.oracle.com/javase/7/docs/api/java/lang/String.html#equals(java.lang.Object)):

{% highlight java %}
s1.equals(s2)
{% endhighlight %}
    
It's important to understand *why* this is the case. The problem lies in how `==` works with non-primitive arguments. Specifically, in that it compares *references* and not *values*. Two distinct string objects with identical values can be stored in different places, and in the eyes of `==` these two strings are not the same:

<sub>**Fig. 1**</sub>
<pre>
+------+
|  s1  |--------> "abc"
+------+

+------+
|  s2  |--------> "abc"
+------+
</pre>

(Note that it is important to think of Java variables as *pointers*; a variable consists of a reference to an *object* in memory.)

In the above situation, `s1 == s2` will result in `false` since `s1` and `s2` do not *refer to the same object*, even though the objects to which they do refer are equal in terms of value. Now, if you had a second case like:

<sub>**Fig. 2**</sub>
<pre>
+------+
|  s1  |--------> "abc"
+------+            ^
                    |
+------+            |
|  s2  |------------+
+------+
</pre>

*then* `s1 == s2` would in fact be `true`, since the two variables *do* refer to the same object.

You can even see this in actual code. Because string literals are interned in a string pool, two equivalent string literals will always refer to the same string instance which is cached inside this pool:

{% highlight java %}
String s1 = "abc";
String s2 = "abc";
    
System.out.println(s1 == s2);
{% endhighlight %}
	
<pre>
true
</pre>

Here we have the case illustrated in *Fig. 2*, so the output is `true`. By contrast, here:

{% highlight java %}
String s1 = "abc";
String s2 = new String("abc");  // distinct instance

System.out.println(s1 == s2)
{% endhighlight %}
	
<pre>
false
</pre>

we have the case depicted in *Fig. 1*, so the output is `false`.

In short, if you want to compare two strings by value (which more often than not is the case), you should use the `equals` method, which goes through the characters of each string one by one to check for equality.
