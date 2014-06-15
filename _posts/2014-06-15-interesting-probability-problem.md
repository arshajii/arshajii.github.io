---
layout: post
title: An interesting problem in probability
tags:
- math
---

This is a problem I first saw in [*Fifty Challenging Problems in Probability*](http://www.amazon.com/Challenging-Problems-Probability-Solutions-Mathematics/dp/0486653552) by Frederick Mostaller. I decided to write briefly about it because I find the solution to be extremely unintuitive and interesting. Much of what's below is derived from Mostaller's solution. Note that this problem is also called the [secretary problem](http://en.wikipedia.org/wiki/Secretary_problem) and [sultan's dowry problem](http://mathworld.wolfram.com/SultansDowryProblem.html).

The problem boils down to the following:

> $100$ random numbers are chosen and each is written on its own slip of paper. You are given the task of selecting the largest of these numbers. You are handed the numbers one by one, and on each number you can either *select* it if you think it is the largest or you can continue on to the next number. Once you select a number, the game ends, and you win if you have chosen the largest number, otherwise you lose. What strategy should you employ?

Your chances of winning this game are significantly higher than most people initially think (assuming of course you use the optimal strategy). Let's generalize the problem so that instead of $100$ numbers we have $n$. 

If we select the first number we are given, our chances of winning are $1 \over n$. If instead we opt to select the second number, things get slightly more complicated:

- If the second number is *smaller* than the first number we were given, we know we will lose if we pick it, since it can't possibly be the largest. Therefore, we might as well continue until we reach a number larger than the first, and select *that* number.

- If the second number is *larger* than the first number we were given, we can simply select it.

In general, we can formulate a strategy where:

1. We let $s - 1$ numbers pass, recording the maximum number $X$ that we saw.

2. From the $s^{\text{th}}$ number onward, we select the first that is greater than $X$.

3. If no such number is encountered, we will lose.

The question now is what's the probability $P(s,n)$ of winning if we use this strategy. Assume we are on the $k^{\text{th}}$ number (where $s \le k \le n$); the probability that this number is the largest is $1 \over n$, and the probability that we *select* this number is $\frac{s-1}{k-1}$, since the largest of the last $k-1$ numbers must have been in the first $s-1$ (otherwise we would have selected an earlier number). The total probability of winning on the $k^{\text{th}}$ number is, therefore:

$$
P_k(s,n) = \frac{s-1}{n(k-1)}.
$$

To obtain the overall probability of winning, we need to sum $P_k$ over all possible $k$:

$$
\begin{align}
P(s,n) &= \sum_{k=s}^{n}P_k(s,n) \\
&= \sum_{k=s}^{n}\frac{s-1}{n(k-1)} \\
&= \frac{s-1}{n}\sum_{k=s}^{n}\frac{1}{k-1} \\
&= \frac{s-1}{n}\sum_{k=s-1}^{n-1}\frac{1}{k} \\
&\approx \frac{s-1}{n}\left(\ln\left(\frac{n-1}{s-1}\right) + \frac{1}{2}\left(\frac{1}{n-1} - \frac{1}{s-1}\right)\right).
\end{align}
$$

Now, we need to maximize this function with respect to $s$. We can do so by taking the derivative and setting it to $0$ (let's call the optimum $s$ value $s^*$). I'll use Mathematica to do the heavy lifting:

{% highlight java %}
P[s_, n_] := ((s - 1)/
     n)*(Log[(n - 1)/(s - 1)] + (1/(n - 1) + 1/(s - 1))/2);
     
Simplify[D[P[s, n], s]]
{% endhighlight %}

This gives us the following result:

$$
\frac{\partial P}{\partial s} = \frac{3-2n}{2n(n-1)} + \frac{1}{n}\ln\left(\frac{n-1}{s-1}\right), \\
\frac{3-2n}{2n(n-1)} + \frac{1}{n}\ln\left(\frac{n-1}{s^*-1}\right) = 0, \\
\therefore s^* = e^{\frac{3-2n}{2n-2}}\left(e^{\frac{3-2n}{2-2n}} + n - 1\right).
$$

<p>
We can verify that this is indeed a <i>maximum</i> by computing the second derivative and checking that $\left.\frac{\partial^2 P}{\partial s^2}\right|_{s=s^*} \lt 0$. Again, I'll use Mathematica:
</p>

{% highlight java %}
N[D[D[P[s, n], s], 
    s] /. {s -> 
     E^((3 - 2 n)/(
      2 (-1 + n))) (-1 + E^((3 - 2 n)/(2 - 2 n)) + n)} /. {n -> 100}]
{% endhighlight %}

<p>
which gives us $-0.000273191 \lt 0$, so we know there is a local maximum at $s = s^*$ (with a little extra work you can show that $\left.\frac{\partial^2 P}{\partial s^2}\right|_{s=s^*} \lt 0$ for all $n$).
</p>

Now we can find $P^* = P(s^*, n)$; plugging in and simplifying yields the unholy expression

$$
P^* = \frac{e^{-\frac{n}{n-1}} \left(e^{\frac{n}{n-1}}+e^{\frac{3}{2 (n-1)}}+2 e^{\frac{3}{2 (n-1)}} (n-1) \log \left(e^{\frac{3-2 n}{2-2 n}}\right)\right)}{2 n}
$$

Returning now to the problem as stated with $n=100$, we find that:

$$
s^* = 37.60 \approx 38, \\
P^* = 0.371.
$$

Our chances of winning are really quite astonishingly high in my view -- much higher than one might first suspect. It can be shown that as $n \to \infty$:

$$
s^* \to \frac{n}{e}, \\
P^* \to \frac{1}{e}.
$$

So even if we play the game with billions of numbers, we still have about a $37\%$ chance of winning if we use this strategy! 

I decided to write some Python code to simulate playing the game just as a sanity check (the code is written in Python 2):

{% highlight python %}
def run(s, n):  # simulates playing the game once
    from random import shuffle
    nums = range(1, n+1)
    shuffle(nums)
    head = max(nums[:s-1])  # max from first s-1 numbers
    pick = next((num for num in nums[s-1:] if num > head), 0)
    return (pick == n)  # we win if we picked the max number

s, n = 38, 100

trials = 100000
wins = 0

for _ in xrange(trials):
    result = run(s, n)
    if result:  # i.e. if we won
        wins += 1

print 'wins/total = %d/%d = %.3f' % (wins, trials, float(wins)/trials)
{% endhighlight %}

which sure enough gives us the following output:

<pre>
wins/total = 37026/100000 = 0.370
</pre>

### References

1. Mosteller, Frederick. *Fifty Challenging Problems in Probability with Solutions*. New York: Dover Publications, 1987. Print.
