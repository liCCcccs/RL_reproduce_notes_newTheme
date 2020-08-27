---
layout: post
title:  "Random Walk (Polynomial and Fourier basis)"
date:   2020-08-9 15:10:15 +1000
categories: jekyll update
---
This is to produce Figure 9.3, 9.4 and 9.5 in RL book.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/RW.PNG" alt="drawing" width="600"/></div>

## **Problem description**

The problem setup is exactly the same as in Example 9.1. For completeness, we duplicate the problem summary as follows.

This random walk problem is a slightly more complex version as what is shown in the above figure. From the description in the book, we conclude the key points as follows:

Number of state: 1000, denoted as [1, 2, 3, ..., 1000] \
Starting state: 500 \
Actions: [-100, -99, ..., -2, -1, 1, 2, ..., 99, 100] In total 200 actions. Note that there is no "not move" action.
         If the resulting next state $$S_{t+1}<=0$$ or $$S_{t+1}>=1001$$, the episode terminates. \
Reward: -1 if terminates at left, 1 if terminates at right, 0 otherwise.


## **Ideas of linear method**

The state function is approximated by linear combination of basis functions, the idea is very much like linear regression.

The polynomial basis function for this problem is:

$$x_i(s) = s^{i-1}, \;\;\; i = 1, 2, ..., n+1$$

The Fourier basis function for this problem is:

$$x_i(s) = \cos((i-1) \pi s), \;\;\; i = 1, 2, ..., n+1$$

where n is the order.

The state aggregation method can be also written as using basis functions. In the situation with 10 groups, the basis function is like:

$$x_1(s) = \begin{cases}
             1,    0 \leq s \leq 99  \\
             0,    otherwise  \end{cases}$$

$$x_2(s) = \begin{cases}
            1,    100 \leq s \leq 199  \\
            0,    otherwise  \end{cases}$$

$$x_3(s) = \begin{cases}
         1,    200 \leq s \leq 299  \\
         0,    otherwise  \end{cases}$$

$$......$$

Note that when using basis functions, it is better to normalize the state s to be within [0, 1]. This prevents the value of polynomial basis function being too large and overflow. For Fourier basis, the normalization enable us to always select the period $$\tau = 2$$ and use only cosine basis, which is consistent with the example (above Figure 9.3) on Page 212 in the book.


With basis function being defined, the approximation function is:

$$ \hat{v}(s) = \mathbf{w}^\top \mathbf{x}(s) $$

The SGD update rule with linear function approximation is simply:

$$ \mathbf{w}_{t+1} = \mathbf{w}_t + \alpha[U_t - \hat{v}(S_t, \mathbf{w}_t)] \mathbf{x}(S_t) $$

The Mean Square Value Error is:

$$ \overline{VE}(\mathbf{w}) = \sum_{s\in S} \mu(s) \left[v_{\pi}(s) - \hat{v}(s,\mathbf{w}) \right]^2 $$




## **Implementation**

There is no technical difficulty to produce Figure 9.3 and 9.4:

<div style="text-align:center"><img src="/files/Chapter9/Fourier_Poly_Basis/Figure9_3.png" alt="drawing" width="700"/></div>

<div style="text-align:center"><img src="/files/Chapter9/Fourier_Poly_Basis/Figure9_4.png" alt="drawing" width="600"/></div>


Gradient Monte Carlo method is used to produce Figure 9.5, whose pseudo code is given as follows.

<div style="text-align:center"><img src="/files/Chapter9/Gradient_MC/GMC_p1.PNG" alt="drawing" width="600"/></div>

The parameters for training are:

Step size: $$\alpha = 0.001$$ for Polynomial basis; $$\alpha = 0.0005$$ for Fourier basis. \
Averaged over 30 runs.

We get Figure 9.5 in the book as follows.

<div style="text-align:center"><img src="/files/Chapter9/Fourier_Poly_Basis/root_VE_history.png" alt="drawing" width="600"/></div>



## **Code Usage**

Download the code [Fourier_Poly_Basis](https://github.com/MingruiSun2019/RLbook_reproducing/tree/master/Chapter9/Fourier_Poly_Basis).

To produce the left figure in Figure 9.3 and 9.4:
{% highlight Bash %}
python3 Figure9_3.py
python3 Figure9_4.py {% endhighlight %}


To produce Figure 9.5:
{% highlight Bash %}
python3 Get_true_val.py
python3 Get_RMS_error_Poly.py -o 5
python3 Get_RMS_error_Poly.py -o 10
python3 Get_RMS_error_Poly.py -o 20
python3 Get_RMS_error_Fourier.py -o 5
python3 Get_RMS_error_Fourier.py -o 10
python3 Get_RMS_error_Fourier.py -o 20
python3 plot_VE.py {% endhighlight %}


In addition, you can also run these method one time to see the estimated value by:
{% highlight Bash %}
python3 Random_walk_poly.py
python3 plot_val_poly.py {% endhighlight %}
or
{% highlight Bash %}
python3 Random_walk_Fourier.py
python3 plot_val_Fourier.py {% endhighlight %}


where you can change any of the parameters you like in the first few lines of script.








**The End**
