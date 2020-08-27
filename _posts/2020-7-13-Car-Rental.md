---
title:  "Car Rental"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Car-Rental/
---
This is Example 4.2 in the RL book.

### Problem setup

The order of the request, return, cars on hold... can be a little confusing, let's clarify it with an example:

**8 a.m.** \
There are 12 cars in first location, 8 cars in second location, #Car available: [12, 8], #Car on hold: [0,0] \
**9 a.m.** \
Request 5 cars at first location and 6 cars at second location, #Car available: [7, 2], #Car on hold: [0,0] \
**5 p.m.** \
Return 7 cars in first location, 5 cars in second location, #Car available: [7, 2], #Car on hold: [7,5] \
**0:01 a.m. the next day** \
There are 14 cars in first location, 7 cars in second location, #Car available: [14, 7], #Car on hold: [0,0] \
**0:30 a.m. the next day** \
Moving 3 cars from first location to second locatoin, #Car available: [11, 10], #Car on hold: [0,0] \
**8 a.m. the next day** \
There are 11 cars in first location, 10 cars in second location, #Car available: [11, 10], #Car on hold: [0,0]. \
Repeat...


**State** [Total #car at location1, Total #car at location2] at 8 a.m.  \
**Action** The number of car being moved from location1 to location2 overnight. Value is taken from {-5,-4,...,4,5} \
**Reward** Calculate two times each day: 9 a.m., reward of request, $10 per car; 0:30 a.m. the next day, cost of moving cars, $2 per car.



### Algorithm and Implementation

The algorithm is simply the Policy iteration method, whose pseudo code is given as follows:

<div style="text-align:center"><img src="/files/Chapter4/Car/CR_p1.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

The difficulty is the calculation of transition probability. One little trick is that we did not directly calculate $$p(s^{\prime}, r\mid s,a)$$, which will require huge amount of memory. Instead, we transform the updating rule:

$$V(s) = \sum_{s^{\prime}, r} p(s^{\prime}, r\mid s,\pi(s))[r + \gamma V(s^{\prime})]$$\
$$ \;\;\;\;\;\;\;\; = \sum_{s^{\prime}}\sum_r p(s^{\prime}, r\mid s,\pi(s))r + \sum_{s^{\prime}}\sum_r p(s^{\prime}, r\mid s,\pi(s)) \gamma V(s^{\prime})$$\
$$\;\;\;\;\;\;\;\; = \sum_{s^{\prime}} \mathbb{E}[r\mid s,a] + \sum_{s^{\prime}} p(s^{\prime}\mid s,a) \gamma V(s^{\prime})$$ \
$$\;\;\;\;\;\;\;\; = \sum_{s^{\prime}} p(s^{\prime}\mid s,a) (\mathbb{E}[r\mid s,a] + \gamma V(s^{\prime}))$$

In such way, we can pre-calculate $$p(s^{\prime} \mid s,a)$$ and corresponding $$\mathbb{E}[r\mid s,a]$$, then use it to do Policy iteration. There are totally 21 * 21 = 441 states, 11 actions therefore we need 441 * 441 * 11 = 2139291 numbers to represent transition probability $$p(s^{\prime}\mid s,a)$$ and another 2139291 numbers to represent $$\mathbb{E}[r\mid s,a]$$. It's still a bit large.

 We calculate it by enumeration method, it is straightforward, but a little computationally costly for a maximum of 20 cars in each location. It took about 2 minutes to calculate on my laptop. After the pre-calculation is down, it is fast to to the DP updating.

We get Figure 4.2 in the book as follows:

<div style="text-align:center"><img src="/files/Chapter4/Car/Figure4_2.svg" alt="drawing" width="900"/></div>

The stopping criteria for generating above figure is $$\Delta < 0.1$$. Applying different threshold value will result in slightly different results.





## **Code Usage**

Download the code [Car_Rental](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter4/Car_Rental).

**To generate Figure 4.2 in the book:**

 {% highlight Shell %}
 python3 Get_probability.py
 python3 Policy_Iteration.py
 python3 Figure4_2.py {% endhighlight %}



**The End**
