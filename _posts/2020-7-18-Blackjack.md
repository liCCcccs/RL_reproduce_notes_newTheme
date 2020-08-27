---
layout: post
title:  "Blackjack Monte Carlo control"
date:   2020-07-13 16:10:15 +1000
categories: jekyll update
---
![](/files/Chapter5/Blackjack_title.jpg)

This example is initiated in Chapter 5.1 in the RL book.

## **Blackjack rules (for this project)**
**Card** &nbsp; 2, 3, 4, 5, 6, 7, 8, 9, 10, J,&nbsp; Q, &nbsp;K, &nbsp;A. \
**Value** 2, 3, 4, 5, 6, 7, 8, 9, 10, 10, 10, 10, 1 or 11. \
Each type of card has a probability of 1/13 to be chosen.

1. The dealer draws two cards, one face up, one face down.
2. Player is dealt two cards.
3. Player can hit as many times as he wants, until he bust (game over, player lose) or choose to stick. Actions are according to player's policy
4. Dealer can hit as many times as he wants, until he bust (game over, player win) or choose to stick. Actions are according to dealer's policy
5. If no one bust, compare player's and dealer's card to get result. (player win, player lose, draw)

**Dealer's policy (fixed):** If dealer's sum is larger or equal to 17, stick. If dealer's sum is smaller or equal to 16, hit.
**Player's policy (to be learned):** Initial policy: If player's sum is larger or equal to 20, stick. If dealer's sum is smaller or equal to 19, hit.

## **Problem setup**

**State:** [Player's sum (12 to 21), dealer's showing card (A to 10), whether player has a usable ace (0 or 1)] \
**Action:** Stick(0) or hit (1) \
**Reward:** Rewards are zeros every step except the last step. Reward for the last step is +1 or 0 or -1, if player win or draw or lose.

Before doing value estimation or policy improvement, Monte Carlo method requires generating an complete episode, \\(S_0, A_0, R_1, S_1, A_1,R_2,..., S_{T-1}, A_{T-1}, R_T\\). Since an episode of a Blackjack game is a bit confusing, an example is given below.

**An example episode** \
Dealer get two cards: A (face up), 3 (face down). \
&#9660;  \
Player get two cards: A, 2.  &nbsp;   &nbsp;   &nbsp;    &#10233; \\(S_0 = [13, A, 1]\\) \
&#9660;  \
Player hit: get 10.   &nbsp;   &nbsp;   &nbsp;   &#10233; \\(A_0 = 1, S_1 = [13, A, 0], R_1 = 0\\)  \
&#9660;  \
Player hit: get 7.  &nbsp;   &nbsp;   &nbsp;    &#10233; \\(A_1 = 1, S_2 = [20, A, 0], R_2 = 0\\)   \
&#9660;  \
Player stick.  &nbsp;   &nbsp;   &nbsp;    &#10233; \\(A_2 = 0\\)   \
&#9660;  \
Dealer hit: get 5.     \
&#9660;  \
Dealer stick.     \
&#9660;  \
Compare: Player's sum is 20, dealer's sum is 19, player win. &nbsp;   &nbsp;    &#10233; \\(R_3 = 1\\)   

### **Problem 1: Estimating state value**

After understanding how to generate episodes, the following is straightforward, just follow the pseudo code in the book. A little modification to the pseudo code: Instead of append G to \\(Returns(S_t)\\) and average, iteratively update \\(Returns(S_t)\\) using the formula:

$$Returns(S_t) = Returns(S_t) + \frac{1}{N_{S_t}} (G - Returns(S_t))$$

where \\(N_{S_t}\\) is the number of time state \\(S_t\\) is visited.

We get Figure 5.1 in the book as follows.

**Usable ace:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **After 10000 episodes** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**After 50000 episodes**\
<img src="/files/Chapter5/Prediction/val_func_ep10000_usable1.png" alt="drawing" width="300"/> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter5/Prediction/val_func_ep50000_usable1.png" alt="drawing" width="300"/>

**No usable ace:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **After 10000 episodes** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;**After 50000 episodes**\
<img src="/files/Chapter5/Prediction/val_func_ep10000_usable0.png" alt="drawing" width="300"/> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter5/Prediction/val_func_ep50000_usable0.png" alt="drawing" width="300"/>


### **Problem 2: Solving Blackjack**

Most of the code from above example can be kept unchanged. The code we need to modify here is:
1. When generating episodes, the first action \\(A_0\\) needs to be random (exploring starts). In the code we select the first action with equal probability to be 0 or 1.
2. Estimating action-value function \\(Q(S_t, A_t)\\), instead of state-value function \\(V(S_t)\\).
3. Do policy improvement after estimating  \\(Q(S_t, A_t)\\).

Figure 5.2 in the book plots the state-value function, so we need to convert \\(Q(S_t, A_t)\\) to \\(V(S_t)\\) by:

$$V(S_t) = \max_{A_t} Q(S_t, A_t)$$

The book does not tell how many episodes are needed to get the result. In the code, we set it to 1 million. We get Figure 5.2 as follows:

**State-value function:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Usable ace** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **No usable ace**\
<img src="/files/Chapter5/MC_ES/val_func_usable0.svg" alt="drawing" width="320"/> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter5/MC_ES/val_func_usable1.svg" alt="drawing" width="320"/>

**Policy:** \
&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **Usable ace** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; **No usable ace**\
<img src="/files/Chapter5/MC_ES/Policy_Usable.svg" alt="drawing" width="300"/> &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
<img src="/files/Chapter5/MC_ES/Policy_noUsable.svg" alt="drawing" width="300"/>

In above figures, 0 and 1 represent STICK and HIT respectively. Note that in above plot with usable ace, policy for [player's sum = 18, dealer's showing card = A] is different from that in the book. This is due to the randomness of MC method, which also indicates that 1 million episodes may not be enough. Certainly you can try more episodes, because 1 million episodes only takes about 1 minute on my laptop.






## **Code Usage**

### Example 5.1 in the book:

Download the code [Blackjack_prediction](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter5/Blackjack_prediction).

**To simulate a single game:**
{% highlight Shell %}
python3 game_simulator.py{% endhighlight %}

**To generate Figure 5.1 in the book:**
{% highlight Shell %}
python3 MC_prediction.py -n 10000
python3 MC_prediction.py -n 50000 {% endhighlight %}
Then find the resulting images in `./img` folder.



### Example 5.3 in the book:
Download the code [Blackjack_MC_ES](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter5/Blackjack_MC_ES).

**To simulate a single game:**
{% highlight Shell %}
python3 game_simulator.py{% endhighlight %}


**To generate Figure 5.1 in the book:**
{% highlight Shell %}
python3 MC_ES.py
python3 plot_val_func.py{% endhighlight %}
Then find the resulting images in `./img` folder.




**The End**
