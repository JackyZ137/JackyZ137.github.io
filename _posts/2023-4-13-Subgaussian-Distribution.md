---
layout: post
title:  Concentrated Differential Privacy Ⅰ——从Subgaussian Distribution开始
date:   2023-4-13 
description: Concentrated Differential Privacy介绍.
tags: [Differential Privacy, Math]
author: 卧龙岗散人
github:  JackyZ137
cover: subgaussian.jpg
---

在介绍Concentrated Differential Privacy(此后记作CDP)之前，首先需要介绍一个重要的概念——Subgaussian Random Variables（记作Subgaussian RV)。本质上来说，CDP是认为Privacy Loss这一随机变量满足Subgaussian Distribution的，在此基础之上，引出的CDP的定义。因此我们首先需要研究一下什么是Subgaussian RV。

Subgaussian RV其实是从高等概率论中引申出来的一个广义分布，其定义如下所示：

**Definition.1.** 我们说一个实值的随机变量$X$是服从Subgaussian分布的，若它具有以下的性质：

$$
\forall t \in R, \exists b>0, \ s.t. \
E[e^{tX}] \leq e^{\frac{b^2 t^2}{2}} = exp \{ \frac{b^2 t^2}{2} \}
$$

该定义表明，一个随机变量$X$是$Subgaussian$的条件是，存在一个正数$b$，$X$的Laplace变换被一个均值为0，方差为$b^2$的高斯随机变量的Laplace变换控制。相应的，我们称满足上述条件的随机变量是$b-Subgaussian$ 的。

对于一个随机变量，我们自然而然地会想到两件事情：它的期望是多少？它的方差是多少？下面的Proposition给出了答案。

**Proposition.1.** 若$X$是$b-Subgaussian$的，那么它的期望$E[X]=0$，它的方差$Var(X) \leq b^2$。

*Proof:*  首先根据Subgaussian的定义，进行泰勒展开，可以得到：

$$
\sum\limits_{n=0}^{\infty} \frac{t^n}{n!} E[X^n] = E[e^{tX}] \leq e^{\frac{b^2 t^2}{2}} = \sum\limits_{n=0}^{\infty} \frac{b^{2n} t^{2n}}{2^n n!}
$$

因此，泰勒展开之后，取$o(t^2)$改写

$$
E[X] \cdot t + E[X^2] \cdot \frac{t^2}{2!} \leq \frac{b^2 t^2}{2} + o(t^2) \qquad as \quad t \rightarrow 0.
$$

$$
\begin{aligned}
当t \rightarrow 0^+ 时，&E[X] \leq 0 ；当t \rightarrow 0^-时，E[X] \geq 0。\\
&\therefore \quad E[X]=0 \quad when \quad t \rightarrow 0 。 \\
&\therefore \quad E[X^2] \cdot \frac{t^2}{2} \leq \frac{b^2 t^2} + o(t^2)。 \\
\therefore E[X^2] \leq b^2 \quad &\therefore Var(X) = E[X^2] - \left( E[X] \right)^2 \leq b^2。
\end{aligned}
$$

在知道了Subgaussian RV的期望和方差之后，我们又会自然产生一个问题：有哪些类型的随机变量也是满足Subgaussian的条件的呢？下面我们给出满足Subgaussian条件的随机变量的例子。第一个例子就是常见的中心化的Gaussian随机变量。

**Example.1.(Centered Gaussian)** 

若$X\sim \mathcal{N}(0,\sigma^2)$ ，那么$ E[e^{tX}] = e^{\frac{\sigma^2 t^2}{2}}$ ，显然正态随机变量$X$是满足$\sigma-subgaussian$的。

**Example.2.(Rademacher)**

若$X$是服从Rademacher distribution的，即$X$具有这样的概率质量：

$$
P[X = x] = \frac{1}{2}\delta^{-1} + \frac{1}{2}\delta \qquad 其中\delta(x)是x处的pdf（概率密度函数）
$$

根据其概率质量，我们可以得到如下不等式：

$$
E[e^{tX}] = \frac{1}{2}e^{-t}+ \frac{1}{2}e^t = \cosh t \leq e^{\frac{t^2}{2}}
$$

最后一个不等式可以通过泰勒展开很容易得到。因此我们可以发现Rademacher的随机变量是$1-subgaussian$的。

**Example.3.** $X$是$[-a, a]$上的均匀分布，其中$(a > 0)$。

那么$P[X=x] = \frac{1}{2a} \mathbb{I}_{[-a,a]} \cdot \lambda$，其中$\lambda$是$[-a, a]$上的Lebesgue测度。

$$
\begin{aligned}
E[e^{tX}] &= \frac{1}{2a} \int_{-a}^{a} e^{tX} dx = \frac{1}{2at} \left[ e^{at} - e^{-at} \right] \\
&= \sum\limits_{n=0}^{\infty} \frac{(at)^{2n}}{(2n+1)!} \leq \sum\limits_{n=0}^{\infty} \frac{(at)^{2n}}{n! \cdot 2^n} \\
&= \sum\limits_{n=0}^{\infty} \frac{(\frac{a^2t^2}{2})^n}{n!} = e^{\frac{a^2t^2}{2}}
\end{aligned}
$$

最后一个不等式是使用了$(2n+1)! \geq n! \cdot 2^n$推出的，因此$X$是$a-$subgaussian的。

看了这些例子之后，一个自然的问题是，究竟什么样的随机变量是subgaussian的呢？事实上，任何一个中心化且有界的随机变量都是subgaussian的，下面的定理可以解释其原因：

**Theorem.1.** 若$X$是一个随机变量，且$E[X] =0, \lvert X \rvert \leq 1, a.s.(almost \, sure, 意味着成立的概率为1）$，那么：

$$
\begin{equation}
E[e^{tX}] \leq \cosh t \quad \forall t \in R.
\end{equation}
$$

同时，$X$是1-subgaussian的。除此以外，若（1）中的不等式对某些$t \neq 0$也成立，那么$X$是Rademacher随机变量，并且（1）对任意的$t\in R$均成立。

*Proof:* 

$$
\begin{aligned}
&定义R上的一个函数f：f(t):=e^t[\cosh t - E[e^{tX}]] \\
&\therefore f(t) = \frac{1}{2}e^{2t} + \frac{1}{2} - E \left[e^{t(1+X)} \right]\\
&设Y = 1+ X \quad \Longrightarrow \quad f(t) = \frac{1}{2}e^{2t} + \frac{1}{2} - E \left[e^{tY} \right] \\
&\therefore f^{\prime}(t) = e^{2t} - E\left[ Ye^{tY} \right] \\
&由E[Y] = 1 (\because E[X] = 0) \quad \Longrightarrow \quad f^{\prime}(t) = E \left[Y(e^{2t} - e^{tY}) \right] \\
&\because |X| \leq 1 \quad \Longrightarrow \quad 0 \leq Y \leq 2 \quad a.s. \\
&\therefore f(t)在[0, + \infty)上单调递增，\quad \therefore f(t) \geq f(0) = 0 \\
&同理，t \leq 0 \Rightarrow Y(e^{2t}-e^{tY}) \leq 0 \quad \Longrightarrow \quad f^{\prime}(t) \leq 0 \quad when \quad t \leq 0 \\
&\therefore f(t)在(-\infty, 0]上单调递减， \quad \therefore f(t) \geq f(0) = 0 \\
&\therefore (1)对所有的 t \in R都成立，下证明X是Rademacher随机变量\\
\\
&现假设(1)中取等号对某些t_0 \geq 0 成立，那么f(t_0) = f(0) = 0。\\
&\therefore f(t) = 0 \quad \forall t \in [0, t_0]成立（由前述单调性可知）。\\
&\therefore f^{\prime}(t) = 0 \Rightarrow Y(e^{2t_0}-e^{t_0Y}) \quad a.s. \quad （解出Y=0或Y=2, a.s.）\\
&\Rightarrow P[Y=0]+P[Y=2]=P[X=-1]+P[X=1]=1， 由于E[X] = 0。\\
&\rightarrow P[X=-1]=P[X=1]=\frac{1}{2} \quad \therefore X是一个Rademacher随机变量。\\
&同理可证t_0 \leq 0情形。\\
&证毕。
\end{aligned}
$$

由**Theorem.1**可以立刻得到一个直接的推论：

**Collary.1.** 若$E[X]=0$，且$\lvert X \rvert\leq b, \ a.s.$，对某些$b > 0$成立，那么$ X$是b-subgaussian的。

知道了什么样的随机变量满足subgaussian之后，和学习其他随机变量一样，我们希望知道两个满足subgaussian的随机变量相加之后是否仍然满足subgaussian，下面的定理证明，两个随机变量的和仍是subgaussian的。

**Theorem.2.** 

设$X$是b-subgaussian的，若$\forall \alpha \neq 0$，随机变量$\alpha X$是$\lvert \alpha \rvert b$-subgaussian的。 

若$X_1, X_2$是$b_i$-subgaussian的$(i=1,2)$，那么$(X_1+X_2)$是$(b_1+b_2)$-subgaussian的。

*Proof:* 

$$
\begin{aligned}
&设X是b-subgaussian的，那么\forall \alpha \neq 0， 有：\\
&E \left[ e^{t(\alpha X)} \right] \leq e^{\frac{b^2 \alpha^2 t^2}{2}} = e^{\frac{(| \alpha | b)^2 t^2}{2}}，第一条证毕。\\
\\
&现设X_i是b_i-subgaussian的，i=1,2.\\
&\forall p,q > 1, \frac{1}{p} + \frac{1}{q} = 1，使用Holder不等式:\\
&\begin{aligned}
E[e^{t(X_1+X_2)}] &\leq \left( E[ \left(e^{tX_1} \right)^p ] \right)^{\frac{1}{p}} \cdot \left( E[ \left(e^{tX_1} \right)^q ] \right)^{\frac{1}{q}} \\
&\leq \left( e^{p^2 b_1^2 t^2}\right)^{\frac{1}{p}} \cdot \left( e^{q^2 b_2^2 t^2}\right)^{\frac{1}{q}} \\
&= exp \left\{ \frac{t^2}{2} (pb_1^2 + qb_2^2) \right\} \\
&=exp \left\{\frac{t^2}{2} (pb_1^2 + \frac{p}{p-1} b_2^2) \right\} \\
\end{aligned} \\
&当p > 1时，最小化上式，可以得到： p=\frac{b_2}{b_1} + 1时最小\\
&E[e^{t(X_1+X_2)}] \leq exp \left\{ \frac{t^2}{2} (b_1^2 + b_2^2) \right\}，第二条证毕。
\end{aligned}
$$

正如定理中所指出的，Subgaussian Distribution具有更丰富的结构。

给定一个Centered随机变量，$X$的subgaussian moment（亚高斯矩）记作$\sigma(X)$：

$$
\sigma(X) = \inf \left\{ b \geq 0|E[e^{tX}] \leq e^{\frac{b^2 t^2}{2}}, \forall t \in R \right\}.
$$

显然，$X$是subgaussian的，$iff. \quad \sigma(X) \leq \infty$。

此外，$\sigma(\cdot)$是Subgaussian随机变量空间上的一个范数，且该赋范空间完备。（看不懂可以忽略）

现在我们知道了Subgaussian随机变量应该满足什么条件，也知道了有哪些随机变量是Subgaussian的；同时，我们还知道了满足什么条件的随机变量一定是Subgaussian的，并且还知道了两个Subgaussian随机变量的和也是Subgaussian的。我们现在关心的问题是，Subgaussian随机变量有哪些性质可以供我们以后使用？下面的定理给出了Subgaussian随机变量的一些等价关系，其中揭示了它的一些较好的性质：

**Theorem.3.**(Subgaussian的等价关系)

设$X$是一个随机变量，则下列陈述是等价的；每条陈述之间的参数$K_i$互相之间至多相差一个常数倍。

1. 随机变量$X$的tails（尾）满足：

$$
P[|X| \geq t] \leq 2 \cdot e^{-\frac{t^2}{K_1^2}} \quad \forall t \geq 0;
$$

2. 随机变量$X$的矩满足：

$$
\| X \|_{L^p} = \left( E[|X|^p] \right)^{\frac{1}{p}} \leq K_2 \sqrt{p} \quad \forall p \geq 1;
$$

3. $X^2$的矩母函数MGF满足：

$$
E \left[ e^{\lambda^2X^2} \right] \leq e^{K_3^2 \lambda^2} \quad \forall \lambda \ s.t. \ |\lambda| \cdot K_3 \leq 1;
$$

4. $X^2$的矩母函数有界：

$$
E \left[ e^{\frac{X^2}{K_4^2}} \right] \leq 2;
$$

若$E[X] = 0$，那么上述1-4条与下面的第5条也是等价的：

5. $X$的矩母函数满足：

$$
E \left[ e^{\lambda X} \right] \leq e^{K_5^2 \lambda} \quad \forall \lambda \in R.
$$

*Proof:* 

$$
\begin{aligned}

&\begin{aligned}
&1 \Rightarrow 2:\\
&由Layer \ Cake \ Representation \ 有 \\
&\begin{aligned}
E \left[ |X|^p \right] &= \int_0^{\infty} P \left[ |X|^p > \mu \right]d \mu \quad \mu \Longleftrightarrow t^p \\
&= \int_0^{\infty} P \left[ |X|^p > t^p \right]d t^p = \int_0^{\infty} pt^{p-1} P[|X| > t]dt \\
由条件1 & \leq \int_0^{\infty} pt^{p-1} \cdot 2e^{-\frac{t^2}{K_1^2}}dt \quad 令t^2 \Longrightarrow s,\ 取K_1 = 1 \\
&=\int_0^{\infty}ps^{\frac{p}{2}-1} \cdot e^{-s}ds = p \cdot \Gamma(\frac{p}{2}) \quad \because \Gamma(x) \leq 3x^x, \ (x \geq \frac{1}{2}) \\
&\leq 3p \cdot \left( \frac{p}{2} \right)^{\frac{p}{2}} \leq 3 \cdot \left(\frac{p}{2} \right)^{\frac{p}{2}} \\
\therefore \left( E \left[|X|^p \right] \right)^{\frac{1}{p}} &\leq 3^{\frac{1}{p}} \cdot \left( \frac{p}{2} \right)^{\frac{1}{2}} \leq \frac{3}{\sqrt{2}} \sqrt{p} \quad 取K_2 = \frac{3}{\sqrt{2}}即得证。
\end{aligned}
\end{aligned} \\
\\
&\begin{aligned}
&2 \Rightarrow 3:\\
&由2，\ E \left[ e^{\lambda^2 X^2} \right] = E \left[ 1 + \sum\limits_{n=1}^{\infty} \frac{(\lambda^2 X^2)^n}{n!} \right] = 1 + \sum\limits_{n=1}{\infty} \frac{\lambda^{2n} \cdot E[X^{2n}]}{n!} \\
&由2，\ E[X^{2n}] \leq (2n)^n, \\
& \therefore E \left[e^{\lambda^2 X^2} \right] \leq 1 +\sum\limits_{n=1}^{\infty} \frac{\lambda^{2n} \cdot (2n)^{n}}{ \left( \frac{n}{e} \right)^n} = \sum\limits_{n=0}^{\infty} (2 \lambda^2 e)^n \\
&当2e \lambda^2 < 1时，\ \Rightarrow E\left[ e^{\lambda^2 X^2}\right] \leq \frac{1}{1-2e \lambda^2}\\
&更严格的bound: 当 0 < 2e \lambda^2 < \frac{1}{2}时， \frac{1}{1-x} \leq e^{2x}，\\
& \Rightarrow E \left[e^{\lambda^2 X^2} \right] \leq e^{4e \lambda^2}, \quad \forall \quad \lambda \leq \frac{1}{2 \sqrt{e}}, \ K_3 = 2 \sqrt{e}.
\end{aligned} \\
\\

&\begin{aligned} 
&3 \Rightarrow 4: \\ 
&令3中 |\lambda| \cdot K_3 \leq \sqrt{\ln 2}即可得证。
\end{aligned}\\
\\
&\begin{aligned}
&4 \Rightarrow 1: \\
&取K_4 = 1, \ E[e^{X^2}] \leq 2.\\
&由Markov不等式：P[|X| > t] = P[e^{X^2} > e^{t^2}] \leq \frac{E[e^{X^2}]}{e^{t^2}} \leq 2e^{-t^2} \\
&取K_1 = 1即可成立。
\end{aligned}\\
\\
&\begin{aligned}
&3 \Rightarrow 5: \\
&使用不等式：e^{x} \leq x + e^{x^2}. \\
& \Rightarrow E[e^{\lambda X}] \leq E[\lambda X + e^{\lambda^2 X^2}] = E[\lambda X] + E[e^{\lambda^2 X^2}] \leq e^{\lambda^2} \quad (|\lambda| \leq 1) \\
&当|\lambda| \geq 1 时，使用不等式 2 \lambda X \leq \lambda^2 + X^2 \\
&\begin{aligned}
\Rightarrow E[e^{\lambda X}] &\leq E \left[e^{\frac{\lambda^2}{2}+\frac{X^2}{2}} \right] = e^{\frac{\lambda^2}{2}} \cdot E \left[\frac{X^2}{2} \right] \\
由3, & \leq e^{\frac{\lambda^2}{2}} \cdot e^{\frac{1}{2}} \quad 取|\lambda| \cdot K_3 \leq \frac{1}{2} \qquad (K_3 = \frac{1}{2}, \lambda = 1即满足) \\
& \leq e^{\lambda^2}
\end{aligned}
\end{aligned} \\
\\
&\begin{aligned}
&4 \Rightarrow 1: 由Markov不等式：\\
&P[X > t] = P[e^{\lambda X} > e^{\lambda t}] \leq \frac{E[e^{\lambda X}]}{e^{\lambda t}} \\
&取5中K_5 = 1, \ 即E[e^{\lambda X}] \leq e^{\lambda^2} \\
&\therefore P[X > t] \leq e^{-t \lambda + \lambda^2} \quad 对右式最小化，取\lambda = \frac{t}{2}\\
&\Rightarrow P[X > t] \leq e^{-\frac{t^2}{4}}. \\
&同理 \ P[X < -t] = P[-X > t] = P[e^{-\lambda X} > e^{\lambda t}] \leq \frac{E[e^{-\lambda X}]}{e^{\lambda t}} \leq e^{-t \lambda + \lambda^2} \leq e^{-\frac{t^2}{4}}. \\
&\therefore 综上所述，可得P[|X| \geq t] \leq P[X > t] + P[X < -t] = 2e^{-\frac{t^2}{4}}
\end{aligned} \\
\\
&证毕
\end{aligned}
$$

## References

1. O. Rivasplata, 《Subgaussian random variables: An expository note》.

2. P. Rigollet, 《Chapter 1: Sub-Gaussian Random Variables》.

3. R. Vershynin, 《High-Dimensional Probability》.
