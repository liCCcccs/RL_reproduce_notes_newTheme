---
layout: post
title:  "Mountain Car (Sarsa(λ))"
date:   2020-08-19 15:10:15 +1000
categories: jekyll update
---
This is to produce Figure 12.10, 12.11 in RL book.
<div style="text-align:center"><img src="/files/Chapter10/Mountain_Car/MCar_p1.PNG" alt="drawing" width="300"/></div>

## **Problem description**

This example is exactly the same as the Mountain Car task in Chapter 10. As shown in above figure, there is a car who wants to reach the goal point. However, its throttle is not enough to fight against gravity. So it needs to exploit the gravity and its momentum to reach the goal point.

State: $$x\in R$$ within range $$-1.2 \leq x \leq 0.5$$; $$xdot\in R$$ within range $$-0.07 \leq xdot \leq 0.07$$
Starting point: A random number $$x\sin Uniform(-0.6,0.6)$$
Goal point: $$x = 0.5$$
Action: -1 or 0 or 1
Reward: -1 each step until termination

Rules:

$$x_{t+1} = bound[x_t + \dot{x}_t+1]$$

$$\dot{x}_{t+1} = bound[\dot{x}_t + 0.001A_t - 0.0025cos(3x_t)]$$

If the car hit the left bound, i.e. $$x=-1.2$$, reset velocity to zero.




## **Algorithm**

The algorithm relating to Asymmetric Tile Coding is not simple. As the book suggests, we use the tile-coding software available at [here](http://incompleteideas.net/tiles/tiles3.html). It involves some data structure called Hash Table. Two main usage is:

{% highlight Bash %}
iht = IHT(4096)
tiles(iht, 8, [8*x/(0.5+1.2), 8*xdot/(0.07+0.07)], [A]).py {% endhighlight %}

where 4096 is the number of tiles, or weight components. The 2nd arg '8' in `tiles()` means we have 8 tilings. The '8' in the third arg means divide each dimension into 8 divisions. (0.5+1.2) and (0.07+0.07) are the range of $$x$$ and $$xdot$$ respectively. For the current version of code, the 4th arg should be iterable, we make it a list [A]. This is different from what has been given in the book, where it only pass a number A to `tiles()`.

The main algorithm for Figure 12.10 is Sarsa($$\lambda$$), whose pseudo code is given below. The three different versions of it are indicated in the pseudo code. In Figure 12.10 we use Sarsa($$\lambda$$) with replacing traces.

<div style="text-align:center"><img src="/files/Chapter12/Mountain_Car/MMC_p2.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

The main algorithm for Figure 12.11 is True online Sarsa($$\lambda$$), whose pseudo code is given below.

<div style="text-align:center"><img src="/files/Chapter12/Mountain_Car/MMC_p1.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*




## **Implementation**

The book did not clarify where it use $$\epsilon$$-greedy or greedy policy. Here we use greedy policy, since we already have exploring start.

We get something similar to Figure 12.10 as follows, the right figure was generated in Chapter 10.

<img src="/files/Chapter12/Mountain_Car/TOSarsa_lam.svg" alt="drawing" width="360"/>
<img src="/files/Chapter12/Mountain_Car/Figure10_4_for_C12.svg" alt="drawing" width="360"/>


For Figure 12.11, we again don't know whether it use $$\epsilon$$-greedy or greedy policy. We also don't know the value of $$\lambda$$ or even whether the four methods being evaluated use the same $$\lambda$$ or not. We use $$\lambda = 0.85$$ for all methods, and the following figure is the best we can do.

<div style="text-align:center"><img src="/files/Chapter12/Mountain_Car/Figure_12_11.svg" alt="drawing" width="500"/></div>



## **Code Usage**

Download the code [Mountain_Car](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter12/Mountain_Car).

To produce Figure 12.10:
{% highlight Bash %}
python3 Get_Figure_12_10_data.py -l 0
python3 Get_Figure_12_10_data.py -l 1
python3 Get_Figure_12_10_data.py -l 2
python3 Get_Figure_12_10_data.py -l 3
python3 Get_Figure_12_10_data.py -l 4
python3 Get_Figure_12_10_data.py -l 5
python3 Get_Figure_12_10_data.py -l 6
python3 Figure_12_10.py {% endhighlight %}
The data for seven curves are generated separately.

To produce Figure 12.11:
{% highlight Bash %}
python3 Get_Figure_12_11_data.py -l 1
python3 Get_Figure_12_11_data.py -l 2
python3 Get_Figure_12_11_data.py -l 3
python3 Get_Figure_12_11_data.py -l 4
python3 Figure_12_11.py {% endhighlight %}
The data for four methods are generated separately.





**The End**
