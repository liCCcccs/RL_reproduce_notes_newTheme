---
layout: post
title:  "Random Walk"
date:   2020-08-17 16:10:15 +1000
categories: jekyll update
---
<div style="text-align:center"><img src="/files/Chapter7/TDn/RW.PNG" alt="drawing" width="500"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This is to produce Figure 12.3, 12.6, 12.8 in the RL book.

## **Random Walk**

This problem is the same as that given in Example 7.1 in the book.

There are in total 19 states, excluding two terminal states on the left and right. The reward is -1 if terminates on the left, 1 if terminates on the right, and 0 on other transitions. The policy is to go left and right with equal probability. The true values for the 19 states are $$-0.9, -0.8, -0.7, ......, 0.7,0.8,0.9$$ from left to right respectively.

### **Algorithm**

**Offline $$\lambda$$-return algorithm (for Figure 12.3)**

The updating rule is:

$$\mathbf{w}_{t+1} = \mathbf{w}_t + \alpha [G^{\lambda}_t - \hat{v}(S_t, \mathbf{w}_t)] \nabla \hat{v}(S_t, \mathbf{w}_t), \;\;\; t = 0, ..., T-1 $$

where the $$\lambda$$-return is:

$$G^{\lambda}_t = (1 - \lambda) \sum \limits_{n=1}^{T-t-1} \lambda^{n-1} G_{t:t+n} + \lambda^{T-t-1} G_t$$

where $$G_t$$ is the return at time step $$t$$, and the n-step return $$G_{t:t+n}$$ is:

$$G_{t:t+n} = R_{t+1} + \gamma R_{t+2} + ... + \gamma^{n-1} R_{t+n} + \gamma^n \hat{v}(S_{t+n}, \mathbf{w}_{t+n-1})$$

Note that if we just follow these formula, the training process will be extremely slow. Therefore, we will need to investigate in how to do these updates iteratively or something else to increase the computational efficiency.

We first notice that $$G_{t:t+n}$$ can be calculated iteratively, observe:

$$G_{t:t+n+1} = R_{t+1} + \gamma R_{t+2} + ... + \gamma^{n-1} R_{t+n} + \gamma^{n} R_{t+n+1} + \gamma^{n+1} \hat{v}(S_{t+n+1}, \mathbf{w}_{t+n})$$
$$ \;\;\;\;\;\;\;\;\;\;\;\;\;\; = G_{t:t+n} - \gamma^n \hat{v}(S_{t+n}, \mathbf{w}_{t+n-1}) + \gamma^{n} R_{t+n+1} + \gamma^{n+1} \hat{v}(S_{t+n+1}, \mathbf{w}_{t+n})$$

In addition, all the exponentiation $$\gamma^n, \lambda^n$$ can be done beforehand. With these two simple improvement, the computation is about 50 times faster than the original one.

There is another possible way of improvement on a higher level, by iteratively calculating $$G^{\lambda}_t$$. Let's look at a example where we set $$T = 4$$:

$$G_0^{\lambda} = (1 - \lambda)(G_{0:1} + \lambda G_{0:2} + \lambda^2 G_{0:3}) + \lambda^3 G_0 $$\
$$ = (1 - \lambda)(R_1 + \gamma \hat{v}_{1,0} + \lambda(R_1 + \gamma R_2 + \gamma^2 \hat{v}_{2,1}) + \lambda^2(R_1 + \gamma R_2 + \gamma^2 R_3 + \gamma^3\hat{v}_{3,2})  ) + \lambda^3 G_0 $$\
$$ = (1 - \lambda)(G_{0:1} + \lambda(R_1 + \gamma R_2 + \gamma^2 \hat{v}_{2,1}) + \lambda^2(R_1 + \gamma R_2 + \gamma^2 R_3 + \gamma^3\hat{v}_{3,2})  ) + \lambda^3 G_0 $$

$$G_1^{\lambda} = (1 - \lambda)(G_{1:2} + \lambda G_{1:3}) + \lambda^2 G_1 $$ \
$$ = (1 - \lambda)( R_2 + \gamma \hat{v}_{2,1} + \lambda(R_2 + \gamma R_3 + \gamma^2\hat{v}_{3,2}) ) + \lambda^2 G_1   $$

$$G_2^{\lambda} = (1 - \lambda)(G_{2:3}) + \lambda G_2 $$ \
$$ = (1 - \lambda)( R_3 + \gamma \hat{v}_{3,2} ) + \lambda G_2   $$

One straightforward way to write the update rule is:

$$G^{\lambda}_{t+1} = \frac{\left(G^{\lambda}_t - \lambda^{T-t-1}G_{t} - (1 - \lambda)(G_{t:t+1} + \sum \limits_{i=1}^{T-t-2}\lambda^{i} R_{t+1}) \right)}{\lambda \gamma} + \lambda^{T-t-1} G_t$$

However, this updating rule causes rounding error (addition betweem large number and very small number). Thus it is not practically useful. Readers can find other way to formulate the updating rule. For generating Figure 12.3, we uses the first iterative method.



**TD($$\lambda$$) algorithm (for Figure 12.6)**

TD($$\lambda$$) is naturally much faster than offline $$\lambda$$-return algorithm, its formulation is much more computation-friendly. The pseudo code is given below.

<div style="text-align:center"><img src="/files/Chapter12/Random_Walk/RRW_p1.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

**True online TD($$\lambda$$) algorithm (for Figure 12.8)**

True online TD($$\lambda$$) approximates Online $$\lambda$$-return algorithm, and gives exactly the same result in a more computation-friendly way. The pseudo code is given below.

<div style="text-align:center"><img src="/files/Chapter12/Random_Walk/RRW_p2.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*




## **Implementation**

**Offline $$\lambda$$-return algorithm (for Figure 12.3)**

The left figure averages over at most 200 runs. The right figure is what we generated in Chapter 7.

<img src="/files/Chapter12/Random_Walk/lam_return.svg" alt="drawing" width="360"/>
<img src="/files/Chapter12/Random_Walk/TDn_title.svg" alt="drawing" width="360"/>


**TD($$\lambda$$) algorithm (for Figure 12.6)**

The left figure averages over 500 runs.

<img src="/files/Chapter12/Random_Walk/TD_lam.svg" alt="drawing" width="360"/>
<img src="/files/Chapter12/Random_Walk/lam_return.svg" alt="drawing" width="360"/>


**True online TD($$\lambda$$) algorithm (for Figure 12.8)**

The left figure averages over 500 runs.

<img src="/files/Chapter12/Random_Walk/TOTD_lam.svg" alt="drawing" width="360"/>
<img src="/files/Chapter12/Random_Walk/lam_return.svg" alt="drawing" width="360"/>






## **Code Usage**

Download the code [Random_Walk_ET](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter12/Random_Walk_ET).

To generate the left figure in Figure 12.3:\
Run the following in the terminal:
{% highlight Bash %}
python3 Get_lam_return_data.py -l 0
python3 Get_lam_return_data.py -l 1
python3 Get_lam_return_data.py -l 2
python3 Get_lam_return_data.py -l 3
python3 Get_lam_return_data.py -l 4
python3 Get_lam_return_data.py -l 5
python3 Get_lam_return_data.py -l 6
python3 Get_lam_return_data.py -l 7
python3 plot_lam_return.py {% endhighlight %}
We generate the eight curve for eight different $$\lambda$$ separately.


To generate the left figure in Figure 12.6:\
Run the following in the terminal:
{% highlight Bash %}
python3 Get_TD_lam_data.py -l 0
python3 Get_TD_lam_data.py -l 1
python3 Get_TD_lam_data.py -l 2
python3 Get_TD_lam_data.py -l 3
python3 Get_TD_lam_data.py -l 4
python3 Get_TD_lam_data.py -l 5
python3 Get_TD_lam_data.py -l 6
python3 Get_TD_lam_data.py -l 7
python3 plot_TD_lam.py {% endhighlight %}
We generate the eight curve for eight different $$\lambda$$ separately.

To generate the left figure in Figure 12.8:\
Run the following in the terminal:
{% highlight Bash %}
python3 Get_TOTD_lam_data.py -l 0
python3 Get_TOTD_lam_data.py -l 1
python3 Get_TOTD_lam_data.py -l 2
python3 Get_TOTD_lam_data.py -l 3
python3 Get_TOTD_lam_data.py -l 4
python3 Get_TOTD_lam_data.py -l 5
python3 Get_TOTD_lam_data.py -l 6
python3 Get_TOTD_lam_data.py -l 7
python3 plot_TOTD_lam.py {% endhighlight %}
We generate the eight curve for eight different $$\lambda$$ separately.




**The End**
