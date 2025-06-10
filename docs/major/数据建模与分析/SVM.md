# SVM

SVM 的学习策略是间隔最大化，间隔最大使得它有别于感知机（最小化误分类点到超平面的总距离，形式化为一个求解凸二次规划问题，也等价于正则化的合页损失函数的最小化问题。

- 线性可分支持向量机
	- 数据线性可分时，通过硬间隔最大化(hard margin maximization)；
	- 数据近似线性可分时，通过软间隔最大化(soft margin maximization)；（允许某些点不满足线性可分）

- 非线性支持向量机
	- 当训练数据线性不可分时，通过使用核技巧(kernel trick)及软间隔最大化。


**间隔**：如果训练数据是线性可分的，可以选择分离两类数据的两个平行超平面，使得它们之间的距离尽可能大，在这两个超平面范围内的区域称为“间隔”。


最大间隔超平面完全是由最靠近它的那些 $\vec{x}_{i}$ 确定的，这些 $\vec{x}_{i}$ 成为支持向量。(删去非支持向量的数据点并不会影响 SVM 的效果)

## 线性可分支持向量机

定义两种间隔


**函数间隔**：点到分离超平面的相对距离：（表示分类预测的确信程度）

$$
\lvert w\cdot x+b \rvert 
$$

$w\cdot x+b$ 的符号与类标记 $y$ 的符号是否一致，表示分类是否正确，$y(w\cdot x+b)$ 表示分类的正确性和确信度。记作 $\hat{y}_{i}$ 。记训练数据集的函数间隔 $\hat{y} = \underset{ i=1,\dots,N }{ \min }\hat{y}_{i}$，当成比例改变 $w$ 和 $b$ ，函数间隔改变而超平面不变。

**几何间隔**：真实的距离：

$$
y_{i}\left( \frac{w}{\lVert w \rVert }\cdot x_{i}+ \frac{b}{\lVert w \rVert }  \right)
$$

记训练数据集的几何间隔：$\bar{y} = \underset{ i=1,\dots,N }{ \min  }y_{i}$


最大化间隔分类超平面：

$$
\max_{w,b}\bar{y} \quad \mathrm{s.t.}\, y_{i}\left( \frac{w}{\lVert w \rVert }\cdot x_{i}+ \frac{b}{\lVert w \rVert }  \right) \geq \bar{y}
$$

根据函数间隔和几何间隔也可以写作

$$
\max_{w,b} \frac{\hat{y}}{\lVert w \rVert } \quad \mathrm{s.t.}\, y_{i}\left( w\cdot x_{i}+ b  \right) \geq \hat{ y}
$$

可以取 $\hat{y}=1$ 

证明如下：由于约束可以写成：

$$
\max_{w,b} \frac{1}{\lVert w \rVert /\hat{y}} \quad \mathrm{s.t.}\, y_{i}\left( \frac{w}{\hat{y}}\cdot x_{i}+ \frac{b}{\hat{y}}  \right) \geq 1
$$

也就是我们做了一个成比例改变，取

$$
\begin{align}
\tilde{w} &= \frac{w}{\hat{y}} \\
\tilde{b} &= \frac{b}{\hat{y}}
\end{align}
$$

由于我们成比例改变 $w$ 和 $b$ 时 ，函数间隔改变而超平面不变。故我们可以取 $\hat{y} =1$ 

根据感知机可以导出线性可分支持向量机学习的最优化问题：

$$
\begin{align}
\min_{w,b}&\, \frac{1}{2}\lVert w \rVert ^{2} \\
\mathrm{s.t.}& \, y_{i}(w\cdot x_{i}+b)-1\geq0
\end{align}
$$

因为 SVM 是求解一个凸二次规划问题，先介绍凸集和凸函数的相关概念：

**凸集**：一个点集（或区域），如果连接其中任意两点 $x_{1}$ 和 $x_{2}$ 的线段都包含在集合内，就称该点为凸集，否则为非凸集

**凸函数**：

1. 定义：$f(tx_{1}+(1-t)x_{2})\leq tf(x_{1})+(1-t)f(x_{2})$ 
2. 一阶导数： $f(x_{2})\geq f(x_{1})+(x_{2}-x_{1})^{T}\nabla f(x_{1})$
3. 二阶矩阵：Hessian 矩阵在凸集处处半正定

凸优化问题是求解如下最优化问题：

!!! note "凸优化问题"

    $$
    \begin{align}
    \min &f(w) \\
    \mathrm{s.t.} &g_{i}(w)\leq 0 \\
    &h_{j}(w)=0 \\
    \end{align}
    $$

    其中目标函数 $f(w)$ 和约束函数 $g_{i}(w)$ 都是 $\mathbb{R}^{n}$ 上连续可微的凸函数，$h_{j}(w)$ 是 $\mathbb{R}^{n}$ 上的仿射函数。(注：$h(x)$ 称为仿射函数，如果它满足 $h(x)=a\cdot x+b$)

    当目标函数为二次函数，$g_{i}$ 函数也为仿射函数时，为凸二次规划问题。


!!! note "最大间隔法"

    输入：线性可分训练数据集

    $$
    T = \left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots,(x_{N},y_{N}) \right\} 
    $$

    输出：最大间隔分离超平面和分类决策函数

    (1) 构造并求解约束最优化问题

    $$
    \begin{align}
    \min_{w,b}&\, \frac{1}{2}\lVert w \rVert ^{2} \\
    \mathrm{s.t.}& \, y_{i}(w\cdot x_{i}+b)-1\geq0
    \end{align}
    $$

    (2) 分离超平面：

    $$
    w^{*} \cdot x+b^{*} =0
    $$

    分离决策函数：

    $$
    f(x)= \mathrm{sign}(w^{*}\cdot x+b^{*})
    $$


!!! note "Theorem"

    若训练数据集 $T$ 线性可分，则可将训练数据集中的样本完全正确分开的最大间隔分离超平面存在且唯一   


**支持向量**是使约束等号成立的点：

$$
y_{i}(w\cdot x_{i}+b)-1=0
$$

支持向量在确定分离超平面其决定作用





