---
layout: post
title:  "Hall's Theorem"
date:   2023-03-02 0:00:00 +0800
categories: alg combinatorics
---

[bipartite perfect matching](https://cs.stackexchange.com/questions/50410/perfect-matching-in-a-graph-and-complete-matching-in-bipartite-graph)
指的是二分图某一侧的顶点都和匹配边相连，我之前确实以为 perfect matching 都是说所有顶点都和匹配边相连。

在 proofs from the book 27章读到的用集合描述的 Hall's theorem

### system of distinct representatives(SDR)

Consider finite set $X$ and a collection $A_1,A_2,...,A_n$ of subsets of $X$. A sequence $x_1,x_2,...,x_n$ is a SDR if $x_i$s are distinct elements in $X$ and $x_i\in A_i$ for all $i$.

实际上就是存在二分图的 perfect matching.

### Hall's Theorem

存在 SDR(存在perfect matching) 的充要条件是 $A_1,A_2,...,A_n$ 中任取 $m$ 个子集的并集, 得到的集合大小至少是 $m$.

### proof
这是 proofs from the book 给出的一个证明

![proof]({{url}}/assets/image/Hall'sTheorem.jpg)

[这是在图上的证明](https://homes.cs.washington.edu/~anuprao/pubs/CSE599sExtremal/lecture6.pdf)

证明这种东西很容易就能想到对$A$的大小归纳，这两个证明几乎是完全相同的，不过从两个不同角度来想这个证明还是挺有意思的

### exercise

proofs from the book 上这章竟然有一道课后题

![exercise]({{url}}/assets/image/Hall'sTheorem_exercise.jpg)

从二分图来考虑我觉得总是比集合要直观一点. 我们证明对于任意的k，这样的图都满足 Hall's theorem 的条件。然后总存在一个匹配，把所有匹配边删掉，图左侧的点度数都是 k-1， 右侧的点度数都小于等于k-1，又满足 Hall's theorem.

把题目中描述的这种二分图叫做$G_n^k$, 唯一需要证明的就是$G_n^k$ 存在一个perfect matching使得所有匹配边都删掉之后图变成一个$G_n^{k-1}$ 。 问题在于图右侧有一些点度数=k, 也就是我们需要证明存在一个 perfect matching 包含所有右侧的度数等于$k$的顶点.

如果二分图是 k-regular(所有顶点的度数都是k) 那么这个问题也很好解决。。删除一个 perfect matching, 所有点的度数都会-1，变成$G_n^{k-1}$. Prove by induction on $k$. 
([exercise12](https://www.ams.jhu.edu/~abasu9/AMS_550-472-672/HW-5-sol.pdf))

但是我们可以在二分图的左侧加上顶点，并且向右侧度数小于k的点连边，把图变成一个 k-regular bipartite graph. 唯一的变化就是我们的到的k个 element distinct SDR 全都比我们想要的更长，在SDR中把新加入的点对应的元素删掉就行了