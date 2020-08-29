---
title:  "Trajectory sampling"
date:   2020-08-5 16:10:15 +1000
categories: jekyll update
pernmalink: /RL_notes/Traj-Sample/
---
This is to produce Figure 8.8 in RL book (including Exercise 8.8).

## **Thoughts about the problem and trajectory sampling**

This is the most confusing problem up to now. The description is extremely ambiguous. I fail to produce the same figure as in the book. However, it is worth to document my experience and effort, which may be helpful for readers.

To my understanding, trajectory sampling is used in the process of planning, instead of learning. It requires a model of the environment. The model is either learned from experience or known as prior knowledge. So it can be applied to either model-based or model-free reinforcement learning problem.

In the example of Figure 8.8, the book assumes the model of the environment is completely known (model-based). The agent uses q-value iteration to estimate the state-action value function, and accordingly, find a greedy policy whose goal is to maximize the return when starting from the "start point".

Along the way of updates, the agent can stop, and evaluate the obtained greedy policy (how good it is) by evaluate the state value of the "start point". The higher the state value is, the better the policy is. However, the book does not tell how to do the value estimation. It just says "exhaustively compute". In my implementation, I used either Monte Carlo prediction (from Chapter 5) or policy evaluation (from Chapter 4). I will explain below how did I chose them.

## **Try to clarify the problem setup**

I will try to explain the problem using "1000 states" as an example.

First, let's be confident and claim that there is literally 1000 states, denoted as [0, 1, 2, ..., 999].

It is also clear that from each of the 1000 states, there are 2 actions.

Next, let's grab a state, say state [369]. From state [369], there are 2 actions: [0] and [1]. This cannot be wrong.

Next, if we take different actions from state [369], the lists of [b] possible next states has to be different, otherwise the two actions will be equivalent. One transition is shown as follows.

<div style="text-align:center"><img src="/files/Chapter8/Traj_sampling/TS_p1.png" alt="drawing" width="400"/></div>


Also note that the assignment of these lists of next state to all state-action pair is pre-defined. Although they are randomly generated, they are pre-defined and fixed within one run.

The lists of rewards corresponding to each transition is also pre-defined. Although they are selected randomly from Gaussian distribution N(0,1), they fixed within one run.

I'm not sure if the rewards of termination of an episode is selected from which type of distribution. Here I take it as also from Gaussian distribution N(0,1).

Finally, the transition probability. With probability 0.1 the episode terminates, and with probability 0.9*(1/b) it goes to a next state which is selected from the pre-assigned lists of [b] possible next states.

=====================================

Note that the evaluation of the policy is not a part of trajectory sampling. It just helps us to explicitly show the difference between the "on-policy" and "uniform" method.

Every 400 updates, we will check the how good the current greedy policy is. One way is to use Monte Carlo (MC) prediction method, since we are only estimating one state, which is the start point. The other way is using the policy evaluation, which estimates the value for all states. It turns out that the MC method is fast, but the resulting curve is not smooth. On the other hand, the policy evaluation method generates smooth curve, however, it is slow, and it takes forever to estimate when there are 10000 states.

Therefore, we use MC method for the situation of 10000 states and policy evaluation method for that of 1000 states.


## **Implementation**

For the 1000 states plot, the parameters are:

Average over 10 runs.

Evaluation interval: every 400 updates.

Stop criterion for policy evaluation: $$\delta = 0.1$$.

The figure I generated is as follows:

<div style="text-align:center"><img src="/files/Chapter8/Traj_sampling/compare_1000.svg" alt="drawing" width="460"/></div>



For the 10000 states plot, the parameters are:

Average over 30 runs.

Evaluation interval: every 400 updates.

Average over 500 episodes for MC prediction.

The figure I generated is as follows (this also includes Exercise 8.8):

<div style="text-align:center"><img src="/files/Chapter8/Traj_sampling/compare_10000.svg" alt="drawing" width="460"/></div>




## **Code Usage**

Download the code [Blocking_Maze](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter8/Trajectory_Sampling).

To produce Figure 8.8 with 1000 states:
{% highlight Bash %}
python3 onPolicy.py -b 1 -n 1000
python3 onPolicy.py -b 3 -n 1000
python3 onPolicy.py -b 10 -n 1000
python3 uniform.py -b 1 -n 1000
python3 uniform.py -b 3 -n 1000
python3 uniform.py -b 10 -n 1000
python3 plot_compare.py {% endhighlight %}

To produce Figure 8.8 with 10000 states:
{% highlight Bash %}
python3 onPolicy.py -b 1 -n 10000
python3 onPolicy.py -b 3 -n 10000
python3 onPolicy.py -b 10 -n 10000
python3 uniform.py -b 1 -n 10000
python3 uniform.py -b 3 -n 10000
python3 uniform.py -b 10 -n 10000
python3 plot_compare_10k.py {% endhighlight %}








**The End**
