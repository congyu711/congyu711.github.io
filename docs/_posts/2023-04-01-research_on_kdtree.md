---
layout: post
title:  "c++ kd-tree implementations"
date:   2023-04-01 0:00:00 +0800
categories: alg
---

勉强坚持一下月更...

操作系统作业要写一下死锁检测 https://en.wikipedia.org/wiki/Banker's_algorithm 里面有一步大概是在所有进程的 required_resources 对应的向量里面找小于系统 available_resources 对应的向量, 更新 available_resources 再找, 重复这样的过程. 实际上就是在一些$m$维的点里面不断询问给定的一个 axis parallel box 当中的点是哪些. 想用kd-tree让这个过程跑的更快一些, 于是开始找c++的kd-tree实现.

理想的 implementation 应该是这样的: 点的类型是一个模板参数, kd-tree只要求这个点的类型重载了<=和[], 分别用来定义点的严格大小关系和取出第k维的值, 另外每个维度的值也应该重载<=, 应该真的支持k维的点, 期望复杂度能做到和理论一致 https://en.wikipedia.org/wiki/K-d_tree, 支持删除插入...

大概找了三个实现:

- kdtree-cpp: https://github.com/cdalitz/kdtree-cpp
- ikd-Tree: https://github.com/hku-mars/ikd-Tree
- nanoflann: https://github.com/jlblancoc/nanoflann

### kdtree-cpp

这是个不错的实现, 作者写的代码比较简洁, 并且knn支持多种距离度量(继承`DistanceMeasure`类之后稍微修改就行了, 不过死锁检测也用不到knn), 但是点的类型是 `vector<vector<double>>` 维度都需要运行时判断, 而且只有knn方面的函数, 另外还有`std::nth_element`的问题, 在ikd-Tree当中一起讨论. 不支持插入删除. 我觉得写的很好, 但是需要补充和调整很多东西才能用到别的地方去.

### ikd-Tree

这个实现是在arxiv上一篇文章中用到的: https://arxiv.org/abs/2102.10808 

看文章和测试的结果, 速度非常快, 支持插入删除, 而且直接提供了`Box_Search`这样的函数. 

严重的问题是文章绝大部分和github的README当中都在说kd-tree, 而且代码当中 PointType 也是一个模板参数, 但是只支持三维... 花了不少时间看文章和文档都没有发现...

更加严重的问题是`std::nth_element`, https://github.com/hku-mars/ikd-Tree/issues/25 在查询一个范围中的点时不能根据kdtree当中一个节点划分的维度对应的值来判断是应该向左子树还是右子树搜索. issue当中已经给了一个例子.

### nanoflann

我没仔细看, 根据 examples 他是支持任意维度的点插入删除的...看起来满足所有的要求.

在ikd-Tree的issue当中看到建树的时候直接用了
```cpp
    DistanceType split_val = (bbox[cutfeat].low + bbox[cutfeat].high) / 2;
```

貌似不能保证树是平衡的?

在看和改这些kd-tree上花费的时间感觉足够从头开始写一个了, 最终也没有用前面两个实现改出一个我能用上的kd-tree...