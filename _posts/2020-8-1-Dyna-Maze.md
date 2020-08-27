---
layout: post
title:  "Dyna Maze"
date:   2020-08-1 16:10:15 +1000
categories: jekyll update
---
<div style="text-align:center"><img src="/files/Chapter8/Dyna_Maze/DM_p2.PNG" alt="drawing" width="250"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This is Example 8.1 in RL book.

## **Problem discription**

The agent start at the point 'S' in above figure, and want to go to the goal point 'G'. The grey grid are the obstacle in the maze The actions he can take is "up" or "down" or "right" or "left". If hit the boundary or the obstacle, the agent remain in the previous location.

**State:** [row, column] \
**Action:** 0 or 1 or 2 or 3 , respectively represents "up", "down", "right", "left" \
**Reward:** 0 every step until termination, 1 when arrive the goal point.


## **Implementation**

***Figure 8.2***

Following exactly the Tabular Dyna-Q algorithm, which is given in the book.

<div style="text-align:center"><img src="/files/Chapter8/Dyna_Maze/DM_p1.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

The book uses random seed for the first episode, making it about 1700 steps. As a result, we tried a few times with different lists of seed, and finally settled down with a list of seed producing 1707 steps for the first episode, saved it and used it every time afterwards.

Hence, it is straight forward to produce Figure 8.2 in the book as follows:

<div style="text-align:center"><img src="/files/Chapter8/Dyna_Maze/history.svg" alt="drawing" width="500"/></div>

***Figure 8.3***

Figure 8.3 demonstrates the policy and agent position in the "halfway" of the second episode. In the implementation, we take "halfway" as `round(total_step / 2)`. Then, we get Figure 8.3 as follows: (The plot varies randomly every time of running)

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**Without planning (n=0)**  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**With planning (n=50)**\
<img src="/files/Chapter8/Dyna_Maze/policy0.png" alt="drawing" width="350"/>
<img src="/files/Chapter8/Dyna_Maze/policy50.png" alt="drawing" width="350"/>


By the way, we also present the situation when n=5 and n=25.

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**With planning (n=5)**  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**With planning (n=25)**\
<img src="/files/Chapter8/Dyna_Maze/policy5.png" alt="drawing" width="350"/>
<img src="/files/Chapter8/Dyna_Maze/policy25.png" alt="drawing" width="350"/>




## **Code Usage**

Download the code [Dyna_Maze](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter8/Dyna_Maze).

To produce Figure 8.2 in the book:
{% highlight Bash %}
python3 Get_data.py -n 0
python3 Get_data.py -n 5
python3 Get_data.py -n 50
python3 plot_history.py {% endhighlight %}

To generate Figure 8.3 in the book:

{% highlight Bash %}
python3 plot_policy.py -n [any step n (n-step planning) you want] {% endhighlight %}








**The End**
