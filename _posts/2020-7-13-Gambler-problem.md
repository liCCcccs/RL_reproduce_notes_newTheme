---
layout: post
title:  "Gambler's problem"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
---
This example is initiated in Chapter 2.3 in the RL book.

### Problem setup

A gambler has the opportunity to make bets on the outcomes of a sequence of coin flips. He wins the stake if it is head, or lose the stake if it is tail. The probability of the coin coming up heads is \\(P_h\\). The gambler wants to find an optimal policy that decides what portion of his capital to stake, given the amount of capital he has.

**Parameter:**
1. Goal: $100
2. \\(P_h\\): 0.4

**State** s \\(\in\\) {1, 2,..., 99} is the capital the gambler has \
**Action** a \\(\in\\) {0, 1, 2,..., min(s,100-s)} is the stake given he has s capital



### Implementation

To implement this example, just follow the value iteration algorithm on page 83 of the book.

The key point is calculate the transition probability \\( p(s^{\prime}, r \| s, a) \\).

$$ p(s^{\prime}, r | s, a) = \begin{cases}
	0.4,           	& s^{\prime} = s+a,\; r = 1 \\
	0.6,  & s^{\prime} = s-a,\; r = 0 \end{cases}, \;\; for \;\; s+a = 100 $$

$$ p(s^{\prime}, r | s, a) = \begin{cases}
  	0.4,           	& s^{\prime} = s+a, \;r = 0 \\
  	0.6,  & s^{\prime} = s-a,\; r = 0 \end{cases}, \;\; for \;\; s+a \neq 100 $$

Set the discount factor \\(\gamma = 1\\), we get Figure 4.3 as follows.

<div style="text-align:center"><img src="/files/Chapter4/Gambler/val_est_plot.png" alt="drawing" width="600"/></div>

It seems that this figure in the book is not correct. Sweep 32 should be sweep 4.

The entire family of optimal policies are shown below.

<div style="text-align:center"><img src="/files/Chapter4/Gambler/all_policy_raw.png" alt="drawing" width="600"/></div>

As the author says, the optimal policy is not unique. Figure 4.3 chooses one policy that is labeled with red line in the figure below

<div style="text-align:center"><img src="/files/Chapter4/Gambler/all_policy.png" alt="drawing" width="600"/></div>

*Exercise 4.9*

**When** \\(p_h = 0.25\\)

<div style="text-align:center"><img src="/files/Chapter4/Gambler/val_est_plot_Pr25.png" alt="drawing" width="600"/></div>

<div style="text-align:center"><img src="/files/Chapter4/Gambler/all_policy_Pr25.png" alt="drawing" width="600"/></div>

**When** \\(p_h = 0.55\\)

<div style="text-align:center"><img src="/files/Chapter4/Gambler/val_est_plot_Pr55.png" alt="drawing" width="600"/></div>

<div style="text-align:center"><img src="/files/Chapter4/Gambler/all_policy_Pr55.png" alt="drawing" width="600"/></div>


## **Code Usage**

Download the code [Gambler problem](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter4/Gambler_problem).

**To generate Figure 4.3 in the book:**

 First generate data:
 {% highlight Shell %}
 python3 Get_data.py{% endhighlight %}

 Then, plot the figure by:

 {% highlight Shell %}
 python3 Gambler_plot.py -o 0{% endhighlight %}
 to save the resulting images in `./img` folder.

Or

 {% highlight Shell %}
 python3 Gambler_plot.py -o 1{% endhighlight %}
 to get interactive plots.

For other plots in Exercise 4.9, simply change the value of `Pr_head` in `Get_data.py` and corresponding filenames in `Gambler_plot.py`


**The End**
