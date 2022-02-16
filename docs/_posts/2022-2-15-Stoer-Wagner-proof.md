---
layout: post
title:  "Stoer-Wagner算法cut-of-the-phase部分证明"
date:   2022-2-15 8:30:08 +0800
categories: alg
---

Stoer-Wagner 算法求无向图上的全局最小割。

任取两点```s```，```t```，考虑这个无向图上的最小割，有下面两种情况：
1. ```s```，```t```在割的同一侧
2. ```s```，```t```在割的两侧

对于第二种情况，普通的s-t最小割就是这个全局最小割。对于第一种情况，已知```s```，```t```
在割的同一侧，把他们看成一个点对结果没有影响，于是把```s```，```t```两个点缩成一个点，再任取两点，重复这个过程，直到图中只有一个点。

### cut-of-the-phase

求任意两点的s-t割如果使用网络流速度有些慢。 cut-of-the-phase 可以求出某两点之间的s-t最小割。既然Stoer-Wagner中的```s```，```t```是任取的，
自然可以选择cut-of-the-phase能求出最小割的那两点。

![cut-of-the-phase]({{url}}/assets/image/cut-of-the-phase.jpg){: width="500" }

图中 most tightly connected vertex 指的是$\underset{v}{\operatorname{arg max}} \sum_{u\in A} d(v,u)$    (if there is no edge e(v,u), d(v,u)=0)，
cut-of-the-phase指的是最后加入$A$的点$t$与倒数第二个加入$A$的点$s$的s-t割就是$\sum_{u\in A} d(t,u)$

下面最后加入$A$的点是$t$，倒数第二个加入的是$s$。令$X,Y\subset V$,$w(X,Y)=\sum_{x\in X}\sum_{y\in Y} d(x,y)$ (if there is no edge e(x,y), d(x,y)=0)，

要证明$s$与$t$之间的最小割是这样的：

![cut-of-the-phase-1]({{url}}/assets/image/cut-of-the-phase-1.svg)

而不是这样的：

![cut-of-the-phase-2]({{url}}/assets/image/cut-of-the-phase-2.svg)

$A_t$表示$t$之前加入$A$的所有点的集合,$X,Y$为上图中的第二种情况（任意一个不是第一种情况的割,t在Y中）。下面用归纳法证明$w(t,A_t)\leq w(X,Y)$

对于n=1,2，$w(t,A_t)=w(X,Y)$

假设n=k时满足$w(t,A_t)\leq w(X,Y)$，n=k+1，最后加入$A$的点是$t'$.

$$w(t',A_{t'})=w(t',t)+w(t',A_t)$$

由于$t'$加入$A$晚于$t$，有

$$w(t',A_t)\leq w(t,A_t)$$

带入，

$$w(t',A_{t'})=w(t',t)+w(t',A_t)\leq w(t',t)+w(t,A_t) \leq w(t',t)+w(X,Y)$$

显然有

$$w(t',t)+w(X,Y)+w(t',Y)=w(X',Y')$$

$$w(t',A_{t'})\leq w(X',Y')$$
