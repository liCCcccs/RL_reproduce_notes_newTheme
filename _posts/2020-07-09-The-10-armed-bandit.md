---
layout: post
title:  "The 10-armed Testbed"
date:   2020-07-09 16:10:15 +1000
categories: jekyll update
---
This is to reproduce Figure 2.2, 2.3, 2.4, 2.5 in the RL book, and give the answer to Exercise 2.6 and 2.8.

### Problem setup

This a k-armed bandit problem with k = 10. The action value \\(q_{\star}(a)\\) is chosen from Normal distribution \\(\mathcal{N}(0,1)\\). The reward \\(R_t\\) is selected from Normal distribution \\(\mathcal{N}(q_{\star}(a), 1)\\).


## Algorithm and Implementation

### Sample average method (Figure 2.2)

Use *sample mean method* to estimate action value:

$$Q_t(a) = \frac{ \sum^{t-1}_{i=1} R_i \cdot \unicode{x1D7D9}_{A_i=a} }{ \sum^{t-1}_{i=1} \cdot \unicode{x1D7D9}_{A_i=a}} $$

or iteratively:

$$Q_{n+1} = Q_n + \frac{1}{n}[R_n - Q_n]$$

we get Figure 2.2 in the book as follows. Note that with different `reward_list` being initiated, the plots look different, however, the tendency remains the same.

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/Sample_ave_reward.png" alt="drawing" width="600"/></div>

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/Sample_ave_action.png" alt="drawing" width="600"/></div>

### Optimistic initial values (Figure 2.3)

To implement the optimistic initial values method, set the initial action values to 5 for all *k* actions, and set \\(\epsilon = 0\\). We get Figure 2.3 in the book as follows.

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/opt_init_action.png" alt="drawing" width="600"/></div>

**Answer for** *Exercise 2.6 Mysterious Spikes*:\
The mysterious spikes happens at step 11,21,31,... if we closely inspect the figure. The reason is simple. Since the reward per visit is highly likely less than 5, once an action is taken, the value of that action drops to less than 5, hence it will not be taken in the next few steps. Then, it is highly likely that all actions are taken once in the first 10 steps. So, before the agent take the 11th step, it has a reasonable estimation of all action values. Therefore, the 11th step has a relatively high probability to be optimal. The same reasoning also applies to the 21st, 31st,... step. However, due to the randomness of the reward, the estimation gets worse every round (10 steps) before enough exploration. Therefore, the peak at 21st step is lower than 11th, and that at 31st step is lower than 21st, etc.

### UCB Action Selection (Figure 2.4)

In UCB, the method of estimating action value is still *sample average method*, while the way of selecting actions, instead of \\(\epsilon\\)-greedy, relies on the criterion:

$$ A_t = \arg \max_{a} \left[ [Q_t(a) + c\sqrt{\frac{lnt}{N_t(a)}} \right] $$

The sentence in the book **"If \\(N_t(a)=0\\), then \\(a \\) is considered to be a maximizing action."** means if there are any actions that hasn't been chosen, (randomly) choose one of them. Also note that \\(t\\) starts from 1. We get Figure 2.4 in the book as follows.

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/UCB_reward_c2.png" alt="drawing" width="600"/></div>

If setting parameter \\(c = 1\\), the plot looks like this:

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/UCB_reward_c1.png" alt="drawing" width="600"/></div>

**Answer for** *Exercise 2.8 UCB Spikes*:\
The answer is similar to *Exercise 2.6 Mysterious Spikes*. When parameter \\(c\\) is large (\\(c = 2\\)), the selection of action depend heavily on the square root term at the beginning. So, similar things happens, spikes appears with decreasing amplitude at step 11, 21,... If parameter \\(c\\) is small (\\(c = 1\\)), the exploration-exploitation balance relatively well. So, after the first round (first 10 steps), the agent starts to make good decisions.


### Gradient Bandit Algorithms (Figure 2.5)

In Gradient Bandit algorithms, instead of \\(\epsilon\\)-greedy, the agent selects actions according to the probability distribution:

$$Pr\{A_t = a\} \dot{=} \frac{e^{H_t(a)}}{\sum^k_{b = 1} e^{H_t(b)}} \dot{=} \pi_t(a)$$

The numerical preference \\(H_t\\) is updated as:

$$H_{t+1}(A_t) \dot{=} H_t(A_t) + \alpha (R_t - \bar{R_t})(1 - \pi_t(A_t)) $$,  &nbsp;    and \
$$ H_{t+1}(a) \dot{=} H_t(a) - \alpha (R_t - \bar{R_t}) \pi_t(a)$$,    &nbsp;   &nbsp;   &nbsp;   for all \\(a \neq A_t\\)

We get Figure 2.5 in the book as follows:

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/Gradient_action.png" alt="drawing" width="600"/></div>

It is worthy to notice that, given different `reward_list`, the amplitude of these curve are different. If the optimal arm has a reward significantly larger than the second optimal one, the agent has higher probability to select the correct arm, and vice versa.

### Parameter study (Figure 2.6)

For this plot, we averaged over 400 runs for each method and each parameter. We get something similar to Figure 2.6 as follows.

<div style="text-align:center"><img src="/files/Chapter2/The_10armed_testbed/Figure2_6.png" alt="drawing" width="600"/></div>


## **Code Usage**

Download the code [The_10_armed_testbed](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter2/The_10_armed_testbed).

To produce Figure 2.2:
{% highlight Bash %}
python3 Get_part1_data.py -o 1
python3 Get_part1_data.py -o 2
python3 Get_part1_data.py -o 3
python3 plot_data_part1.py {% endhighlight %}

To produce Figure 2.3:
{% highlight Bash %}
python3 Get_part2_data.py -o 1
python3 Get_part2_data.py -o 2
python3 plot_data_part2.py {% endhighlight %}

To produce Figure 2.4:
{% highlight Bash %}
python3 Get_part3_data.py -o 1
python3 Get_part3_data.py -o 2
python3 plot_data_part3.py {% endhighlight %}

To produce Figure 2.5:
{% highlight Bash %}
python3 Get_part4_data.py -o 1
python3 Get_part4_data.py -o 2
python3 Get_part4_data.py -o 3
python3 Get_part4_data.py -o 4
python3 plot_data_part4.py {% endhighlight %}

To produce Figure 2.6:
{% highlight Bash %}
python3 Get_Figure2_6_data.py -m 1
python3 Get_Figure2_6_data.py -m 2
python3 Get_Figure2_6_data.py -m 3
python3 Get_Figure2_6_data.py -m 4
python3 plot_Figure2_6.py {% endhighlight %}










**The End**
