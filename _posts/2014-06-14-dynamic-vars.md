---
layout: post
title: (FAQ) Dynamic variables
categories: [programming]
tags:
- programming
- java
- faq
---

### Q: How can I create an arbitrary number of variables that I can reference dynamically in Java?

For example:

{% highlight java %}
int a0 = 42;
int a1 = 12;
int a2 = -9;
...    

System.out.println(a[some_int]);
{% endhighlight %}

### A:

**TL;DR:** You can't (at least not easily) -- you should use an array or collection for this purpose instead.

---

Something like this is not directly possible in Java because local variable names are merely a convenience for the programmer; they are not kept track of after your code is compiled. You can see this yourself by looking at compiled bytecode (with `javap -c`). For example:

{% highlight java %}
int someCoolName = 0;
int anotherName = 0;
		
System.out.println(someCoolName);
{% endhighlight %}

will become:

<pre>
ICONST_0
ISTORE 1
ICONST_0
ISTORE 2
GETSTATIC java/lang/System.out : Ljava/io/PrintStream;
ILOAD 1
INVOKEVIRTUAL java/io/PrintStream.println(I)V
</pre>
    
Notice that the names we gave our variables do not show up in the bytecode.

Now, in theory it could be possible to compile your code with the `-g` option of `javac` which would cause the `.class` file to contain all debugging information, *including* local variable names. You could then extract this data and use it to dynamically access variables. As you can probably imagine, this is more trouble than its worth, and will simply perform poorly (not to mention it relies on the condition that the code was compiled using `-g`).

The correct way to go about this problem would be to use some sort of collection or an array, depending on the context.

If your "variable names" are linear (e.g. `a0`, `a1`, `a2` …), then more often than not a [`List`](http://docs.oracle.com/javase/7/docs/api/java/util/List.html) would be most appropriate. Adapting the snippet in the question:

{% highlight java %}
List<Integer> a = new ArrayList<>(Arrays.asList(42, 19, -9));
{% endhighlight %}
    
(It goes without saying that in any *real* code you should use a better name than `a`.) In any case, the idea is still here; now that we have this list, the `a[some_int]` above can become `a.get(some_int)`.

---

[Back](.)
