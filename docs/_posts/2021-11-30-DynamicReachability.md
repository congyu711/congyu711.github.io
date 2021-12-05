---
layout: post
title:  "A Fully Dynamic Reachability Algorithm for Directed Graphs with an Almost Linear Update Time"
date:   2021-11-30 11:30:08 +0800
categories: reachability
---

在读的文章
  

[A Fully Dynamic Reachability Algorithm for Directed Graphs with an Almost Linear Update Time](https://dl.acm.org/doi/pdf/10.1145/1007352.1007387)
  



## FULLY DYNAMIC STRONG CONNECTIVITY WITH PERSISTENCY
  
The algorithm handles each
insert operation in $O(mα(m, n))$ worst-case
time and each deletion operation in $O(mα(m, n) + t)$ amortized time, and each query in $O(1)$ time. The space complexity of the algorithm is $O(m+n)$

插入和删除都可以对任意边集操作；每次插入会新建一个版本的图，删除则会从所有当前存在的版本中把要删除的边集删掉。
  

$Insert(E′): t \leftarrow t + 1 , E_t \leftarrow E_{t−1} \cup E^′$

$Delete(E′): E_i \leftarrow E_i − E^′ , \text{for} \quad 1 \leq i \leq t$

$Query(u, v, i): \text{Are u and v in the same component of the graph Gi?}$

$G_i$'s SCC is either a SCC of $G_{i-1}$ or a union of some SCCs of $G_{i-1}$;

$G_1,G_2,...,G_t$的所有SCC(不同G中相同SCC只记录一个)可以构成森林。叶子是单个的点。
The parent of a component w in
the forest is the smallest component that strictly contains w.  

下面提出一个edge partition 的概念用来维护上述森林。


### Dynamic edge partitioning:  
  

1. $H_i=\{(u,v) \in E_i\|Query(u, v, i) ∧ (¬Query(u, v, i − 1) ∨ (u, v) \notin Ei−1) \}$
2. $H_{t+1}=E_t\ \cup_{i=1}^{t}H_i$
  

$H_i$中的边是$G_i$中的SCC里面的边，并且要么在$G_{i-1}$中没有出现，要么在$G_{i-1}$中是不同SCC之间的边。  

用两个数组parent和version来记录森林中每个节点的父亲和最早出现的版本（version记录最早那一次插入操作后出现了这个scc）。  

现在要查询$G_i$中的两个点u,v是否在同一个SCC中，只需要在森林中查找u,v的LCA，如果version[LCA]小于等于i，说明u,v在同一个SCC中。（存在LCA说明在同一棵树，在某个版本u,v是在一个SCC中的，这个版本就是version[LCA]）

文中要使用[tarjan的LCA算法](https://dl.acm.org/doi/abs/10.1145/800061.808753)均摊$O(1)$的单次询问和$O(n)$预处理（并查集使用路径压缩）。  

### Insert & Delete Operation

初始化：$G_0$是空图，森林是n个孤立的点，version都是0；

#### Insert:

进行第t个插入操作，插入的边集是$E'$：

首先根据 dynamic edge partitioning 的过程，$H_t$已经存在并且里面有$G_{t-1}$中SCC之间的边.

1. $H_t \leftarrow H_t \cup E'$
2. a temporary set of edges $H'$ is created by contracting the endpoints
of $H_t$ edges with respect to the components of $G_{t−1}$.
3. compute SCC in $H'$
4. for each SCC(denoted C) in $H'$, union vertices in C, update version and parent
5. move edges which shouldn't in $H_t$ to $H_{t+1}$
6. preprocess LCA
7. t=t+1

解释一下第二步：$H'\leftarrow \{(find(u),find(v))|(u,v)\in H_t \}$
$H_t$中的边是新加入的边和$G_{t}$中SCC之间的边，同一个scc中的点可以看成是一个点（因为在联通性方面他们等价），于是直接用其中一个点来代替他们。如果在$H'$中形成了SCC，那么每个SCC中的点就会形成一个大的SCC，再把他们union起来。思想就是使用并查集来维护不断插入边来维护SCC的过程。


#### Delete:

delete操作十分暴力，直接破坏维护的整个并查集结构然后重新构建。

1. for each $v\in V$, parent[$v$]=null;
2. do insert k times (k versions)

复杂度证明略。见论文。 （也提到了删除操作的$O(mα(m, n) + t)$ amortized time中的t是由于
要遍历t个$H_i$集合造成的，但是由于每次至少插入一条边，t一定小于m，所以也可以去掉）

##  Identify and Report Decompositions of SCC

观察到无论是insert还是delete任意边集E，每个SCC在森林中的version不会减少。（insert只能增加version；delete一些边，相同的SCC只能在相同时间或者更晚形成。）

如果一个SCC在delete操作时被分开了，我们希望找到他在新的森林中对应的最大的儿子是哪些。

part操作：对于一个SCC（记为C），找到他子树中节点在delete操作之后的森林中的对应（记为集合S）。可以保证这些点的并集一定是C，但是不能保证S中的SCC都是最大的。

统计S中所有节点的父亲（如果没有父亲就跳过）记为集合T（其实是一个按version排列的优先队列）.每次取T中version最小的节点u，如果u的儿子都属于S，就把u的儿子从S中删除，S中加入u，且把u的父亲也加入T。 

然后有split操作对于给定的delete前的SCC v和version i，得到delete后分成了哪些version小于等于i的点：

   1.  $R\leftarrow Part(v)$
   2.  $\text{while }\exists u\in R\text{ with }version[u]\geq i\text{ do}$
   3.  $\quad R \leftarrow R\cup children[u]-\{u\}$
   4.  $\text{return } R$

## DECREMENTAL MAINTENANCE OF REACHABILITY TREES

reachability tree是用来维护对于某个点r的联通性的数据结构。每个节点是一个SCC，根节点是包含r的一个SCC，如果图中的某个SCC不在树中，说明从r不能到达这个scc中的节点。

>For every component w of the graph, the algorithm maintains a doubly linked list active[w] that contains all the active vertices of the component.
>A vertex v is active if it has uninspected inter-component incoming edges.
>
> For every **active** vertex v, the algorithm maintains a doubly linked list in[v] containing all the uninspected inter-component edges that enter v.
> 
>For every vertex v, **active or inactive**, the algorithm maintains a doubly linked list out[v] containing all the edges emanating from v.

最开始所有边都是 uninspected，处理到一条边之后有两种情况：
   1. 发现这条边没用，删掉
   2. 这条边有用，变成 reachability tree 中的树边，保持 uninspected.


reachability tree 要保持这样的结构：
>If w is a component of G, v
>is the first vertex in active[w], and u is the first
>vertex in in[v], then (u, v) is the tree edge connecting 
>component w to the tree. In particular,
>if active[w] is empty, then w is not connected to
>the tree and the vertices of w cannot be reached
>from r.

-----

为什么 in[v] 中只保存 inter-component 的边？ 

在SCC构成的森林中， intra-component 的边是自环，不可能成为树边，不要考虑他们。当 delete 操作之后，一个SCC中的 intra-component 边可能会变成 inter-component. 
数据结构会维护这个部分。

当删除一些边的时候，要维护 reachability tree 就需要把删除边之后分裂的SCC找出、加入新的 inter-component 边，删除被删除的 inter-component 边。

但是这些操作可能会影响上面 reachability tree 要保持的结构，所以还需要再维护。

接下来只描述一下思想。

在 Identify and Report Decompositions of SCC 部分我们能得到任意一个SCC在 delete 操作之后会被 split 成新图中的哪些SCC。

$$w\rightarrow \{w_1,w_2,...,w_j,...,w_l\}$$

可以得到一个被分裂出来的SCC的大小$O(1)$和里面有哪些点$O(\|w_j\|)$。

可以得到那些边从 intra-component 变成 inter-component.(比如被分开的SCC是$G_i$中的，那么这一步找到的改变状态的边就是$H_i$中被删除的边，根据 dynamic edge partitioning 中$H_i$的定义)

现在要维护出$\{w_1,w_2,...,w_j,...,w_l\}$中每个SCC的active链表。

$\{w_1,w_2,...,w_j,...,w_l\}$中每个SCC的active链表中的点有两部分。一部分是由$w$的active链表中的点继承下来的，另一部分是因为$w$中的一些边被删掉了，某些点的入边从 intra-component 变成 inter-component ，导致这个点从 inactive 变为 active.

首先第二部分的点可以在更改边的状态时直接解决，这里不讨论。

第一部分的点有这样的做法。假设$\{w_1,w_2,...,w_j,...,w_l\}$中这些SCC的大小是递减的。首先构建$w_1$的active list，遍历$w$的active list中的点，把其中不属于a的都移动到其他某个$w_k$的active list中（注意分成的SCC是不可能有交集的）（这一步需要$O(\sum_{j=2}^{l}\|w_j\|)$。

下面文中说：
>This makes a huge difference! If a vertex is moved from one active list to another, the size of the component containing it must have decreased by a factor of at least 2. Each vertex is therefore moved at most $\log_2n$ times and the total amount of time spent on constructing these lists is at most O(n log n).

不是很懂为什么size会减半，复杂度为什么是O(nlogn)

接下来 Reconnecting the tree after edge deletions

>Let W be a set containing all the new components with no incoming tree edge, and all the old components that lost their incoming tree edges. (If the root r is contained in a new component, this new component is not added to W.) The set W can be easily constructed in O(\|W\|) time.

删除一些边之后，原来的reachability tree可能会变成森林。森林中的树根显然就是W中的SCC（根据定义）（也有可能是r现在所在的SCC，r原来所在的SCC因为删边被破坏了）。

现在，我们想把W中的这些树根连起来，重新组合成r的 reachability tree

很容易想到利用上面早已构造好的 active 链表和 in 链表。

1. $\text{ for each }w \in W$
2. $\quad \text{for each }u\in active[w]$
3. $\quad \quad \text{for each }v\in in[u]$
4. $\quad \quad \quad \text{if } active[SCCNO[v]]!=null\text{ or }SCCNO[v]==SCCNO[r]$
5. $\quad \quad \quad \quad \text{remove w from W, this edge is a tentative tree edge}$
6. $\quad \quad \quad \text{else}$
7. $\quad \quad \quad \quad \text{remove that edge}$


A vertex v whose list in[v] becomes empty ceases to be active and is removed from active[w].

如果在检查w的入边是否符合要求的时候发现并没有入边能符合要求，显然他是真的不行了，删边之后不会进入新的 reachability tree 了。但是还是要维护一下他的能到达的SCC（下面解释原因）。遍历他的出边（out[v]，v是w中的点)，如果out[v]中有边(v,u) 是 tentative tree edge，那需要把u所在的SCC加入W中。

这样做的原因：上面的标记 tentative tree edge 的步骤并没有考虑顺序，可能一些本来应该在新的 reachability tree 中的SCC直接连接到了某个已经不会在 reachability tree 里的SCC上面。为了防止漏掉SCC，要把不回进入新的 reachability tree 的SCC的 tentative tree edge 连接到的SCC重新加入W。

## AN ALMOST LINEAR FULLY DYNAMIC REACHABILITY ALGORITHM

支持的操作：

1. 插入：插入一些边（起始点或结束点都为同一个点）
2. 删除：删除任意边集
3. 询问：是否存在从u到v的路径
   
每次插入操作都会围绕一个中心点v，那就为v建立两棵 reachability tree，分别保存v能到达的点和能到达v的点（也相当于在原图和反图上建立上文的 reachability tree）这也是为什么上文 reachability tree 只需要处理删除操作。

查询是否存在从u到v的路径：检查是否存在这样一个w，使得u可以到达w（$u\in T_{in}[w]$），而且w可以到达v（$v\in T_{out}[w]$）.
这可以在$O(n)$的时间内查找到。???

我思考了一下，不知道为何能在$O(n)$回答询问。最坏情况要查找n个w，在reachability tree中并不能在常数时间内回答树中是否存在某个点。有点迷惑。


周末花了很长时间来读这个，感觉太长了，我对动态图上的reachability问题或者他的算法都没什么想法。。。也没有完全理解。