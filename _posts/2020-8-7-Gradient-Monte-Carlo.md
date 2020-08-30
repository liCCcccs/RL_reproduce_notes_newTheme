---
title:  "Random Walk (Gradient Monte Carlo)"
date:   2020-08-5 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Gradient-MC/
---
This is to produce Figure 9.1 in RL book.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/RW.PNG" alt="drawing" width="600"/></div>

## **Problem description**

This random walk problem is a slightly more complex version as what is shown in the above figure. From the description in the book, we conclude the key points as follows:

Number of state: 1000, denoted as [1, 2, 3, ..., 1000] \
Starting state: 500 \
Actions: [-100, -99, ..., -2, -1, 1, 2, ..., 99, 100] In total 200 actions. Note that there is no "not move" action.
         If the resulting next state $$S_{t+1}<=0$$ or $$S_{t+1}>=1001$$, the episode terminates. \
Reward: -1 if terminates at left, 1 if terminates at right, 0 otherwise.



## **Algorithm**

The state value function of the model is approximated by state aggregation, which aggregates n states together and give them the same value. In this problem, the pattern of aggregation is like: [1:100],[101:200] ... [901:1000]. There are in total 10 bundles, so the state value function has only 10 entries. We denote these 10 values using a weight vector $$\mathbf{w} \in R^{10}$$. This is a kind of function approximation, maybe not so intuitive, but it indeed achieves representing those state values with less number of parameters (10 parameters represents 1000 state values).


The algorithm for this problem is the Gradient Monte Carlo method, whose pseudo code is given in the book. We copy it below.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/GMC_p2.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

It is kind of weird that it does not involve state distribution, although the it appears in Figure 9.1. The pseudo code neglects some details relative to Monte Carlo method itself (like how to calculate $$G_t$$), we can find them from the previous part of the book. It is also important to notice that what the pseudo code describes it the "Every-time" Monte Carlo method.


In terms of calculating the state distribution, the book gives a formula for episodic tasks. However, it involves solving a set of equations which is not easy to code this directly. Here, we present two method to solve this. The first method is simply doing Monte Carlo simulation and count the time of visit for each state. By running 100000 episodes, we get the result shown in the left figure below. The second method is calculating it iteratively, very similar to policy evaluation in dynamic programming. The only difference is to change the Bellman's equation to equation 9.2 in the book:

$$\eta (s) = h(s) + \sum_s \eta(\bar{s}) \sum_a \pi(a|\bar{s}) p(s|\bar{s}, a)$$

Set the threshold to 1e-5 and we get the result as shown in the right figure below. It is much smoother. What's more, the time cost of the second method is about half of the first method to get the figures below.

<img src="/files/Chapter9/Gradient_MC/distr.svg" alt="drawing" width="440"/>
<img src="/files/Chapter9/Gradient_MC/distr_iter.svg" alt="drawing" width="440"/>

The book does not tell how to find the true value neither. It turns out we can use policy evaluation (Dynamic programming) to solve this. Just following the pseudo code below, it is very straight forward.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/GMC_p1.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*


## **Implementation**

To produce Figure 9.1, the parameters are:

Number of episode: 100000

Step size: $$\alpha = 2e-5$$

Discount factor: $$\gamma = 1$$

We get Figure 9.1 as follows

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/compare.svg" alt="drawing" width="660"/></div>





## **Code Usage**

Download the code [Gradient_MC](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter9/Gradient_MC).

To produce Figure 9.1:
{% highlight Bash %}
python3 Get_distribution.py
python3 Random_walk.py
python3 plot_val.py {% endhighlight %}

To plot state distribution with both methods:
{% highlight Bash %}
python3 Get_distribution.py
python3 plot_distr.py {% endhighlight %}








**The End**
