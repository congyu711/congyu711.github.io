---
layout: post
title:  "Primal Dual Method 5"
date:   2022-3-15 12:30:08 +0800
categories: alg
---

从寒假开始也算是看了两本书关于 primal-dual 方法的章节。

大多数问题的建模都是整数规划，primal-dual 用的是线性松弛的对偶，找到的可行解也不会比线性松弛的最优解更好，
只有线性松弛的解和整数规划相比比较接近才有很好的近似比，如果像是 feedback vertex set 用hitting set建模的整数规划，这种线性松弛最优解近似比是logn级别，
primal-dual近似比最好也只能做到logn级别了，但是最短路的hitting set建模线性松弛最优解和整数规划最优解相等，primal-dual甚至能得出精确算法（dijkstra），
同样的问题，不同的整数规划建模方法，就有不同的线性松弛，就有不同的近似比。

为什么不直接解线性松弛而要用primal-dual？我觉得主要原因是原来的整数规划约束个数可能是指数级别，解线性规划会解不出，
但是primal-dual有时候（Violation Oracle能很快给出还没满足的约束）需要维护的对偶变量数量只是多项式级别的。

要用primal-dual方法解决一个问题，需要考虑
- 用什么方法来写出整数规划，是否能找出一个能用的Violation Oracle，线性松弛是否有一个好的近似比
- 根据具体问题是否有好用的design rules，按照什么方法和顺序找violated set等，复杂度又如何
- 怎么分析近似比，能不能做到线性松弛的近似比一样的级别

我发现大概这个整数规划约束有指数多个，一般整数规划和线性松弛差的不大。



对比The primal-dual method for approximation algorithms and its application to network design problems, chapter 4这个章节和The Design of Approximation Algorithms chapter 7，
明显后者更加简单易懂，使用了很多例子，但是我觉得前者要系统很多，而且更加符合逻辑和直觉。首先介绍什么是classic primal-dual method，从这个方法出发有了设计近似算法的基本框架，如何从整数规划的线性松弛和LP的对偶
找到原问题（IP）的可行解，以及后面对用hitting set建模抽象出来的函数$f(S)$的讨论，而后者通过众多例子介绍了primal-dual，比较易懂，但是关键部分的近似比证明受例子的影响，没有一般化，感觉很难想到。

根据之前看的两本书，做了ppt.


[primal-dual-2]({{url}}/../../assets/pdf/primal-dual_method.pdf)