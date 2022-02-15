---
layout: post
title:  "对 Primal-Dual 的不深刻理解"
date:   2022-2-12 11:30:08 +0800
categories: alg
---

读了The primal-dual method for approximation algorithms and its application to network design problems。

开始读它是因为想找 densest subgraph 非线性规划，非网络流的组合算法，老师说学学 primal-dual 方法，说不定可以在图上找到有意义的对应问题。读完了4.5，不出意料 primal-dual 方法在这个问题上完全找不到对应问题，
原问题的解一定是整数，但是互补松弛条件不能保证对偶问题的解也是整数， restricted primal 的对偶根本找不到图上对应的问题，遭遇惨败。但是还是继续了解这种方法。

基本上在近似算法的书里都有这么一章来介绍 primal-dual method，而且讲的内容几乎都完全一样。很多时候用primal dual搞出来的近似解甚至不如显然的贪心或者线性松弛的解，我觉得他的优点是基本上什么问题都能用，
只要能用整数规划描述的问题都能找个 primal-dual 方法出来，而且如果约束中割的函数$f:2^N \rightarrow N$有些良好性质（比如是一个 0-1 downwards monotone function），近似比还有一个不错的保证。
并且一般来说写出的算法都只要维护联通分量、求生成树等就能解决，复杂度一般都比较低。

不过，读完之后好像什么都没学到……总体来说寒假是摸了，而且我觉得也不该直接来看这种设计方法，因为我对近似算法基本上是一无所知。