---
title:  "Mountain Car (Semi-gradient (n-step) SARSA)"
date:   2020-08-13 15:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Mountain-Car/
---
This is to produce Figure 10.1, 10.2, 10.3, 10.4 in RL book.
<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/MCar_p1.PNG" alt="drawing" width="300"/></div>

## **Problem description**

As shown in above figure, there is a car who wants to reach the goal point. However, its throttle is not enough to fight against gravity. So it needs to exploit the gravity and its momentum to reach the goal point.

**State**: [x, xdot], $$x\in R$$ within range $$-1.2 \leq x \leq 0.5$$; $$xdot\in R$$ within range $$-0.07 \leq xdot \leq 0.07$$ \
**Starting point**: A random number $$x\sin Uniform(-0.6,0.6)$$ \
**Goal point**: $$x = 0.5$$ \
**Action**: -1 or 0 or 1 \
**Reward**: -1 each step until termination

Rules:

$$x_{t+1} = bound[x_t + \dot{x}_t+1]$$

$$\dot{x}_{t+1} = bound[\dot{x}_t + 0.001A_t - 0.0025cos(3x_t)]$$

If the car hit the left bound, i.e. $$x=-1.2$$, reset velocity to zero.

The state-action pair is a three dimensional space, position and velocity are two continuous dimension, and action is a discrete dimension (we can also treat it as continuous dimension). As the books says, each tile covering 1/8th of the bounded distance in each dimension, these are 8 * 8 * 8 = 512 tiles in one tiling. Since we use 8 tilings, there should be 512*8 = 4096 tiles, corresponding to 4096 components in the weight vector.

Actually, since there are only 3 action values, the total number of tiles can be visited is only 8*8*3*8 = 1536, which suggests that there will be 4096-1536 = 2560 weight components that have never been used, hence their value remains zeros no matter how episodes time we train it. Experiments confirm this idea, during the training, the number of zero weight components converge to 2560.


## **Algorithm**

The algorithm relating to Asymmetric Tile Coding is not simple. As the book suggests, we use the tile-coding software available at [here](http://incompleteideas.net/tiles/tiles3.html). It involves some data structure called Hash Table. Two main usage is:

{% highlight Bash %}
iht = IHT(4096)
tiles(iht, 8, [8*x/(0.5+1.2), 8*xdot/(0.07+0.07)], [A]).py {% endhighlight %}

where 4096 is the number of tiles, or weight components. The 2nd arg '8' in `tiles()` means we have 8 tilings. The '8' in the third arg means divide each dimension into 8 divisions. (0.5+1.2) and (0.07+0.07) are the range of $$x$$ and $$xdot$$ respectively. For the current version of code, the 4th arg should be iterable, we make it a list [A]. This is different from what has been given in the book, where it only pass a number A to `tiles()`.

The main algorithm for Example 10.1 is Episodic Semi-gradient Sarsa, whose pseudo code is given below.

<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/MCar_p2.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

It is easy to generalize above algorithm to Episodic Semi-gradient n-step Sarsa, whose pseudo code is given below. This wil be used to produce Figure 10.3 and 10.4.

<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/MCar_p3.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*




## **Implementation**

The book did not clarify where it use $$\epsilon$$-greedy or greedy policy. Here we use greedy policy, since we already have exploring start.

We get something similar to Figure 10.1 as follows.

<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/ctg_func.png" alt="drawing" width="800"/></div>

The book has already explained that Figure 10.1 is not from Episodic Semi-gradient Sarsa algorithm. Thus, there will be some difference between above figure and what is presented in the book. Especially look at the region where the position is near 0.5 and velocity is near -0.07. In our figure, these values are almost zero, but in the book it has value around 50. It is because these states are impossible to happen in reality (very close to the goal point but have very large negative velocity), hence the value at these states are never updated.

For Figure 10.2, we also use greedy policy, and averaged over 50 runs. Notice that y axis is log scale. We get the following figure, which is very similar , even in some details.

<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/Train_history.svg" alt="drawing" width="500"/></div>

Using Episodic Semi-gradient n-step Sarsa algorithm, we can produce Figure 10.3 and 10.4 as follows. The procedure is similar to previous examples. Since they are computational intensive, we averaged only 50 and 20 runs for Figure 10.3 and 10.4 respectively.

<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/Train_history_TDn.svg" alt="drawing" width="550"/></div>
<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/Figure10_4.svg" alt="drawing" width="550"/></div>





## **Code Usage**

Download the code [Mountain_Car](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter10/Mountain_Car).

To produce Figure 10.1:
{% highlight Bash %}
python3 One_Run_TD0.py
python3 plot_val.py {% endhighlight %}

To produce Figure 10.2:
{% highlight Bash %}
python3 Multi_Run_TD0.py
python3 plot_history.py -o 0 {% endhighlight %}

To produce Figure 10.3:
{% highlight Bash %}
python3 Multi_Run_TDn.py
python3 plot_history.py -o 1 {% endhighlight %}

To produce Figure 10.4:
{% highlight Bash %}
python3 Get_Figure10_4_data.py -n 1
python3 Get_Figure10_4_data.py -n 2
python3 Get_Figure10_4_data.py -n 4
python3 Get_Figure10_4_data.py -n 8
python3 Get_Figure10_4_data.py -n 16
python3 Figure10_4.py {% endhighlight %}




**The End**
