---
layout: post
title: Lorenz Attractor using turtle
categories: [curiosities]
tags:
- curiosities
- python
---

I recently discovered that a nice way to visualize the behavior of the [Lorenz system](http://en.wikipedia.org/wiki/Lorenz_system) is to use Python's [`turtle`](https://docs.python.org/3/library/turtle.html#module-turtle) module. In case you didn't know, the Lorenz system is defined by the equations:

$$
\begin{align}
\frac{dx}{dt} &= \sigma (y-x), \\
\frac{dy}{dt} &= x (\rho - z) - y, \\
\frac{dz}{dt} &= xy - \beta z.
\end{align}
$$

It's interesting that such a simple system of differential equations can lead to such nontrivial results. Here's what I came up with:

{% highlight python3 %}
import turtle
from math import atan2

screen = turtle.Screen()
screen.title('Lorenz Attractor')

t = turtle.Turtle()
t.speed('fastest')
t.pensize(1)
t.pencolor('red')
t.radians()
t.pendown()

dt = 0.01
sigma = 10.0
beta = 2.667
rho = 28.0

x, y, z = 0.1, 1.0, 1.05
dx, dy, dz = 0.0, 0.0, 0.0
scale = 10

while True:
    t.setpos(x*scale, y*scale)
    t.setheading(atan2(dy, dx))
    
    dx = (sigma*(y - x)) * dt
    dy = (x*(rho - z) - y) * dt
    dz = (x*y - beta*z) * dt
    
    x += dx
    y += dy
    z += dz
{% endhighlight %}

Pretty self-explanatory I think, but its output is pretty cool:

![Lorenz attractor GIF]({{ site.url }}/assets/lorenz_attractor_clip.gif)

Here is what gets produced after it runs for a while:

![Lorenz attractor PNG]({{ site.url }}/assets/lorenz_attractor_full.png)
