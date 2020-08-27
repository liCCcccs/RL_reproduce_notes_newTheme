---
layout: post
title:  "Cliff Walk (Compare Q-learning, SARSA, Expected SARSA)"
date:   2020-07-27 16:10:15 +1000
categories: jekyll update
---
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/CW_p1.PNG" alt="drawing" width="400"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This is Example 6.6 and Figure 6.3 in the RL book.

### **Problem discription**

A man is born at the start point 'S' in above figure, and want to go to the goal point 'G'. The actions he can take is "up" or "down" or "right" or "left". He should also avoid stepping off the cliff along his way.

**State:** [row, column] \
**Action:** 0 or 1 or 2 or 3 , respectively represents "up", "down", "right", "left" \
**Reward:** -1 every step, expect that if going into "The cliff" the reward is -100

### **Algorihtm and Implementation**

#### Example 6.4

The algorithm (SARSA) and (Q-learning) for this problem is given in the book. The book only gives a hint on for Expected SARSA algorithm, here we also present the pseudo code for Expected SARSA (modified base on Q-learning).
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/WGW_p2.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/CW_p2.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/CW_p3.png" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

Log the training data and we can produce the 'training history'. When plotting the trajectory, we take greedy actions, instead of following the $$\epsilon$$-greedy policy. We get the figure in Example 6.6 in the RL book:
<img src="/files/Chapter6/Cliff_Walk/SARSA_traj.png" alt="drawing" width="360"/>
<img src="/files/Chapter6/Cliff_Walk/Q_traj.png" alt="drawing" width="360"/>\
*Left figure: SARSA, right figure: Q-learning*
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/history.png" alt="drawing" width="600"/></div>

Look at the "Training history" figure, it is not the same as that in the book in detail. The figure shown here is averaged over 1000 independent runs. It is still not as smooth as that in the book. However, averaging over more runs can only reduce the oscillation amplitude, it does not smooth out the curve at all. It is very weird how the figure looks like in the book, I have no idea how the authors produced it. E-mail me if you have an idea :)

The book did not plot the trajectory resulted from Expected SARSA, we present it below. It just looks perfect as it reflects that Expected SARSA is a "combination" of advantages of SARSA and Q-learning.
<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/ESARSA_traj.png" alt="drawing" width="400"/></div>


### Figure 6.3

For this figure, the main algorithm are exactly the same as in Example 6.4. However, it requires huge computational power. As a result, we present a simplified version of this figure. For Interim performance, we reduce the number of runs to 50, instead of 50000. For Asymptotic performance, we reduce the number pf episodes to 2000, instead of 100000. It can be predicted that the asymptotic curve will be shifted a bit lower, but insignificantly. In addition, the interval of $$\alpha$$ is set to 0.1, instead of 0.05. Fortunately, we produce very similar plot as Figure 6.3 as follows.

<div style="text-align:center"><img src="/files/Chapter6/Cliff_Walk/performance.png" alt="drawing" width="600"/></div>



## **Code Usage**

Download the code [Cliff_Walk](https://github.com/MingruiSun2019/RLbook_reproducing/tree/master/Chapter6/Cliff_Walk).

To generate the "trajectory" figure in Example 6.4 (in addition, Expected SARSA):

{% highlight Bash %}
python3 Q_cliff.py
python3 plot_traj_Q.py

python3 SARSA_cliff.py
python3 plot_traj_SARSA.py

python3 ESARSA_cliff.py
python3 plot_traj_ESARSA.py {% endhighlight %}

To generate the "training history" figure in Example 6.4:

{% highlight Bash %}
python3 Q_cliff_getHistory.py -o [1:start new training, 0:continue training] -n [number of run]
python3 SARSA_cliff_getHistory.py -o [1:start new training, 0:continue training] -n [number of run]
python3 plot_history.py {% endhighlight %}


To generate Figure 6.3 in the book:

{% highlight Bash %}
python3 Performance.py
python3 plot_performance.py {% endhighlight %}

It took about 5 minutes on my laptop, you can change the variables to customize your plot.






**The End**
