---
layout: post
title:  OpenDP阅读记录
date:   2023-4-12 
description: OpenDP阅读过程中的代码块解释和英文说明文档翻译.
tags: [Differential Privacy, coding]
author: 卧龙岗散人
github:  JackyZ137
cover: open_dp.jpg
---


## Limitations

### Privacy Comcerns

1. **Floating point issues:** 一些OpenDP的Transformations和Measurements假设一个理想化的实算术模型（在DP论文中很常见）。他们在实施过程中由于采用的浮点数有一些已知的问题，由于实数算术和浮点数算术存在的差异性，这些实际实施不能满足差分隐私的性质。尽管正在进行一些隐私证明的审查工作，我们仍可以清晰区别这样的机制与那些具体实施准确满足差分隐私的机制。

2. **Side-channel attacks:** OpenDP还不能完全防御单通道攻击。这些攻击包括世纪攻击（timing attack）、缓存影响（cache effects）等。这些会使得攻击者有可能与一个运行OpenDP的软件交互从而通过预期接口以外的方式获取信息，潜在的威胁差分隐私。

### Incomplete Privacy Proofs（不完全的隐私证明）

OpenDP一个重要的元素就是一个所有library库组成都必须经历的正式审查过程，目的是为了验证他们的隐私特征。这个过程包括提供所有算法隐私性质的数学证明，并且验证代码是否真实地实施了对应的算法。

这个审查过程现在正在为OpenDP库中的代码进行。通过这个审查过程，我们希望可以揭露一些代码和证明中的bug，并且对库中code做出修改，保证他们可以满足特定的隐私相关的性质。一旦这些components完成了审查过程，他们就会去掉*contrib*的特征标签，并且将会是可获得的（不需要额外去选择未审查的components）。

### Missing Functionality

有一些OpenDP程序框架的元素（一些概念基础for OpenDP）还没有被实现。这些元素中最重要（最著名、最前Foremost）的就是交互度量的概念（有一个只在Rust中实现的初始原型，但还没有被完全充实）。这些元素已经在我们的计划之中了，但是现在的应用还必须等待一段时间才能使用它们。

### API Stability

OpendDP的接口仍处在不断变化之中，随时可能发生变动。我们在这些接口上迭代了很多次，感觉上一般形态都运行良好，但是可能许多细节会以向后不兼容的方式发生。这些早期的灵活性可以帮助OpenDP库的演进，我们很感激你们的理解和反馈。

当我们发布OpenDP 1.0.0之后，我们会开始在主要的版本中提供稳定的API接口。到那时，请不要依赖于那些在以往发布中保持的接口。

## Getting Started

### Installation

OpenDP是按照分层模型构建的。核心是由一个native的库组成，由Rust实现。在这个核心之上，是其他语言使用OpenDP的bindings。

首先需要安装OpenDP库才能使用OpenDP。

#### Platforms

1. Python 3.7-3.10

2. Linux，x86 64-bit，在manylinux上可编译（其他版本也许也可以）

3. macOS，x86 64-bit，10.13以后的版本可以

4. Windows，x86 64-bit，win7以后的版本可以

#### 使用pip安装OpenDP for Python

```powershell
pip install opendp
```

验证是否安装好：

```python
import opendp
```

### Hello，OpenDP！

一旦你安装好了OpenDP之后，你就可以开始编写第一个程序。

需要注意的是，现在审查过程仍然在进行。没有完成审查过程的代码会被标记为*contrib*，并且不会被运行除非你主动选择（opt in）。用以下方式使这些*contrib*代码可以全局运行：

```python
from opendp.mode import enable_features
enable_features('contrib')
```

在下面的例子中，我们建立了一个 *$\color{red} {Transformation}$* ，它是OpenDP中的一个对象，用于转换数据（in some way）。在这个例子中，展现出的操作是等值转换（identity transformation）——也就是说没有做任何转换。然后，我们将这个transformation应用到一个由字符串组成的向量上，然后返回一个该向量的复制。

```python
from opendp.transformations import make_identity
from opendp.typing import VectorDomain, AllDomain, SymmetricDistance
.....
identity = make_identity(D=VectorDomain[AllDomain[str]], M=SymmeticDistance)
identity(["Hello, world!"])
["Hello, world!"]
```

首先，我们导入一些类型使他们在范围内。``make_identity`` 是一个``constructor function``，从``opendp.typing``导入的内容对于消除transformation工作时使用的类型的歧义来说是很有必要的。

接下来，我们调用``make_identity()``函数来建立一个身份``Transformation``。因为OpenDP是一个静态类型（即使是从一个动态类型语言调用的，比如Python），我们需要去具体说明类型的信息。这些可以通过提供一些键值声明解决。

``D=VectorDomain[AllDomain[str]]``的意思是我们想要``Transformation``有一个输入和输出的Domain域，这个域由所有的字符串向量组成；而``M=SymmetricDistance``的意思是我们想要返回结果的``Transformation``使用OpenDP的类型``SymmetricDistance``用于他的输入和输出测量（Metric）。

最后，我们通过调用一个字符串向量的函数的方式，援引我们的``identity``的转换transformation，正如所期望的那样，他会返回一个相同的字符串向量给我们。

这没什么值得特别兴奋的，但是这个现实了一个OpenDP程序的雏形。别担心没弄明白一些概念的含义，接下来这些概念都会被逐一解释。

### What's Next？接下来？

现在你已经初步尝试了OpenDP，你可以开始探索OpenDP库中更深的地方。这篇导航的剩余部分会带你完整的了解一遍OpenDP中的基础概念，从这些概念的基础也就是[OpenDP Programming Framework](https://docs.opendp.org/en/stable/user/programming-framework/index.html)开始。

如果你急切的想跳过直接开始编程，那么你可以首先看看一些[OpenDP使用例子](https://docs.opendp.org/en/stable/examples/index.html).

如果你想学习参考材料，你可以去看看[API Docs](https://docs.opendp.org/en/stable/api/index.html)

## Programming Framework

OpenDP基于一个概念模型建立，这个概念模型定义了隐私保护操作的特征，并且提供了将想要的操作的组成成分聚合到程序中的方法。这个模型，也就是OpenDP Programming Framework，在[《A programming Framework for OpenDP》](https://projects.iq.harvard.edu/files/opendp/files/opendp_programming_framework_11may2020_1_01.pdf)这篇文章中详细介绍。整个框架围绕一个清晰可验证的捕捉算法中隐私方面的方法设计，同时保持了高度的灵活性和可扩展性。OpenDP库倾向于成为该框架的真实实现。

## Summary

OpenDP程序框架由一系列高级概念性的元素组成。我们将在这里介绍其中重要的部分，这些应该足够你去熟悉OpenDP编程了。如果你对框架背后更多的细节和动机感兴趣，你可以读读这篇[paper](https://projects.iq.harvard.edu/files/opendp/files/opendp_programming_framework_11may2020_1_01.pdf)。同时还有一本解释说明书[A Framework to Understand DP](https://docs.opendp.org/en/stable/user/programming-framework/a-framework-to-understand-dp.html)也许也可以提供帮助。

- [Measurements](https://docs.opendp.org/en/stable/user/programming-framework/core-structures.html#measurement)是从一个数据集到一个任意输出值得随机映射。他们是一种可控制的、在计算中注入隐私性的方法（比如噪声）。其中一个例子就是对某一个值添加高斯噪声。

- [Transformations]("https://docs.opendp.org/en/stable/user/programming-framework/core-structures.html#transformation")是指从一个数据集到另一个数据集的确定性映射。它们是用来以某种方式总计或者转换数据。其中一个例子就是计算一些数值的平均值。

- [Domains]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#domains")是某些对象可能取到的所有可能的值得集合。它们用于限制measurements和transformations的输入或者输出。Domains的例子比如1到10之间的所有证书，或者长度为5的由浮点数构成的向量。

- [Measures]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#measures")和[metrics]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#metrics")是确定两个数学对象之间距离的东东。
  
  - Measures用于刻画两个概率分布之间的距离。比如pure DP中的最大熵。
  
  - Metrics用于刻画两个相邻数据集之间的距离。一个例子是symmetric distance（系统度量），用于统计有多少个元素被改变了。

- [Privacy relations and stability relations]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#relations")是布尔函数，用于刻画操作输入与输出的clossness性。它们是把所有事情串起来的线索。
  
  - Privacy relation是关于一个measurement的描述。它是一个布尔函数，包含两个值，输入距离（在一种特定的metric下）和一个输出距离（在一个特定的measure下）。一个privacy relation可以让你对一个在相邻数据集上评估出的measurement做出断言。如果privacy relation为真，它可以保证任何一对在输入距离内的measurement的输出都可以产生一对在输出距离内的transformation的输出。
  
  - Stability relation是关于一个transformation的描述。它也是一个布尔函数，包含两个值，一个输入距离（在一种特定的metric下）和一个输出距离（在一个特定的metric下，大概率是和输入距离的不同）。stability relation可以让你对一个transformation的行为做出断言，当transformation作用在任意的相邻数据集上时。如果stability relation为真时，他可以保证任意一对在输入距离内的transformation的输入都可以产生在输出距离之内的transformation 的输出。

Relations用一种一般性方法描述了closeness的概念，使得OpenDP可以扩展到不同的隐私定义中。

正如你所看到的那样，这些元素是独立且相互支撑的。这些元素共同作用，赋予了OpenDP程序框架灵活性和可表达性。这些会在以下更细粒度的章节介绍

[Core Structure]("https://docs.opendp.org/en/stable/user/programming-framework/core-structures.html")

[Supporting Elements]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html")

[A Framework to Understand DP]("https://docs.opendp.org/en/stable/user/programming-framework/a-framework-to-understand-dp.html")

## Key Points

在编写OpenDP应用时，你不需要知道程序框架的所有内容，但是这可以帮助你理解一些关键点：

- OpenDP的计算是通过将许多transformation和measurement聚集组成为一个measurement的方法建立的，典型的方法是通过链chaining或者组合composition。

- 当建立一个measurement时，measurement没有一个给定的静态隐私损失。相反，measuremts的建立是通过给定噪声的规模，而隐私损失是通过推导出的privacy relation界定的。除了直接指定隐私损失外，这些往往还需要一些额外的工作；然而，OpenDP提供了一些工具让这些额外的工作变得容易一些，并且为程序框架提供了灵活性。

## Implementation Differences

目前OpenDP没有实现程序框架中的所有细节。

### Interactive Measurements

程序框架的一个重要的方面是，它以一种灵活的方式建模了interactive measurements。这些measurements对应的操作不是一个静态的函数，而是刻画了一些列的查询和响应，这些查询和响应的序列很可能是动态决定的。这是一种非常灵活的计算模型，可以被用于刻画适应性组合。

不幸的是，OpenDP还没有实现interactive measuremens，只能用于non-interactive measurements。

### Row Transforms

Row transforms是将用户自定义的函数应用到数据集中的每个元素上。这个概念可以被用于构建一些不是直接通过OpenDP获得的操作的transformation。但是，支持row transformation需要一些隐私上的限制，需要一些技术性的工作，目前OpenDP还不支持。

## Core Structures

OpenDP专注于创造一些有特定隐私特征的计算。这些计算在OpenDP中由两个核心结构建模： ``opendp.mod.Transformation``和``opendp.mod.Measurement``。这些结构存在于所有的OpenDP的程序中，无论底层的算法或者隐私的定义是什么。通过这种抽象的方式对计算进行建模，我们可以以一种关于结果程序灵活的安排和理由组合他们。

从一个统一的视角来看，OpenDP系统是用于relating：

1. 一个相邻函数输入之间距离的上界
2. 一个相关函数输出（或分布）之间距离的上界

OpenDP通过relations自然的刻画了隐私的定义，因为隐私的定义就是关于概率分布之间距离的上界。

每一个measurement或transformation都是一个自洽的结构，包含一个关系，一个函数，和作为支撑的证明。

### Measurement

一个``Measurement``是一个从数据集到任意类型输出的随机化映射。假设我们有一个Measurement的任意实例，叫做``meas``，以及一个程序片断``meas.check(d_in, d_out)``。如果代码片断估计为真，那么``meas``在``d_in``-close输入上是满足``d_out``-DP的，或者等价地称为“(``d_in, d_out``)-close"。代码片断用来简单的检验包含在``meas``内部的隐私关系（privacy relation）。

距离``d_in``和``d_out``在input metric和output metric的单元中表达。根据文意，``d_in``可以是相邻数据集上的距离界限或者是一个全局敏感度；``d_out``可以是``epsilon``, ``(epsilon, delta)``或者其他的隐私度量。更多的信息可以看[这里]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#distances")。

每一次调用measurement函数（通过``meas.invoke(data)``或者``meas(data)``）都是一次满足差分隐私的发布。每一次发布的隐私开销是任意一个满足隐私关系的``d_out``。最紧的隐私开销``d_out``可以直接通过``meas.map(d_in)``找到。

一个measurement结构包含一下内部域：

- **input_domain:** 一个描述函数所有可能的输入值的[域]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#domains")。

- **output_domain:** 一个描述函数所有可能输出的[域]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#domains")。

- **function:** 一个在隐私数据上计算满足差分隐私发布的[函数]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#functions")。

- **input_metric:** 用于计算输入域中两个成员之间距离的[metric]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#metrics")。

- **output_measure:** 用于计算输出域中两个分布之间距离的[measure]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#measures")。

- **privacy_map:** 一个包含所有的函数隐私特征的[映射]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#maps")。

该框架通过measure量化measurements上的输出距离，因为measurement是从一个概率分布上采样，而measure就是用来量化概率分布之间距离的。这是measurements和transformations之间的主要鉴别因素。

### Transformation

一个``Transformation``是数据集到数据集上的确定性映射。Transformations是在用某个measurement链接起来之前，用来预处理和聚合数据的。

和上面提到的``meas``相同，假设我们有任意一个Transformation的实例``trans``，和一个代码片断``trans.check(d_in, d_out)``。如果代码片断评估为真，那么``trans``在``d_in``-close的输入上是``d_out``-close的，或者等价地说，是"(``d_in, d_out``)-close"的。代码片断主要检查``trans``内部蕴含的stability relation。在本文中，这个relation刻画了一个transformation的稳定性。

距离``d_in``和``d_out``在input metric和output metric的单元中表达。根据文意，``d_in``可以是相邻数据集上的距离界限或者是一个全局敏感度；``d_out``可以是``epsilon``, ``(epsilon, delta)``或者其他的隐私度量。更多的信息可以看[这里]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#distances")。

调用这些函数（通过``trans.invoke(data)``或者``trans(data)``）可以转换数据，但是产生的输出是不满足差分隐私的。Transformations需要和某个measurement做[链接]("https://docs.opendp.org/en/stable/user/constructors/combinators.html#chaining")，之后才可以创建一个满足差分隐私的发布。

一个transformation的结构包含以下内部域：

- **input_domain:** 一个描述函数输入所有可能取值的集合的[域]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#domains")。
- **output_domain:** 一个描述函数输出所有可能值得集合的[域]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#domains")。
- **function:** 一个用于转换数据的[函数]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#functions")。
- **input_metric:** 一个用于计算两个输入域中成员距离的[metric]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#metrics")。
- **output_metric:** 一个用于计算两个输出域中成员距离的[metric]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#metrics")。
- **stability_map:** 一个用于刻画函数稳定特性的[映射]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#maps")。

## Supporting Elements

本节建立在[Core Structures]("https://docs.opendp.org/en/stable/user/programming-framework/core-structures.html#core-structures")文档上，进一步解释Measurements和Transformations中的组成部分。

### Function

我们通常期望所有的数据处理都可以只通过一个函数解决。储存在一个Transformation或者Measurement结构中的函数成员是一个完美的数学函数的典型（直接）代表。表示两个集合间的二元关系的数学函数，可以将输入集中的每个值与输出集合中的某个值联系起来。在OpenDP中，我们用domains来刻画这些集合。。。

### Domains

一个域表示了函数输入值的所有可能的集合，或者函数所有可能输出的值的集合。两个域（``input_domain``和``output_domain``）包含在每个Transformation或Measurement中，用于描述函数所有可能的输入和输出。

一些常用的域包括：

- **AllDomain\<T>：** 类型T中所有的非空值得集合。比如``AllDomain<u8>``描述了可能的无符号8位整数的集合：``{0,1,2,3,...,127}``。

- **BoundedDomain\<T>：** 类型T中介于某个L和U之间的所有非空值。比如``<BoundedDomain<i32>``表示在-2和2之间的：``{-2, -1, 0, 1, 2}``。

- **VectorDomain\<D>：** 所有向量的集合，其中，向量中的每个值都是域D中的成员。比如``VectorDomain<AllDomain<bool>>``描述的是所有的布尔值向量：``{[True],  [False], [True, True], [True, False], ...}``。

- **SizedDomain\<D>：** 域D中的所有值，并且拥有的某个给定的形状大小。比如大小为2的``SizedDomain<VectorDomain<AllDomain<bool>>>``，描述的是这样的布尔向量：``{[True, True], [True, False], [False, True], [False, False]}``。

通常，你只需提供某一些关于底层域的性质，剩下的由constructor自动选择。

让我们来考察从``make_bounded_sum(bound=(0, 1))``返回的Transformation。他的输入域类型为``VectorDomain<BoundedDomain<i32>>``, 意思是“所有介于0和1之间的32位有符号整数构成的所有向量”。边界参数向constructor提供L和U，然而由于TIA（原子输入类型）没有通过，TIA就会从公共边界的类型中推断产生。因此，输出域就是简单的``AllDomain<i32>``， 或“所有32位有符号整数的集合”。

这些域的使用出于以下两种目的：

1. privacy relation或者stability relation在它们的证明中依赖输入和输出域，来限制相邻数据集或分布的集合。一个例子是用于``opendp.transformations.make_sized_bounded_sum()``的关系，它利用``SizedDomain``的域描述器来更紧的界定sensitivity敏感度。

2. 聚合器同样需要域来保证输出是良定义的。例来说，链接constructor检查中间域是等价（相等，相同）的从而保证内部函数的输出总是外部函数的一个合法输入。

### Metrics

Metric是用来计算集合中两个元素之间距离的函数。

Metric在OpenDP中的一个具体的例子是``SymmetricDistance``，或者“系统性距离$|A\Delta B|=|(A\setminus B) \cup (B\setminus A)|$ "。这个是用于将数据集``A``转换为数据集``B``所需要的最少次数的添加或删除操作次数。

每一个metric都需要和一个域进行捆绑，并且数据集``A``和``B``都是该域中的成员。由于symmetric distance总是和``VectorDomain<D>``成对出现，``A``和``B``通常都是向量类型。实际上，如果我们有这样一个数据集，每个用户至多可以影响其中的k条记录，我们就可以说数据集上的symmetric distance可以用k为界限定住，因此``d_in=k``。

另一个metric的例子是``AbsoluteDistance<f64>``。这个metric的意思是$|A -B|$，这个距离使用64位浮点数表达的。这个metric是用来表示全局敏感度（global sensitivity，在对某项记录进行扰动时，聚合值（应该是指查询结果之类的意思）可以变化的最大上界）。实际上，大多数用户都不需要向privacy relations提供全局敏感度，因为它只是在关联数据集距离和隐私距离时中间偶然产生的一个距离界限。然而，有时候constructor不得不接收一个metric用于指定敏感度。

### Measures

在OpenDP中，measure是用于衡量两个概率分布之间距离的函数。

measure的一个具体的例子是``MaxDivergence<f64>``，意思是最大散度指标，其中数字使用64位浮点数表达。由最大散度推导出的距离在pure DP的定义中和$\epsilon$相关联。

另一个例子是``SmoothMaxDivergence<f64>``。由平滑最大散度推导出的距离和approximate DP相关，其中距离是一个形如$(\epsilon, \delta)$的元组。

每一个Measurement（[见列表]("https://docs.opendp.org/en/stable/user/constructors/measurements.html#measurement-constructors")）都包含一个output_measure，并且compositor的类型总是由对应的Measure来确定。

### Relations

我们通过一种relation来断言一个Transformation或者Measurement的函数的隐私性质。Relations接收一个``d_in``和一个``d_out``，并且返回一个布尔值。有一对等价的解释来说明什么时候一个relation的返回值为真True：

- 所有潜在的输入扰动都不会显著地影响输出。

- Transformation或者measurement是（``d_in, d_out``)-close的。

（``d_in, d_out``)-close意味着什么？如果一个measurement是(``d_in, d_out``)-close的，那么当输入变动最大在``d_in``之内时，其输出就是满足``d_out``-DP的。

什么是``d_in``和``d_out``？`d_in`和`d_out`是从输入和输出的metric或measure的角度推导出的距离。可以看[Distances]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#distances")获取更多细节。

这些应该足够去熟悉并使用了，但是我们还是快速地了解一些数学方面的内容。可以参考programming framework那篇文章如果需要一些更深入的理解。考虑输入的metric是``d_X``，输出的metric或者measure是``d_Y``，并且`f`是Transformation或者Measurement中的函数。

一个稍微数学化的表达方式：如果relations是满足（通过）的，那么也就是说，对输入域中的所有`x`,`x'`：

- 如果`d_X(x, x') <= d_in`(如果相邻数据集是至多`d_in`-close的)

- 那么`d_Y(f(x), f(x')) <= d_out`(那么函数输出之间的距离不会超过`d_out`)

注意到如果关系在`d_out`上满足了，那么对于任意大于`d_out`的值，关系都可以满足。这是一个非常有用的观察，我们会在[Parameter Search]("https://docs.opendp.org/en/stable/user/utilities/parameter-search.html#parameter-search")那一章看到它的作用。

将这些投入到实际应用中，下面的例子是检查一个clamp transformation上的stability relaitions。

```python
from opendp.transformations import make_clamp
clamp = make_clamp(bounds=(1,10))

...
#在你的数据集中，任意一个用户最多可以影响到的记录的个数
in_symmetric_distance = 3
#clamp是一个1-stable的Transformation，所以他可以在任何symmetric distance >= 3的关系上通过
assert clamp.check(d_in=in_symmetric_distance, d_out=4)
```

### Maps

一个map是一个函数，它可以接收一些`d_in`，然后返回最小的`d_out`，因此它是(`d_in, d_out`)-close的。

```python
#再次使用先前的clamp transformation为例子
clamp.map(d_in=3)
#输出为3
```

关系检查声明函数主要通过如下方式比较map的输出和`d_out`：`d_out >= map(d_in)`。需要更深入了解maps，可以看[relations]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#relations")的章节。

### Distance

基于`input_metric`和`output_metric`或`output_measure`，你可以决定单元`d_in`和`d_out`是以什么形式表达的。按照示例metrics和measures中的链接，详细了解对应的metric或measure的含义。

在Transformations中，`input_metric`是一个数据集metric，比如[SymmetricDistance]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#symmetric-distance")。而`output_metric`要么是一个数据集metric（在数据集的transformations）上，要么就是某种全局敏感度，比如[AbsoluteDistance]("[Supporting Elements &#8212; OpenDP](https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#absolute-distance)")（在聚合查询或计算上）。

Measurements的`input_metric`一开始只有一些全局敏感度的metric。然而，如果你将Measurement和Transformation链接起来，结果产生的Measurement将具有Transformation上的任何`input_metric`。Measurements的`output_metric`是一些privacy measure，比如[MaxDivergence(最大散度)]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#max-divergence")或者[SmoothedMaxDivergence(平滑最大散度)]("https://docs.opendp.org/en/stable/user/programming-framework/supporting-elements.html#smoothed-max-divergence")。

为relation选择正确的`d_in`非常重要，尽管你可以使用[binary search utilities]("https://docs.opendp.org/en/stable/user/utilities/parameter-search.html#parameter-search")来寻找最紧的`d_out`。实际上，`d_out`的值越小，你的隐私分析就会越紧。

你也许会惊奇的发现metrics和measures从来没有被真正评估过。程序框架不会评估他们是因为框架只需要将一个用户提供的输入距离和另一个用户提供的输出距离联系起来即可。即使是用户本身也不应该直接计算输入和输出的距离：它们是[solved-for(已解决的)]("https://docs.opendp.org/en/stable/user/utilities/accuracy/index.html#determining-accuracy")，[bisected(一分为二的)]("https://docs.opendp.org/en/stable/user/utilities/parameter-search.html#parameter-search")，甚至是[contextual(需要结合语境的)]("https://docs.opendp.org/en/stable/user/putting-it-together.html#putting-together")。

请注意：即使是用于确定个人最大贡献数量的数据集查询，其本身也可能是隐私信息。

# 
