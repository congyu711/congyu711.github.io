---
layout: post
author: congyu
title:  "collecting pebbles and sparsity"
date:   2024-03-11 00:00:00 +0800
categories: alg sage
---


\DeclareMathOperator{\R}{\mathbb{R}}
\DeclareMathOperator{\Z}{\mathbb{Z}}
\DeclareMathOperator{\CH}{CH}
\DeclareMathOperator{\opt}{OPT}


**Problem** collecting pebbles. Given a directed graph $G=(V,E)$. There are pebbles on a subset of vertives $T\subset V$. We can move pebbles along the directed edges. Once we move a pebble along some edge, this edge is immediately inverted. The problem is can we move $k$ pebbles to a special vertex $s$.

I think this should be NP-Hard but I don't know how to prove it.

I found this problem in a paper ["Pebble game algorithms and sparse graphs"](https://linkinghub.elsevier.com/retrieve/pii/S0012365X07005602). On $(k,\ell)$-sparse graphs with special rules for putting initial pebbles this problem can be solved through simple depth-first-search.

There are two interesting ideas in the paper. The relation of sparsity and pebble games and why the above problem can be solved with simple dfs.

### $(k,\ell)$-sparsity

$(k,\ell)$-sparsity is a quantitative way to say how sparse an undirected graph is. $k,\ell\in \Z_+$ and $\ell \leq 2k-1$. A graph $G$ is $(k,\ell)$-sparse if for any subgraph $H=(V,E)$ of $G$ $|E|\leq k|V|-\ell$. One can see that $\ell \leq 2k-1$ since otherwise there must be no edge between any two vertices. The $(k,\ell)$-sparse subgraphs(edge set) of $G$ form the independent sets of a matroid. For more on matroid and especially sparsity matroid, read [matroid](https://en.wikipedia.org/wiki/Matroid) and [sparsity matroid](https://en.wikipedia.org/wiki/Sparsity_matroid). The `Pairs (k,l) that form a matroid` part in the sparsity matroid link is misleading. Matroids with $k,l$ and $n$ satisfying those condition will have a spanning tight graph as their bases; those who don't satisfying conditions are still matroids but their bases will never be $(k,\ell)$-tight graphs.

Actually more general things are also matroids. They are called count matroids. read Andras Frank's ["Connections in Combinatorial Optimization"](http://scholar.google.com/scholar?hl=en&btnG=Search&q=intitle:Connections+in+Combinatorial+Optimization#0) Theorem 13.5.5 for proofs.

In terms of sparsity, the proof in the book basically shows $r(F)=k|V(F)|-l$ is a matroid rank function and the independnet sets of our sparsity matroid defined above are exactly the independent sets of the matroid defined by rank function $r$.

If you want to prove the independnet set exchange property directed, it seems a lot harder. We need to show for any two $(k,\ell)$-sparse subgraph $(V_1,I_1)$ and $(V_2,I_2)$ s.t. $|I_1|>|I_2|$, there exists $e\in I_1\setminus I_2$ s.t. $I_2\cup \{e\}$ is $(k,\ell)$-sparse. We only consider connnected graphs. If $V(I_1)$ is not a subset of $V(I_2)$, there would be $u\in V(I_1)$ and some edge $e$ connecting $u$ and $V(I_2)$. $e$ can be added to $I_2$ since $k\geq 1$.
On the other hand if $V(I_1)$ is a subset of $V(I_2)$ how to find such an edge? Suppose such an edge does not exist. Then for any $e=(u,v)\in I_1\setminus I_2$, we can find a tight subgraph $H=(V',E')$ of $I_2$ containing $u$ and $v$ but not the edge $e$.(see Theorem 5 in the pebble game paper). Also there exists at least one edge in $E'$ but not in $I_1$. (I think the contradiction is $|I_2|\geq |I_1|$  but don't know what to do next...)


### pebble game

{% include_relative notebooks/pebblegame.html %}