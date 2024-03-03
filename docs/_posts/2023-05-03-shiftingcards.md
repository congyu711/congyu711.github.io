---
layout: post
title:  "Shifting Cards"
date:   2023-05-03 0:00:00 +0800
categories: PFTB combinatorics
---

[hackmd.io](https://hackmd.io/@r1NLzG2QQuKF14FgHKxugg/r1kQ4V67n)搬过来的

# Chapter28 Shifting Cards

proofs from the book chap 28. 这一章讲的是各种洗牌算法, 很有意思. 用看起来比较简单的模型分析了听起来很难分析的东西.

- 洗的牌是什么? 52张扑克牌
- 要洗成什么样子? 希望洗好之后出现任何一种排列的概率都大概是$1/52!$

## Preliminary

需要知道 birthday paradox 和 一个叫做 coupon collector 的问题, 生日悖论很常见, coupon collector 是这样一个问题:
盒子里有n个编号不同的球, 我们每次取一个记下编号然后放回, 我们取到所有1到n的标号的球的时候停止, 拿球的次数的期望是多少?

如果已经拿到了k种球, 想再拿到一个以前没拿过的球还需要拿多少次呢?

$$
\sum_{s\geq 1}  (\frac{k}{n})^{s-1}(1-\frac{k}{n})s=\frac{1}{1-\frac{k}{n}}
$$

所以想拿到n个不同的球需要拿的次数的期望是

$$
\sum_{k=0}^{n-1} \frac{1}{1-\frac{k}{n}}=nH_n\approx n\log n
$$

还有一件事, 取超过期望的次数的概率大概是多少? 这个问题被定义成 取得次数 $V_n$ 大于 $m=\lceil{n\log n+cn}\rceil$ 的概率是多少? 

$A_i$ 是前m次抓取编号为i这个球都没有被抓上来的事件

$$
\text{Prob}[V_n \geq m]=\text{Prob} \big[ \bigcup_i A_i\big] \leq \sum_i \text{Prob}[A_i]=n(1-/n)^m\leq e^{-c}
$$

还有一件事, 需要搞清楚如何衡量随机. 洗牌之后可能出现$n!$种排列, 所有排列的集合是$\Pi$, 从而构成了一个分布, $E(\pi)$ 表示出现$\pi$这个排列的概率是多少. 我们想知道的就是洗牌之后的这个分布和均匀分布有多相似. 用一个叫做variation distance的东西来衡量, 定义是 $\|Q_1-Q_2\|=\frac{1}{2}\sum_{\pi\in \Pi}\|Q_1(\pi)-Q_2(\pi)\|$, 令 $S=\{\pi\in \Pi\mid Q_1(\pi)>Q_2(\pi) \}$, variation distance就可以写成 $\|Q_1(S)-Q_2(S)\| \mid S\in \Pi$.

<!-- > https://en.wikipedia.org/wiki/Kullback%E2%80%93Leibler_divergence 为什么这里不用kl散度?
 -->

下面可以介绍第一种洗牌方法啦

## Top-in-at-random shuffles

怎么洗? 拿到牌堆顶部的牌, 等概率插入到其他n个位置

洗牌之前的的排列是$\pi$, 概率分布就记为$E$, $E(\pi)=1$, 其他概率都是0. 由上面variation distance的定义, $\|E-U\|=1-\frac{1}{n!}$, U是$n!$种排列的均匀分布.

一次 top-in-at-random 洗牌之后, 有n种可能的排列, 每一种的概率都是$1/n$, 可以算出$\|E'-U\|=1-\frac{1}{(n-1)!}$

书当中提到这种洗牌方法如果不断地进行下去, 与均匀分布相比的variation distance会 goes to zero exponentially fast.

## Strong uniform stopping rules

通过上面的例子我们大概知道洗牌是一个什么样的过程. 首先有一个洗牌的方法, 这个方法涉及到"随机"的做改变牌的顺序的操作, 然后把洗牌的方法重复很多次. 我们想知道什么时候(重复多少次这个方法)牌才可以说是"洗好了".

stopping rules 是什么意思? 指的就是停止洗牌的规则, 满足什么条件我们就停止重复洗牌.

![](https://i.imgur.com/57SI38i.png)

书里给出了 Top-in-at-random shuffles 的一个 stopping rule. 当开始洗牌之前牌堆最下面的一张牌被重新插入到牌堆内的时候停止.

这很有意思, 假设洗牌之前整个牌组的顺序是1,...,n, 我们可以发现进行任意次数的 Top-in-at-random shuffle 之后, 位于n下面的牌的所有排列出现的概率是相等的, 可以简单的归纳出. 因此当所有牌都位于n下面的时候,我们再把n这张牌用Top-in-at-random shuffle插入到牌堆, 牌堆的所有排列出现的概率都是$1/n!$. 

下面我们想知道stopping rule满足的时候大概使用了多少次Top-in-at-random shuffle.

哦, 好巧, 如果把这个过程反过来, 也就是最开始n在排队顶部, 每次在牌堆里随机抽一张牌扔到牌堆顶, 这就是刚刚计算过的coupon collector 问题. 直接使用上面的结果, 我们就知道如果shuffle的次数大于 $k=\lceil{n\log n+cn}\rceil$ 的概率是小于 $e^{-c}$的.

![](https://i.imgur.com/f4Q5E9V.png)

注意证明当中的第三个等号, 这是由于满足了stopping rule之后得到的$x_j$就一定是uniform distribution了.

用这个lemma我们能得到

Top-in-at-random shuffles 加入上面说的stopping rule之后跑$k=\lceil{n\log n+cn}\rceil$轮, 对均匀分布的variation distance 小于$e^{-c}$.

## Riffle shuffles

这就是赌场当中的洗牌方法, 大概是把初始的牌堆分成两份,放在左右手中, 然后每次可以选择把左手或者右手的牌堆最下面的一张放到新的牌堆顶部.

![](https://i.imgur.com/qlnB8wO.png)

一次riffle shuffle可能会产生多少种不同的排列?

假设左手拿了t张, 右手就有n-t张, 下面把两个序列组合起来, 相当于在n个位置里面选出t个来, 然后把左手的序列按顺序插入, 右手的n-t张也按顺序放入剩下的n-t个位置, 所以一共$\binom{n}{k}$. k可以取0到n, 这n+1个情况当中都有一个是和'原来的排列'是一样的, 因此一共有$2^n-n$个排列. (我建议读下去之前想一想为什么对于不同的k没有其他重复的排列)

>However, the following model, developed first by Edgar N. Gilbert and Claude Shannon in 1955 (at the legendary Bell Labs “Mathematics of Communication” department at the time), has several virtues:
>
> - it is elegant, simple, and seems natural, 
> - it models quite well the way an amateur would perform riffle shuffles,
> - and we have a chance to analyze it.

下面要说的就是进行一次 riffle shuffle 之后得到的分布是什么样子的.

Gilbert 和 Shannon 假设, 第一步从牌堆当中拿出上面t张牌的时候, 概率是$\frac{1}{2^n}\binom{n}{k}$. 这很听起来很合理. 拿到n/2 左右的数字的概率要大于拿很少或者很多牌的概率. 之后把t张牌放到右手, n-t张牌放到左手. 当右手有r张牌,左手有l张牌的时候, 我们按照概率$l/(l+r)$和$r/(l+r)$把左手和右手的最下面的一张牌放下. 这听起来也很合理...

#### 进行一次riffle shuffle之后得到原来的排列的概率是多少?

对于固定的k, 我们需要让右手的t张牌都是后放下的.

$$
P=\sum_{k=0}^n \frac{1}{2^n}\binom{n}{k}\frac{k!}{(n-k)!}=\frac{n+1}{2^n}
$$

#### 进行一次riffle shuffle之后得到其他某种排列的概率是多少?

还剩下$2^n-n-1$个不同的排列. 按照上面的假设, 我们知道这些剩下的排列出现的概率是相等的. 因为每个位置取左手牌堆当中的牌还是右手牌堆当中的牌的概率相等, 所以在选定k的条件下进行 riffle shuffle 得到的排列对应的概率都是相等的.

他们都是$\frac{1}{2^n}$

#### strong uniform stopping rule

这个规则是, 每次洗牌的时候首先要把前面k张牌分到右手, 后面n-k张牌放到左手, 此时我们就在左右手的卡片上分别写下`0`和`1`, 不断重复 riffle shuffle 会让每个卡片上写下一个`0/1`构成的二进制串, 当所有卡片上的字符串都不同的时候我们停止重复 riffle shuffle.

我们考虑 riffle shuffle 的逆过程. 上面我们说过, 按照 Gilbert 和 Shannon 的假设, 给定k, 一个位置是放上左手的卡片还是放上右手的卡片的概率是相等的, 也就是说, 卡片上写下`0`或者`1`的概率是相等的. 通过上面的图我们观察到, 如果把k次riffle shuffle反过来做, 最后得到的初始牌堆的排列, 卡牌上面写的二进制串是有序的. 因此我们可以这样理解, 每一轮都给所有卡牌上的二进制串加一位,50%的概率是`0`, 50%的概率是`1`, 等到所有卡牌上的二进制串都不同的时候, 我们按照二进制串来给卡牌排序, 得到所有排列的概率都一样. 因为这个二进制串对于每个卡牌来说都是perfectly random and independent.

#### T

最后, 我们还想知道进行多少次 riffle shuffle 之后才满足了stopping rule. 也就是说多少次shuffle会让卡牌上的二进制串都不同?

终于用到了开头提到的生日悖论.

$$
\text{Prob}[T>k]=1-\prod_{i=1}^{n-1}(1-\frac{i}{2^k})
$$