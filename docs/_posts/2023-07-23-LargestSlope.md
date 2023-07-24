---
layout: post
title:  "finding the largest slope of segments connecting two sets of points on 2D plane"
date:   2023-07-23 0:00:00 +0800
categories: alg
---

>好长的标题啊

二维平面上有一些点 $E$, 分成两组 $B$ 和 $E\backslash B$. $\forall b\in B, \forall c\in E\backslash B$, find the largest slope of the segment $bc$. 

这个问题是在解决 parametric matroid optimization 的时候遇到的, 但是用这种方法得到的东西甚至没有最显然的办法快.

### parametric matroid optimization

问题大概是这样

$$
\begin{aligned}
\max_x \quad & (v-\lambda c)\cdot x\\
s.t. \quad 
x(S)&\leq r(S)  &\forall S\subseteq E\\
x(E)&=p\\
x&\geq 0
\end{aligned}
$$

有一个matroid $M$, ground set 是 $E$, rank 是 $p$, 每个element的权重是一个线性函数, 随着$\lambda$变化, 我们想要找到随着$\lambda$变化的这个线性规划的最优解的函数$\tau$. 

首先这个$\tau$一定是 piecewise linear convex function. 这是因为所有的约束都是线性的(虽然可能数量是ground set大小的指数函数), $x$ 的范围是一个凸多面体, 凸多面体顶点的$x$全部拿出来, 我们会得到一个候选的直线的集合, 然后实际上我们在这些直线里面取max, 找的是平面上直线的[upper envelope](https://en.wikipedia.org/wiki/Kinetic_convex_hull#:~:text=The%20upper%20envelope%20of%20a%20set%20of%20static%20lines%20can,the%20two%20resulting%20upper%20envelopes.), $\tau$一定是piecewise linear convex function.

然后关于$\tau$上面的 breakpoints 数量还有一个很好的性质. 数量是 $\Theta(mp^{1/3})$, 这个是[Improved Bounds for Planar k -Sets and Related Problems](https://link.springer.com/article/10.1007/PL00009354) 文章里证明的. ($m=\|E\|$)

然后问题是怎么找到$\tau$. 

#### uniform matroid

uniform matroid 就是所有大小小于等于 $p$ 的 $E$ 的子集都是独立的. uniform matroid的特殊情况下, 这个问题就变成一个叫做 [k-level](https://en.wikipedia.org/wiki/K-set_(geometry)) 的问题. 从上面的规划也能看出来, 相当于对于所有$\lambda$我们都找纵坐标最大的 $p$ 条直线. 这个有超快的算法. [Chan, T. M. (1999). "Remarks on k-level algorithms in the plane". Archived from the original on 2010-11-04.](https://web.archive.org/web/20101104182509/http://www.cs.uwaterloo.ca/~tmchan/lev2d_7_7_99.ps.gz) 然后我写了文章里一个比较慢的用 kinetic queue的版本 [github](https://github.com/congyu711/IncentiveAllocation/tree/main/k-level) 写的又乱又烂 没有任何说明. 但是当时写的时候并不觉得写的很烂, 能用就行...

#### graphic matroid

graphic matroid 的 ground set 是一个无向图里面的边集, ground set的任何子集,只要在图里不成环就是独立的. 他的基也就是任何生成树.

graphic matroid 情况下的$\tau$ 也有超快的算法. [https://link.springer.com/article/10.1007/PL00009396](https://link.springer.com/article/10.1007/PL00009396)

关于这两种特殊的matroid情况下 $\tau$ 上面的 breakpoints 数量有这样一个表.

| "beakpoints"      | lowerbound | upperbound | ref |
| ----------- | ----------- | ---------- | ----- | 
| k-level      |    $\Omega(nc^{\sqrt{\log k}})$ for some constant c    | $O(n k^{1/3})$ | [https://en.wikipedia.org/wiki/K-set_(geometry)](https://en.wikipedia.org/wiki/K-set_(geometry)) |
| spanning tree | $\Omega(m\alpha(n))$ graph with m edges and n vertices | the same as general matroid | [https://link.springer.com/article/10.1007/PL00009396](https://link.springer.com/article/10.1007/PL00009396) |
| general matroid(number of different minimum weight bases) | $\Omega(nr^{1/3})$  | $O(nr^{1/3})$ | lb: [https://link.springer.com/article/10.1007/PL00009396](https://link.springer.com/article/10.1007/PL00009396) ub: [https://link.springer.com/article/10.1007/PL00009354](https://link.springer.com/article/10.1007/PL00009354) |

想要找到一个办法快速把一般的matroid的$\tau$计算出来.

### geometric view

我最开始想, 我可以想办法按照$\lambda$递增的顺序把 $\tau$ 上面所有breakpoints都找到. 假设我们现在的 $\lambda=\lambda_1$, 当前在$\lambda_1$我们也知道选择的optimum base是什么. 我们要找base里的直线和base外面的直线的大于$\lambda_1$的最小交点坐标. 也就是

$$
\begin{aligned}
\min \quad  (b-d)/(c-a)\\
s.t. \quad 
\{y=a\lambda+b\}&\in B  \\
\{y=c\lambda+d\}&\in E\backslash B\\
(b-d)/(c-a)&\geq \lambda_1
\end{aligned}
$$

我们找到下一个 $\lambda$ 之后要看 matroid base 是否改变. 上面这个优化问题只是找到base 和 不是base的直线的下一个交点, 但是他们交换了未必optimum base就会变.

下面有两个问题:
1. 快速找到下一个 $\lambda$
2. 我们找的 $\lambda$ 有多少个?

其实到这里已经该意识到这个思路可能有问题了.

但是我反而觉得我这个办法超过了上面 kinetic spanning tree(graphic matroid)的文章...

### find the next breakpoint

相当于在二维平面里有两组点, 找点之间连线斜率最接近$\lambda$的点对. 

通过改变$b$可以把约束改成$(b_i-b_j)/(a_i-a_j)\leq 0$, 再把所有点先对$y=0$做对称在对$y=x$ 做对称, 就变成了无约束的$\max (b'_i-b'_j)/(a'_i-a'_j)$

如果只有一组点, 求连线斜率最大可以$O(n\log n)$的时间内算出, 因为有一个性质, 斜率最大的线段一定是在按a的大小排序之后相邻的两个点取到, 但是如果是两组点就没有了这个性质...

#### trivial version

这是问组里打acm同学得知的. cdq分治. $O(n\log^2 n)$.

我问了打acm的同学, 可以使用叫做[cdq分治](https://oi-wiki.org/misc/cdq-divide/#%E8%A7%A3%E5%86%B3%E5%92%8C%E7%82%B9%E5%AF%B9%E6%9C%89%E5%85%B3%E7%9A%84%E9%97%AE%E9%A2%98)的技巧.

把所有二维点（不管是哪一组）按照a的大小排序, 然后开始分治。 把所有点分成左右两部分数量相等的点，两个组分别记为A和B，左侧的属于A组的点记为$A_l$, 右侧A组记为$A_r$, B组也同样记为$B_l,B_r$, 斜率最大的线段可能出现在3个地方：

1. $A_l,B_l$之间的连线
2. $A_r,B_r$之间的连线
3. $A_l,B_r$ or $A_r,B_l$ 之间的连线

前两种情况我们递归解决。第三种情况单独处理，发现$A_l,B_r$ 或者 $A_r,B_l$对应的点集都完全被 $x=mid$这个直线给分开了，$A_l,B_r$之间的连线斜率最大的点一定只在$A_l$的右半凸包和$B_r$的左半凸包取, 因为点已经排过序了，求凸包$O(n)$， 然后问题变成怎么求$A_l$的右半凸包和$B_r$的左半凸包上的点连线的最大斜率. 这个问题实际上又是在求[bitangent](https://en.wikipedia.org/wiki/Bitangent).
可以做到 $O(\log m)$.

$T(n)=2T(n/2)+O(n)=O(n\log n)$

#### complicated version...

观察上面cdq分治的过程, 发现我们总是在维护左半\右半凸包, 然而 [*M. H. Overmars and J. Van Leeuwen, “Maintenance of configurations in the plane,” Journal of Computer and System Sciences, vol. 23, no. 2, pp. 166–204, Oct. 1981, doi: 10.1016/0022-0000(81)90012-X.*](https://www.sciencedirect.com/science/article/pii/002200008190012X) 当中 fully dynamic convex hull 的数据结构几乎是在做一样的事. 他的数据结构是一个平衡树套平衡树. 我们直接把他的数据结构拿过来, 把base中的点和不在base的点分开, 外层平衡树每个节点维护八个半凸包, 每次delete或者insert操作之后都要维护我们要求的slope, 就可以在和动态凸包完全相同的时间内找出我们想要的下一个交点. 也就是$O(\log^2 m)$. (我觉得应该没问题...)

[2D fully dynamic convex hull notes-hackmd.io](https://hackmd.io/@r1NLzG2QQuKF14FgHKxugg/SJgOi8CSh)

然后我注意到 kinetic spanning tree 的文章每个breakpoint大概要花 $O(n^{2/3}\log^{c}n)$, 那这个动态凸包的方法岂不是超快. 然而没有那么好的事...

### number of "breakpoint"s


然而实际上我找的这个 "breakpoints" 的数量不是 $O(k)=O(mp^{1/3})$, 而是 $O(m^2)$. 上文说的[Improved Bounds for Planar k -Sets and Related Problems](https://link.springer.com/article/10.1007/PL00009354)文章里的证明用到叫做 Polygons in Arrangements 的问题(实际上是表格里general matroid lowerbound那个文章先提出来的) 看起来和我找 breakpoint的过程非常相似. 我以为我的 "breakpoints" 的数量就是 $O(k)$.

可以构造出一定会遇到$m^2/4$个 "breakpoints" 的matroid和直线. 比如

![img](https://s2.loli.net/2023/07/14/fl4snAitbNhKOd9.jpg)

水平的直线都是最开始的optimum base, 然而每一条斜线都是loop, 都不能存在于任何一个base中, 因此我们找了$m^2/4$个交点, 而真正的breakpoint个数是0个.


然后我又意识到, 花了这么久找到的极其复杂的动态凸包做法竟然比最显然的做法还要慢. 动态凸包的办法(假设他完全正确)计算整个 $\tau$ 需要 $O(m^2\log^2 m+ kT')$, $T'$ 是计算一次rank的时间; 而最显然的办法, 直接提前算出所有直线的交点然后排序, 竟然只需要 $O(m^2\log m+ kT')$...

然而如果你需要一个数据结构来维护平面上两组点之间连线的最大斜率, 并且需要支持对两个点集的插入删除操作, 动态凸包的办法或许是最快的?