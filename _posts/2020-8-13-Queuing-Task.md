---
title:  "Queuing Task (Differential semi-gredient Sarsa)"
date:   2020-08-13 15:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Queuing-Task/
---
This is to produce Figure 10.5 in RL book.

## **Problem description**

There are totally 10 servers for customers to use. The customers have different priority, namely, [1,2,4,8]. At a certain moment, all information available to us is only the number of free server and the priority of the current customer. Our task is to decide whether to reject or accept the current customer, in order to maximize the long-term reward without discounting.

**State**: [num_free_server, customer_priority] \
**Action**: 0 or 1: reject or accept \
**Reward**: 0 if reject. If accept, reward is identical to the customer's priority

In addition, if there is zero free server, the action can only be rejecting.


## **Implementation**

This example is straight forward, just follow the pseudo code of Differential semi-gredient Sarsa, which is given below.

<div style="text-align:center"><img src="/files/Chapter10/Queuing_Task/QT_p1.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

We get the two figures in Figure 10.5 in the book.

<div style="text-align:center"><img src="/files/Chapter10/Queuing_Task/policy.svg" alt="drawing" width="460"/></div>
<div style="text-align:center"><img src="/files/Chapter10/Queuing_Task/Figure10_5_lower_v2.svg" alt="drawing" width="560"/></div>

where in the "Policy" figure, red represents rejecting, and green represents accepting. The policy is a little different, since every time of training will give slight different results. In addition, the average reward is kind of forgetting, since it has a constant step size. Thus, it will not converge to some constant value, given the priority of customers are random, and where a busy server becomes a free one is also random. To our experience, the average reward oscillates between 1.9 and 3.1.


## **Code Usage**

Download the code [Queuing_Task](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter10/Queuing_Task).

To produce the two figures in Figure 10.5:
{% highlight Bash %}
python3 Train_queuing_task.py
python3 Figure_policy.py
python3 Figure_val_func.py {% endhighlight %}





**The End**
