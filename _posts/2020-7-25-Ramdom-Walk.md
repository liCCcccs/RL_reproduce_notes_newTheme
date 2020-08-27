---
layout: post
title:  "Random Walk"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
---
<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/RW.PNG" alt="drawing" width="500"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

This is Example 6.2 and Example 6.3 in the RL book.

## **Random Walk**

The purpose is to compare Temporal difference method and Monte Carlo method in terms of estimating state values.

There are in total 5 states, A B C D E, in above figure. The reward on each transition is also labelled in the figure. The policy is to go left and right with equal probability. The true values for states A,B,C,D,E are respectively \\(\frac{1}{6}\\), \\(\frac{2}{6}\\), \\(\frac{3}{6}\\), \\(\frac{4}{6}\\), \\(\frac{5}{6}\\).

### **Implementation**

The algorithm (TD(0)) to generate the left figure in Example 6.2 is given in the book:
<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/RW_pc1.PNG" alt="drawing" width="600"/></div>
*(source: Reinforcement Learning: An Introduction, 2nd Edition, by Richard S. Sutton and Andrew G. Barto)*

Following the pseudo code, it is easy to generate this figure:
<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/TD0.png" alt="drawing" width="500"/></div>

However, for the right figure, things are going weird. The book didn't tell which type of Monte Carlo method it uses (first visit or every visit, the value of discounting factor \\(\gamma\\), no pseudo code...). Finally, it turns out it uses the following algorithm, without even mentioning in previous chapters, to make it clear, I directly modify the first-visit MC pseudo code:

<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/RW_pc2.PNG" alt="drawing" width="600"/></div>

The initial value of \\(V(s)\\) is set to 0.5 for all states according to the book. We get the right figure as follows:

<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/MCTD0.png" alt="drawing" width="500"/></div>

Is Monte Carlo method really that bad like the book shows? Not really. The book purposely used a very poor structured MC algorithm to emphasis that TD method is better. Actually, if we just follow the first-visit MC method with slight modification, as the pseudo code shown below, we have make MC method perform really well.

<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/RW_pc3.png" alt="drawing" width="600"/></div>

Using the same parameter, and extend the number of episode to 1000, we get the following figure.

<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/MCTD0_vs.png" alt="drawing" width="500"/></div>

Now we cannot depreciate MC method that much in this example.




## **Random Walk under batch updating**

The book does not present any pseudo code for the batch updating algorithm. Here we first present some python code that can produce very similar plot as Figure 6.2 in the book. The following code is for generate one run of data (The estimated value of states from episode 1 to 100). We will use this function to generate 100 runs of data and plot the RMS error.



{% highlight Python %}
def Get_TD0P_data_batch(walkman, num_episode, alpha):
    est_history = np.zeros((num_episode + 1, NUM_STATE))   # store the estimated state value for each episode
    est_history[0, :] = np.copy(walkman.state_value)  # initial state value

    state_batch = []
    reward_batch = []
    est_values = np.copy(walkman.state_value)    # initial state value
    for episode in range(num_episode):
        walkman.Restart()   # Restart at point C
        state_list, reward_list, num_step = Episode_generator(walkman)  # Generate an episode
        state_batch.append(state_list)         # all available experience up to now
        reward_batch.append(reward_list)       # all available experience up to now

        while True:
            innovation = np.zeros(5)
            for episode in range(len(state_batch)):
                for step in range(len(state_batch[episode])):
                    state = int(state_batch[episode][step])
                    reward = reward_batch[episode][step]
                    if step != len(state_batch[episode]) - 1:
                        next_state = int(state_batch[episode][step+1])
                        innovation[state] += alpha * (reward + est_values[next_state] - est_values[state]) # Do increments by (6.2)
                    else:
                        innovation[state] += alpha * (reward - est_values[state])    # if it's the last step, the return is equal to the reward
            if np.sum(np.abs(innovation)) < 0.001:   # If the increments is less than a threshold, end the loop for current episode
                break
            est_values += innovation    # Update state value function once per episode

        est_history[episode, :] = est_values  # save the result

    return est_history   {% endhighlight %}


For Monte Carlo batch updating method, we also present the python code here, its only difference from TD batch method is the increment part, so we only comment on the part that is different.

{% highlight Python %}
def Get_MC_data_batch(walkman, num_episode, alpha):
    est_history = np.zeros((num_episode + 1, NUM_STATE))
    est_history[0, :] = np.copy(walkman.state_value)

    state_batch = []
    reward_batch = []
    est_values = np.copy(walkman.state_value)
    for episode in range(num_episode):
        walkman.Restart()
        state_list, reward_list, num_step = Episode_generator(walkman)
        state_batch.append(state_list)
        reward_batch.append(reward_list[-1])  # The returns is simply the reward at the last step, since all previous rewards are zero

        while True:
            innovation = np.zeros(5)
            for episode in range(len(state_batch)):
                returns = reward_batch[episode]
                for step in range(len(state_batch[episode])):
                    state = int(state_batch[episode][step])
                    innovation[state] += alpha * (returns - est_values[state])  # Do increments by (6.1)

            if np.sum(np.abs(innovation)) < 0.001:
                break
            est_values += innovation

        est_history[episode, :] = est_values

    return est_history   {% endhighlight %}

While trying to generate Figure 6.2, it seems that the plot does not use the "all states are 0.5" initial value, because the initial value for both method are larger than the RMS error:

$$ \sqrt{\frac{(1/6-0.5)^2 + (2/6-0.5)^2 + (3/6-0.5)^2 + (4/6-0.5)^2 + (5/6-0.5)^2}{5}} \approx 0.2357$$

In addition, the plot seems to have been truncated at $$y = 0.25$$. Thus, we set the initial value to be zero for all states and simulate, we get a plot very similar to Figure 6.2 as follows:

<div style="text-align:center"><img src="/files/Chapter6/RandomWalk/MCTD_batch.png" alt="drawing" width="500"/></div>




## **Code Usage**

Download the code [Random_Walk](https://github.com/MingruiSun2019/RLbook_reproducing/tree/master/Chapter6/Random_Walk).

### Random walk:

To generate the left figure in Example 6.2:

Run the following in the terminal:
{% highlight Bash %}
python3 TD0.py {% endhighlight %}

To generate the right figure in Example 6.2:

Run the following in the terminal:
{% highlight Bash %}
python3 Get_TDMC_data.py -m 0 -a 0.01
python3 Get_TDMC_data.py -m 0 -a 0.02
python3 Get_TDMC_data.py -m 0 -a 0.03
python3 Get_TDMC_data.py -m 0 -a 0.04
python3 Get_TDMC_data.py -m 1 -a 0.05
python3 Get_TDMC_data.py -m 1 -a 0.1
python3 Get_TDMC_data.py -m 1 -a 0.15
python3 plot_MCTD.py {% endhighlight %}

To plot the figure with better Monte Carlo performance:
{% highlight Bash %}
python3 Get_TDMC_data_vs.py -m 0 -a 0.01
python3 Get_TDMC_data_vs.py -m 0 -a 0.02
python3 Get_TDMC_data_vs.py -m 0 -a 0.03
python3 Get_TDMC_data_vs.py -m 0 -a 0.04
python3 Get_TDMC_data_vs.py -m 1 -a 0.05
python3 Get_TDMC_data_vs.py -m 1 -a 0.1
python3 Get_TDMC_data_vs.py -m 1 -a 0.15
python3 plot_MCTD_vs.py {% endhighlight %}


###  Random Walk under batch updating:

To generate Figure 6.2 in the book:

Run the following in the terminal:
{% highlight Bash %}
python3 Get_TDMC_data_batch.py -m 0
python3 Get_TDMC_data_batch.py -m 1
python3 plot_MCTD_batch.py {% endhighlight %}




**The End**
