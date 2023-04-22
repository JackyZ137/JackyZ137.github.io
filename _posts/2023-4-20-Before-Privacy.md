---
layout: post
title:  Before Privacy
date:   2023-4-20 
description: Privacy先导篇
tags: [Privacy, Math]
cover: nonprivacy.jpg
author: 卧龙岗散人
github:  JackyZ137
---

> **题记：**
> 
> <center>愿君学长松，慎勿作桃李</center>
> 
> <center>受屈不改心，然后知君子</center>



在市面上，有非常多关于隐私的定义，比如我最近做的差分隐私的定义，是指无论攻击者的背景知识（即除了被保护信息外的所有被攻击者掌握的信息）几何，都无法通过统计查询或者子集查询获得被保护的任何信息，称为隐私被保护了。更古早的定义，例如K-anonymity，是指相同的敏感信息在同一数据库中至少有k条，则称为隐私被保护了。还有从博弈论角度出发的，也就是破解某个隐私信息的花销超过了与其所带来的收益，则称为隐私被保护了。

凡此种种，不一而同。

实际上，每一种角度出发定义的隐私被“保护”了都有其自身的含义，称不上错误，只能说角度不同。因此，我们很难给隐私被保护这件事情一个确切的、能够包容万象的定义。

我们首先对所有的数据隐私进行总结，大部分的隐私定义中，隐私保护都接近于在两个函数之间的一个Game。一个是Private Function，也就是我们希望保护的部分；另一个是“Information function”是我们希望去披露的那部分。在大部分的隐私定义中，private function往往是代表数据库中某一条目，即若攻击者有能力对数据库中特定条目的特定属性计算出一个可信范围内的值时代表用户的隐私被侵犯了；而在统计数据库的隐私的背景下，information function的意义往往是指数据库中某个满足条件的子集的总和。公式化的定义如下：

假设数据库为$d=(d_1,d_2, \dots, d_n)$,则:

1. private function： $\{\pi_i \}_{i \in [n]}:= \pi_i(d_1,\dots,d_n) = d_i$;
   2. information function： $\{f_q \}_{q \subseteq [n]}:= f_q(d_1,\dots,d_n) = \sum_{i\in q} d_i$

基于这两个function的隐私定义，往往是从在查询的响应中添加较大的方差或者添加某些估计量的方差来达到目的。然而这样的方法有两个局限性：首先是不知道添加多少方差才能真正阻止隐私泄露，其次是这些定义也不允许考虑对攻击者的限制。比如说，如何去界定攻击者掌握了多少对隐私信息的背景知识是很难去判断的。

一种可行的刻画攻击者先验知识的方案，是假设数据库是某些由$\{0,1\}^n$形成的二进制串上的分布，没有任何背景知识可以认为攻击者对这些分布的描述是服从均匀分布的。

既然对隐私的正面定义存在诸多争议和困难，那么我们应该反其道而行之，考虑什么样的算法在任何隐私定义中都应该被认定为Non-privacy的。我们先给出讨论这个问题的一些设定和记号。

考虑一个如下表述的统计数据库：数据库$d=(d_1,d_2,\dots,d_n) \in \{0,1 \}^n$，一个查询$q \subseteq [n]$的响应记作$\mathcal{A}(q)$。我们允许数据库对它的响应做出一些扰动，但仍可以对某些有价值的二进制为保持一定的有意义的信息——也就是说，允许$|\mathcal{A}(q)-\sum_{i\in q}d_i| \geq 0$。

**Definition.1.**（统计查询-Statistical Query)

设数据库$d = (d_1,d_2,\dots,d_n) \in \{0,1\}^n$。一个统计查询（查询）是指一个子集$q \subseteq [n]$。对该查询$q$的准确回答是所有由该子集$q$指定的数据库中条目的总和，也就是$a_q = \sum_{i\in q} d_i$。

一个统计数据库(statistical database, or database)$\mathcal{D} = (d, \mathcal{A})$是一个查询-响应机制，其针对特定查询$q$的响应记作$\mathcal{A}(q,d,\sigma)$。其中，$\sigma$是算法$\mathcal{A}$的一个中间状态（受到计算过程的影响，有时候扰动也是在这一步完成的）。有时也会直接简写为$\mathcal{A}(q)$。

**Definition.2.**（算法$\mathcal{A}$的品质）

称一个响应$\mathcal{A}(q)$是在$\mathcal{E}$扰动之内的，若$|a_q - \mathcal{A}(q)| \leq \mathcal{E}$。

我们说一个算法$\mathcal{A}$是在$\mathcal{E}$扰动之内的，若对所有查询$q \subseteq [n]$都有上式成立。

ps:一定要注意，$q$是一个子集，实际上也就是一个查询中需要的子集${d_i}$的指标集！

我们记$\mathcal{M}^{\mathcal{A}}$为一个对数据库拥有算法$\mathcal{A}$访问权限的图灵机，其每次调用算法$\mathcal{A}$都花费一个单位时间。

# Non-Privacy

一个数据库的算法$\mathcal{A}$是non-privacy的，意味着攻击者有能力通过有限复杂度的查询获得的响应$\mathcal{A}(q)$来重建一个一个近似准确的相同数据库。因此Non-privacy的定义应如下所示：

**Definition.3.**（Non-privacy）

一个数据库$\mathcal{D}=(d,\mathcal{A})$是$t(n)$-non-private的，若对任意常数$\epsilon > 0$，存在一个概率图灵机$M$，和时间复杂度$t(n)$使得

$$
Pr[\mathcal{M}^{\mathcal{A}}(1^n) 输出 c 使得 \quad dist(c,d) \leq \epsilon n]\geq 1-neg(n).
$$

其中$neg(n)$代表一个比任意n的多项式的倒数小的函数（大致理解为小于$\frac{1}{n^r}$，r是任意的常数），$dist(c,d)$是指$c,d \in \{ 0,1 \}^n$的汉明距离（即c和d有多少位是不同的）。

## 指数级攻击者

在这种设定下的攻击可能对数据库发起所有可能的查询，在这种情况下任何小于$o(n)$扰动的算法$\mathcal{A}$都是non-private的。

**Theorem.1.** 数据库$\mathcal{D} = (d,\mathcal{A})$中的算法的扰动是小于$o(n)$的，那么这种情况下的$\mathcal{D}$是$exp(n)$-non-private的。

_Proof:_ 

设$\mathcal{A}$是$\mathcal{E}$扰动内的，其中$\mathcal{E}=o(n)$的。设计$\mathcal{M}$是如下算法：

> [QUERY PHASE]
> 
> 对任意的$q \subseteq [n]$：设$\tilde{a}_q \leftarrow \mathcal{A}(q)$。
> 
> [WEEDING PHASE]
> 
> 对所有的$c \in \{0,1 \}^n$：若$|\sum_{i\in q}c_i - \tilde{a}_q| \leq \mathcal{E}$对所有的$q\subseteq [n]$都成立，则输出c并且停止。

容易验证$\mathcal{M}$是运行在指数时间内的。并且应该注意到$\mathcal{M}$总是会停止并且输出一些备选的数据库c——因为真实数据库d永远不会被$\mathcal{M}$排除出去。（这一部分是说明算法一定是有输出的，并且运行时间为指数级）

下面说明该算法输出的备选数据库c是满足$dist(d,c) \leq 4\mathcal{E}=o(n)$。我们采用反证法来说明。

假设输出的数据库c是满足$dist(d,c) > 4\mathcal{E}$的。我们取这样两个子集$q_0 = \{i \mid d_i = 1, c_i = 0 \}$和$q_1 = \{ i \mid d_i = 0, c_i = 1\}$。

由于$|q_0|+|q_1| = dist(d,c) >4\mathcal{E}$，因此这两个不想交集中至少有一个是大于$2\mathcal{E}+1$的。

不失一般性，我们假设$|q_1|>2\mathcal{E}$。显然，容易得到$\sum_{i \in q_i}d_i = 0$。

由于$\mathcal{A}$的扰动被限制在$\mathcal{E}=o(n)$内，因此，根据$|\sum_{i\in q_1}d_i - \tilde{a}_{q_1}| \leq \mathcal{E}$，

显然可以得到$\tilde{a}_{q_1} \leq \mathcal{E}$。另一方面，由于$\sum_{i \in q_1}c_i = |q_1| > 2\mathcal{E}$，

可以推出$|\sum_{i \in q_1} c_i - \tilde{a}_{q_1}| > \mathcal{E}$，而这与我们在WEEDING PHASE中的设定是矛盾的。

因此$dist(d,c) \leq 4\mathcal{E}=o(n)$。                                                                                                $\square$

## 多项式级攻击者

显然上述的指数级攻击者在现实中并不是太常见，下面我们来考虑一个更实际的多项式级攻击者。顾名思义，也就是攻击者的查询复杂度被一个多项式给bound住，在此种情况下，最小的扰动需要满足$\Omega(\sqrt{n})$的界限，更具体的说，任何在$\mathcal{E} = o(n)$内的数据库扰动算法都是non-private的。下述定理的证明，主要是通过构造一个线性规划算法来证明，在多项式时间内，该算法重构的备选数据库c与原数据库相差在一定范围之内。

**Theorem.2.** 设数据库$\mathcal{D} = (d,\mathcal{A})$，其中$\mathcal{A}$是在$o(\sqrt{n})$之内的扰动，那么这种情况下的$\mathcal{D}$是$poly(n)$-non-private的。

_Proof:_ 

设$\mathcal{A}$的扰动在$\mathcal{E}$之内，其中$\mathcal{E} = o(\sqrt{n})$。设任意的$\epsilon > 0$，我们通过构造如下算法，使得其输出的备选数据库c满足$dist(c,d) < \epsilon n$：

> [QUERY PHASE]
> 
> 设$t = n(\log n)^2$，对$1 \leq j \leq t$，按照均匀分布随机选取$q_j \subseteq_R [n]$，并且设$\tilde{a}_{q_j}\leftarrow \mathcal{A}(q_j)$。
> 
> [WEEDING PHASE]
> 
> 按如下线性规划算法求解未知数$c_1,c_2,\dots, c_n$：
> 
> $$
> \begin{align}
\tilde{a}_{q_j} - \mathcal{E} \leq &\sum_{i\in q_j} c_i \leq 
\tilde{a}_{q_j} + \mathcal{E} \quad &for \ 1 \leq j \leq t \\
&0 \leq c_i \leq 1 &for \ 1 \leq i \leq n
\end{align}
> $$
> 
> [ROUNDING PHASE]
> 
> 若$c_i > \frac{1}{2}$，则取$c_i^{\prime} = 1$；反之，取$c_i^{\prime} = 0$。
> 
> 输出$c^{\prime}$。

上述线性规划算法同样是总是有解的，因为真实的数据库$d$永远不会被WEEDING PHRASE筛选出去。下面我们证明$dist(c^{\prime},d) < \epsilon n$，我们用概率的方法来证明一个随机选取的查询序列$q_1,q_2,\dots,q_t$会筛除掉所有与原始数据库相差较大的可能的备选数据库。

固定一个精度参数$k = n$，同时定义$K = \{0, \frac{1}{k},\frac{2}{k},\dots,\frac{k-1}{k},1 \}$。对任意的$x \in [0,1]^n$，我们记向量$\bar{x} \in K^n$：$\bar{x}$中的每个坐标的选取，是选取$K$中离$x$中对应位置的坐标值最近的元素值组成的。采用三角不等式，我们可以得到：

$$
\begin{align}
|\sum\limits_{i\in q_j} &(\bar{c}_i -d_i)| \leq \\
&|\sum\limits_{i\in q_j} (\bar{c}_i -c_i)|+
|\sum\limits_{i\in q_j} (c_i -\tilde{a}_{q_j})|+
|\sum\limits_{i\in q_j} (\tilde{a}_{q_j} -d_i)| \leq \\
&\frac{|q_j|}{k}+\mathcal{E}+\mathcal{E}\leq 1+2\mathcal{E}.
\end{align}
$$

对任意的$x\in[0,1]^n$，我们说一个查询$q\subseteq [n]$disqualify $\bar{x}$，若$|\sum_{i\in q}(\bar{x}_i - d_i)| \geq 2\mathcal{E}+1$。若查询$q$恰是我们算法中的一个，那么这就说明$x$不是我们线性规划问题的解。下面证明，若$x$和$d$在很多坐标上相差很大，那么$\bar{x}$会被$q_1,q_2,\dots,q_t$中的至少一个disqualify。这部分的证明会用到如下的引理：

> *Lemma.1.*(Disqualifying Lemma)
> 
> 设$x,d \in [0.1]^n$，并且$\mathcal{E} = o(\sqrt{n})$。若$Pr_i[|x_i - d_i| \geq \frac{1}{3}] > \epsilon$，那么至少存在一个常数$\delta > 0$使得
> 
> $$
> \mathop{Pr}\limits_{q \subseteq_R [n]} \left[ 
\vert \sum\limits_{i\in q} (x_i -d_i) \vert > 2\mathcal{E}+1
 \right] > \delta.
> $$

定义一个集合，集合中的元素是所有与d相差很大的x：

$$
X = \left \{ x\in K^n \mid \mathop{Pr}\limits_i[\vert x_i - d_i \vert
\geq \frac{1}{3}] >\epsilon n \right \}.
$$

考察$x\in X$。根据*Lemma.1.*，存在一个常数$\delta > 0$，使得$Pr_{q\subseteq_R [n]}[q \ disqualify \ x] \geq \delta$。通过选取t个[n]上的独立的随机查询$q_1, \dots,q_t$，我们可以得到其中至少有一个disqualify x的概率为$1-(1-\delta)^t$。也就是说，对于$X$中的每个$x$，只有$(1-\delta)^t$这么小的比例选取到的$q_1,\dots,q_t$，其中任何一个都不会disqualify x。对$X$上的所有$x$去联合bound，注意到$\vert X \vert \leq \vert K \vert^n = (k+1)^n$，可以得到

$$
\mathop{Pr}\limits_{q_1,\dots,q_t \subseteq_R [n]}
[\forall x \in X \ \exists i, q_i \ disqualifies \ x] \geq
\\
1-(k+1)^n(1-\delta)^t > 1- neg(n).
$$

当t取足够大时，最后一个不等式成立（例如，$t = n(\log n)^2$)。

由于$c$是线性规划问题的解，因此根据$c$获得的向量$\bar{c}$是不会被任何我们算法中选取的$q_1, \dots,q_t \subseteq [n]$disqualify的。因此，若这些$q_1, \dots, q_t$disqualify了$X$中所有的x，那么也就说明$\bar{c} \notin X$，因此$dist(c^{\prime} , d) \leq \epsilon n$。

<p align = "right"> $\square$</p>

## Tightness of The Results

在这一部分中，主要说明上述针对多项式级攻击者的扰动界限是否足够紧。在这一章中，主要通过构造了一个$\tilde{O}(\sqrt{n})$扰动内的数据库算法，证明在最强的多项式攻击者的假设下，该算法仍然是Private的（也就是说，数据库不会泄露任何记录相关的信息），从而说明了Theorem3中提供的界限是足够紧的。

我们假设数据库是服从均匀分布的n个bits。设数据库$d\in_R \{0,1\}^n$。设扰动的规模上限$\mathcal{E} = \sqrt{n} \cdot (\log n)^{1+\epsilon} = \tilde{O}(\sqrt{n})$。考虑数据库(及其算法)$\mathcal{D} = (d,\mathcal{A})$，其中算法$\mathcal{A}$如下：

1. 每输入一个查询$q\subseteq [n]$，算法$\mathcal{A}$计算$a_q = \sum_{i \in q} d_i.$

2. 若$\vert a_q - \frac{|q|}{2}| < \mathcal{E}$，则$\mathcal{A}$输出$\frac{|q|}{2}$.

3. 否则$\mathcal{A}$返回$a_q$.

显然$\mathcal{A}$的扰动在$\mathcal{E}$之内的。而由于$\vert a_q \vert \leq |q|$，因此$\vert a_q - \frac{|q|}{2}| \leq \frac{|q|}{2} \leq \frac{n}{2}$。而当$n \geq 3$时，显然有$\frac{n}{2} \leq \sqrt{n} \cdot (\log n)^{1+ \epsilon} = \mathcal{E}$，因此$\mathcal{A}$几乎总是按照2来输出，几乎不可能按3输出。而3中的输出与数据库中的任何条目都无关，只与查询$|q|$有关，因此该算法不会输出数据库中的任何信息，是Private的。

<p align = "right"> $\square$</p>

**Remark**： 这里之所以使用$\tilde{O}(n)$是因为，要讨论$O(n)$是不是一个足够紧的上界，因此选取了一个对数增长率因子，来保证相对较紧（因为对数的增长率和其他超对数函数增长率来说非常平稳，尤其是当n足够大时，毕竟增长率为$\frac{1}{n}$。

## A Closer Look On the Threshold

显然在上一部分中构造的算法，不会输出数据库中的任何有效信息，但这也意味着这样的数据库算法几乎是无效的——用户不会期望采取这样的数据库做任何有价值的计算。因此，在这一部分中，我们主要是想说明，数据库算法在保持隐私性的同时，还可以保持数据库本身的一些重要信息。

为此，我们对**Definition.2.** 中的设定进行一些放松，也就是：

$$
\mathop{Pr}\limits_{q \subseteq [n]}[
    \mathcal{A}(q)的扰动在\mathcal{E}之内] = 1- neg(n).
$$

在此放宽的设定下，**Theorem.3.** 仍然成立。

现在我们假设$\mathcal{DB}$是一个$\{0,1 \}^n$上的均匀分布，并且$d\in_R \mathcal{DB}$。之前提到过，数据库算法在被调用过程中，可能采取某个中间状态$\sigma$来保持信息。假设数据库算法$\mathcal{A}$保持这样的一个中间状态：他在第一次被调用时进行初始化，并且不在更新他的状态，直至有新的条目被添加到数据库中。

设数据库算法$\mathcal{A}$的中间状态由这样n个bits组成：$d^{\prime} = (d^{\prime}_1, \dots, d^{\prime}_n)$，其中

$$
d^{\prime}_i = \Big \{
    \begin{array}
& d_i, \quad & w.p.\ 1+\delta \\
1-d_i, \quad & w.p. \ 1-\delta
\end{array} .
$$

而这样的数据库算法，当$\mathcal{A}$的扰动保持在$\tilde{O}(\sqrt{n})$内时，仍然有可能保持一些可用性。例如，可以计算一些[n]的子集查询，子集中半数以上的条目在原数据库中为1。

## 总结

本文主要是作为Differential Privacy（差分隐私）介绍系列开启的先导篇。本文的目的是在于打开思路，希望后续的博文不会引导人走向一个误区，就是这个世界上只有Differential Privacy是最完备的隐私定义（这也是笔者曾经走入过的误区）。隐私的定义在不同的场景中应该是不一样的，我个人认为在机器学习领域，也不应该局限于所谓的差分隐私化的机器学习。差分隐私的一系列研究，更多的是提供了一种隐私定义的思路，也就是从概率的方法来研判什么样的算法是保护了隐私，同时也从概率的角度来逼近隐私的边界。但差分隐私绝不是终点。

#Reference
[1] I. Dinur和K. Nissim, 《Revealing information while preserving privacy》, 收入 Proceedings of the twenty-second ACM SIGMOD-SIGACT-SIGART symposium on Principles of database systems, San Diego California: ACM, 6月 2003, 页 202–210. doi: 10.1145/773153.773173.
[2] http://www.gautamkamath.com/CS860-fa2020.html

# Appendix A

这篇附录主要是关于文中的大O表示法做一些说明：

1. $f(n) = o(g(n))$：
   $\forall k > 0, \exists n > n_0, \ s.t. \ |f(n)| < k \cdot g(n)$.
   Limit: $\lim\limits_{n \rightarrow \infty}\frac{f(n)}{g(n)} = 0$.
2. $f(n) = O(g(n))$：
   $\exists k > 0, \exists n_0, \forall n > n_0, \ s.t. \ |f(n)| \leq k \cdot g(n).$
   Limit：$\mathop{\lim \sup}\limits_{n \rightarrow \infty}\frac{f(n)}{g(n)} < \infty.$
3. $f(n) = \Theta(g(n))$：
   $\exists k_1 > 0 \ \exists k_2 > 0 \ \exists n_0 \ \forall n > n_0: \ k_1g(n) \leq f(n) \leq k_2g(n).$
   Limit:$f(n) = O(g(n))$ and $f(n) = \Theta(g(n)).$
4. $f(n) = \Omega(g(n))$：
   $\exists k >0 \ \exists n_0 \ \forall n > n_0: \ f(n) \geq kg(n).$
   Limit: $\mathop{\lim \sup}\limits_{n \rightarrow \infty} \frac{f(n)}{g(n)} > 0.$
5. $f(n) = \omega(g(n))$：
   $\forall k>0 \ \exists n_0 \ \forall n > n_0: \ f(n) > k \cdot g(n).$
   Limit: $\lim\limits_{n \rightarrow \infty} \frac{f(n)}{g(n)} = \infty.$
