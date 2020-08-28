---
title:  "Dyna Maze (Prioritized Sweep)"
date:   2020-08-2 16:10:15 +1000
categories: jekyll update
permalink: /RL_notes/Prioritized-sweep/
---
This is Example 8.4 in RL book.

## **Problem discription**

The goal is to test the performance of Dyna-Q and Prioritized Sweep on difference size of mazes.

**State:** [row, column] \
**Action:** 0 or 1 or 2 or 3 , respectively represents "up", "down", "right", "left" \
**Reward:** 0 every step until termination, 1 when arrive the goal point.

The problem itself is simple, however, difficulty arises as the book does not present the map it used for experiments, and the corresponding optimal solution. Therefore, we have to find a way to generate these maps and solutions. Since the maps may contains thousands of states, we are unable to define them and find the solution manually, we can only pursue for approximate answers.

To see how these maps are generated, see [Map_file](https://github.com/MingruiSun2019/RLbook_reproducing/blob/master/Chapter8/Prioritized_sweeping/Map_file.py) (just some tricks with the index). Some of these maps are shown below. The number of states of these maps are every close to that being used in the book.

<img src="/files/Chapter8/Psweep/Map_0.png" alt="drawing" width="320"/>
<img src="/files/Chapter8/Psweep/Map_1.png" alt="drawing" width="320"/>

<img src="/files/Chapter8/Psweep/Map_2.png" alt="drawing" width="320"/>
<img src="/files/Chapter8/Psweep/Map_3.png" alt="drawing" width="320"/>

We are not able to solve each of these maps to find the optimal solution. We could only define the suboptimal length of path to be $$14*extend\_factor*relax\_factor$$, where 14 is the optimal path length for the smallest map, the *extend_factor* are defined as $$[1, \sqrt{2}, 2, \sqrt{8}, 4, \sqrt{32}, 8, ...]$$, so that the total states are extended by $$[1,2,4,8,16...]$$ times. The *relax_factor* is set to 1.25. With the relax factor being defined, the solution is hence suboptimal.


## **Implementation**

The Prioritized algorithm is given in the book, which is shown below, with my personal comments:
<div style="text-align:center"><img src="/files/Chapter8/Psweep/PS_p1.PNG" alt="drawing" width="650"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

It's worth to notice when searching for "$$\bar{S},\bar{A}$$ predictied lead to S", we should search it from the model (that has already experienced), instead of searching it globally, since all the knowledge about the environment that we have is in the model.

Since Dyna-Q takes too many updates to converge in large maps, we can only get the data for Dyna-Q in the first few smaller maps. The parameters for generating the following plot is: \
Step size: $$\alpha = 1$$ \
Discounting factor: $$\gamma = 0.95$$ \
$$\epsilon$$-greedy policy: $$\epsilon = 0.2$$ \
N-step planning: n = 5 \
Threshold: $$\theta = 0.0001$$
The number of repetition is up to 10 times in smaller maps, and are at least 2 times in larger maps.

We get a compromised version of figure in Example 8.4 in the book as follow. Note that we have under estimated the value since we only test for convergence to suboptimal solutions.
<div style="text-align:center"><img src="/files/Chapter8/Psweep/compare_v3.svg" alt="drawing" width="460"/></div>





## **Code Usage**

Download the code [Blocking_Maze](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter8/Prioritized_sweeping).

To produce Figure 8.4 in the book:
{% highlight Bash %}
python3 Get_data_DynaQ.py
python3 Get_data_P_sweep.py
python3 plot_compare.py {% endhighlight %}

To visualize the maps:

{% highlight Bash %}
python3 plot_map.py -m [The index of map you want to visualize] {% endhighlight %}








**The End**
