---
layout: post
title:  "Primal Dual Method 3"
date:   2022-2-15 12:30:08 +0800
categories: alg
---

[primal-dual-1]({{url}}alg/2022/01/16/Primal_Dual_Method_1.html)\\
[primal-dual-2]({{url}}/alg/2022/02/15/Primal_Dual_Method_2.html)


### design rules

(不是记录design rules，是想搞清楚design rules是怎么来的)

原问题一般都能写成这样的整数规划：

$$
\begin{align*}
    \min \quad \sum_{e\in E}&c_ex_e\\
    s.t. \quad \sum_{e\in \delta(S)}x_e&\geq f(S)\\
    x_e&\in \{0,1\}
\end{align*}
$$

$\delta(S)$是把$V$分成$S,V-S$的一个割，$f(S):2^{\|V\|}\rightarrow \mathbf{N}$

书中是以hitting set problem 为例讲解的design rules，如果$f(S):2^{\|V\|}\rightarrow \\{0,1\\}$，那么这就是hitting set problem了

***
hitting set problem:

Hitting set is an equivalent reformulation of Set Cover.（放到二分图上，分别是选左边和选右边）

NP-Complete.

Given subsets $T_1,\ldots,T_p$ of a ground set $E$ and given a nonnegative cost $c_e$ for every element in $E$, find a minimum-cost subset $A$ s.t. $A\cap T_i\not ={\emptyset}$ for $i=1,\ldots,p$.

***

hitting set problem 既然是 NP-Complete 问题，很多常见问题都能建立hitting set的模型，上面的IP是把ground set当成图的所有割，需要hit的subsets当成
$f(S)=1$的那些割$\delta(S)$。

方便起见定义一些符号：
* $A=\\{e:x_e=1\\}$
* $y$: dual variable
* $T_1,\ldots,T_p$ sets to be hit

根据[primal-dual-2]({{url}}/alg/2022/02/15/Primal_Dual_Method_2.html)中的force primal complementary 
slackness conditions relax dual CSCs 的方法，

1. $y\rightarrow 0$
2. $A\rightarrow \emptyset$
3. While $\exists k$ : $A\cap T_k=\emptyset$
4. $\quad$Increase $y_k$ until $\exists e\in T_k : \sum_{i:e\in T_i} y_i=c_e$
5. $\quad$ $A\rightarrow A\cup \\{e\\}$

首先在```3```中，如何选择$T_k$,如果存在多个$T_k$怎么选？

这要根据问题来确定。如果找到了多个$T_k$（称为一个violated set），我们可以同时增加对应的对偶变量$y_k$，直到$\exists e\notin A : \sum_{i:e\in T_i} y_i=c_e$.

由于整数规划的描述或者violation oracle可能没有严格返回$f(S)=1$的cut等问题（比如整数规划的约束是$\leq$，对于正权无向图的s-t最短路来说最短路一定和每个s-t cut的交集正好都是一条边，然而换成$\leq$会让结果
变成单源最短路），$A$中的边可能会选的过多，去掉某些边也可能是可行解。由此可以在最后加入一个删边的过程，按照某种顺序（比如加入边的顺序）测试删掉某条边后$A$是否
仍是可行解，如果是就删掉这条边。

![fig4.3]({{url}}/assets/image/primal-dual/fig4.3.jpg)

### evaluate the performance guarantee

$$
\begin{align}
    c(A)&=\sum_{e\in A} c_e\\
        &=\sum_{e\in A}\sum_{i:e\in T_i}y_i\\
        &=\sum_{i=1}^p|A\cap T_i|y_i
\end{align}
$$

(2th -> 3th line: exchanging the two summations)

令$\alpha=\max\\{\|A\cap T_i\|\\}$，根据$\sum y_i\leq OPT$容易得到

$$
c(A)\leq \alpha OPT
$$

这种计算近似比的方法必须要得到A之后才能算出$\alpha$，有些不便，引入一个 minimal augmentation set $B$.（$B$是$A$加上极少数量的边从而成为一个可行解，从$B$中去掉任何一条边都不是可行解）
由于design rules中最后删边的过程，$\|A_f\cap T_i\|\leq \|B\cap T_i\|$（$A_f$是最终结果），那么对于任意当前解$A$，我们找出对应的$B$，分析最大的$\|B\cap T_i\|$（记为$\beta$），
$c(A)\leq \beta OPT$。尽管minimal augmentation set听起来很复杂，这样可以在算法的执行过程中分析。

再考虑design rules中的violated set. 如果考虑每次取出的violated set $\mathcal{V}_j$，有

$$y_i=\sum_{j:T_i\in \mathcal{V}_j}\epsilon_j$$

那么

$$
\begin{align}
    \sum_{i=1}^p y_i&=\sum_{i=1}^p\sum_{j:T_i\in \mathcal{V}_j}\epsilon_j\\
                    &=\sum_{j=1}^{l} |\mathcal{V}_j|\epsilon_j
\end{align}
$$

$$
\begin{align}
    \sum_{i=1}^p|A_f\cap T_i|y_i&=\sum_{i=1}^p|A_f\cap T_i|\sum_{j:T_i\in \mathcal{V}_j}\epsilon_j\\
                                &=\sum_{j=1}^l(\sum_{T_i\in \mathcal{V}_j}|A_f\cap T_i|)\epsilon_j
\end{align}
$$

比较$\sum_{j=1}^l(\sum_{T_i\in \mathcal{V}_j}\|A_f\cap T_i\|)\epsilon_j$ 

和 $\sum_{j=1}^{l} \|\mathcal{V}_j\|\epsilon_j$，（不知为何这两个行内公式不能写到一行。。。）

if, for all $j=1,\ldots,l$,

$$
\sum_{i=1}^p |A_f\cap T_i|\leq \gamma|\mathcal{V}_j|
$$

then

$$
\begin{align}
    \sum_{i=1}^p|A_f\cap T_i|y_i&=\sum_{i=1}^p|A_f\cap T_i|\sum_{j:T_i\in \mathcal{V}_j}\epsilon_j\\
                                &=\sum_{j=1}^l(\sum_{T_i\in \mathcal{V}_j}|A_f\cap T_i|)\epsilon_j\\
                                &\leq \sum_{j=1}^l\gamma|\mathcal{V}_j|\epsilon_j\\
                                &=\gamma\sum_{i=1}^p y_i
\end{align}
$$

这还是需要先计算出$A_f$，现在结合上minimal augmentation set $B$，

$$
\sum_{i=1}^p |A_f\cap T_i|\leq \sum_{T_i\in \mathcal{V}(A)}|B\cap T_i| \leq \gamma|\mathcal{V}_j|
$$

同上，$\gamma$就是近似比。

再进一步考虑如果violation oracle返回的$\mathcal{V}$中有$f(S)=0$的cut，那么与上文类似，

$$
\sum_{T_i\in \mathcal{V}(A)}|B\cap T_i| \leq \gamma c
$$

$c$是$\mathcal{V}(A)$中$f(S)=1$的cut的数量。


design rules 并不是一定最优，只是对于某些问题这样做挺好，对于这些design rules 现在都有办法通过分析设计出的近似算法的过程来确定近似比了。