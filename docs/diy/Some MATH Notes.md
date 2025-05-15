!!! success "Info"
    主要是记一些遇到的数学笔记！一般会给出参考（大部分以 wikipedia 和 公开课程 Notes 为主）

    我很喜欢推理的过程，很多定理非常优雅地叙述了一些普世的认识，看着这些数学符号翩翩起舞的过程无疑是一件幸福的事情，因而在阅读文献、或学习的过程中，看到一些优美的定理或公式，在网上查找相关知识无疑是一件快乐的事情。

## 病态
**Definition**：其性质被认为是非典型的或反直觉的现象。

一个经典的例子的病态结构是魏尔斯特拉斯函数，它处处连续但处处不可微。可微函数和魏尔斯特拉斯函数的总和也是连续的，但是无处可微；所以这种函数至少与可微函数一样多。事实上，由贝尔纲定理之一可以显示，连续函数通常是无处可微的。

通俗地说，大多数函数都不可微，而且相对较少的函数可以被描述或研究。一般而言，大多数实用的函数也具有某种物理基础或实际应用，这意味着它们在理论数学或逻辑层面上不能是病态的；如果没有像狄拉克 $\delta$ 函数那样的某些限制性情况，它们往往表现得非常正态，直观。

> 有时，逻辑生出妖怪。近半世纪，怪诞的函数异军突起，竭力标奇立异，与实际有用的函数大相径庭。其不再连续，甚或连续但不可导，如此种种。诚然，以逻辑观之，该等怪异函数才是最普遍；相反，以简单法则定义，无需刻意寻找的函数，反倒是特例，仅占墙隅立锥之地。
>
> 以前，发明新函数，是为实用；今时，则祗为刻意突显先贤的逻辑缺陷，此外别无他用。
>
> 师者若祗以逻辑为纲领，则必由最普遍的函数开始教授，亦即最怪异的函数。如此，学生初学已须勉强面对整座畸形学博物馆（musée tératologique）；否则，逻辑学家会挑剔你仅逐步迈向严谨。
> 
> ——Henri Poincaré，1899


在线性代数中，如果线性方程组 $Ax = b$ 中，$A$ 或 $b$ 的元素微小变化，会引起方程组解的巨大变化，则称方程组为病态方程组，称矩阵 $A$ 为病态矩阵。可用条件数 $\kappa(A) = \lVert A^{-1} \rVert\cdot \lVert A \rVert$ 判断方程组的“病态”程度。

一阶常见的病态矩阵为 $n$ 阶Hilbert矩阵 $H_{ij} = \frac{1}{i+j-1}$ ，随着阶数 $n$ 的增加而呈现严重的病态。

## 克莱姆法则
一个线性方程组可以用矩阵与向量方程表示：

$$
Ax = c
$$

克莱姆法则说明，如果 $A$ 式一个可逆矩阵，那么方程有解 $x= \left( x_{1},x_{2},\dots,x_{n} \right)^{T}$

$$
x_{i} = \frac{\text{det}(A_{i})}{\det(A)}
$$

其中 $A_{i}$ 是列向量 $c$ 取代了 $A$ 的第 $i$ 列后得到的矩阵。

## Dirichlet 分布

[lecture24.pdf](https://people.eecs.berkeley.edu/~jordan/courses/260-spring10/lectures/lecture24.pdf)

[Dirichlet Distribution（狄利克雷分布）与Dirichlet Process（狄利克雷过程） | 数据学习者官方网站(Datalearner)](https://www.datalearner.com/blog/1051459673766843#2%E3%80%81Dirichlet%20Distribution%EF%BC%88%E7%8B%84%E5%88%A9%E5%85%8B%E9%9B%B7%E5%88%86%E5%B8%83%EF%BC%89)


狄利克雷分布 $\mathrm{Dir(\alpha)}$ 是一个连续多元概率分布，其中 $\alpha$ 是由正实数组成的向量。最常见的是作为混合模型的先验，这是一个分布的分布，Dirichlet 过程每次样本的抽取都是一个分布，其在有限维的边缘分布也是 Dirichlet 分布。从Dirichlet过程中抽取的分布是离散的，但无法使用有限个参数描述，因此它被归为非参数模型。

假定 

$$
\Theta = \left\{ \theta_{1},\dots,\theta_{m} \right\} 
$$

对 Dirichlet 分布有如下写法：

$$
\begin{align}
\Theta &\sim \mathrm{Dir}(\alpha_{1},\dots,\alpha_{m}) \\
P(\theta_{1},\dots,\theta_{m}) &= \frac{\Gamma\left( \sum_{k}^{} \alpha_{k} \right)}{\prod^{}_{k}\Gamma(\alpha_{k})}\prod^{m}_{k=1}\theta^{\alpha_{k}-1}_{k}
\end{align}
$$

介绍 Beta 分布，beta 分布也是一组连续概率分布，其区间在 $[0,1]$ 上，参数是两个正值，用 $\alpha$ 和 $\beta$ 表示，这两个参数被称为形状参数，一般以随机变量的指数存在，它们控制着分布的形状。beta分布经常用来为随机变量的行为建模，它把随机变量的行为限制在一个有限的长度范围内。贝叶斯推断中，beta分布通常可以作为贝努利（Bernoulli）、二项分布（binomial）、负二项分布（negative binomial）和几何分布（ geometric distributions）的先验概率分布。例如，在贝叶斯分析中，beta分布可以用来描述关注成功率的分布的原始知识，如太空飞船完成任务的概率。beta分布非常适合用于为百分比和比例模型建模。


## Fisher information
[费雪信息 (Fisher information) 的直观意义是什么？ - 知乎](https://www.zhihu.com/question/26561604)

[Lecture15.pdf](https://web.stanford.edu/class/stats200/Lecture15.pdf)

[费舍尔信息 - 维基百科，自由的百科全书](https://en.wikipedia.org/wiki/Fisher_information)


!!! info "Definition"
    假定你观察到i.i.d.的数据 $X_{1},X_{2},\dots,X_{n}$  服从一个概率分布 $f(X;\theta)$ ，$\theta$ 是你的目标参数，那么似然函数：

    $$
    L(\mathbf{X};\theta) = \prod _{i=1}^{n}f(X_{i};\theta)
    $$

    为了求得最大似然函数，通常的做法是取对数似然函数的一阶导数为零，得到 $\hat{\theta}_{\mathrm{MLE}}$，对数函数的一阶导数称为 Score function:

    $$
    S(\mathbf{X};\theta)= \sum_{i=1}^{n}  \frac{\partial \log f(X_{i};\theta)}{\partial \theta } 
    $$

    那么 Fisher information 定义为

    $$
    I(\theta)= \mathbb{E}[S(X;\theta)^{2}]
    $$

    也就是 Score function 的二阶矩


在一般情况下，可以证明 $\mathbb{E}(S(\mathbf{X};\theta))=0$ ，从而得到：

$$
	I(\theta)= \mathbb{E}[S(X;\theta)^{2}]-\mathbb{E}[S(X;\theta)]^{2} = \mathrm{Var}[S(X;\theta)]
$$

所以 Fisher information 的第一条数学意义就是用来估计 MLE 方程的方差。它的直观表述就是，随着收集的数据越来越多，这个方差由于是一个Independent sum的形式，也就变的越来越大，也就象征着得到的信息越来越多。

如果对数似然函数二阶可导，在一般情况下可以证明：

$$
\mathbb{E}[S(\mathbf{X};\theta)^{2}] = - \mathbb{E}\left( \frac{\partial^{2} }{\partial \theta^{2}} \log L(\mathbf{X};\theta) \right) 
$$

于是得到了 Fisher Information 的第二条数学意义：对数似然函数在参数真实值的负二阶导数的期望。其反映了对数似然函数在顶点处的曲率，曲率越大，对数似然函数的形状就偏向于高而窄，也就代表掌握的信息越多


## Gram 矩阵

内积空间中，一组向量 $v_{1},\dots,v_{n}$ 的 Gram 矩阵是内积的 Hermite 矩阵，其元素由 $G_{ij}=\langle v_{i},v_{j} \rangle$ 给出。考虑这组向量组成的矩阵： $A = \begin{bmatrix}v_{1},\dots,v_{n}\end{bmatrix}$，其对应的一个 Gram 矩阵可以写作：

$$
A^{T}A = \begin{bmatrix}
v_{1}^{T}v_{1} & v_{1}^{T}v_{2} & \dots & v_{1}^{T}v_{n} \\
v_{2}^{T}v_{1} & v_{2}^{T}v_{2} & \dots & v_{2}^{T}v_{n} \\
\vdots  & \vdots  & \ddots & \vdots\\
v_{n}^{T}v_{1} & v_{n}^{T}v_{2} & \dots & v_{n}^{T}v_{n}
\end{bmatrix}
$$

对于 $AA^{T}$ 其是矩阵 $A$ 行向量的 Gram 矩阵

Gram 矩阵满足如下定理：

!!!note "Theorem"

    1. 每一个 Gram 矩阵都是非负的
    2. 由一组线性独立向量构成的 Gram 矩阵是正定矩阵
    3. 每一个半正定矩阵都是某些向量的 Gram 矩阵


Gram 矩阵用于度量各个维度自己的特性以及各个维度之间的关系。内积之后得到的多尺度矩阵中，对角线元素提供了不同特征图各自的信息，其余元素提供了不同特征图之间的相关信息。这样一个矩阵，既能体现出有哪些特征，又能体现出不同特征间的紧密程度。

## Parseval theorem
[lecture31.pdf](https://people.math.harvard.edu/~knill/teaching/math22b2019/handouts/lecture31.pdf)

对于一个函数，可展开如下傅里叶级数形式：

$$
		f(x)= \frac{a_{0}}{\sqrt{ 2 }}+\sum_{n=1}^{\infty} a_{n}\cos(nx)+\sum_{n=1}^{\infty} b_{n}\sin (nx)
$$

系数由如下式子给出：

$$
\begin{align}
a_{0} & = \frac{1}{\pi}\int_{-\pi} ^{\pi}f(x) /\sqrt{ 2 }\,\mathrm{d}x \\
a_{n} & = \frac{1}{\pi}\int_{-\pi} ^{\pi}f(x) \cos (nx)\mathrm{d}x \\
b_{n} & = \frac{1}{\pi}\int_{-\pi} ^{\pi}f(x) \sin (nx)\mathrm{d}x
\end{align}
$$

将勾股定理拓展到内积空间中可得 [Parseval 等式](https://en.wikipedia.org/wiki/Parseval%27s_identity)，通俗地说就是“函数的傅里叶系数的平方和”与“函数平方后的积分值”按如下换算：

!!! note "Theorem"

    $$
        \frac{1}{\pi} \int_{-\pi}^{\pi} \lVert f(x) \rVert ^{2} \mathrm{d}x = a_{0}^{2}+\sum_{n=1}^{\infty} a_{n}^{2}+b_{n}^{2}
    $$


我们有不同的方式描述函数的收敛性，对于 Parseval 等式，如果有：

$$
\lVert f-f_{n} \rVert \to 0
$$

称其为 $L_{2}$ 收敛。定义 $\lVert f-g \rVert_{\infty} =\max \lvert f(x)-g(x) \rvert$，可得如下推论：


!!! tip "Corollary"
    如果 $f$ 是光滑的，则

    $$
    \lVert f-g \rVert_{\infty} \to 0
    $$

对于不连续的函数，随着傅里叶级数数目的增加，在不连续点处会产生振荡行为，这种现象称为 [Gibbs 现象](https://en.wikipedia.org/wiki/Gibbs_phenomenon) 

## NP困难
P 问题是指在多项式时间内，可以找出解的决定性问题，而NP问题则包含在可在多项式时间内验证其解是否正确，但不保证能在多项式时间内找出解的决定性问题。

如果所有 NP 问题都可以多项式时间归约到某个问题，则称该问题为 NP 困难。

## 斯特林数(Stirling Number)

第二类斯特林数 $\begin{Bmatrix}n \\ k\end{Bmatrix}$ ， 表示把 $n$ 个不同的元素划分分成 $k$ 个互不区分的非空子集的方案数。

当我们插入一个新元素时，有两种方案：
- 将新元素单独放入一个子集，有  $\begin{Bmatrix}n -1\\ k-1\end{Bmatrix}$  种方案
- 将新元素放入一个现有的非空子集，有  $k\begin{Bmatrix}n-1 \\ k\end{Bmatrix}$ 
根据加法原理，可得得到递推式：

$$
\begin{Bmatrix}n \\ k\end{Bmatrix}=\begin{Bmatrix}n -1\\ k-1\end{Bmatrix}+k\begin{Bmatrix}n-1 \\ k\end{Bmatrix}
$$

根据容斥原理，可得上式解析表达式：

$$
\begin{Bmatrix}n \\ k\end{Bmatrix} = \frac{1}{k!} \sum_{i=0}^{k} (-1)^{i}\binom{k}{i}(k-i)^{n}
$$

## 容斥原理
[lec03.pdf](https://jhc.sjtu.edu.cn/~kuanyang/teaching/CS0901/notes/lec03.pdf)
!!!important "Theorem"

    对于一组有限集合 $A_{1},A_{2},\dots,A_{n}$ ，容斥原理为：

    $$
    \begin{align}
    \left\lvert  \bigcup_{i=1}^{n}A_{i} \right\rvert  &= \sum_{i=1}^{n} \lvert A_{i} \rvert -\sum_{i\leq j\leq n} \lvert A_{i}\cap A_{j} \rvert +\cdots-(-1)^{n}\left\lvert  \bigcap^{n}_{i=1}A_{i}  \right\rvert  \\
    &=\sum_{k=1}^{n}(-1)^{k-1} \sum_{S \subseteq [n],\lvert S \rvert =k}\left\lvert  \bigcap_{i \in S}^{}A_{i}  \right\rvert    \\
    &=\sum_{\emptyset\neq S \subseteq[n]}(-1)^{\lvert S \rvert -1} \left\lvert  \bigcap_{i \in S} A_{i} \right\rvert 
    \end{align}  
    $$

    其中 $[n] =\{1,2,\dots,n\}$



我们通过每个元素在等式两边的贡献（出现的次数）来证明这个原理：固定元素 $x$ ，假定 $I \subseteq[n]$ 为满足 $x\in A_{i}$ 的索引集合 $i$ 的集合，若 $I=\emptyset$ ，显然 $x$ 对两边的贡献均为零。如果 $I$ 是非空的，$x$ 在左侧的贡献为 $1$ ，在右侧，$x$ 出现在所有包含它的交集项中（比如说 $x$ 属于 $A_{1},A_{2}$ ，则它会被计入 $\lvert A_{1} \rvert,\lvert A_{2} \rvert,\lvert A_{1}\cap A_{2} \rvert$），利用如下等式

$$
\sum_{i=1}^{k} (-1)^{i-1}\binom{k}{i}=1
$$

也就是说尽管 $x$ 被被多次计数，但交替加减后净贡献为 $1$ ，与左侧的贡献一致

在组合数学中，我们通常使用补集形式：将 $B_{1},B_{2},\dots,B_{n}$ 视为一组“坏事件”，而而目标是计算不包含任何坏事件的“好”元素的个数。

!!!tip "Corollary"

    对于有限全集 $U$ 的子集 $B_{1},B_{2},\dots ,B_{n}$ ，不属于其中任何子集的元素个数为：

    $$
    \sum_{S \subseteq [n]}(-1)^{\lvert S \rvert }\left\lvert  \bigcap_{i\in S} B_{i} \right\rvert  
    $$

    其中约定 $\cap_{i\in \emptyset}B_{i}=U$ 。


该原则基于过度慷慨的包容，然后补偿性排斥。

在一个非常抽象的环境中，容斥原理可以表示为某个矩阵的逆的计算。这个逆元具有特殊的结构，使该原理在组合学和数学相关领域中成为一种非常有价值的技术。

> One of the most useful principles of enumeration in discrete probability and combinatorial theory is the celebrated principle of inclusion–exclusion. When skillfully applied, this principle has yielded the solution to many a combinatorial problem
> 
> ——Gian-Carlo Rota

