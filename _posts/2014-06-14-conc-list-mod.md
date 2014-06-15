---
layout: post
title: (FAQ) Removing elements while iterating over a collection
categories: [programming]
tags:
- programming
- java
- python
- faq
---

### Q: Why does removing elements from a list while iterating over it "skip" some elements?

### A:

Many programming newcomers try to do something like the following:

{% highlight python %}
# pseudocode
for index in list.indices {
    if (some_condition) {
        list.remove(index);
    }
}
{% endhighlight %}
    
and are surprised to find that some elements that should be removed are not.

To understand why this happens, imagine that we want to remove all `b`s from the list `[a, b, b, c]` while looping over it as in the pseudocode above. It'll look something like this:

<pre>
+-----------------------+
|  a  |  b  |  b  |  c  |
+-----------------------+
   ^ (first iteration)
   
+-----------------------+
|  a  |  b  |  b  |  c  |
+-----------------------+
         ^ (next iteration: we found a 'b' -- remove it)
         
+-----------------------+
|  a  |     |  b  |  c  |
+-----------------------+
         ^ (removed b)
         
+-----------------+
|  a  |  b  |  c  |
+-----------------+
         ^ (shift subsequent elements down to fill vacancy)
         
+-----------------+
|  a  |  b  |  c  |
+-----------------+
               ^ (next iteration)
</pre>

Notice that we skipped the second `b`! Once we removed the first `b`, elements were shifted down and our `for`-loop consequently failed to touch every element of the list.

This problem *can* be solved if we iterate over the list backwards:

<pre>
+-----------------------+
|  a  |  b  |  b  |  c  |
+-----------------------+
                     ^ (first iteration)
   
+-----------------------+
|  a  |  b  |  b  |  c  |
+-----------------------+
               ^ (next iteration: we found a 'b' -- remove it)
         
+-----------------------+
|  a  |  b  |     |  c  |
+-----------------------+
               ^ (removed b)
         
+-----------------+
|  a  |  b  |  c  |
+-----------------+
               ^ (shift subsequent elements down to fill vacancy)
         
+-----------------+
|  a  |  b  |  c  |
+-----------------+
         ^ (next iteration)
         
etc.
</pre>

Since removing an element results in subsequent elements being shifted *down* as opposed to previous elements being shifted *up*, iterating in reverse order circumvents the skipping issue.

**However,** there are generally better approaches than iterating backwards. For example, in Java, the appropriate method would be to use an `Iterator` as returned by the [`iterator`](http://docs.oracle.com/javase/7/docs/api/java/util/Collection.html#iterator()) method of `Collection` and to call `remove` on it:

{% highlight java %}
Iterator<Object> iter = list.iterator();

while (iter.hasNext()) {
    Object elem = iter.next();
    ...
    if (some_condition)
        iter.remove();
}
{% endhighlight %}
    
In Python, the appropriate method would be to use a [list comprehension](http://docs.python.org/3.3/tutorial/datastructures.html#list-comprehensions):

{% highlight python %}
list[:] = [elem for elem in list if not some_condition]
{% endhighlight %}
    
or, possibly, the built-in function [`filter()`](http://docs.python.org/3.3/library/functions.html#filter).
