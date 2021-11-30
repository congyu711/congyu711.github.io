---
layout: post
title:  "A Fully Dynamic Reachability Algorithm for Directed Graphs with an Almost Linear Update Time"
date:   2021-11-30 11:30:08 +0800
categories: reachability
---

在读的文章
  

# [A Fully Dynamic Reachability Algorithm for Directed Graphs with an Almost Linear Update Time](https://dl.acm.org/doi/pdf/10.1145/1007352.1007387)
  



## FULLY DYNAMIC STRONG CONNECTIVITY WITH PERSISTENCY
  
The algorithm handles each
insert operation in $O(mα(m, n))$ worst-case
time and each deletion operation in $O(mα(m, n) + t)$ amortized time, and each query in $O(1)$ time. The space complexity of the algorithm is $O(m+n)$

插入和删除都可以对任意边集操作；每次插入会新建一个版本的图，删除则会从所有当前存在的版本中把要删除的边集删掉。
  

$Insert(E′): t \leftarrow t + 1 , E_t \leftarrow E_{t−1} \cup E^′$

$Delete(E′): E_i \leftarrow E_i − E^′ , for \quad 1 \leq i \leq t$

$Query(u, v, i): \text{Are u and v in the same component of the graph Gi?}$

$G_i$'s SCC is either a SCC of $G_{i-1}$ or a union of some SCCs of $G_{i-1}$;

$G_1,G_2,...,G_t$的所有SCC(不同G中相同SCC只记录一个)可以构成森林。叶子是单个的点。
The parent of a component w in
the forest is the smallest component that strictly contains w.  

下面提出一个edeg partition的概念用来维护上述森林。


### Dynamic edge partitioning:  
  

1. $H_i=\{(u,v) \in E_i\|Query(u, v, i) ∧ (¬Query(u, v, i − 1) ∨ (u, v) \notin Ei−1) \}$
2. $H_{t+1}=E_t\ \cup_{i=1}^{t}H_i$
  

$H_i$中的边是$G_i$中的SCC里面的边，并且要么在$G_{i-1}$中没有出现，要么在$G_{i-1}$中是不同SCC之间的边。  

用两个数组parent和version来记录森林中每个节点的父亲和最早出现的版本（version记录最早那一次插入操作后出现了这个scc）。  

现在要查询$G_i$中的两个点u,v是否在同一个SCC中，只需要在森林中查找u,v的LCA，如果version[LCA]小于等于i，说明u,v在同一个SCC中。 (存在LCA说明在同一棵树，在某个版本u,v是在一个SCC中的，这个版本就是version[LCA])

文中要使用[tarjan的LCA算法](https://dl.acm.org/doi/abs/10.1145/800061.808753)均摊$O(1)$的单次询问和$O(n)$预处理（并查集仅使用路径压缩）。  

### Insert & Delete Operation

初始化：$G_0$是空图，森林是n个孤立的点，version都是0；

Insert:

进行第t个插入操作，插入的边集是$E'$：

首先根据 dynamic edge partitioning 的过程，$H_t$已经存在并且里面有$G_{t-1}$中SCC之间的边。

1. $H_t \leftarrow H_t \cup E'$
2. a temporary set of edges $H′$ is created by contracting the endpoints
of $H_t$ edges with respect to the components of $G_{t−1}$.
3. compute SCC in $H'$
4. for each SCC(denoted C) in $H'$, union vertices in C, update version and parent
5. move edges which shouldn't in $H_t$ to $H_{t+1}$
6. preprocess LCA

目前我只读到这里，但是时间有点久了，快期末了事情多，先挂上来
