---
title:  "Random Walk (Semi-gradient TD)"
date:   2020-08-5 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Semi-Gradient-TD/
---
This is to produce Figure 9.2 in RL book.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/RW.PNG" alt="drawing" width="600"/></div>

## **Problem description**

The problem setup is exactly the same as in Example 9.1. For completeness, we duplicate the problem summary as follows.

This random walk problem is a slightly more complex version as what is shown in the above figure. From the description in the book, we conclude the key points as follows:

Number of state: 1000, denoted as [1, 2, 3, ..., 1000] \
Starting state: 500 \
Actions: [-100, -99, ..., -2, -1, 1, 2, ..., 99, 100] In total 200 actions. Note that there is no "not move" action.
         If the resulting next state $$S_{t+1}<=0$$ or $$S_{t+1}>=1001$$, the episode terminates. \
Reward: -1 if terminates at left, 1 if terminates at right, 0 otherwise.



## **Algorithm**

To produce the left figure in Figure 9.2, simply follow the pseudo code of Semi-gradien TD(0), which is given below. Again, state distribution is not involved here.

<div style="text-align:center"><img src="/files/Chapter9/Semi_gradient_TD/SMT_p1.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

The right figure in Figure 9.2 is rendered using Semi-gradient TD(n) method, whose pseudo code is given below. The scheme is very similar to tabular TD(n) mehod, The only difference is updating the weight instead of value function, which is highlighted with yellow block.

<div style="text-align:center"><img src="/files/Chapter9/Semi_gradient_TD/SMT_p2.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*


## **Implementation**

The book does not give the exact parameter to producing the left figure in Figure 9.2, the parameters we used that gives pretty similar result is:

Number of episode: 400000

Step size: $$\alpha = 2^{-5}$$

Discount factor: $$\gamma = 1$$

We get the left figure as follows:

<div style="text-align:center"><img src="/files/Chapter9/Semi_gradient_TD/compare_400000.png" alt="drawing" width="600"/></div>

The procedure of producing the right figure is the same as that of Figure 7.2. The details can be found in the previous post [Random Walk (n-step TD)](/RL_notes/n-step-TD/).

The description of the parameters is ambiguous in the book. We use the following parameters to produce similar plot:

Number of state: 1000 \
State aggregation: 20 groups, 50 states each group. \
Action: [-100, -99, ..., -2, -1, 1, 2, ..., 99, 100] \
Average over 200 runs.

It is weird that the book used state aggregation with 10 groups to form the approximation function, but the actions and true state value are from that with 10 groups, which is really confusing, although one can define the approximation function to be whatever he likes regardless of what the model looks like.

We get the right figure in Figure 9.2 as follows.

<div style="text-align:center"><img src="/files/Chapter9/Semi_gradient_TD/RMS_TDn.png" alt="drawing" width="600"/></div>


## **Code Usage**

Download the code [Semi_Gradient_TD](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter9/Semi_Gradient_TD).

To produce the left figure in Figure 9.2:
{% highlight Bash %}
python3 Get_true_val.py
python3 Random_walk_TD0.py -o 0 -n 400000
python3 plot_val_TD0.py {% endhighlight %}

You can also run them incrementally to see the value change like:

{% highlight Bash %}
python3 Get_true_val.py
python3 Random_walk_TD0.py -o 0 -n 100000
python3 plot_val_TD0.py
python3 Random_walk_TD0.py -o 1 -n 100000
python3 plot_val_TD0.py
python3 Random_walk_TD0.py -o 1 -n 100000
python3 plot_val_TD0.py
python3 Random_walk_TD0.py -o 1 -n 100000
python3 plot_val_TD0.py {% endhighlight %}

To plot the right figure in Figure 9.2:
{% highlight Bash %}
python3 Get_true_val.py
python3 Get_RMS_error.py -n 1
python3 Get_RMS_error.py -n 2
python3 Get_RMS_error.py -n 4
python3 Get_RMS_error.py -n 8
python3 Get_RMS_error.py -n 16
python3 Get_RMS_error.py -n 32
python3 Get_RMS_error.py -n 64
python3 Get_RMS_error.py -n 128
python3 Get_RMS_error.py -n 256
python3 Get_RMS_error.py -n 512
python3 plot_RMS_TDn.py {% endhighlight %}








**The End**
