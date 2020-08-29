---
title:  "Expected vs. Sample Updates"
date:   2020-08-2 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/EvS/
---
This is Example 8.4 in RL book.

## **Problem discription**

The dynamic model in this problem is very simple, it is essentially the backup diagram shown below.
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter8/Exp_sample/PS_p2.PNG" alt="drawing" width="150"/>
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter8/Exp_sample/PS_p3.png" alt="drawing" width="200"/>

As the author says "the values at the next state are assumed correct", we don't need to know how many actions there are. Then, the model is simplified to the figure on the right hand side.


## **Main idea**

Expected updated method:

$$Q(s,a) \leftarrow \sum_{s^{\prime},r} \hat{p}(s^{\prime},r|s,a)[r+\gamma \max_{a^{\prime}} Q(s^{\prime},a^{\prime})]$$

Sample update method:

$$Q(s,a) \leftarrow Q(s,a) + \alpha[R+\gamma \max_{a^{\prime}} Q(s^{\prime},a^{\prime}) - Q(s,a)]$$

under different *branching factor b*. The *branching factor b* represents the number of possible next states that a State-Action pair $$Q(S,A)$$ can give with probability larger than zero.

Let's define a "computational unit" as "calculate $$\max_{a^{\prime}} Q(s^{\prime},a^{\prime})$$ once".

In this problem, we are going to use the expected update once (contains b computational units), and use the sample update many times (contains 1 computational units per time), and compare their performance in terms of estimating the action-value $$Q(S,A)$$.

## **Implementation**

The branching factor used for evaluation is [2,10,100,1000,10000]. The value for "next state" is generated from Gaussian distribution (mean = 1, variance = 1). The transition probability to all "next state" are identical. The number of repetition for each of the branching factor is [4000,2000,500,300,200] respectively. Set the reward to zero, as we want to keep it simple. In addition, since we assume there is only one "current state", the RMS error is identical to the absolute value of error.

With above information, we can get a plot very similar to Figure 8.7 in the book as follows:
<div style="text-align:center"><img src="/files/Chapter8/Exp_sample/history_v3.svg" alt="drawing" width="550"/></div>


By the way, we also plot a figure for *Exercise 8.6*, where the transition probability to all "next state" is highly skewed. We generate the transition probability randomly in each repetition for every "next state", and normalize them to a sum of 1. We get the following figure. It is very similar to Figure 8.7 in the book. The only slight difference is that all curves in the figure below are a little bit lower than in above figure, which means the performance of sample updates is better with highly skewed transition probability. (I'm not sure about the correctness of this conclusion)

<div style="text-align:center"><img src="/files/Chapter8/Exp_sample/history_skewed_v2.svg" alt="drawing" width="550"/></div>


## **Code Usage**

Download the code [Expected_Sample_Update](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter8/Expected_Sample_Update).

To produce Figure 8.7 in the book:
{% highlight Bash %}
python3 Exp_update.py {% endhighlight %}

To produce figure for Exercise 8.6 in the book:

{% highlight Bash %}
python3 Exp_update_skewed.py {% endhighlight %}








**The End**
