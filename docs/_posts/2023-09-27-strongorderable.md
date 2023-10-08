---
layout: post
title:  "determine storng orderable matroid"
date:   2023-09-27 0:00:00 +0800
categories: matroid
---


[![img]({{url}}/assets/image/../../../../assets/image/SBO/definition.jpg)](https://en.wikipedia.org/wiki/Base-orderable_matroid)

<!-- $\mathcal M=(E,\mathcal F),n=|E|$, rank $r$

### number of optimal bases

unifrom: $\binom{n}{r}$

graphic: $n^{n-2}$ (Cayley's theorem, # spanning trees in complete graphs)

### number of cut(or circuit?)

unifrom: $\binom{n}{r+1}$

graphic: $n(n-1)/2$ (Karger's random global min cut algorithm) -->

# Determining strong base-orderability of a matroid

<https://mathoverflow.net/questions/194006/determining-strong-base-orderability-of-a-matroid>

determining base-orderability of a matroid can be done through finding perfect matching in a bipartite graph with $\|E\|^2$ edges. call oracle $\|E\|^2$ times to build the graph and run a matching algorithm.

Is the union of strongly base-orderable matroids strongly base-orderable? yes. 
<https://mathoverflow.net/questions/201101/is-the-union-of-strongly-base-orderable-matroids-strongly-base-orderable>
also see [wikipedia](https://en.wikipedia.org/wiki/Base-orderable_matroid#:~:text=feasible%20exchange%20bijection.-,Completeness,-%5Bedit%5D)

### difference between base-orderable and strong base orderable?

<https://arxiv.org/abs/1507.05521>

an induction on the size of the set $X$

- 1-BO
- 2-BO
- ...
- $\lceil r/2 \rceil$-BO = SBO

given 2 bases of a matroid and a k-BO bijection f, find subsets with k+1 elements which do satisfy $k+1$-BO?

1-BO -> 2-BO is already hard...
