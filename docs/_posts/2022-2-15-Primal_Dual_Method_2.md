---
layout: post
title:  "Primal Dual Method 2"
date:   2022-2-15 11:30:08 +0800
categories: alg
---

大概算是整理一下

assignment problem (minimum-weight perfect matching problem in bipartite graphs)

integer program:

![example_IP]({{url}}/assets/image/primal-dual/example_IP.png)

可以证明IP的线性松弛最优解是整数解。

dual of LP relaxation:

![example_dual_LP]({{url}}/assets/image/primal-dual/example_dual_LP.png)

primal-dual 要从一个对偶问题的可行解开始。取$u=v=0$

restricted primal:

![rp]({{url}}/assets/image/primal-dual/rp.png)

$I=\emptyset,J=\\{(a,b)\in E: u_a+v_b=c_{ab}\\}$

实际上可以证明restricted primal的基本可行解中的变量只能取0，1.
发现这是在求$G=(A,B,J)$上的最大匹配。

那么对于任意一个对偶问题的解，我们都能写出restricted primal问题，而restriced primal问题就是求一个二分图的最大匹配。
如果找到的最大匹配是一个完美匹配，可以发现restricted primal的目标函数值为0，说明找到了最优解，否则，继续写出restricted primal的对偶：

![dualofrp]({{url}}/assets/image/primal-dual/dualofrp.png)

二分图上Maximum matching 的对偶实际上是 vertex cover，
在这里u取-1表示选择了这个点，取1表示没选这个点，max u恰好是在求最小顶点覆盖

上面对于assignment problem primal-dual方法给出了一个精确算法，但是过程中有两个条件难以满足：
1. 问题是由整数规划描述的，线性松弛的最优解恰好是整数解
2. 找到restricted primal problem之后直接发现了图上的对应问题，有已知的算法来解决这个问题

满足不了这两个条件，得到的就是一个近似算法。

整数规划一般不满足强对偶定理，因此对偶问题的最优解一般与原问题最优解不相等；restricted primal problem不一定容易解决，那么就放松互补松弛条件
>the central modification made to the primal-dual method is to enforce the primal complementary slackness conditions and relax the dual conditions

primal-dual方法是对偶问题的可行解出发，看是否有能同时满足互补松弛条件和原问题约束的原问题的解，现在由于条件2无法满足， restricted primal 及其对偶难解，
我们就只能根据 primal complementary slackness conditions ，从对偶解来计算原问题的解，而对于dual conditions，原问题约束有可能未被满足，有可能取等，有可能不取等，对于对偶问题变量是否取0未必有影响，因此只根据未被满足的原问题约束来想办法更新对偶解。

我觉得他的思路大概是这样的。