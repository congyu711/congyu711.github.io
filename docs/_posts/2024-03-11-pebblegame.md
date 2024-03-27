---
layout: post
author: congyu
title:  "collecting pebbles and sparsity"
date:   2024-03-11 00:00:00 +0800
categories: alg sage
---

> assuming familiarity with basic matroid thoery

\DeclareMathOperator{\R}{\mathbb{R}}
\DeclareMathOperator{\Z}{\mathbb{Z}}
\DeclareMathOperator{\CH}{CH}
\DeclareMathOperator{\opt}{OPT}
\newcommand{\kl}{(k,\ell)}


**Problem** collecting pebbles. Given a directed graph $G=(V,E)$. There are pebbles on a subset of vertives $T\subset V$. We can move pebbles along the directed edges. Once we move a pebble along some edge, this edge is immediately inverted. The problem is can we move $k$ pebbles to a special vertex $s$.

I think this should be NP-Hard but I don't know how to prove it.

I found this problem in a paper ["Pebble game algorithms and sparse graphs"](https://linkinghub.elsevier.com/retrieve/pii/S0012365X07005602). On $(k,\ell)$-sparse graphs with special rules for putting initial pebbles this problem can be solved through simple depth-first-search.

There are two interesting ideas in the paper. The relation of sparsity and pebble games and why the above problem can be solved with simple dfs.

### $(k,\ell)$-sparsity

$(k,\ell)$-sparsity is a quantitative way to say how sparse an undirected graph is. $k,\ell\in \Z_+$ and $\ell \leq 2k-1$. A graph $G$ is $(k,\ell)$-sparse if for any subgraph $H=(V,E)$ of $G$ $|E|\leq k|V|-\ell$. One can see that $\ell \leq 2k-1$ since otherwise there must be no edge between any two vertices. The $(k,\ell)$-sparse subgraphs(edge set) of $G$ form the independent sets of a matroid. For more on matroid and especially sparsity matroid, read [matroid](https://en.wikipedia.org/wiki/Matroid) and [sparsity matroid](https://en.wikipedia.org/wiki/Sparsity_matroid). The `Pairs (k,l) that form a matroid` part in the sparsity matroid link is misleading. Matroids with $k,l$ and $n$ satisfying those condition will have a spanning tight graph as their bases; those who don't satisfying conditions are still matroids but their bases will never be $(k,\ell)$-tight graphs.

Actually more general things are also matroids. They are called count matroids. read Andras Frank's ["Connections in Combinatorial Optimization"](http://scholar.google.com/scholar?hl=en&btnG=Search&q=intitle:Connections+in+Combinatorial+Optimization#0) Theorem 13.5.5 for proofs.

In terms of sparsity, the proof in the book basically shows $r(F)=k|V(F)|-l$ is a matroid rank function and the independnet sets of our sparsity matroid defined above are exactly the independent sets of the matroid defined by rank function $r$.

If you want to prove the independnet set exchange property directed, it seems a little harder. We need to show for any two $(k,\ell)$-sparse subgraph $(V_1,I_1)$ and $(V_2,I_2)$ s.t. $|I_1|>|I_2|$, there exists $e\in I_1\setminus I_2$ s.t. $I_2\cup \{e\}$ is $(k,\ell)$-sparse. We only consider connnected graphs. If $V(I_1)$ is not a subset of $V(I_2)$, there would be $u\in V(I_1)$ and some edge $e$ connecting $u$ and $V(I_2)$. $e$ can be added to $I_2$ since $k\geq 1$.
On the other hand if $V(I_1)$ is a subset of $V(I_2)$ how to find such an edge? Suppose such an edge does not exist. Then for any $e=(u,v)\in I_1\setminus I_2$, we can find a tight subgraph $H=(V',E')$ of $I_2$ containing $u$ and $v$ but not the edge $e$.(see Theorem 5 in the pebble game paper). Also there exists at least one edge in $E'$ but not in $I_1$. ~~(I think the contradiction is $|I_2|\geq |I_1|$  but don't know what to do next...)~~ This method doesn't work

![]({{url}}/assets/image/pebblegame/klsparseproof.png)

Googling 'graph sparsity' normally returns sparsity measurement like upperbounds on average degree or max degree. 
There are many literature about bounded max(average) vertex degree graphs(see [this lecture from mim_uw](https://www.mimuw.edu.pl/~mp248287/sparsity2/) for example). However these definitions don't imply matroids.
For example graph with max degree 4. 

![]({{url}}/assets/image/pebblegame/ce.jpeg)
One can see that the two graphs do not satisfy the independent set exchange property.

### pebble game

How to decide whether a given graph is $\kl$-sparse? ["Pebble game algorithms and sparse graphs"](https://linkinghub.elsevier.com/retrieve/pii/S0012365X07005602) provides an algorithm solving the problem in polynomial time($O(n^2)$,$n$ is the number of vertices) and a proof of equivalence of pebble game and graph $\kl$-sparsity.

The algorithm described in the paper basically finds a base of $\kl$-sparsity matorid defined on the input graph. Note that finding a base of any matroid $\mathcal{M}=(E,\mathcal{I})$ can be easily done with $O(|E|)$ independence oracle calls. If we want a polynomial time alg then the independence oracle must be fast. A bruteforce method is to check every subgraph. We can certainly not afford that. 

In the paper the authors designed a nice way to check independence. The idea is instead of comparing $k|V|-\ell$ and $|E|$, they compare $k|V|-|E|$ and $\ell$. Then $\ell$ is fixed and checking every subgraph to compute $k|V|-|E|$ is still needed. They manage to count $k|V|-|E|$ by counting pebbles on vertices instead of counting edges. 
Thus the rules of pebble games are quite straightforward(but still some ambiguous rules). Initially we have an empty graph and $n$ vertices. On each vertex there are $k$ pebbles(for $k|V|$). We consider edges one by one in arbitrary order. Once we added an edge to the graph, we should remove one pebble from one of the edge's endpoints. We need to design rules for accepting or rejecting edges. Astute readers may find that simply removing pebbles while adding new edges is completely not working 😅. We want the remaining pebbles on any subgraph to be $k|V|-|E|$ but the method we try to use doesn't guarantee this since we may remove a pebble on either endpoint of the added edge...

In the paper they use directed graph. When considering an undirected edge $(u,v)$, they make it directed(i.e. $u\rightarrow v$) and then remove a pebble from the source($u$). An edge is accepted if and only the endpoints can collect $l+1$ pebbles in total. Collecting pebbles is just searching paths from $u$ or $v$ to some vertex with pebbles and moving one pebble from that vertex to $u$(or $v$) and reversing the edges on the path. For any vertex, if an out edge is added, an pebble is removed(accepting edge); if one pebble is removed, an out edge is added(pebble collecting vertex); if one pebble is added, one out edge is removed(reverse pebble collecting path).

One can see that for any subgraph $G'=(V',E')$ this pebble collection and edge adding operation preserve the sum of

1. total number of pebbles on $V'$
2. |E'|
3. the out edges starting at $V'$ and ending elsewhere.

see the paper for detailed proof.

![]({{url}}/assets/image/pebblegame/invariant.png)

The last question is can we do every operations in polynomial time? or in other words why collecting pebbles can be done in polynomial time? In the paper there is a lemma saying that if adding an edge $(u,v)$ does not break sparsity and there are not enough pebbles on $u$ and $v$(we need to do pebble collection), then we can always find a pebble collecting path without changing the pebble count of other vertices. Thus we can collect pebbles by simple dfs.



code for an straightforward $O(n^3)$ implementation,

{% include_relative notebooks/pebblegame.html %}