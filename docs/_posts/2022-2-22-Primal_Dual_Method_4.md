---
layout: post
title:  "Primal Dual Method 4"
date:   2022-2-22 12:30:08 +0800
categories: alg
---

后来在读 the design of approximation algorithms 中的 chap 7. 

首先Theorem 7.1的定理$f=\max{\|\\{j:e_i\in S_j\\}\|}$是近似比，就是[primal-dual-3]({{url}}/alg/2022/02/15/Primal_Dual_Method_3.html)
中的 evaluate the performance guarantee 部分第一个定理（参数是$\alpha$那个）

对于[primal-dual-2]({{url}}/alg/2022/02/15/Primal_Dual_Method_2.html)中说到的近似算法放松互补松弛条件（CSC），这里又更详细的解释：
- enforce primal CSC 意味着如果$x_i>0$，$x_i$在对偶问题里对应的不等式取等。
- relax dual CSC 如果某个对偶变量$y_i>0$，在原问题里对应的约束不等式却未必取等。

求近似比的部分中$f=\max{\|\\{j:e_i\in S_j\\}\|}=\sum_{j:e_i\in S_j}x_j$ 也就是$y_i>0$在原问题中对应的约束不等式左侧。

因此 relax dual CSC 保证了近似比，而 enforce primal CSC 提供了通过对偶构造原问题可行解的方法。

***
整数规划的对偶应该是什么样的？对于同一个问题，不同的整数规划建模的对偶最优解都相同吗？

整数规划没有严格意义上的对偶，但是有个叫拉格朗日对偶的东西。

对于同一个问题不同整数规划建模对偶的最优解是不一样的，不同建模的线性松弛最优解都不一样。

拉格朗日对偶大概是这样的东西：（看了知乎上王源的integer programming翻译，我推了一下，但基本上变成latex公式输入练习了。。。）
[primal-dual-1]({{url}}/alg/2022/01/16/Primal_Dual_Method_1.html)中有一点拉格朗日函数和对偶的部分，那里用的原问题是一个线性规划，
把他变成整数规划，加入一个$x\in X\subset Z$的约束，然后求$\max_{\lambda}\min_{x}\lambda^Tb-(c^T-\lambda^TA)x$
（在$x\in X\subset Z$的约束下）实际上也就是通过调整$\lambda$，让拉格朗日函数的下确界尽量大。

我总觉得这里拉格朗日函数的最小值就是原来的整数规划最优解，但是实际上不是，拉格朗日函数已经是原问题的松弛了，因为在拉格朗日函数中，扔掉了$Ax-b\geq 0$这个
约束，后面的过程根本没有考虑过$Ax-b\geq 0$这个约束

IP:

$$
\begin{align*}
    \min \quad &c^Tx\\
    s.t. \quad Ax&\geq b\\
     x&\geq 0\\
     x&\in Z
\end{align*}
$$

构造$f(x)=c^Tx-\mu^T(Ax-b)\leq c^Tx$(let $\mu\leq 0$)

$f(x)$的下确界尽量大：$g(\mu)=\max_{\mu}\min_{x}(c^T-\mu^TA)x+\mu^Tb$

写成线性规划的形式：

$$
\begin{align*}
    \min \quad &\eta\\
    s.t. \quad 
    (c^T-\mu^TA)x_1+\mu^Tb&\geq \eta\\
    (c^T-\mu^TA)x_2+\mu^Tb&\geq \eta\\
    &\ldots\\
    (c^T-\mu^TA)x_n+\mu^Tb&\geq \eta\\
    \mu &\geq 0\\
    \eta &\in R
\end{align*}
$$

然后写出上面的线性规划的对偶：

$$
\begin{align*}
    \min \quad \sum_{i=1}^n \lambda_i(c^Tx_i)&\\
    s.t. \quad
    \sum_{i=1}^n \lambda_i (Ax_i-b)&\geq 0\\
    \sum_{i=1}^n \lambda_i &= 1\\
    \lambda &\geq 0
\end{align*}
$$

把此处的$x_i\in Z$放大为$x_i\in R$,
由于$x_i\in R, x_i\geq 0$是凸集，所以$x=\sum_{i=1}^n \lambda_i x_i\in R$，上面的对偶可以直接改写为整数规划的线性松弛。

这说明了通过调整乘子来让拉格朗日函数的下确界最大的这种这种方法获得的解要比线性松弛的解更好，因为上面有一个把$x\in Z$放大到$x\in R$的过程。

然后我就发现了一个问题，观察$g(\mu)=\max_{\mu}\min_{x}(c^T-\mu^TA)x+\mu^Tb$，发现如果取某个$\mu$使得$c^T-\mu^TA$的某一项是小于0的，
由于$x$在这里的范围是$0\leq x \in Z$，为了取到$\min$，$x$对应分量就会取正无穷，那么下确界也就是负无穷了，无法得到一个有效的对偶，因此
取$\mu$必须满足$(c^T-\mu^TA)\geq 0$，这样一来$x$一定会取到$\mathbf{0}$，下确界也就是$\mu^Tb$，写成一个线性规划问题竟然和原来的整数规划的
线性松弛的对偶一样，但是根据刚刚的证明拉格朗日函数下确界的这种方法应该比线性松弛获得的解更好。

我觉得问题在于：上一段的简单分析应该是没有错误的，这也是为什么 integer programming 中讲拉格朗日对偶只是把一部分约束放入函数中。在处理的过程中，
$Ax-b$被扔到了目标函数里面去，而$x$的取值范围并没有保证$Ax-b\geq 0$。实际上如果把所有约束都放到目标函数内的话拉格朗日对偶就是和线性松弛的对偶一样的。
观察上面$x_i\in Z$放大为$x_i\in R$的部分，实际上$x_i$是各个分量大于等于0的所有n维整数向量，他们构成的凸包和$x_i\in R$构成的完全一样。

***

