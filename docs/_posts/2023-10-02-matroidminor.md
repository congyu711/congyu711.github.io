---
layout: post
title:  "Matroid Minor"
date:   2023-10-02 0:00:00 +0800
categories: matroid
---

## interesting facts


$\mathcal M=(E,\mathcal F),n=\|E\|$, rank $r$

### number of optimal bases

unifrom: $\binom{n}{r}$

graphic: $n^{n-2}$ (Cayley's theorem, # spanning trees in complete graphs)

### number of cut(or circuit?)

unifrom: $\binom{n}{r+1}$

graphic: $n(n-1)/2$ (Karger's random global min cut algorithm)

## minor

> ![Fun2022 all your bases are belong to us]({{url}}/assets/image/matroidminor/fun2022minordef.jpg)
> Fun2022 all your bases are belong to us


### the maximal independent set $T$ does not affect the independent sets after contraction

suppose set $X$ has two maximal independent sets $T_1,T_2$ with the same size and there exists $I\subseteq E-X : I\cup T_1 \in \mathcal{I}, I\cup T_2\notin \mathcal{I}$. matroid rank function is submodular. $r(I\cup T_1)=r(I)+r(T_2)\geq r(I\cup)< r(I\cup T_2)$ which means $\|T_1\|\not ={\|T_2\|}$, a contradiction.


### why dual?

这里 deletion 是直接在 ground set 里面删掉元素, contraction 就像 graph minor 一样, 把 element(边) 收缩. 区别是把 contract 掉的集合 $X$ 的一个最大的 independent set 加入新的 matroid 的某个 independent set 里面的话, 得到的集合也必须是独立的.

用中文描述一下为啥 contraction 相当于在 dual matroid 上对同一个集合做 deletion 然后再取 dual. 原来的 matroid 是 $M$, $M'=M/X$. 对于任意一个 $M$ 的 base $T$, 都有一个 cobase $T^* $. $T^* - X$ 得到的 matroid 的 base 是什么? 是 $\|T^* \cap X\|$ 最小的 $T^* $. 也就是对应的 $T\cap X$ 最大. 这样一来 contraction 之后的对偶得到的 matroid 的 base 就是 $\min \|T- X\|$ 再删掉所有 $X$ 中的元素. 

