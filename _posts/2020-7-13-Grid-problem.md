---
title:  "Grid problem"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Grid-problem/
---
This is **Example 4.1** in the RL book.

### Problem setup

Iterative policy evaluation for grid problem:

<div style="text-align:center"><img src="/files/Chapter4/Grid/grid.JPG" alt="drawing" width="250"/></div>

**State:** S={1,2,3,...,14}. Grey grid are terminal states. \
**Action:** A = {up, down, right, left}, represented by array [0,1,2,3] \
**Policy:** Equiprobable random policy (all actions equally likely)

### Implementation

Use the pseudo code provided in the book as a guide for this example:

<div style="text-align:center"><img src="/files/Chapter4/Grid/E41_pc.JPG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

A key point to notice in this example is that, we cannot replicate Figure 4.1 in the book if just completely following the pseudo code. In the pseudo code, after V(s) is updated, it is immediately used for bootstrap (Gauss–Seidel-style algorithm). However, to replicate Figure 4.1, the updated V(s) should be hold until the current sweep of all states are finished, then used for bootstrap in sweeps afterwards (Jacobi-style algorithm). This part of code can be modified as:

<div style="text-align:center"><img src="/files/Chapter4/Grid/E41_pc2.JPG" alt="drawing" width="700"/></div>

We get Figure 4.1 in the book as follows:

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;<img src="/files/Chapter4/Grid/Grid1.png" alt="drawing" width="220"/> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter4/Grid/Grid2.png" alt="drawing" width="222"/>


## **Code Usage**

Download the code [Grid_problem](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter4/Grid_problem).

To produce Figure 4.1 in the book:

**To draw the state-value function:**
{% highlight Bash %}
python3 Grid_plot.py --option 0 {% endhighlight %}

**To draw the greedy policy:**
{% highlight Bash %}
python3 Grid_plot.py --option 1 {% endhighlight %}












**The End**
