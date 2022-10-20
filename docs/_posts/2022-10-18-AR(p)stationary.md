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

[充分条件不会证明](https://www.zhihu.com/question/22385598/answer/297245327)

## Weakly Stationarity

The process $\{X_t, t \in T\}$ is said to be weakly stationary (or covariance
stationary or second-order stationary) if: $E(X^2_
t ) < \inf$ and both $EX_t$
and $Cov(X_t, X_{t+h})$, for any integer $h$, do not depend on $t$.

## Differencial & Difference?

在差分方程中一些比较难以理解的东西（比如 AR process 常见的lag operator）在微分方程中都能找到相对容易理解的对应？我尝试一下用微分方程理解差分方程。

[links-between-difference-and-differential-equations](https://math.stackexchange.com/questions/145523/links-between-difference-and-differential-equations)

### characteristic equation

### solution format



## Questions？

- 能否构造出一个随机变量的期望与时间有关而任意两个随机变量的协方差与时间无关（或者反之）的时间序列
