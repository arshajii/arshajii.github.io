---
layout: post
title: (FAQ) Python list multiplication
categories: [programming]
tags:
- programming
- python
- faq
---

### Q: In Python, how come mutating one nested list in a list of lists mutates all of them in the following scenario?

{% highlight pycon %}
>>> l = [[]] * 3
>>> l[0].append(42)
>>> l
[[42], [42], [42]]
{% endhighlight %}


The expected output is `[[42], [], []]`.

### A:

First of all, it's important to understand that Python lists are actually lists of *pointers*. Each position of a list *points to* an object in memory. Therefore, multiple list positions can point to the same object.

Now to answer the question: it's because multiplying a list duplicates the pointers to the elements as opposed to creating copies of them. In other words, the three nested lists in the example above are all really the *same* object that is being pointed to from three different places, so of course a change in one shows up in the others.

We can see this by using [`id()`](http://docs.python.org/3/library/functions.html#id):

{% highlight pycon %}
>>> l = [[]] * 3
>>> id(l[0]), id(l[1]), id(l[2])
(2945232, 2945232, 2945232)
{% endhighlight %}
    
Notice that all three elements have the same identity.

Mutating one of the members of `l` here is kind of like doing the following:

{% highlight pycon %}
>>> a = []
>>> l = [a, a, a]
>>> a.append(42)  # <--
>>> l
[[42], [42], [42]]
{% endhighlight %}
    
This is to be expected since `l` contains several references to the same object: `a`.

So, overall, the list `[[]] * 3` looks like this:

<pre>
        [ elem1, elem2, elem3 ]
            |      |      |
            |      |      |
            |      |      |
            +-->  <b>[ ]</b>  <--+
</pre>

All three elements refer to the *same* list.            

If you want to produce a list of `n` *independent* sublists, a [list comprehension](http://docs.python.org/3.3/tutorial/datastructures.html#list-comprehensions) can be used instead of multiplication:

{% highlight pycon %}
>>> l = [[] for _ in range(3)]
>>> l[0].append(42)
>>> l
[[42], [], []]
{% endhighlight %}
    
Now `l` looks like this:

<pre>
        [ elem1, elem2, elem3 ]
            |      |      |
            |      |      |
            |      |      |
           <b>[ ]</b>    <b>[ ]</b>    <b>[ ]</b>
</pre>

Each element is unique: mutating one will not affect the others.

As a result of all this, you should exercise caution when multiplying a list whose elements are mutable. For a list of immutable types, there will not be a problem of this kind, so something like `[0] * 10` is perfectly valid and reasonable.

