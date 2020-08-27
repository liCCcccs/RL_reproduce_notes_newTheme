---
classes: wide
title:  "Short Corridor (Policy gradient methods)"
date:   2020-08-20 15:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Short-Corridor/
---
This is to produce Example 13.1, Figure 13.1 and Figure 13.2 in RL book.
<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p0.PNG" alt="drawing" width="300"/></div>

## **Problem description**

As shown in above figure, there are totally 3 states and a terminal at the right most. The actions are to go left or go right. For state 0 and state 2, actions will have normal results, but in state 1, actions will lead to reversed results. The problem setup is summarized below.

State: 0 or 1 or 2
Starting point: 0
Action: 0:go right, 1:go left
Reward: -1 each step until termination (the reward for the state-action pair leading to terminal is zero)

The special thing about this problem is we cannot distinguish the three states, that is, we assume that all three states have the same state value under function approximation. This problem is meaningful probably because we can see it as a small portion of an environment with huge number of states, where some states share the same feature vector and thus has the same value. In this case, methods rely on state value may behave badly.



## **Example 13.1**

The book does not tell how to produce the figure in Example 13.1. However, we can solve it with the knowledge learned from previous Chapters. Since this problem has only 3 states, we can directly solve the Bellman equation to get the exact solution. The Bellman equation is:

$$v_{\pi}(s) = \sum \limits_a \pi(a|s) \sum \limits_{s^{\prime}, r} p(s^{\prime}, r|s,a)[r + \gamma v_{\pi}(s^{\prime})] $$

For this problem we have the following set of equation:

$$v_0 = (1-p)(-1+v_0) + p(-1+v_1)$$\
$$v_1 = (1-p)(-1+v_2) + p(-1+v_0)$$\
$$v_2 = (1-p)(-1+v_1)$$

where $$v_0, v_1, v_2$$ represents the value of the three states from left to right respectively. $p$ is the probability of going right. Solve this equation we can get the relationshop between $$p$$ and $$v_0$$ which is exactly the curve in the figure.

$$v_0 = \frac{p^2 - 3p + 4}{p^2 - p}, \;\;\;\; 0 < p < 1$$



We can also solve it using some learning methods. As the book says, we define feature vector $$\mathbf{x}(S,right)=[1,0]^{\top}$$ and $$\mathbf{x}(S,left)=[0,1]^{\top}$$. Hence, we define weights with the same dimension $$\mathbf{w} = [w_0, w_1]$$. Then the state-action value can be written as:

$$Q_{\pi}(S, A_0) = \mathbf{x}(S,right)^{\top} \mathbf{w} = w_0, \;\;\;\; Q_{\pi}(S, A_1) = \mathbf{x}(S,left)^{\top} \mathbf{w} = w_1$$

where $$A_0, A_1$$ is the action going right and left respectively. Hence the state value is:

$$v_{\pi}(S) = \pi(A_0|S) * Q_{\pi}(S, A_0) + \pi(A_1|s) * Q_{\pi}(S, A_1), \;\;\;\; \text{for all } S$$

and the gradient of state value with respect to $$\mathbf{w}$$ is:

$$\nabla v_{\pi}(S) = [\pi(A_0|S), \pi(A_1|S)]^{\top}$$

Then it is straightforward to use the semi-gradient TD(0) algorithm to solve the problem, giving different policies, and then form the curve. For completeness, the pseudo code of semi-gradient on-policy TD(0) is given below.

<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p1.PNG" alt="drawing" width="700"/></div>

Another way is to use Semi-gradient DP method. However, here we have to use trajectory sampling as mentioned in Section 8.6 in the book, because all states share the same value. If a state is less visited, then it should contributed less to the state-value.
Sweeping with equal probability does not works in this problem. If we insist to sweep with equal probability, we have to multiply a factor indicating the state distribution. Thus, in either DP method we have to obtain the state distribution first. This can be easily done by simulating the task many times. The pseudo code for these two DP methods are shown below.

<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p2.PNG" alt="drawing" width="700"/></div>
<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p3.PNG" alt="drawing" width="700"/></div>

For the TD method, we looped for 10000 episodes; for two DP methods, we looped for 50000 samples or sweeps. and the step size are $$\alpha = 0.01$$. The results are shown below.

<img src="/files/Chapter13/Short_C/Example13_1.svg" alt="drawing" width="240"/>
<img src="/files/Chapter13/Short_C/Example13_1_DP.svg" alt="drawing" width="240"/>
<img src="/files/Chapter13/Short_C/Example13_1_DP_v2.svg" alt="drawing" width="240"/>


## **Figure 13.1**

The book does not tell much detail of the problem formulation. Here we list them that can be used to generate very similar plot as Figure 13.1.

The feature vectors are the same as before: $$\mathbf{x}(S,right)=[1,0]^{\top}$$ and $$\mathbf{x}(S,left)=[0,1]^{\top}$$. Parameter $$\mathbf{\theta} = [\theta_0, \theta_1]^{\top}$$. Then we formulate the parameterized numerical preferences:

$$h(s,a,\mathbf{\theta}) = \mathbf{\theta}^{\top} \mathbf{x}(s,a)$$

The choice of action is according to softmax distribution:

$$\pi(a|s,\mathbf{\theta}) = \frac{e^{h(s,a,\mathbf{\theta})}}{\sum_b e^{h(s,b,\mathbf{\theta})}}$$

In this problem above equation can be specified as:

$$\pi(A_0|s,\mathbf{\theta}) = \frac{e^{\theta_0}}{e^{\theta_0} + e^{\theta_1}}, \;\;\;\; \pi(A_1|s,\mathbf{\theta}) = \frac{e^{\theta_1}}{e^{\theta_0} + e^{\theta_1}}$$

Then, the gradient is:

$$\nabla \ln (A_0|S_t, \mathbf{\theta}) = [\frac{e^{\theta_0 + \theta_1}}{e^{\theta_0} + e^{\theta_1}}, -\frac{e^{\theta_0 + \theta_1}}{e^{\theta_0} + e^{\theta_1}}]^{\top}$$

$$\nabla \ln (A_1|S_t, \mathbf{\theta}) = [- \frac{e^{\theta_0 + \theta_1}}{e^{\theta_0} + e^{\theta_1}}, \frac{e^{\theta_0 + \theta_1}}{e^{\theta_0} + e^{\theta_1}}]^{\top}$$

Notice that when the step size $$\alpha$$ is too large, the convergence is not stable. Hence it may cause the probability of choose left or right action approaching 0 or 1, which will result in infinitely long episode. To prevent this from happening, we bound the probability of going right within $$[0.00001, 0.99999]$$, and set the maximum episode length to be 800. These parameters are carefully tuned to produce similar figure as Figure 13.1.

The pseudo code for REINFORCE algorithm is given as follows.

<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p4.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

We get something similar to Figure 13.1 as follows.

<div style="text-align:center"><img src="/files/Chapter13/Short_C/Figure13_1.svg" alt="drawing" width="600"/></div>


## **Figure 13.2**

For this figure, the problem formulation keeps the same as before, the only difference is we have a state-value weights $$\mathbf{w}$$ for estimating state value. To keep the problem consistent as in Example 13.1, all three states share one same weight. Thus,

$$\hat{v}(S_t, \mathbf{w}) = w \;\;\;\;  \text{for all } S_t $$

$$\nabla \hat{v}(S_t, \mathbf{w}) = 1  \;\;\;\;  \text{for all } S_t $$

Then it is straightforward to follow the pseudo code of REINFORCE with Baseline algorithm:

<div style="text-align:center"><img src="/files/Chapter13/Short_C/SC_p5.PNG" alt="drawing" width="700"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

We get something similar to Figure 13.2 as follows.

<div style="text-align:center"><img src="/files/Chapter13/Short_C/Figure13_2.svg" alt="drawing" width="600"/></div>



## **Code Usage**

Download the code [Short_Corridor](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter13/Short_Corridor).

To produce the figure in Example 13.1:
{% highlight Bash %}
python3 Example13_1.py    # Semi-gradient TD(0)
python3 plot_val.py -m 0  {% endhighlight %}

{% highlight Bash %}
python3 Example13_1_DP.py    # DP(trajectory sampling)
python3 plot_val.py -m 1  {% endhighlight %}

{% highlight Bash %}
python3 Example13_1_DP_v2.py    # DP(trajectory sampling)
python3 plot_val.py -m 2  {% endhighlight %}

To produce Figure 13.1:
{% highlight Bash %}
python3 Get_REINFORCE_data.py
python3 plot_REIN.py {% endhighlight %}

To produce Figure 13.2:
{% highlight Bash %}
python3 Get_REINFORCE_BL_data.py
python3 plot_REIN_BL.py {% endhighlight %}



**The End**
