---
layout: post
title:  "Bipartite Graph Drawing Problem"
date:   2022-9-11 12:30:08 +0800
categories: alg combinatorics
---

最近遇到一个问题：给出二分图$G(V_1,V_2,E)$，$E\subset V_1 \times V_2$求两侧顶点排序使得边的交叉数量最少。


[Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf](https://learn.fmi.uni-sofia.bg/pluginfile.php/160153/mod_resource/content/4/Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf)
证明了是NP-Hard，所以考虑一些启发式方法。

首先考虑固定其中一边的情况，假设$V_2$固定，而$V_1$可动，求$V_1$的一个排列使得边的交叉最少。

这种情况下下我们可以被固定的$V_2$编号，然后对于可动的$V_1$的某个排列，按顺序写出$V_1$中每个点连接到的$V_2$中的点的编号，
形成了一个序列，交叉点的数量相当于序列当中的逆序对数

![ex1]({{url}}/assets/image/../../../../assets/image/bipartitedrawingprob.png)

写出的序列为V_1: ```[1 2] [1 4] [1 3]```

逆序对有四个，对应着图中的四个交点

### 1

然后我提出一个问题，加入一种对$V_1$的这个排列的修改操作，可以将某个点插入到某个位置，如何维护逆序对个数?

经过分析，实际上需要维护的是在插入操作下的区间大于某个值的数字的个数，和同学讨论了一下本以为用树状数组套权值线段树可以解决，结果发现插入会改变树状数组每个节点分配的序列区间，并不能行，主席树（可持久化权值线段树）也不知道怎么做这种点的插入的维护，这个问题仍然没有解决


### 2 (5/17)

[dpls](http://scis.scichina.com/cn/2021/SSI-2019-0122.pdf)用local search，先固定一侧$V_1$，对另一侧$V_2$用dp求在限制下的最优解，然后按照最优解修改$V_2$的顺序，然后固定$V_2$，用同样的方法修改$V_1$

限制是插入的区间（被移动的点到插入的目的地）不能重叠

这个限制使得用一个$\mathcal{O}(n^2)$的dp就能算出一侧的最优解，因此大概是损失了很多信息，~~可能会比最优解差很多~~
只考虑单侧的情况，并没有比$O(n^2*2^n)$的dp最优解差多少。主要问题在于local search的方式。

我目前想:

1. 读一下[Convergence of Local Search](https://www.sstich.ch/files/Stich12-trash12.pdf)如果可以收敛到最优解当然很好(保持一次操作仍然是多项式时间内但是操作次数是指数的) -- 根本想不出
2. 把限制变宽，但是仍保持一侧的OPT在多项式时间内可解，不容易掉到局部最优解当中去 -- 还在想（x）
3. 观察到样例的平均度数都很小

### 3 one side local search

固定一侧找另一侧最优解的local search不能找到最优解
![ex2]({{url}}/assets/image/../../../../assets/image/bipartitedrawing_counterexample.png)

上图中固定任意一侧，另外一侧都已经达到最优解，但是下图说明更改中间两个点的位置可以让交叉只有两个。

类似的构造应该可以说明这种 local search 的近似比没有保证

#### (5/18) 固定一侧的最优解是否是NP-Hard

~~另外我觉得固定一侧找另一侧最优解应该也是 NP-Hard ~~

~~大概类似[Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf](https://learn.fmi.uni-sofia.bg/pluginfile.php/160153/mod_resource/content/4/Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf)证明 bipartite crossing的方法，从 linear arrangement 归约，考虑一个linear arrangement问题，构造二分图，用类似栅栏的形状来让一侧的点无法移动，然后把linear arrangement 的点插入到另一侧，连边。~~

![ex2]({{url}}/../../assets/image/bipartitedrawing_ex2.JPG) ~~没有编号的点形成栅栏形状，并且连$\|E\|^2$条边，来确保下半部分点无法改变顺序(移动之后会产生至少$\|E\|^4$条边)，有编号的点对应 linear arrangement的点，原来的限制是$k$，新的二分图上限制是$(2k-\|E\|)*\|E\|+\binom{\|E\|}{2}$，完全类似bipartite crossing的证明方法。(我觉得大概没问题)~~

错误在于不是完全等价的，linear arrangement 在约束为$k$下有解，在构造的二分图上约束 $(2k-\|E\|)*\|E\|+\binom{\|E\|}{2}$ 一定有解，

但是反之并不成立，因为标号的一行中上面的点可以在栅栏形状的不可动的点之间移动，导致对于linear arrangement 问题中的每条边(长度为$l$)，新的约束会小于 $(2l-1)*\|E\|$.

(5/19)

用上面那种逆序对的思路来看，相当于给定一个由集合构成的序列，排列集合的顺序，使得每个集合内的元素顺序排列直接展开集合之后的逆序对最少，比如：

```{1,2,3},{1,3,4}``` 展开成```1,2,3,1,3,4```，逆序对一共2个

```{1,3,4},{1,2,3}``` 展开成```1,3,4,1,2,3```，逆序对一共5个

<!-- 但是跟同学讨论了一下，同学告诉我这是一个排序问题。。任意两个相邻的集合可以判断出前后顺序，这大概比上面论文的dp要好。。。 -->

任意两个相邻的集合都能判断出位置顺序，但是这并不意味着可以通过任意两个相邻集合的前后位置关系来确定出逆序对最少的排序。

没有想出新办法，把dpls代码略微优化，用cpp重写，~~这个问题大概结束了。~~

(又过了一段时间)

今日想到了一种把两侧点合起来连成新的图，比如$\sqrt{n}$个点一组合成一个大点，然后在做一次原来的local search 来进行扰乱，通过合适的分组可能能把两边必须同时移动的情况解决一部分，发现效果并不好，需要同时移动两侧的点的情况目前还是在靠局部搜索走不动之后的随机扰乱解决，如果想获得比[dpls](http://scis.scichina.com/cn/2021/SSI-2019-0122.pdf)更好的结果，我觉得一定需要想出一点解决两侧点必须同时移动的情况的办法


## 9/10

[Layered graph drawing](https://en.wikipedia.org/wiki/Layered_graph_drawing)
>However, graphs often contain cycles, minimizing the number of inconsistently-oriented edges is NP-hard, and minimizing the number of crossings is also NP-hard

[Upward planar drawing](https://en.wikipedia.org/wiki/Upward_planar_drawing)

#### 固定一侧的最优解是否是NP-Hard

得知了固定一侧的这个问题叫做 OSCM(one sided crossing minimization)，有文章证明了它确实是NP-Hard
[One Sided Crossing Minimization Is NP-Hard for Sparse Graphs](https://link.springer.com/content/pdf/10.1007/3-540-45848-4_10.pdf)

[dense graph](https://link.springer.com/article/10.1007/BF01187020)

对于两侧都可以移动的情况的证明在这里[Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf](https://learn.fmi.uni-sofia.bg/pluginfile.php/160153/mod_resource/content/4/Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf)

大概看了一下sparse graph那个证明，感觉难度远高于[Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf](https://learn.fmi.uni-sofia.bg/pluginfile.php/160153/mod_resource/content/4/Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf)，目前有别的事做，对这个证明没有那么感兴趣了，有时间再看

#### FTP alg for OSCM

[Fixed Parameter Algorithms for one-sided crossing minimization Revisited](https://www.sciencedirect.com/science/article/pii/S1570866707000469)

文章 section3 给出了一个很有意思的看法和问题：

首先，讨论的是OSCM问题，V1 排序固定，讨论的点都在V2中，如果有两个点$a,b\in V_2$，并且a在V1中的邻居最靠右的那个仍然在b最靠左的邻居的左边，这说明如果我在V2中把a放在b的左边一定不会出现交叉边，而b放在a的左边会出现交叉边。这可以确定一个V2中的点的偏序。

k-WCO(k weighted completion of an ordering):
a digraph P(V,A), a cost function $c: A(D([U(P)]^c))\rightarrow \mathbb{N^+}$ （这个得解释以下，c的定义域是：P这个有向图首先忽略边的方向，变成一个无向图，然后取这个无向图的补，然后每个无向边都改成双向的有向边，最后这个图的边集就是c的定义域）,a para k.

Question: is there a edge set $A'\subset A(D([U(P)]^c))$ s.t. the transitive closure of $A(P) \cup A'$ is a linear order and $\sum_{a\in TC(A'\cup A(P))} c(a) \leq k$

显然这个函数c如果把P中已经存在的边都设置为0，P中没有的边(a,b)设置成a放在b左边的交叉边数，这个问题就是k-OSCM，不过既然OSCM是NP-Hard，这个WCO就是NP-Complete

