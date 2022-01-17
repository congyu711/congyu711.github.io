---
layout: post
title:  "Primal Dual Method"
date:   2022-1-16 11:30:08 +0800
categories: alg
---

## Primal Dual Method

Goemans M X, Williamson D P. The primal-dual method for approximation algorithms and its application to network design problems, chapter 4

考虑一个线性规划问题

$$
\begin{align*}
    \min \quad &c^Tx\\
    s.t. \quad Ax&\geq b\\
     x&\geq 0
\end{align*}
$$

他的对偶：

$$
\begin{align*}
    \max \quad &b^Ty\\
    s.t. \quad A^Ty&\leq c\\
    y&\geq 0
\end{align*}
$$

根据原问题的kkt条件中的互补松弛条件（叫做对偶问题的互补松弛条件）：

$$
y^T(Ax-b)=0
$$

同样根据对偶问题的kkt的互补松弛条件（叫做原问题的互补松弛条件），有

$$
x^T(A^Ty-c)=0
$$

primal dual方法是一种内点法，首先有一个对偶问题的可行解$y$，如果能找到一个原问题的可行解$x$满足互补松弛条件，
那么$x$和$y$就是原问题和对偶问题的最优解，但是$y$如果并非最优解，就找不到可行解$x$满足互补松弛条件。于是希望
引入一个新问题，最小化原问题可行解$x$对互补松弛条件的违反。首先引入下标集$I= \\{ i|y_i=0 \\} ,J= \\{j |A^jy=c \\}$，其中
$A_i$表示$A$的第$i$行，$A^j$表示$A$的第$j$列（写成行向量），下面是restricted primal problems:

$$
\begin{align*}
    \min \quad &\sum_{i\notin I} s_i+\sum_{j\notin J} x_j\\
    s.t. \quad Ax_i&\geq b_i \qquad i\in I\\
    \quad Ax_i-s_i&= b_i \qquad i\notin I\\
    x&\geq 0\\
    s&\geq 0
\end{align*}
$$

其中$s_i(i\notin I)$是对对偶问题的互补松弛条件的惩罚项，$y_i\not ={0}\rightarrow A_ix=b_i$;
$x_j(j\notin J)$是对原问题互补松弛条件的惩罚，$A^jy\not ={c_j}\rightarrow x_j=0$;

如果restricted primal problem的最优解是0，那么说明找到了原问题的一个完全满足互补松弛条件的可行解$x$，$x$和$y$应该是原问题和对偶问题的最优解；
如果最优解不是0，说明$y$并非最优解，需要改进（在例子中需要变大）。我们首先考虑restricted primal problem的对偶：

$$
\begin{align*}
    \max \quad &b^Ty'\\
    s.t. \quad A^jy'&\leq 0\qquad j\in J\\
    A^jy'&\leq 1 \qquad j\notin J\\
    y'_i&\geq -1 \qquad i\notin I\\
    y'_i&\geq 0  \qquad i\in I
\end{align*}

$$

***

这里得说说拉格朗日函数，kkt，线性规划对偶的关系，考虑一个线性规划问题:

$$
\begin{align*}
    \min \quad &c^Tx\\
    s.t. \quad Ax&\geq b\\
     x&\geq 0
\end{align*}
$$

把他当成一个约束优化问题，写出拉格朗日函数：

$$
L(x,\lambda)=c^Tx-\lambda^T(Ax-b)
$$

$Ax-b\geq 0$，所以有$c^Tx\geq L(x,\lambda)$，然后考虑拉格朗日函数的下确界：

$$
L(x,\lambda)=\lambda^Tb-(c^T-\lambda^TA)x
$$

如果$c^T-\lambda^TA> 0$，下界是$-\inf$，如果$c^T-\lambda^TA\leq 0$，下确界显然是$\lambda^Tb$.

第二种情况就是线性规划的对偶问题了。$c^Tx\geq L(x,\lambda) \geq \lambda^Tb$

***
继续考虑restricted primal problem的对偶，首先由于restricted primal problem的最优解是大于0的，
上面那个对偶问题一定存在一个解$y'>0$（线性规划对偶都满足强对偶定理），利用原问题的对偶的可行解$y$
和上面的对偶问题的可行解$y'$构造一个新的对偶问题的可行解$y'' =y+\epsilon y'$，并且显然$b^Ty'' >b^Ty$，（$\epsilon >0$）

这样可以得到一个更接近最优解的对偶问题可行解。

为了保证$y'' $是一个对偶问题的可行解，要满足两个条件
1. $y'' \geq 0$. 这需要$\epsilon \leq \min_{y'_i<0}\frac{-y_i}{y'_i}$
2. $A^Ty'' \leq c$，这需要$\epsilon \leq \min_{A^jy'>0}\frac{c_j-A^jy}{A^jy'}$

得到$y'' $之后重复上面的步骤，每次都能得到一个更接近最优解的对偶问题可行解，是一个内点法。