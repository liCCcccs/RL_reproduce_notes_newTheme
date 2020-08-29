---
title:  "Windy Grid World (SARSA)"
date:   2020-07-26 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Windy/
---
<div style="text-align:center"><img src="/files/Chapter6/Windy_GridWorld/WGW_p1.PNG" alt="drawing" width="400"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This is Example 6.4 in the RL book. Exercise 6.9 and 6.10 are also solved.

## **Problem discription**

A man is born at the start point 'S' in above figure, and want to go to the goal point 'G'. The actions he can take is "up" or "down" or "right" or "left". In addition, his movement is also affect by the wind.

**State:** [row, column] \
**Action:** 0 or 1 or 2 or 3 , respectively represents "up", "down", "right", "left" \
**Reward:** -1 every step until termination

## **Implementation**

### Example 6.4

The algorithm (SARSA) for this problem is given in the book:
<div style="text-align:center"><img src="/files/Chapter6/Windy_GridWorld/WGW_p2.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

Log the training data and we can produce the 'training history'. When plotting the trajectory, we take greedy actions, instead of following the $$\epsilon$$-greedy policy. We get the figure in Example 6.4 in the RL book:
<img src="/files/Chapter6/Windy_GridWorld/original.png" alt="drawing" width="360"/>
<img src="/files/Chapter6/Windy_GridWorld/history.png" alt="drawing" width="360"/>

### Exercise 6.9, King's move

Just define 4 more actions, "north-east", "north-west", "south-east", "south-west", and keep the rest of the code the same. With king's move, it indeed performs better, taking only 7 steps to the goal point.
<div style="text-align:center"><img src="/files/Chapter6/Windy_GridWorld/kingsMove.png" alt="drawing" width="400"/></div>

Adding the ninth action: "Not move", and training again, we get the following trajectory. It still takes 7 steps to reach the goal point.
<div style="text-align:center"><img src="/files/Chapter6/Windy_GridWorld/nineMove.png" alt="drawing" width="400"/></div>

### Exercise 6.10, Stochastic Wind

Just add a stochastic wind effect, by randomly adding 1 or 0 or -1 to the row number with equal probability in addition to the original wind effect, and keep the rest of the code the same. Since the wind is stochastic, the results are different for each simulation of trajectory. We give two of the trajectories below. Notice that if a later step overlaps with a previous step, the number on the grid will be overwritten, which causes the discontinuance in number of steps. \
<img src="/files/Chapter6/Windy_GridWorld/stoch1.png" alt="drawing" width="360"/>
<img src="/files/Chapter6/Windy_GridWorld/stoch2.png" alt="drawing" width="360"/>



## **Code Usage**

Download the code [Windy_Gridworld](https://github.com/MingruiSun2019/RLbook_reproducing/tree/master/Chapter6/Windy_Gridworld).

To generate the figures in Example 6.4:

{% highlight Bash %}
python3 SARSA_original.py
python3 plot_traj.py
python3 SARSA_original_history.py
python3 plot_history.py {% endhighlight %}

To generate the figures for Exercise 6.9, King's move

Kings's move:
{% highlight Bash %}
python3 SARSA_KingsMove.py
python3 plot_traj_KingsMove.py {% endhighlight %}

Kings's move plus "not move":
{% highlight Bash %}
python3 SARSA_NineMove.py
python3 plot_traj_NineMove.py {% endhighlight %}

To generate the figures for Exercise 6.10,  Stochastic Wind
{% highlight Bash %}
python3 SARSA_StochasticWind.py
python3 plot_traj_stoch.py {% endhighlight %}







**The End**
