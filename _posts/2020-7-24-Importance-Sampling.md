---
layout: post
title:  "Off-policy evaluation by Importance Sampling"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
---

This is Example 5.4 and Example 5.5 in the RL book.

## **Off-policy Estimation of a Blackjack State value**

The state we are going to evaluate is [13, 2, 1], which means the player's sum is 13, the dealer's showing card is 2, the player has a usable ace. The behavior policy is choosing hit or stick with equal probability. Target policy is to hit whenever the player's sum is less or equal to 19, and to stick whenever player's sum is 20 or 21.

The formula for importance sampling ratio is:

$$\rho_{t:T-1} = \prod^{T-1}_{k=t} \frac{\pi(A_k|S_k)}{b(A_k|S_k)}$$

Formula for ordinary importance-sampling:

$$V(s) = \frac{\sum_{t\in \tau(s)} \rho_{t:T(t)-1} G_t}{|\tau(s)|}$$

Formula for weighted importance-sampling:

$$V(s) = \frac{\sum_{t\in \tau(s)} \rho_{t:T(t)-1} G_t}{\sum_{t\in \tau(s)} \rho_{t:T(t)-1}}$$

Please refer to the book to find out what do these symbols mean.

For this specific example, above formula can be simplified. The importance-sampling ratio is 1 when the target and behavior is the same at time \\(t\\), it is 0 otherwise. The return G is simply equal to the reward at the last time step of an episode. \\(\tau(s)\\) increase by 1 every episode, because state [13, 2, 1] will appear only once in each episode.

We get Figure 5.3 in the book as follows.

<div style="text-align:center"><img src="/files/Chapter5/Importance_sampling/result.png" alt="drawing" width="500"/></div>

## **Infinite Variance**

<div style="text-align:center"><img src="/files/Chapter5/Importance_sampling/Invar.JPG" alt="drawing" width="360"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

Referring to above figure, behavior policy is to choose to go left or right with equal probability. Target policy is always go left.

Since we need to generate 100 million episodes, the efficiency of algorithm is of critical importance. Firstly, \\(V(s)\\) should be calculated iteratively:

For each new episode:\
$$Numerator = Numerator + \rho_{t:T(t)-1} G_t$$\
$$N = N + 1$$\
$$V(s) = \frac{Numerator}{N}$$

Secondly, since the the figure uses log scale for episodes, the data points will be very dense as episodes increases. Thus, in our code, after 100000 episodes, we will only save one data point per 10000 episodes.

Simulating for ten times and we can get Figure 5.4 in the book:
<div style="text-align:center"><img src="/files/Chapter5/Importance_sampling/OIS_plot.png" alt="drawing" width="700"/></div>






## **Code Usage**

### Off-policy Estimation of a Blackjack State value:

Download the code [Blackjack_off_policy_prediction](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter5/Blackjack_off_policy_prediction).

Run the following in the terminal:
{% highlight Bash %}
python3 Off_policy_prediction.py -o 0
python3 Off_policy_prediction.py -o 1
python3 plot_val_func.py {% endhighlight %}


###  Off-policy MC control:

Download the code [Infinite_var](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter5/Infinite_var).

Run the following in the terminal:
{% highlight Bash %}
python3 simulation.py -n 1
python3 simulation.py -n 2
python3 simulation.py -n 3
python3 simulation.py -n 4
python3 simulation.py -n 5
python3 simulation.py -n 6
python3 simulation.py -n 7
python3 simulation.py -n 8
python3 simulation.py -n 9
python3 simulation.py -n 10
python3 plot_simulation.py -o [1: interactive image, 0: save image] {% endhighlight %}

Every simulation takes about 2 minutes. I separate them in order to let the computer take a breath between simulations.



**The End**
