---
layout: post
title:  "Dyna Maze (Dyna-Q+)"
date:   2020-08-2 16:10:15 +1000
categories: jekyll update
---
This is Example 8.2 and 8.3 in RL book.

## **Problem discription**

The agent start at the point 'S' in above figure, and want to go to the goal point 'G'. The grey grid are the obstacle in the maze The actions he can take is "up" or "down" or "right" or "left". If hit the boundary or the obstacle, the agent remain in the previous location. During the training, the map will change, resulting in a wrong model within Dyna-Q(+) agent. A good learning agent should be able to correct the model and find way to reach the goal point in the changed map. Dyna-Q and Dyna-Q+ will be compared under two situations: Blocking maze and shortcut maze.

**State:** [row, column] \
**Action:** 0 or 1 or 2 or 3 , respectively represents "up", "down", "right", "left" \
**Reward:** 0 every step until termination, 1 when arrive the goal point.

### **Difference between blocking maze and shortcut maze**

**Blocking Maze** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter8/Blocking_Maze/BM_pc1.PNG" alt="drawing" width="400"/>

**Shortcut Maze** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter8/Blocking_Maze/BM_pc2.PNG" alt="drawing" width="400"/>

&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Map A** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
**Map B**

Let's call the initial map as "Map A", and the changed map as "Map B"

In blocking maze, the change from Map A to Map results in the block of the original path from $$S$$ to $$G$$. The states (or corresponding state-action pair) near the original path has high value before the change and right after the change, this is what the author called "optimistic (in the sense of better state transitions)". Hence, as the book says, the agent experience these "optimistic" state-action pair and discover that they are actually not good at all. Exploitation in the sense of Map A is the exploration in the sense of Map B.

In shortcut maze, the original path from $$S$$ to $$G$$ still exist. Exploitation in the sense of Map A is still near exploitation in the sense of Map B. So the agent may not find the better shortcut, with Dyna-Q algorithm.

## **Implementation**

***Example 8.2***

The book does not give any pseudo code for Dyna-Q+, so the key point is to figure out the algorithm by ourselves from the description in words. For reader's convenience, we present Dyna-Q+ algorithm on the base of Dyna-Q.

<div style="text-align:center"><img src="/files/Chapter8/Blocking_Maze/BM_pc3.png" alt="drawing" width="600"/></div>

For completeness, we still present the Tabular Dyna-Q algorithm here.

<div style="text-align:center"><img src="/files/Chapter8/Dyna_Maze/DM_p1.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

The parameters are not fully given in the book. As a result, we give the parameters that we used for the following plots: \
Step size: $$\alpha = 1$$ \
Discounting factor: $$\gamma = 0.95$$ \
$$\epsilon$$-greedy policy: $$\epsilon = 0.1$$ \
N-step planning: n = 50 \
Elapsed time weight: $$\kappa = 0.00005$$ (For Dyna-Q+, in Blocking maze) \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;$$\kappa = 0.001$$ (For Dyna-Q+, in Shortcut maze) \
Averaged over 50 runs.

We get Figure 8.4 and 8.5 as follows:\
<img src="/files/Chapter8/Blocking_Maze/history.png" alt="drawing" width="360"/>
<img src="/files/Chapter8/Blocking_Maze/history_sc.png" alt="drawing" width="360"/>








## **Code Usage**

Download the code [Blocking_Maze](https://github.com/MingruiSun2019/RLbook_reproducing/tree/master/Chapter8/Blocking_Maze).

To produce Figure 8.4 in the book:
{% highlight Bash %}
python3 Get_data_Q.py -n 50
python3 Get_data_Qplus.py -n 50
python3 plot_history.py {% endhighlight %}

To generate Figure 8.5 in the book:

{% highlight Bash %}
python3 Get_data_Q_shortcut.py -n 50
python3 Get_data_Qplus_shortcut.py -n 50
python3 plot_history_sc.py {% endhighlight %}








**The End**
