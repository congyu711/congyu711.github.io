---
layout: post
title:  "a fast alg for a LP on incentive allocation"
date:   2022-9-16 8:30:08 +0800
categories: mip combinatorics
---

[MCKP.pdf]({{url}}/../../assets/pdf/MCKP.pdf)

>   7.8突然发现gurobi的许可证和硬件绑定甚至包括mac地址，wsl2每次重启都随机生成mac地址，这非常不妙，许可证直接失效了，\
> [这里](https://support.gurobi.com/hc/en-us/articles/7367019222929)有在wsl2上激活的提示，固定mac地址再激活...




##   2. lyft optimization -> blog -> [paper](https://www.sciencedirect.com/science/article/pii/S0377221799004518)

https://eng.lyft.com/how-to-solve-a-linear-optimization-problem-on-incentive-allocation-5a8fb5d04db1

### at most one variable with a fractional solution?

![LP](https://miro.medium.com/max/1400/1%2A_5K88oTSRTSSKR7fI2uBow.png)

分组01背包的线性松弛；一共$m$组物品，每组有 $k_i$ 个物品，每个物品有价值 $w_{ij}$ 
和费用 $c_{ij}$，组内只能选择至多一个物品，并且选择的物品费用总和不能超过上限 $C$

可以发现线性松弛的最优解中至多只有一个$i$ s.t. $x_{ij}$ for some j 不为整数

假设最优解中某一组中有两个分数解，其中一个物品效率比另一个更高，降低效率低的物品对应的$x_{ij}$
提高效率高的物品对应的$x_{ij}$，总价值未必更高，可以轻易构造出一组中有两个分数解的反例（我开始时想要这样证明，但是并不行）

<!-- 然而如果是不同的组中都有分数解，把所有$x_{ij}\in (0,1)$对应的物品找出，然后按照效率$\frac{v}{c}$ 排序，从效率最低的物品$u$开始考虑，选择一个不在当前组中的、$x_{ij}\in (0,1)$的、效率最高的物品$o$，把分配给$u$的cost分配给$v$,这样$x_u$会减小，$x_o$增加，而$\frac{v_u}{c_u}>\frac{v_o}{c_o}$，目标函数会变大 完全错误，不能这样解释 -->


### preprocessing
> Coupons eliminated in the preprocessing step are dominated by at least one coupon in the feasible set. In other words, the cost, value and efficiency of feasible coupons for each given rider are in increasing order. 

> ![assumption](https://miro.medium.com/max/1400/1%2AoiIOEcbepQKgrkAfTubB-w.png)

这两个问题（fractional solution 和 preprocessing）都在[this](https://doi.org/10.1287/opre.27.3.503)文章中解决了，第二个问题对应文章中的`proposition 2`，同时也说明了预处理就是求同组物品在$v,c$平面上的下凸包上的物品。注意到proposition 2 的条件如果再加上每组总有一个$v=0,w=0$的物品（对应着不选择任何物品），就相当于上面图片中的assumption

第一个问题对应文章的`Properties of an Optimal Solution` 部分，文章证明了线性规划所有基本解都只有最多两个$x_{ij}\in (0,1)$

每组中一定有一个$x_{ij}$在基本解中，而我们可以给每组都增加一个$v=0,w=0$的物品，可以把约束写成 $\sum_jx_{ij}=1\quad i\in [n]$，线性规划一共有$n+1$个约束，基本可行解一共就有$n+1$个变量，那么一定有且仅有一组中两个变量都在基本解中，对于其他组，组内一定只有整数解（为了满足$\sum_jx_{ij}=1\quad i\in [n]$）,最多只有两个分数解


### the algorithm

简单总结一下lyft解决这个lp的方法和复杂度。
![Dual](https://miro.medium.com/max/1400/1*nWbaZVpT_fjXDQYDo7P2Tg.png)

首先把对偶问题目标函数看成$\lambda$的函数，根据第一个约束$y_i\geq \max_j\{v_{ij}-c_{ij}\lambda\}$, 得到目标函数中的$\sum y_i \geq \max\{0,\max_j\{v_{ij}-c_{ij}\lambda\}\}$

然后发现每一个$y_i$的最大值只和$\lambda$ 还有第$i$组的coupons有关，可以求出在给定$\lambda$的情况下$y_i$的最优解然后对于$m$个$y_i$求解，这是lyft的大概思路

1. 可以求出在给定$\lambda$的情况下$y_i$的最优解，也就是说对于所有i，算出$y_i$最优解关于$\lambda$的函数，根据之前的`preprocessing`这是一个直线构成的分段函数，并且是下凸的。（实际上对于每一个物品都有$y_i=v_{ij}-c_{ij}\lambda$这样的直线，而我们要找的函数显然就是这些直线最上面的边缘线up envelope）而且可以知道如果把物品按照cost大小排序，**只有相邻的物品对应的直线交点位于up envelope上**(此处出现了错误，未必是相邻物品对应的直线交点才在up envelope上，相邻物品对应的交点也可能不在上面。 见[graph from lyft blog](https://miro.medium.com/max/1050/1*2o1r3nque_JvPpd5ck7BNQ.png))，~~我们只需要维护这些breakpoints，每个passenger有k个coupon，time complexity $O(k)$.~~找到这k条直线的up envelope 可以使用[这本书](https://graphics.stanford.edu/courses/cs468-06-fall/Papers/01%20har-peled%20notes.pdf) 中duality一章17.1.1.2 Convex hull and upper/lower envelopes 介绍的内容，因此k条直线需要$O(k\log k)$... 然而计算一下得知直线为$y=v_{ij}-c_{ij}\lambda$ 对偶的点为 $(-c_{ij},-v_{ij})$，要求$y=v_{ij}-c_{ij}\lambda$构成的up envelope 就相当于求$(-c_{ij},-v_{ij})$这些点的凸包的下半部分。 可以看到在预处理的时候（[文章](https://doi.org/10.1287/opre.27.3.503)）我们采用的方法是求$(v_{ij},c_{ij})$ 的“右下方”凸包（这个预处理比lyft blog也就是上文的图片中讲的更强），很容易发现这个部分一定是$(-c_{ij},-v_{ij})$的下凸包的一部分。于是发现做过预处理之后这里不需要再做了，剩下的所有物品对应的直线都会出现在up envelope 上，需要$O(k)$ 的时间找到所有 breakpoints
2. LP Dual 的最优解，对于每个i，1中的$y_i,\lambda$函数都是凸函数，他们的和也是，我们需要找到$\sum_{i=1}^m y_i(\lambda)+C\lambda$这个函数的最小值点.

> For each rider we keep a pointer to the largest element in the sorted list of breakpoints that has yet to be processed. We start with an active set of breakpoints, one for each passenger i = 1, . . . , m, initially equal to vᵢ₁/cᵢ₁(which is that passenger’s greatest bang/buck value). We initialize the current slope, which is for λ greater than all breakpoints, equal to C, and introduce the notation that cᵢ₀ = 0, i = 1, . . . , m, for a latter use (though this doesn’t correspond to any coupon option).We keep the currently active breakpoints in a priority queue, and in each iteration, extract the maximum remaining one. Suppose that the current breakpoint extracted corresponds to coupon j* for rider i*. We know the slope of the dual objective function greater than this breakpoint, and want to compute the slope just less than it. To do so, we need to reflect the change from the old contribution to the slope for i* to its new one: the new one is option j*, and the old one is option j* − 1. If we let let OLD denote the cost of coupon j* − 1 for rider i* and let NEW denote the cost of coupon j* for rider i*, then the change of cost is OLD-NEW. To replace the breakpoint extracted for i*, we insert the next breakpoint of i* into the priority queue (if there is one available), and the algorithm continues to the next iteration. We continue to iterate until we have decreased the slope from C to below zero.

from [lyft blog](https://eng.lyft.com/how-to-solve-a-linear-optimization-problem-on-incentive-allocation-5a8fb5d04db1)


## LP(one person can be assigned $p$ coupons)

$$
\begin{aligned}
\max \quad \sum_{i,j} w_{i,j}x_{i,j} \\
s.t.     \sum_{i,j} c_{i,j}x_{i,j} &\leq B & \\
         \sum_{j} x_{i,j} &\leq p  & i\in [n]\\
         x_{i,j}  &\leq  1 & i\in [n],j\in [k]\\
         x_{i,j}  &\geq  0 & i\in [n],j\in [k]\\
\end{aligned}
$$


### Dual

$$
\begin{aligned}
\min \quad B\lambda + \sum_{i\in [n]} \left(py_i + \sum_{j\in [k]} z_{i,j} \right)\\
s.t. \qquad y_i+c_{i,j}\lambda+z_{i,j} &\geq w_{i,j} & i\in [n],j\in [k]\\
         y_i  &\geq 0 & i\in [n] \\
         z_{i,j}  &\geq  0 & i\in [n],j\in [k] \\
         \lambda  &\geq  0 \\
\end{aligned}
$$


### For one passenger

$$
\begin{aligned}
\max \quad \sum_{i} x_iw_i \\
s.t.     \sum_{i} c_{i}x_{i} &\leq C' & \\
         \sum_{i} x_{i} &\leq p  & \\
         x_{i}  &\leq  1 & i\in [n]\\
         x_{i}  &\geq  0 & i\in [n]\\
\end{aligned}
$$

### Dual

$$
\begin{aligned}
\min \quad  C'y_1+py_2+\sum_{i=1}^nz_i\\
s.t.   \qquad c_iy_1+y_2+z_i & \geq  w_i &i \in [n]\\
    z_i, y_1, y_2 & \geq 0 & i \in [n]\\
\end{aligned}
$$

### LP 最优解中: 至多存在一个i 满足 $x_{ij}$ 存在非整数解{#1}

p=1 时的证明方法大概不行了.

$$
\begin{aligned}
    \sum_{ij}c_{ij}x_{ij}+Y&=B\\
     x_{i1}+\ldots+ x_{im} +x_{i,m+1}+\ldots + x_{i,m+p}&=p  & i\in[n]\\
    x_{ij}+a_{ij}&=1 &i\in[n],j\in[m+p]\\
    x,a,Y & \geq 0 
\end{aligned}
$$

对于 $x_{i1}+\ldots+ x_{im} +x_{i,m+1}+\ldots + x_{i,m+p}=p$ 每行至少有一个x不为0，
但是基本解应该有 $n(m+p)+n+1$ 个元素. 

对于每组物品，新的线性规划变量$x_{ij}$表示是否取$i$组物品的某一个组合($j\in [m^p]$)

这样一来就变成了p=1的情况.
(coauthor中许超老师的方法)
### p=1 时的预处理

这个显然不满足了，这就导致在计算breakpoint的时候不能拿相邻物品对应的直线直接求，也不能确定哪些直线在 up envelope 上。

### 凸包？

每组有n个物品，在其中取p个物品 ==> 二维平面有n个点，找其中任意p个点的和（两维坐标分别相加）形成的$\binom{n}{p}$个点
形成的凸包

[https://arxiv.org/pdf/1111.5340.pdf](https://arxiv.org/pdf/1111.5340.pdf)有关于均匀分布的点凸包上点的数量的研究

n个点在二维平面上均匀分布，凸包上的点只有$O(\log n)$个