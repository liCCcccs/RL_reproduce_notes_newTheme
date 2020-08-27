---
layout: post
title:  "Tic Tac Toe"
date:   2020-07-09 16:10:15 +1000
categories: jekyll update
---
This example is initiated in Section 1.5 in the RL book.

### Problem setup

1. Train an agent against a imperfect fixed opponent.
2. Train two agent against each other.

* A fixed opponent means that given a state, the action taken by the opponent is deterministic.

In problem 1, the opponent goes first, its piece is denoted as an **X**, while the agent's piece is denoted as an **O**.
One grid on a chess board has 3 possible states: empty, a piece **X**, a piece **O**, we denote them as 0, 1, 2 respectively.

For a 3x3 chess board, there are $$3^{3\times 3} = 19683$$ different states. \
Actions for players are placing an **X** or **O** on an empty grid. \
Rewards are 0 each step before the game ends. If agent wins, reward is 1 for the last step, if loses or tie, reward is -1 for th elast step.

### Demo

1. Match against an imperfect opponent

![](/files/Chapter1/Game2.gif)

2. Agent vs agent

Matches gets interesting here. Most of the time

![](/files/Chapter1/Game1.gif)  ![](/files/Chapter1/Game3.gif)  ![](/files/Chapter1/Game4_bad.gif)

### Thoughts

It's interesting that the way of initialize state value function and reward is not 'aggressive' enough. Because it gives reward -1 to both lose and tie game. As a result, when in some situation, the player realizes it cannot win, it might just give up and let the opponent win. Therefore, if one agent wins, we take half the value off the state that leads to loss for the other agent. In such a way we penalize the agent's action that let opponent win, and after training, our agent will still fight for tie game even if it is already impossible to win in some situation.



## **Code Usage**

Download the code [TicTacToe](https://github.com/liCCcccs/Reinforcement-Learning-Book-Reproduce/tree/master/Chapter1/TicTacToe).

To train an agent (against a fixed opponent) and simulate a game:
{% highlight Bash %}
python3 train_vs_fix_opponent.py
python3 virtual_match.py {% endhighlight %}

To train an agent (against another agent) and simulate a game:
{% highlight Bash %}
python3 train_vs_self.py -o [0:train from scratch, 1:continue training]
python3 agent_vs_agent.py {% endhighlight %}



















**The End**
