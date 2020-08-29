---
title:  "Racetrack Monte Carlo control"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Racetrack/
---
<div style="text-align:center"><img src="/files/Chapter5/Racetrack/Racetrack.JPG" alt="drawing" width="600"/></div> \
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This example is in Chapter 5.7 in the RL book.

## **Problem description**

A car is intended to learn how to do a right turn (driving from the start line to the finish line). The rules are:

1. The car starts from a random position on the start line with zero velocity.
2. Actions at each time step is the change of velocity.
3. The velocity cannot be zeros on both direction expect it is on the start line. The absolute value of the velocity is restricted to be less than 5 on both direction.
4. If the car hit the boundary, restart on a random position on the start line with zero velocity.
5. If the car goes across the finish line, end the episode.
6. Noise: with probability 0.1 the car keep the velocity unchanged regardless of the actions given.


### **Problem setup**

**State:** Position of the car: [row, column] \
**Action:** Change of velocity on both direction: [change on row velocity, change on column velocity] \
**Reward:** Rewards are -1 every step, except that at the final step before an episode ends, the reward is 0.

**Define the map:** The maps are represented by 2D numpy arrays. Area outside the track are padded with zero; the track are marked as 1, start line and finish line are marked as 2 and 3 respectively.\
**Out of track Detection:** This is achieved by simply detect if the next state are out of track, regardless of whether every points along the trajectory are on the track.\
**Finish Detection:** If any potential trajectory from current position to next position across the finish line, we take it as finish, regardless of whether the next state is out of the track. This is achieved by detecting if there exist any "3", in the submatrix defined by the current position and next position as the main diagonal.


This example is solved with two different method:
1. On-policy first-visit Monte Carlo control (for \\(\epsilon\\))-soft policies
2. Off-policy Monte Carlo control

### **Method 1: On-policy first-visit MC control**

This is implemented by exactly following the pseudo code provide in the book (on Page 101).

<div style="text-align:center"><img src="/files/Chapter5/Racetrack/Racetrack_pc.JPG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

**Parameters:**

Initial action-value: All zeros, (since the rewards are always negative, this is kind of exploring start)\
Initial policy: All nine actions are equiprobable.\
Discount factor: \\(\gamma = 0.9\\)\
\\(\epsilon\\)-soft policy: \\(\epsilon = 0.1\\)

**Training history**\
*Map A*
<div style="text-align:center"><img src="/files/Chapter5/Racetrack/train_history_m1_v3.png" alt="drawing" width="700"/></div>

*Map B*
<div style="text-align:center"><img src="/files/Chapter5/Racetrack/train_history_m2_v1.png" alt="drawing" width="700"/></div>


**Some results with different starting point (Map A):** \
<img src="/files/Chapter5/Racetrack/example_m1_v3_ex1.png" alt="drawing" width="220"/>
<img src="/files/Chapter5/Racetrack/example_m1_v3_ex2.png" alt="drawing" width="220"/>
<img src="/files/Chapter5/Racetrack/example_m1_v3_ex3.png" alt="drawing" width="220"/>

**Some results with different starting point (Map B):** \
<img src="/files/Chapter5/Racetrack/example_m2_v1_ex1.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/example_m2_v1_ex2.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/example_m2_v1_ex3.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/example_m2_v1_ex4.png" alt="drawing" width="360"/>

It's interesting that when the car is initialized at some starting point, it does not go forward, instead, it purposely drive out of track to seek for a new starting point. It looks stupid, but does not violate our purpose, it can still reach the finish line within very limited steps.


### **Method 2: Off-policy MC control**

**Parameters:**

Initial action-value: All values are initialize to be -30000, (if set to zero, it converges very slowly)\
Initial policy: Random deterministic policy.\
Discount factor: \\(\gamma = 0.9\\)\
\\(\epsilon\\)-soft behavior policy: \\(\epsilon = 0.3\\)

Note: The book didn't mention how to choose behavior policy. For this example two kind of behavior policy are explored:
1. Completely random policy
2. \\(\epsilon\\)-soft policy (\\(\epsilon = 0.3\\)) updated with target policy.

It is found that the second behavior policy is more efficient in terms of training speed, because it typically generates shorter episodes. However, for the first behavior policy, it can achieve better results in small number of episodes because it explores more.

**Training history**\
*Map A*
<div style="text-align:center"><img src="/files/Chapter5/Racetrack/img_off/train_history_m1_v1.png" alt="drawing" width="700"/></div>

*Map B*
<div style="text-align:center"><img src="/files/Chapter5/Racetrack/img_off/train_history_m2_v1.png" alt="drawing" width="700"/></div>

**Some results with different starting point (Map A):** \
<img src="/files/Chapter5/Racetrack/img_off/example_m1_v1_ex1.png" alt="drawing" width="220"/>
<img src="/files/Chapter5/Racetrack/img_off/example_m1_v1_ex2.png" alt="drawing" width="220"/>
<img src="/files/Chapter5/Racetrack/img_off/example_m1_v1_ex3.png" alt="drawing" width="220"/>

**Some results with different starting point (Map B):** \
<img src="/files/Chapter5/Racetrack/img_off/example_m2_v1_ex1.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/img_off/example_m2_v1_ex2.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/img_off/example_m2_v1_ex3.png" alt="drawing" width="360"/>
<img src="/files/Chapter5/Racetrack/img_off/example_m2_v1_ex4.png" alt="drawing" width="360"/>







## **Code Usage**

Download the code [Racetrack](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter5/Racetrack).

### On-policy first-visit MC control:

**To simulate a single episode:** (input the maximum episode length and which map you are going to use)
{% highlight Bash %}
python3 Episode_generator.py --max_len [MAX_LENGTH, i.e.10000] --map [MAP 1 or 2] {% endhighlight %}


**To train a model:**
{% highlight Bash %}
python3 on_policy_MC_eSoft.py -- map [1 or 2] -- num_episode [Any number] --option [1:start new training or 0: continue training] {% endhighlight %}
You can also change the variables defined at the top of the script to custom your training.

**To plot the training result:**
{% highlight Bash %}
python3 Policy_test_on.py -- map [MAP 1 or 2] -- version [Any number] --option [1:show the plot or 0: save the plot] {% endhighlight %}
If you choose to save the plot, make sure to you have a folder named "img" in the current folder.


###  Off-policy MC control:

**To train a model:**
{% highlight Bash %}
python3 off_policy_MC.py -- map [MAP 1 or 2] -- num_episode [Any number] --option [1:start new training or 0: continue training] {% endhighlight %}
You can also change the variables defined at the top of the script to custom your training.

**To plot the training result:**
{% highlight Bash %}
python3 Policy_test_off.py -- map [MAP 1 or 2] -- version [Any number] --option [1:show the plot or 0: save the plot] {% endhighlight %}
If you choose to save the plot, make sure to you have a folder named "img_off" in the current folder.





**The End**
