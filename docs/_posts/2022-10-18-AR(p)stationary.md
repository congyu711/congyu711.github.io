---
layout: post
title:  "Autoregressive Process & Weakly Stationarity"
date:   2022-10-18 0:00:00 +0800
categories: econometrics
---
# UNFINISHED

> 2022 计量2 课程内容

我首先评论一下时间序列，AR Process 是与我之前学过的东西关系最紧密的内容（在计量2中），它是Inhomogeneous Linear Difference Equation
常数项换成了一个白噪声，而这个非齐次线性差分方程在计算递归算法复杂度的时候也经常遇到；而且他基本上就是微分方程的离散形式。

首先关于线性差分方程，[link](https://www.cl.cam.ac.uk/teaching/2003/Probability/prob07.pdf) 一般的解法是直接猜测形式是$u_n=Aw^n$，也可以
证明线性差分方程的解的形式一定是$u_n=A_1w_1^n+A_2w_2^n+\ldots$ 这种形式。 特征方程的所有解(假设无重根)的n次方都是一个特解，
根据给定的k个初值可以确定一个特解，而且对于任意一个初始解，带入特征方程得到的k个解中正好形成一个k行的线性方程组，
左侧系数是一个Vandermonde行列式，一定有解。

[线性齐次ODE解的形式为什么一定是指数函数的线性组合](https://math.stackexchange.com/questions/2752909/why-does-a-linear-homogeneous-ode-have-only-a-solution-of-summed-exponentials)

[特征方程有重根的线性齐次递推式的通项形式“证明”](https://www.zhihu.com/question/516043073)

[充分和必要条件](https://www.zhihu.com/question/22385598/answer/297245327)
> 充分条件的证明：（sxy） 考虑$|\alpha|>1$ 的多项式，证明前后两项的模长不相同->前后两项不相等->$|\alpha|>1$ 没有根
> 必要条件：（qhj） 构造多项式$\Pi (1-x_i)$,韦达定理凑一下

## Weakly Stationarity

The process $\\{X_t, t \in T\\}$ is said to be weakly stationary (or covariance
stationary or second-order stationary) if $E(X_t^2 ) < \infty$ and both $EX_t$ and $Cov(X_t, X_{t+h})$, 
for any integer $h$, do not depend on $t$.

## Differencial & Difference?

[links-between-difference-and-differential-equations](https://math.stackexchange.com/questions/145523/links-between-difference-and-differential-equations)


## AR(p) Weakly Stationarity

$$
\begin{aligned}
    y_t=\phi_0 +\phi_1y_{t-1}+&\ldots+\phi_p y_{t-p}+\epsilon_t\\
    (1-\phi_1L-\phi_2L^2-\ldots-\phi_p L^p)y_t&=\phi_0+\epsilon_t\\
\end{aligned}
$$

$$
    [(1-L/x_1)(1-L/x_2)\ldots(1-L/x_p)]y_t=\phi_0+\epsilon_t\\
$$

$$
    [1-(\phi_1-\phi_2L-\ldots-\phi_pL^{p-1})L]y_t=\phi_0+\epsilon_t\\
$$

*$L$是lag-operator*

对于1，如果$(1-L/x_1), (1-L/x_2)\ldots$ 都可逆(也就是都能写成$(1+x_1L+x_1^2L^2+\ldots)(1+x_2L+x_2^2L^2+\ldots)\ldots(1+x_pL+x_p^2L^2+\ldots) \epsilon_t$
)，
就能轻松将一个AR(p)转化成一个MA(inf)；对于2，如果$1-(\phi_1-\phi_2L-\ldots)L$可逆也可以转化成MA(inf)

对于MA(inf)，期望$E(y_t)=\mu$,方差$Var(y_t)=\sigma^2 \sum_{j=0}^{\inf}\theta_j^2$, 需要让方差收敛.

对于1, 考虑$\epsilon_{t-i}$前面的系数, $\epsilon_{t-i}$需要在前面的p个括号中每个括号选择一项，我们尝试写出几项：

$$
\begin{aligned}
    i=1 \quad & \sum x_i\\
    i=2 \quad & \sum x_i^2+\sum_{i<j} x_i x_j\\
    i=3 \quad & \sum x_i^3+\sum_{i<j} x_i^2 x_j+\sum_{i<j} x_i x_j^2+\sum_{i<j<k} x_i x_j x_k\\
    \ldots
\end{aligned}
$$

**Time Series Analysis** James D. Hamilton page35，用另外一种方法推出上面的系数

哎，放弃了，James Hamilton 说可以轻松证明上面的系数是绝对收敛的，我想了好久也无法证明出来。。。以后有时间再说吧

## Questions？

- 能否构造出一个随机变量的期望与时间有关而任意两个随机变量的协方差与时间无关（或者反之）的时间序列
