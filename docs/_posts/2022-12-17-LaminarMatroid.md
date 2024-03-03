---
layout: post
title:  "Notes on Laminar Matroid"
date:   2022-12-17 0:00:00 +0800
categories: alg matroid
---

> references: 
> 
> [https://cdn.vanderbilt.edu/vu-my/wp-content/uploads/sites/2392/2015/07/14141714/Tara-E-Fife.pdf](https://cdn.vanderbilt.edu/vu-my/wp-content/uploads/sites/2392/2015/07/14141714/Tara-E-Fife.pdf)
>
> [https://arxiv.org/abs/1606.08354](https://arxiv.org/abs/1606.08354)


### prove the exchange property

prove through induction on $|A|$(use [definition 1](https://arxiv.org/abs/1606.08354))

- $|A|=1$, this is obvious.
- assume exchange property holds for $|A|\leq k$. Need to prove that for two independent set $I_1,I_2$, $|I_1|>|I_2|$, $\exists i\in I_1\backslash I_2$ s.t. $\forall A, |A|=k+1, |\{I_2\cup i\}\cap A|\leq c(A)$. Suppose the exchange property doesn't hold for $|A|=k+1$. For all $i$ exists $A\ni i$, $|I_2\cap A|=c(A)$. Since $I_1$ is an independent set, $|I_1\cap A|\leq c(A)$ for those $A$, $\{I_2\backslash I_1\}\cap A\not= \emptyset$, then for those $A$ $|A\cap I_1|\leq |A\cap I_2|$. Thus for any $i\in I_1\backslash I_2$, there is a set $A$ s.t. $|A\cap \{I_2\backslash I_1\}| \geq |A\cap \{I_1\backslash I_2\}|$, and those $A$ are disjoint since $A\in \mathcal{A}$. Thus $|I_1|\leq|I_2|$. So exchange property holds for $k+1$


一点点关于拟阵交和带权拟阵交的原始的想法：

- [https://www.bilibili.com/read/cv20927549](https://www.bilibili.com/read/cv20927549)<br> ![matroid intersection]({{url}}/assets/image/matroid_intersection/MI.jpeg)
- [https://www.bilibili.com/read/cv20954565](https://www.bilibili.com/read/cv20954565)<br> ![weighted matroid intersection]({{url}}/assets/image/matroid_intersection/weightedMI.jpeg)