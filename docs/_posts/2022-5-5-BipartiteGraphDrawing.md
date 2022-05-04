---
layout: post
title:  "Bipartite Graph Drawing Problem"
date:   2022-5-4 14:30:08 +0800
categories: alg
---

最近遇到一个问题：给出二分图$G(V_1,V_2,E)$，$E\subset V_1 \times V_2$求两侧顶点排序使得边的交叉数量最少。


[Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf](https://learn.fmi.uni-sofia.bg/pluginfile.php/160153/mod_resource/content/4/Crossing-Number-Is-NP-Complete_Garey_Johnson.pdf)
证明了是NP-Hard，所以考虑一些启发式方法。

首先考虑固定其中一边的情况，假设$V_2$固定，而$V_1$可动，求$V_1$的一个排列使得边的交叉最少。

这种情况下下我们可以被固定的$V_2$编号，然后对于可动的$V_1$的某个排列，按顺序写出$V_1$中每个点连接到的$V_2$中的点的编号，
形成了一个序列，交叉点的数量相当于序列当中的逆序对数

![ex1]({{url}}/assets/image/../../../../assets/image/bipartitedrawingprob.png)

写出的序列为V_1: ```1 2 1 4 1 3```

逆序对有四个，对应着图中的四个交点

### 1

然后我提出一个问题，加入一种对$V_1$的这个排列的修改操作，可以将某个点插入到某个位置，如何维护逆序对个数?

经过分析，实际上需要维护的是在插入操作下的区间大于某个值的数字的个数，和同学讨论了一下本以为用树状数组套权值线段树可以解决，结果发现插入会改变树状数组每个节点分配的序列区间，并不能行，主席树（可持久化权值线段树）可以维护区间大于某个值的数的个数，但是不知道怎么做这种点的插入的维护，这个问题仍然没有解决


### 2

[有人](http://scis.scichina.com/cn/2021/SSI-2019-0122.pdf)用local search的方法解决这个问题，先固定一侧$V_1$，对另一侧$V_2$用dp求在某种限制下的最优解，然后按照最优解修改$V_2$的顺序，然后固定$V_2$，用同样的方法修改$V_1$

限制是插入的区间（被移动的点到插入的目的地）不能重叠

这个限制使得用一个$\mathcal{O}(n^2)$的dp就能算出一侧的最优解，因此大概是损失了很多信息，可能会比最优解差很多

我目前想把限制变宽，但是仍保持一侧的OPT在多项式时间内可解

想到再回来写