# DFT_book

**目录**
1. [DFT_book](#dft_book)
   1. [背景与简介](#背景与简介)
      1. [背景](#背景)
      2. [密度泛函理论简介](#密度泛函理论简介)
      3. [平面波方法](#平面波方法)
         1. [交换关联泛函（交换关联势）](#交换关联泛函交换关联势)
         2. [基组](#基组)
         3. [赝势](#赝势)
         4. [总能和力](#总能和力)
      4. [电子结构分析](#电子结构分析)
         1. [态密度相关](#态密度相关)
         2. [能带结构相关](#能带结构相关)

## 背景与简介

### 背景

对 DFT 的全面概述超出了本书的范围，因为在文献中很容易找到关于这些主题的优秀评论，建议阅读以下段落。 相反，本章旨在为非专家以本书中使用的方式开始学习和使用 DFT 提供一个有用的起点。 此处提供的大部分信息是专家之间的标准知识，但其结果是目前文献中的论文很少对其进行讨论。 本章的第二个目标是为新用户提供了解大量可用文献的途径，并指出这些计算中的潜在困难和陷阱。

可以在 Sholl 和 Steckel 的 [Density Functional Theory: A Practical Introduction] 一书中找到对密度泛函理论的现代实用介绍。 Parr 和 Yang parr-yang 编写的一本关于 DFT 的相当标准的教科书。  The Chemist's Guide to DFT koch2001 更具可读性，包含更多用于运行计算的实用信息，但这两本书都侧重于分子系统。 固态物理学的标准教材是 Kittel kittel 和 Ashcroft 以及 Mermin ashcroft-mermin 所著。 两者都有其优点，前者在数学上更严谨，后者更具可读性。 然而，这两本书都不是特别容易与化学相关的。 为此，应参考 Roald Hoffman hoffmann 1987，RevModPhys.60.601 的异常清晰的著作，并遵循 N\o rskov 和同事hammer2000:adv-cat,greeley2002:elect 的工作。

[Density Functional Theory: A Practical Introduction]: DavidSholl&JaniceSteckel,Wiley,2009.

### 密度泛函理论简介

Kohn-Sham 方程（ K-S 方程）的自洽 DFT-PW 计算中的关键参数是电子密度。然后电子密度建立一个有效电位，它由 Hartree、交换相关和外部电位给出。知道有效势使我们能够获得 Kohn-Sham 哈密顿量。

### 平面波方法

平面波方法在平面波基础上展开单粒子的波函数来求解 K-S 方程。

#### 交换关联泛函（交换关联势）

在 DFT 方法中，电子-电子相互作用的量子力学部分由交换相关项近似，并且存在大量不同的近似交换相关密度泛函。

交换相关势被定义为交换相关能相对于电子密度的函数导数，它对应于电子之间的平均场量子力学相互作用势：
$$
V^{XC}[n](r) = \frac{\delta E^{XC}}{\delta n}(r)
$$

**广义梯度近似**（Generalized-gradient approximation (GGA)） 

GGA 泛函是一大类交换关联能的半局部近似，其中泛函取决于电子密度的局部值和局部梯度：
$$
E^{GGA}[n] = \int n(\bm{r})\epsilon^{GGA}(n(\bm{r}), \nabla n(\bm{r}))d\bm{r}
$$

#### 基组

基组其实就是一套模板化的函数。进行量子化学计算，最关键的部分就是求解出体系各个能级的波函数。根据分子轨道理论的思想，分子轨道可以用各个原子轨道的线性组合来拟合。因此，我们就可以拿一套原子的波函数（**基函数**）作为基底，混合它们来求解体系波函数。描述一个原子所用到的全部基函数就叫做一套**基组**。[参考来源](https://zhuanlan.zhihu.com/p/363177076)

#### 赝势

赝势方法是相对于全电子势方法而言的。原子的内层电子波函数振荡很剧烈，于是基函数就需要很多平面波才能收敛，计算量就会很大，而通过模守恒赝势norm-conserved、超软赝势ultra-soft、投影扩展波projector augmented wave等方法，可以有效的减少平面波的个数。

QE官网给出了一个较为完整的赝势数据库（Link ），赝势文件可以直接下载。QE赝势的格式为UPF（Unified Pseudopotential Format）。QE中不同元素的不同类型赝势（NC，US，PAW）允许混用，不同交换关联（LDA、GGA等）赝势也允许混用，但是在输入文件需要重新设置统一的交换关联近似（input_dft），非共线计算（noncolin=.true.）需要至少一种元素使用非共线(rel)赝势。早期的赝势比较缺少，现在需要混用的情况不常见。

赝势所需截断能ecutwfc和ecutrho需要测试以得到准确的计算结果，以下列出的赝势，作者通常公布出了赝势测试的结果，测试包括对单质及化合物的晶格常数、能带、声子频率、磁矩等的计算，赝势的结果比较是和全电子势进行的，与实验结果的比较是一种参照，即具有相似的误差，误差来源的大部分来自采用的交换关联泛函，所以误差并不是通过赝势的生成而减小的。实际使用中，如果这些计算结果与文献基本一致，则通常说明了赝势及截断能选取的可靠性。

[关于 Quantum Espresso 的赝势](https://yyyu200.github.io/DFTbook/blogs/2019/06/03/PPlib/)

#### 总能和力

多电子系统的 DFT 总能量是电子密度 $n$ 的泛函:
$$
E[n] = T[n] + E^{XC}[n] + E^H[n] + E^{EXT}[n]
$$

其中 $T[n]$ 是非作用电子气对密度 $n$ 的动能，$E^{XC}[n]$ 是交换-关联能，$E^H[n]$ 是 Hartree 势能，$ E^{EXT}[n]$ 是电子在离子和其他外部来源创建的静电势场中的相互作用能。

第一性原理力可以从原子 $i$ 在位置 $R[i]$ 相对于离子坐标的总能的微分求得：
$$
\bm{F_i} = -\frac{dE[n]}{d\bm{R_i}}
$$

### 电子结构分析

#### 态密度相关

1. 态密度（Density of states）
2. 局域态密度（Local density of state）
3. 投影态密度（Projected density of state）

#### 能带结构相关

1. 能带结构（band structure）
2. 投影能带结构（fatband structure）

投影能带结构（fatband structure 或 projected band structure）用于可视化体相系统中不同轨道对能带结构的贡献。对于子空间中给定的一个投影，我们能够计算与该投影对每一个本征值贡献的权重。