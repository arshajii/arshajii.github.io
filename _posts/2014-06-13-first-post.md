---
layout: post
title: First Post
tags:
- test
---

This is more of a test post than anything. Let's see if everything works...

MathJax...

$$\begin{align}
\int_0^\infty x^{2n} e^{-a x^2}\,dx & = \frac{2n-1}{2a} \int_0^\infty x^{2(n-1)} e^{-a x^2}\,dx \\
 & = \frac{(2n-1)!!}{2^{n+1}} \sqrt{\frac{\pi}{a^{2n+1}}} \\ 
 & = \frac{(2n)!}{n! 2^{2n+1}} \sqrt{\frac{\pi}{a^{2n+1}}}
\end{align}$$

And inline MathJax: $E = mc^2$

And code snippets:

{% highlight java %}
class Hello {
    public static void main(String... args) {
        System.out.println("hello, world!");
    }
}
{% endhighlight %}

And tables...

| First cell|Second cell|Third cell
| First | Second | Third |
| X | Y | Z |
| A | B | C |

And lists...

- Item 1
- Item 2
- Item 3

1. Item 1
2. Item 2
3. Item 3
