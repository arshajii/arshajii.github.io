---
layout: post
title: Solving the "Cheryl's Birthday Puzzle"
categories: [math]
tags:
- math
---

An interesting logic puzzle has gone viral recently. It goes like this (with minor grammar corrections and clarifications):

> Albert and Bernard have just become friends with Cheryl, and they want to know when her birthday is. Cheryl gives them a list of 10 possible dates, of which her birthday is one:
>
> - May 15
> - May 16
> - May 19
> - June 17
> - June 18
> - July 14
> - July 16
> - August 14
> - August 15
> - August 17
>
> Cheryl then tells Albert the month of her birthday, and tells Bernard the day of her birthday. Albert and Bernard then have the following conversation:
>
> **Albert:** I don't know when Cheryl's birthday is, but I know that Bernard does not know either. <br />
> **Bernard:** At first I didn't know when Cheryl's birthday was, but now I know. <br />
> **Albert:** Then I also know when Cheryl's birthday is.
>
> **So, when is Cheryl's birthday?**

---

If we approach this systematically, it's actually fairly straightforward. Let's look at each of the statements of the conversation one at a time.

First, Albert says *"I don't know when Cheryl's birthday is, but I know that Bernard does not know either"*. The first part of this -- the fact that Albert himself does not know -- is actually pretty obvious, since he can't possibly know the full date if he only has the month (each of the possible months has more than one date on the list). The second part is more interesting: Albert is sure that Bernard also does not know the full date; how can this be? Well, let's think about the cases in which Bernard *could* have potentially known the full date based on the day alone. If Bernard was told "18", then he would know that the true date is "June 18", since that's the only place "18" appears on the list. Similarly, if he was told "19", he would know that the true date is "May 19", since that's the only place "19" appears on the list. All of the other days appear more than once on the list. Now, Albert, based on only the month, is *certain* that Bernard does not know the full date, which means that he is certain that Bernard does now have 18 or 19 as the day. How can Albert be sure of this? The only way is for Albert to have a month other than May or June. If he had May, for example, then there would be a possibility that Bernard had 19, and if he had June, then there would be a possibility that Bernard had 18. Therefore, May and June are off the table:

> - <s>May 15</s>
> - <s>May 16</s>
> - <s>May 19</s>
> - <s>June 17</s>
> - <s>June 18</s>
> - July 14
> - July 16
> - August 14
> - August 15
> - August 17

Now, Albert knew that May and June were off the table from the start, because he knows the true month, but it is important to realize that Bernard will follow the exact same reasoning as above and arive at that conclusion also. So, Bernard now knows that neither May nor June is the true month.

Bernard now says *"At first I didn't know when Cheryl's birthday was, but now I know"*. There are only 4 possible days that Bernard could have at this point: 14, 15, 16, 17. If he has 14, then he still wouldn't know, since both "July 14" and "August 14" would be possible. If he has 15, 16 or 17, however, then he certainly would know, since there is only one remaining date left for each of those days. Since Bernard says he *does* in fact know the true date at this point, we can conclude that the true day is either 15, 16 or 17. Therefore, "July 14" and "August 14" are now off the table as well:

> - <s>May 15</s>
> - <s>May 16</s>
> - <s>May 19</s>
> - <s>June 17</s>
> - <s>June 18</s>
> - <s>July 14</s>
> - July 16
> - <s>August 14</s>
> - August 15
> - August 17

This time, Albert will follow the same reasoning to arive at this conclusion, meaning he also knows that neither "July 14" nor "August 14" are the correct dates.

Finally, Albert says *"Then I also know when Cheryl's birthday is"*. At this point, Albert can only have one of two possible months: July or August. If he has August, then he still wouldn't know the full date, because both "August 15" and "August 17" are still possible. If he has July, however, then he would know for sure, because there is only one remaining date for July. Hence, we can conclude that Albert has July as the month, meaning "August 15" and "August 17" are now off the table:

> - <s>May 15</s>
> - <s>May 16</s>
> - <s>May 19</s>
> - <s>June 17</s>
> - <s>June 18</s>
> - <s>July 14</s>
> - **July 16**
> - <s>August 14</s>
> - <s>August 15</s>
> - <s>August 17</s>

And now we're done: **July 16** is the only remaining possibility, so that's the answer.
