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

## 对偶算法

设 $f(x),c(x),h(x)$ 是定义在 $\mathbb{R}^{n}$ 上的连续可微函数：

$$
\begin{align}
\min &f(x) \\
\mathrm{s.t.} &c_{i}(x)\leq 0 \\
&h_{j}(x)=0 \\
\end{align}
$$

引入拉格朗日函数 $\alpha_{i},\beta_{j}$ 为乘子：

$$
L(x,\alpha,\beta) = f(x)+\sum_{i=1}^{k} \alpha_{i}c_{i}(x)+\sum_{j=1}^{l} \beta_{j}h_{j}(x)
$$

考虑 $x$ 的函数，$P$ 为原始问题：

$$
\theta_{P}(x) = \max_{\alpha,\beta:\alpha_{i}\geq 0} L(x,\alpha,\beta)
$$

假定给定某个 $x$ ，如果 $x$ 违反约束条件：

$$
c_{i}(x)>0 \text{ or }h_{j}(x)\neq 0
$$

则

$$
\theta_{P}(x) = \max_{\alpha,\beta:\alpha_{i}\geq 0} \left[ f(x)+\sum_{i=1}^{k} \alpha_{i}c_{i}(x)+\sum_{j=1}^{l} \beta_{j}h_{j}(x) \right] =+\infty
$$

也就是说我们总是可以选择一个 $\alpha_{i}$ 或者 $\beta_{j}$ ，使得 $\theta_{P}$ 取到无限大的值。

原始问题：（极小极大问题）

$$
\min_{x}\theta_{P}(x)= \min_{x}\max_{\alpha,\beta;\alpha_{i}\geq 0} L(x,\alpha,\beta)
$$

原始问题的最优值：

$$
p^{*} = \min_{x}\theta_{P}(x)
$$

对偶问题：

$$
\theta_{D}(\alpha,\beta ) = \min_{x}L(x,\alpha,\beta)
$$


广义拉格朗日的极大极小问题可以表示为约束最优化问题：

$$
\max_{\alpha,\beta}\theta_{D}(\alpha,\beta ) = \max_{\alpha,\beta}\min_{x}L(x,\alpha,\beta)
$$

称为原始问题的**对偶问题**。

取对偶问题的最优值：

$$
d^{*} = \max_{\alpha,\beta;\alpha_{i}\geq 0} \theta_{D}(\alpha,\beta)
$$

!!! note "Theorem"

    如果原始问题和对偶问题都有最优值，则

    $$
    d^{*}=\max_{\alpha,\beta}\min_{x}L(x,\alpha,\beta)\leq \min_{x}\max_{\alpha,\beta;\alpha_{i}\geq 0} L(x,\alpha,\beta)=p^{*}
    $$


!!! tip "Corollary"

    设 $x^{*}$ 和 $\alpha ^{*},\beta ^{*}$ 分别使原始问题和对偶问题的可行解，且 $d^{*}=p^{*}$，则 $x^{*}$ 和 $\alpha ^{*},\beta ^{*}$  分别是原始问题和对偶问题的最优解。

定理证明对偶问题给出了原始问题的最优解下限，这种情况称为弱对偶，而引理说明了原始问题和对偶问题最优解相同时，两个问题使用同一种可行解。因此，如果能限制对偶问题的最优解为原始问题的最优解，那么就可以求解出原始问题，这种情况称为强对偶

虽然我们总有弱对偶条件 $f ^{*} \geq d ^{*}$ ，但有些问题中，我们可以得到 $f^{*} = d^{*}$，这被称为强对偶。我们有一个强对偶成立的一个充分条件：Slater 条件

!!! note "Slater 条件"
    如果原始问题是一个凸函数，即 $f_{i}$ 和 $g_{i},i=1,\dots,m$  都是凸函数，$h_{j},j=1,\dots r$ 都是仿射函数，且存在至少一个严格可行解 $x \in \mathbb{R}^{n}$ ，也就是

    $$
    \begin{align}
    g_{1} (x)<0 ,&\dots,g_{m}(x)<0 \\
    h_{1}(x)=0,&\dots,h_{r}(x)=0
    \end{align}
    $$

    那么，强对偶成立


强对偶满足的限制条件称为 [Karush-Kuhn-Tucker(KKT) 条件](https://en.wikipedia.org/wiki/Karush%E2%80%93Kuhn%E2%80%93Tucker_conditions)。

对于一个一般问题，KKT 条件包括如下四个条件：稳定性条件、原始问题可行条件、对偶问题可行条件、互补松弛条件。


|                         |                                                                                                                                                                        |
| ----------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Stationarity            | $0\in \partial f(x^{*})+\underset{ i=1 }{ \overset{m  }{ \sum } }\mu^{(i)}\partial g_{i}(x^{*})+\underset{ j=1 }{ \overset{ r }{ \sum } }v^{(j)}\partial h_{j}(x^{*})$ |
| Primal feasibility      | $\eqalign{g_{i}(x)\leq 0,i = 1, \dots, m\\h_{j}(x^{*})=0,j=1,\dots,r}$                                                                                                 |
| Dual feasibility        | $\mu^{(i)}\geq 0,i=1,\dots,m$<br>                                                                                                                                      |
| Complementary slackness | $\mu^{(i)}g_{i}(x ^{*})=0,i=1,\dots,m$                                                                                                                                 |

之前的定理表明 Slater 条件可以推导出 KKT 与最优解等价，另一方面，假设已知强对偶成立这个前提，也有同样的结论：

- 如果强对偶性成立，那么原问题的最优解 $x^{*}$ 和对偶问题的最优解 $u_{*},v_{*}$ 满足 KKT 条件
- 如果强对偶性成立，$x^{*}$ 和 $u_{*},v_{*}$ 满足 KKT 条件，那么 $x^{*}$ 为原问题最优解，$u_{*},v_{*}$ 是对偶问题最优解

### 线性可分支持向量机的对偶形式

定义线性可分支持向量机的拉格朗日函数：

$$
L(w,b,\alpha) = \frac{1}{2}\lVert w \rVert ^{2}-\sum_{i=1}^{N} \alpha_{i}y_{i}(w\cdot x_{i}+b) + \sum_{i=1}^{N} \alpha_{i}
$$

则将极小极大值的原问题转换成如下极大极小的对偶问题：

$$
\max_{\alpha}\min_{w,b}L(w,b,\alpha)
$$

先求 $\underset{ w,b }{ \min }L(w,b,\alpha)$，对 $w,b$ 分别取偏导并令其为 $0$ 可得

$$
w = \sum_{i=1}^{N} \alpha_{i}y_{i}x_{i} \quad \sum_{i=1}^{N} \alpha_{i}y_{i}=0
$$

回带可得：

$$
L(w,b,\alpha) = -  \frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{i}\alpha_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N} \alpha_{i}
$$

现在求上述拉格朗日函数对 $\alpha$ 的极大，转换成对偶问题：

$$
\begin{align}
\min_{\alpha}& \, \frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{j}\alpha_{i}y_{i}y_{j}(x_{i}\cdot x_{j})-\sum_{i=1}^{N} \alpha_{i} \\
\mathrm{s.t.}&\, \sum_{i=1}^{N} \alpha_{i}y_{i}=0 \\
&\, \alpha_{i}\geq 0
\end{align}
$$

这是一个二次规划的问题，一般采用 SMO 算法来求解。

假定 $\alpha ^{*} =(\alpha_{1}^{*}，\alpha_{2}^{*},\dots,\alpha_{N}^{*})^{T}$ 是上述对偶最优问题的解，则存在下标 $j$ 使 $\alpha_{j}^{*}>0$ ，并可按下式求得原始问题的解。

$$
\begin{align}
w^{*} & = \sum_{i=1}^{N} \alpha_{i}^{*} y_{i}x_{i} \\
b^{*}&=y_{j } - \sum_{i=1}^{N} \alpha ^{*}_{i}y_{i}(x_{i}\cdot x_{j})
\end{align}
$$

由此定理可知，分离超平面可以写作：

$$
\sum_{i=1}^{N}  \alpha_{i}^{*}y_{i}(x\cdot x_{i})+b^{*} =0
$$

对于分类决策函数：

$$
f(x) = \mathrm{ sign }\left( \sum_{i=1}^{N}  \alpha_{i}^{*}y_{i}(x\cdot x_{i})+b^{*} \right) 
$$

这就是说，分类决策函数只依赖于输入 $x$ 和训练样本输入的内积，上式称为线性可分支持向量机的对偶形式。

!!! note "线性可分支持向量机学习算法"

    输入：线性可分训练数据集 $T=\left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots,(x_{N},y_{N}) \right\}$

    输出：最大间隔分离超平面和分类决策

    (1) 构造并求解约束最优化问题：

    $$
    \begin{align}
    \min_{\alpha}& \, \frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{j}\alpha_{i}y_{i}y_{j}(x_{i}\cdot x_{j})-\sum_{i=1}^{N} \alpha_{i} \\
    \mathrm{s.t.}&\, \sum_{i=1}^{N} \alpha_{i}y_{i}=0 \\
    &\, \alpha_{i}\geq 0
    \end{align}
    $$

    求得最优解 $\alpha ^{*} =(\alpha_{1}^{*}，\alpha_{2}^{*},\dots,\alpha_{N}^{*})^{T}$

    (2) 计算 

    $$
    w^{*} =\sum_{i=1}^{N}\alpha ^{*}_{i}y_{i}x_{i}
    $$

    并选择一个正分量 $\alpha_{j}^{*}>0$ 计算 

    $$
    b^{*}=y_{j } - \sum_{i=1}^{N} \alpha ^{*}_{i}y_{i}(x_{i}\cdot x_{j})
    $$

    (3) 求得分离超平面：

    $$
    \sum_{i=1}^{N}  \alpha_{i}^{*}y_{i}(x\cdot x_{i})+b^{*} =0
    $$

    和分类决策函数：

    $$
    f(x) = \mathrm{ sign }\left( \sum_{i=1}^{N}  \alpha_{i}^{*}y_{i}(x\cdot x_{i})+b^{*} \right) 
    $$

## 软间隔支持向量机

训练数据中有一些特异点（outlier），不能满足函数间隔大于等于1的约束条件，这些特异点可能是因为噪声等缘故产生的。在实际中，数据点类的分布可能会有一定重合，在这种情况下对训练数据的精确划分会导致较差的泛化能力。因此需要允许一些数据点被误分类。

对每个样本点 $(x_{i},y_{i})$ 引进一个松弛变量 $\xi_{i}\geq 0$ 约束条件变为：

$$
y_{i}(w\cdot x_{i}+b)\geq 1-\xi_{i}
$$

目标函数变为 

$$
\frac{1}{2}\lVert  w \rVert ^{2} + C \sum_{i=1}^{N}  \xi_{i}
$$

$C$ 称为惩罚参数，用来衡量不同参数的重要程度

$$
\begin{array}{|c|c|c|} 
\hline
&\text{small C}&\text{big C}\\
\hline
\text{desire}&\text{maximize margin } 1/||w||&\text{keep most slack variables zero or small}\\
\hline
\text{danger}&\text{underfitting } &\text{overfitting}\\
\hline
\text{outliers}&\text{less sensitive}&\text{very senstive}\\
\hline\text{boundary}&\text{more flat } &\text{more sinuous}\\
\hline
\end{array}
$$

---
线性不可份线性支持向量机（软间隔最大化）

$$
\begin{align}
\min_{w,b,\xi}&\, \frac{1}{2}\lVert w \rVert ^{2} +C \sum_{i=1}^{N} \xi_{i}\\
\mathrm{s.t.}& \, y_{i}(w\cdot x_{i}+b)\geq 1-\xi_{i} \\
&\xi_{i}\geq 0
\end{align}
$$

可证明 $w$ 的解是唯一的，但 $b$ 可能不唯一

原问题的拉格朗日函数：

$$
L(w,b,\xi,\alpha,\mu) = \frac{1}{2}\lVert w \rVert ^{2} + C \sum_{i=1}^{N} \xi_{i}-\sum_{i=1}^{N} \alpha_{i}(y_{i}(w\cdot x_{i}+b)-1+\xi_{i})-\sum_{i=1}^{N} \mu_{i}\xi_{i}
$$

拉格朗日函数对 $w,b,\xi$ 的极小给出：

$$
\begin{align}
w = \sum_{i=1}^{N} \alpha_{i}y_{i}x_{i} \\
\sum_{i=1}^{N} \alpha_{i}y_{i}=0 \\
C -\alpha_{i}-\mu_{i}=0
\end{align}
$$

可以得到：

$$
\min_{w,b,\xi} L(w,b,\xi,\alpha,\mu)= -\frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N}  \alpha_{i}\alpha_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N} \alpha_{i}
$$

求其对 $\alpha$ 的极大得到对偶问题：

$$
\begin{align}
\max_{\alpha}&-\frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N}  \alpha_{i}\alpha_{j}y_{i}y_{j}(x_{i}\cdot x_{j})+\sum_{i=1}^{N} \alpha_{i} \\
\mathrm{s.t.}&\sum_{i=1}^{N} \alpha_{i}y_{i}=0 \\
&C-\alpha_{i}-\mu_{i}=0 \\
&\alpha_{i}\geq 0 \\
&\mu_{i}\geq 0
\end{align}
$$

求得

$$
0\leq \alpha_{i}\leq C
$$

所以原始问题的对偶问题为：

$$
\begin{align}
\min_{\alpha}& \frac{1}{2}\sum_{i=1}^{N} \sum_{j-1}^{N} \alpha_{i}\alpha_{j}y_{i}y_{j}(x_{i}\cdot x_{j}) -\sum_{i=1}^{N}   \alpha_{i} \\
\mathrm{s.t.} & \sum_{i=1}^{N} \alpha_{i}y_{i}=0 \\
&0\leq \alpha_{i}\leq C
\end{align}
$$

设 $\alpha ^{*}=(\alpha_{1} ^{*},\alpha_{2}^{*},\dots,\alpha_{N}^{*})^{T}$ 是对偶问题的一个解，若存在 $\alpha ^{*}$ 的一个分量 $\alpha_{j}^{*},0<\alpha_{j}^{*}<C$，则原始问题的解 $w^{*},b^{*}$ 为

$$
w^{*} =\sum_{i=1}^{N} \alpha_{i}^{*}y_{i}x_{i}\quad b ^{*} = y_{i}-\sum_{i=1}^{N} y_{i}\alpha_{i}^{*}(x_{i}\cdot x_{j})
$$

---

线性支持向量机学习还有另外一种解释，就是最小化以下目标函数：

$$
\sum_{i=1}^{N} [1-y_{i}(w\cdot x_{i}+b)]_{+}+\lambda \lVert w \rVert ^{2}
$$

第一项称为合页损失函数([Hinge loss](https://en.wikipedia.org/wiki/Hinge_loss))

$$
[z]_{+} = \begin{cases}
z & z> 0 \\
0  & z\leq 0
\end{cases}
$$

也就是说，当样本点 $(x_{i},y_{i})$ 被正确分类且函数间隔大于 $1$ 时，损失是 $0$ ，否则，损失是 $1-y_{i}(w\cdot x_{i}+b)$

线性支持向量机原始最优化问题可以等价于如下最优化问题：

$$
\boxed{\min_{w,b}\sum_{i=1}^{N} [1-y_{i}(w\cdot x_{i}+b)]_{+}+\lambda \lVert w \rVert ^{2}}
$$


证明如下：取

$$
[1-y_{i}(w\cdot x_{i}+b)]_{+}=\xi_{i}
$$

则有 $\xi_{i}\geq 0$ 。当 $1-y_{i}(w\cdot x_{i}+b)> 0$ 时，有 $y_{i}(w\cdot x_{i}+b)=1-\xi_{i}$ ，当 $1-y_{i}(w\cdot x_{i}+b)\leq 0$ 时，有 $\xi_{i} =0$  。综上两种情况，可以总结为

$$
y_{i}(w\cdot x_{i}+b)\geq 1-\xi_{i}
$$

满足线性可分支持向量机的约束条件。

将 $[1-y_{i}(w\cdot x_{i}+b)]_{+}=\xi_{i}$ 带入上式可得：

$$
\min_{w,b} \sum_{i=1}^{N} \xi_{i}+\lambda \lVert w \rVert ^{2} 
$$

取 $\lambda = \frac{1}{2C}$ 则转化成：

$$
\min_{w,b} \frac{1}{C}\left( \frac{1}{2}\lVert w \rVert ^{2}+C \sum_{i=1}^{N} \xi_{i} \right) 
$$


另一方面，也可将约束问题转化成上述正则化算法，由于 $y_{i}(w\cdot x_{i}+b)\geq 1-\xi_{i}$，也即 $\xi_{i}\geq 1-y_{i}(w\cdot x_{i}+b)$ ，又因为 $\xi_{i}\geq 0$ ，由于优化目标是最小化 $\xi_{i}$ ，因此最优解必然满足：

$$
\xi_{i} = \max\left\{ 1-y_{i}(w\cdot x_{i}+b),0 \right\} 
$$

即

$$
\xi_{i}= [1-y_{i}(w\cdot x_{i}+b)]_{+}
$$

因此软间隔最大化目标函数可以写成如下的正则化算法：

$$
\frac{1}{2}\lVert w \rVert ^{2} +C \sum_{i=1}^{N} [1-y_{i}(w\cdot x_{i}+b)]_{+}
$$

## 非线性支持向量机和核技巧

### 核技巧
如果能用 $\mathbb{R}^{n}$ 的一个超曲面将正负例正确分开，则称这个问题为非线性可分问题。非线性问题往往不好求解，所以希望能用解线性分类问题的方法来解决这个问题，采取的方法是进行一个非线性变换（特征映射），将非线性问题变换为线性问题，通过解变换后的线性问题的方法来求解原来的非线性问题。

核技巧还有另一种解释：

假定特征向量有 $d$ 个特征，特征函数 $\Phi$ 可以将其拓展到 $D$ 个特征。比如说特征向量有 $100$ 个特征，我们想用四次多项式来拓展我们的特征向量，于是我们可以得到大约四百万个拓展后的特征向量。也就是说，当原始特征数量 $d$ 较大且多项式次数 $p$ 较高时，特征化后的特征向量长度会急剧增加。

但是，我们可以不用直接计算，这是因为在学习算法中

- 权重可以表示为训练点的线性组合
- 我们只需要使用 $\Phi(x)$ 的内积而不需要显式计算 $\Phi(x)$

假定权重满足

$$
w = X^{T}\alpha = \sum_{i=1}^{n} a_{i}X_{i}
$$

$n$ 一般为样本点数，我们将上述表达式带入我们的算法中，于是，与其优化原有的权重 $w$ ，我们现在只需要优化 $n$ 个对偶权重 $\alpha$ （也称对偶参数）

用线性分类方法求解非线性分类问题分为两步

1. 首先使用一个变换将原空间的数据映射到新空间；
2. 在新空间里用线性分类学习方法从训练数据中学习分类模型。

!!! note "核函数"

    设 $\mathcal{X}$ 是输入空间，又设 $\mathcal{H}$ 为特征空间（希尔伯特空间），如果存在一个从 $\mathcal{X}$ 到 $\mathcal{H}$ 的映射：

    $$
    \phi(x):\mathcal{X}\to \mathcal{H}  
    $$

    使得对所有 $x,z\in \mathcal{X}$ ，函数 $K(x,z)$ 满足条件：

    $$
    K(x,z) =\phi(x)\cdot \phi(z)
    $$

    则称 $K(x,z)$ 为核函数，$\phi(x)$ 为映射函数。



??? example

    假设输入空间 $\mathbb{R}^{2}$ ，核函数是 $K(x,z)=(x\cdot z)^{2}$ ，找出其相关地特征空间 $\mathcal{H}$ 和映射 $\phi(x):\mathbb{R}^{2}\to \mathcal{H}$

    取特征空间 $\mathcal{H}=\mathbb{R}^{3}$ ，记 $x=(x^{(1)},x^{(2)})^{T},z=(z^{(1)},z^{(2)})^{T}$

    $$
    (x\cdot z)^{2} = \left( x^{(1)}z^{(1)} +x^{(2)}z^{(2)} \right)^{2} = \left( x^{(1)}z^{(1)}  \right)^{2}+2x^{(1)}z^{(1)} x^{(2)}z^{(2)}+\left( x^{(2)}z^{(2)}  \right)^{2}   
    $$

    则，可以取：

    $$
    \phi(x)= \left( (x^{(1)})^{2},\sqrt{ 2 }x^{(1)}x^{(2)},(x^{(2)})^{2} \right) ^{T}
    $$

    可以验证 $\phi(x)\cdot \phi(z)= (x\cdot z)^{2}=K(x,z)$

    说明：核函数不是唯一的


在线性支持向量机的对偶问题中，无论是目标函数还是决策函数（分离超平面）都只涉及输入实例与实例之间的内积，对偶问题的目标函数中的内积 $x_{i}\cdot x_{j}$ 可以用核函数 $K(x_{i},x_{j})=\phi(x_{i})\cdot \phi(x_{j})$ 来替代，此时对偶问题的目标函数成为：

$$
W(\alpha) = \frac{1}{2} \sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{i}\alpha_{j}y_{i}y_{j}K(x_{i},x_{j})-\sum_{i=1}^{N} \alpha_{i}
$$

对应决策函数：

$$
f(x)= \mathrm{sign}\left( \sum_{i=1}^{N_{s}}\alpha_{i}^{*}y_{i}K(x_{i},x)+b^{*}  \right) 
$$

这等价于经过映射函数 $\phi$ 将原来的输入空间变换到一个新的特征空间，将输入空间中的内积 $x_{i}\cdot x_{j}$ 变换为特征空间中的内积 $\phi(x_{i})\cdot \phi(x_{j})$ ，在新的特征空间里从训练样本中学习到线性支持向量机。当映射函数是非线性函数时，学习到的含有核函数的支持向量机是非线性模型。

也就是说，在核函数 $K(x,z)$ 给定的条件下，可以利用解线性分类问题的方法求解非线性分类问题的支持向量机。在实际应用中，往往依赖领域知识直接选择核函数，核函数选择的有效性需要通过实验验证。

---
给定一个函数 $K(x,z)$ 我们如何判断其是否是一个核函数呢？

假设 $K(x,z)$ 是定义在 $\mathcal{X}\times \mathcal{X}$ 上的对称函数，并且对任意的 $x_{1},x_{2},\dots,x_{m}\in \mathcal{X},K(x,z)$ 关于 $x_{1},x_{2}\dots,x_{m}$ 的 Gram 矩阵式半正定的。可以依据函数 $K(x,z)$ 构造称一个希尔伯特空间，其步骤是：首先定义映射 $\phi$ 并构成向量空间 $\mathcal{S}$ ；然后在 $\mathcal{S}$ 上定义内积构成内积空间；最后将 $\mathcal{S}$ 完备化构成希尔伯特空间

理论性非常强的说明：

??? note

    **定义映射，构成向量空间 $\mathcal{S}$**

    定义映射和对应线性组合：

    $$
    \phi:x\to K(\cdot,x) \quad f(\cdot) =\sum_{i=1}^{m} \alpha_{i}K(\cdot,x_{i})
    $$

    考虑由线性组合为元素的集合 $\mathcal{S}$ 。由于集合 $\mathcal{S}$ 对加法和数乘运算是封闭的，所以 $\mathcal{S}$ 构成一个向量空间。

    **在 $\mathcal{S}$ 上定义内积，使其成为内积空间**

    在 $\mathcal{S}$ 上定义一个运算 $*$ ：对任意 $f,g\in \mathcal{S}$ 

    $$
    f(\cdot)=\sum_{i=1}^{m} \alpha_{i}K(\cdot,x_{i})\quad g(\cdot)=\sum_{j=1}^{l} \beta_{j}K(\cdot,z_{j})
    $$

    定义运算 ${*}$ 

    $$
    f*g =\sum_{i=1}^{m} \sum_{j=1}^{l} \alpha_{i}\beta_{j}K(x_{i},z_{j})
    $$

    可以证明运算 ${*}$ 是空间 $\mathcal{S}$ 的内积，后用 $\cdot$  表示。赋予内积的向量空间为内积空间，即 $\mathcal{S}$ 为内积空间

    **将内积空间 $\mathcal{S}$ 完备化为希尔伯特空间**

    范数：

    $$
    \lVert f \rVert = \sqrt{ f\cdot f }
    $$

    因此，$\mathcal{S}$ 是一个赋范向量空间，泛函分析理论给出，对于不完备的赋范向量空间 $\mathcal{S}$ ，一定可以使之完备化，得到完备的赋范向量空间 $\mathcal{H}$ 。一个内积空间，当作为一个赋范向量空间是完备的时候，就是希尔伯特空间。这样，就得到了希尔伯特空间 $\mathcal{H}$ 。

这一希尔伯特空间称为再生核希尔伯特空间（reproducing kernel Hilbert space）。这时由于核 $K$ 具有再生性，即满足：

$$
K(\cdot,x)\cdot f=f(x)
$$

及 

$$
K(\cdot,x)\cdot K(\cdot,z) = K(x,z)
$$

称为再生核。

!!! note "Theorem"

    设 $K:\mathcal{X}\times \mathcal{X}\to \mathbb{R}$ 是对称函数，则 $K(x,z)$ 为正定核函数的充要条件是对任意 $x_{i}\in \mathcal{X},i=1,2,\dots,m,K(x,z)$ 对应的 Gram 矩阵

    $$
    K=[K(x_{i},x_{j})]_{m\times m}
    $$

    是半正定矩阵。



常用核函数：

- 多项式核函数

$$
K(x,z)= (x\cdot z+1)^{p}
$$
 
- 高斯核函数

$$
K(x,z)= \exp \left( -\frac{\lVert x-z \rVert ^{2}}{2\sigma^{2}} \right) 
$$

###  非线性支持向量机

!!! note "非线性支持向量机学习算法"

    输入：线性不可分训练数据集 $T=\left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots,(x_{N},y_{N}) \right\}x_{i}\in \mathcal{X}=\mathbb{R}^{n},y_{i}\in \mathcal{Y}={-1,+1}$

    输出：分类决策函数

    (1) 选择适当的核函数和参数 $C$ ，构造最优化问题：

    $$
    \begin{align}
    \min_{\alpha}& \frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{i}\alpha_{j}y_{i}y_{j}K(x_{i},x_{j} )-\sum_{i=1}^{N} \alpha_{i} \\
    \mathrm{s.t.}&\sum_{t=1}^{N} \alpha_{i}y_{i}=0 \\
    &0\leq \alpha_{i} \leq C
    \end{align}
    $$

    求得最优解 

    $$
    \alpha ^{*} =\left( \alpha_{1}^{*} ,\alpha_{2}^{*},\dots,\alpha_{N}^{*}\right)^{T} 
    $$

    (2) 选择 $0<\alpha_{j} ^{*}<C$ ，计算

    $$
    b ^{*} =y_{i}-\sum_{i=1}^{N} \alpha_{i}^{*}y_{i}K(x_{i},x_{j})
    $$

    (3) 构造选择函数：

    $$
    f(x)=\mathrm{sign}\left( \sum_{i=1}^{N_{s}}\alpha_{i}^{*}y_{i}K(x_{i},x)+b^{*}  \right) 
    $$


    当 $K(x,z)$ 是正定核函数时，最优化问题是凸二次规划问题，解是存在的


## SMO 算法

!!! tip
    这个算法本身我是没咋搞懂的（，所以直接抄书

    不过 sklearn 中的 linearSVC 用的就是这个算法。

支持向量机的学习问题可以形式化为求解凸二次规划问题，这样的凸二次规划问题具有全局最优解，并且有许多最优化算法可以用于这一问题的求解，但是当训练样本容量很大时，这些算法往往变得非常低效，以致无法使用，所以，如何高效地实现支持向量机学习就成为一个重要的问题。

序列最小优化算法 (Sequential minimal optimization，SMO) 算法要解如下凸二次规划的对偶问题

$$
\begin{align}
\min_{\alpha}& \frac{1}{2}\sum_{i=1}^{N} \sum_{j=1}^{N} \alpha_{i}\alpha_{j}y_{i}y_{j}K(x_{i},x_{j} )-\sum_{i=1}^{N} \alpha_{i} \\
\mathrm{s.t.}&\sum_{t=1}^{N} \alpha_{i}y_{i}=0 \\
&0\leq \alpha_{i} \leq C
\end{align}
$$


SMO 是一个启发式算法，其基本思路为：如果所有变量的解都满足此最优化问题的KKT条件，那么得到解；否则，选择两个变量，固定其它变量，针对这两个变量构建一个二次规划问题，称为子问题，可通过解析方法求解，提高计算速度。子问题的两个变量：一个是违反KKT条件最严重的那个，另一个由约束条件自动确定。

整个 SMO 算法包括两个部分：求解两个变量二次规划的解析方法和选择变量的启发式方法。

假定选择的两个变量是 $\alpha_{1},\alpha_{2}$ 则 SMO 最优化问题的子问题：

$$
\begin{align}
\min_{\alpha_{1}\alpha_{2}} &W(\alpha_{1},\alpha_{2}) = \frac{1}{2}K_{11}\alpha_{1}^{2} +\frac{1}{2}K_{22}\alpha^{2}_{2}+y_{1}y_{2} K_{12}\alpha_{1}\alpha_{2} -  (\alpha_{1}+\alpha_{2}) \\
&\qquad \qquad \quad+y_{1} \alpha_{1} \sum_{i=3}^{N}y_{i}\alpha_{i}K_{i1} +y_{2}\alpha_{2} \sum_{i=3}^{N} y_{i}\alpha_{i}K_{i 2} \\
\mathrm{s.t.}& \alpha_{1}y_{1}+\alpha_{2} y_{2} =-\sum_{i=3}^{N} y_{i}\alpha_{i}= \varsigma\\
&0\leq \alpha_{i} \leq C
\end{align}
$$

两个变量的约束可以用二维空间中的图形表式

![alt text](<pics/Pasted image 20250421212200.png>)

要求目标函数在一条平行于对角线上的线段的最优值。

假设问题的初始可行解 $\alpha_{1}^{\mathrm{old}},\alpha_{2}^{\mathrm{old}}$,最优解 $\alpha_{1}^{\mathrm{new}},\alpha_{2}^{\mathrm{new}}$，并且假设沿着约束方向未经剪辑时 $\alpha_{2}$ 的最优解为 $\alpha_{2}^{\mathrm{new,unc}}$。

最优解 $\alpha_{2}^{new}$ 满足

$$
L\leq \alpha_{2}^{\mathrm{new}}\leq H
$$

$L$ 和 $H$ 是 $\alpha_{2}^{\mathrm{new}}$ 所在的对角线段端点的界，如果 $y_{1} \neq n_{2}$ (图 a )：

$$
L= \max(0,\alpha_{2}^{\mathrm{old}}-\alpha_{1}^{\mathrm{old}}) \quad H = \min (C,C+\alpha_{2}^{\mathrm{old}}-\alpha_{1}^{\mathrm{old}})
$$

如果 $y_{1}= y_{2}$ (图 b)

$$
L= \max(0,\alpha_{2}^{\mathrm{old}}+\alpha_{1}^{\mathrm{old}}-C) \quad H = \min (C,\alpha_{2}^{\mathrm{old}}+\alpha_{1}^{\mathrm{old}})
$$

记

$$
g(x)= \sum_{i=1}^{N} \alpha_{i}y_{i}K(x_{i},x)+b
$$

令 

$$
E(x)= g(x_{i})-y_{i} = \left( \sum_{j=1}^{N} \alpha_{j}y_{j}K(x_{j},x)+b \right) -y_{i}
$$

为函数 $g(x)$ 对输入 $x_{i}$ 的预测值与真实输出 $y_{i}$ 之差。


!!! note "Theorem"

    最优化问题沿着约束方向未经剪辑的解是

    $$
    \alpha_{2}^{\mathrm{new,unc}} = \alpha_{2}^{\mathrm{old}} +\frac{y_{2}(E_{1}-E_{2})}{\eta}
    $$

    其中 

    $$
    \eta = K_{11} +K_{22}-2K_{12} = \lVert \phi(x_{1})-\phi(x_{2}) \rVert 
    $$

    剪辑后 $\alpha_{2}$ 的解是

    $$
    \alpha_{2}^{\mathrm{new}} = \begin{cases}
    H & \alpha_{2}^{\mathrm{new,unc}}>H \\
    \alpha_{2}^{\mathrm{new,unc}} & L\leq \alpha_{2}^{\mathrm{new}}  \leq H \\
        L  & \alpha_{2}^{\mathrm{new,unc }}<L   
    \end{cases}
    $$

    由 $\alpha_{2}^{\mathrm{new}}$ 求得 $\alpha_{1}^{\mathrm{new}}$ 为：

    $$
    \alpha_{1}^{\mathrm{new}}  =\alpha_{1}^{\mathrm{old}} +y_{1}y_{2}(\alpha_{2}^{\mathrm{old}}-\alpha_{2}^{\mathrm{new}})
    $$


---
SMO 称选择第 $1$ 个变量的过程为外层循环。外层循环在训练样本中选取违反 KKT 条件最严重的样本点，并将其对应的变量作为第 $1$ 个变量。具体地，检验训练样本点 $(x_{i},y_{i})$ 是否满足 KKT 条件，即：

$$
\begin{align}
\alpha_{i}=0 &\Leftrightarrow y_{i}g(x_{i})\geq 1 \\
0<\alpha_{i}< C &\Leftrightarrow y_{i}g(x_{i})= 1 \\
\alpha_{i}=C &\Leftrightarrow y_{i}g(x_{i})\leq 1
\end{align}
$$

在检验过程中，外层循环首先遍历所有满足条件 $0<\alpha_{i}<C$ 的样本点，如果这些样本点都满足 KKT 条件，那么遍历整个训练集。

SMO 称选择第 $2$ 个变量的过程为内层循环，内层循环的选择标准是希望能使 $\alpha_{2}$ 由足够的变换，一种简单做法是选择 $\alpha_{2}$ ，使其对应的 $\lvert E_{1}-E_{2} \rvert$ 最大。

如果在特殊情况下，选择的 $\alpha_{2}$ 不能使目标函数有足够的下降，那么采用如下启发式规则：遍历间隔边界上的支持向量点，依次作为 $\alpha_{2}$ 试用，直到目标函数有足够的下降；若找不到合适的 $\alpha_{2}$ ，那么遍历训练数据集，若找不到合适的 $\alpha_{2}$ ，则放弃第 $1$ 个 $\alpha_{1}$ ，重新寻找 $\alpha_{1}$

在每次完成两个变量的优化后，重新计算阈值 $b$ 。当 $0<\alpha_{1}^{\mathrm{new}}<C$ 时，KKT 条件给出：

$$
\sum_{i=1}^{N} \alpha_{i}y_{i}K_{i1}+ b = y_{1}
$$

则

$$
b^{\mathrm{new}}_{1 }= y_{1} -\sum_{i=3}^{N} \alpha_{i}y_{i}K_{i1} -\alpha_{1}^{\mathrm{new}} y_{1} K_{11} -\alpha_{2}^{\mathrm{new}}y_{2}K_{21}
$$

$E_{1}$ 的定义式给出：

$$
E_{1} = \sum_{i=3}^{N}\alpha_{i}y_{i}K_{i1} +\alpha_{1}^{\mathrm{old}}y_{1}K_{11}+\alpha_{2}^{\mathrm{old}}y_{2}K_{21}+b^{\mathrm{old}}-y_{1}
$$

则

$$
b^{\mathrm{new}}_{1 }=-E_{1} - y_{1} K_{11}(\alpha_{1}^{\mathrm{new}}-\alpha_{1}^{\mathrm{old}}) -y_{2}K_{21}(\alpha_{2}^{\mathrm{new}}-\alpha_{2}^{\mathrm{old}})+b^{\mathrm{old}}
$$

同样的：

$$
b^{\mathrm{new}}_{2 }=-E_{2} - y_{1} K_{12}(\alpha_{1}^{\mathrm{new}}-\alpha_{1}^{\mathrm{old}}) -y_{2}K_{22}(\alpha_{2}^{\mathrm{new}}-\alpha_{2}^{\mathrm{old}})+b^{\mathrm{old}}
$$

如果 $\alpha_{1}^{\mathrm{new}},\alpha_{2}^{\mathrm{new}}$  同时满足 $0<\alpha_{i}^{\mathrm{new}}<C$ ，则 $b^{\mathrm{new}}_{1 }=b^{\mathrm{new}}_{2 }$ ，否则，取中点。

$$
E_{i}^{\mathrm{new}} = \sum_{S}^{} y_{i}\alpha_{i}K(x_{i},x_{j})+b^{\mathrm{new}}-y_{i}
$$

!!! note "SMO 算法"

    输入：训练数据集 $T=\left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots,(x_{N},y_{N}) \right\}x_{i}\in \mathcal{X}=\mathbb{R}^{n},y_{i}\in \mathcal{Y}={-1,+1}$ ，精度 $\varepsilon$ 

    输出：近似解 $\hat{\alpha}$ 

    (1) 选择初值 $\alpha^{(0)} =0$ ，取 $k=0$ 
    (2) 选择优化变量 $\alpha_{1}^{(k)},\alpha_{2}^{(k)}$ ，求解如上最优化问题，得到最优解 $\alpha_{1}^{(k+1)},\alpha_{2}^{(k+1)}$ ，更新 $\alpha$ 
    (3) 停机条件：

    $$
    \sum_{i=1}^{N} \alpha_{i}y_{i} =0 \quad 0 \leq \alpha_{i}\leq C
    $$

    $$
    y_i \cdot g(x_i) \begin{cases} 
    \geqslant 1, & \{x_i | \alpha_i = 0\} \\
    = 1, & \{x_i | 0 < \alpha_i < C\} \\
    \leqslant 1, & \{x_i | \alpha_i = C\}
    \end{cases}
    $$

    则转 4 ，否则 $k=k+1$ ,转 $2$

    (4) 取 $\hat{\alpha} = \alpha^{(k+1)}$ 



误差分析：

考虑一个二分类分类器： $\mathcal{C}:X\to Y =\left\{ +1 ,-1\right\}$ ，对应错分误差：

$$
\mathcal{R}(\mathcal{C}  ) = \underset{ z\in \mathcal{Z}  }{ \mathrm{Prob} } \left\{ \mathcal{C} (x)\neq y \right\}  = \int_{X}\underset{ z\in \mathcal{Z}  }{ \mathrm{Prob} }(y\neq\mathcal{C}(x)|x)d\rho_{x}
$$

贝叶斯规则给出最小的错分误差：

$$
f_{C} = \arg \min \mathcal{R}(\mathcal{C} ) =\begin{cases}
+1 &\mathrm{\,if\,} P(y=1|x)\geq P(y=-1|x) \\
-1 &\mathrm{\,if\,} P(y=1|x)< P(y=-1|x)
\end{cases} 
$$

假定我们的分类算法是：

$$
f^{\phi}_{z,\lambda} = \arg \min_{f\in \mathcal{H}_{K} }\left\{ \varepsilon_{z}^{\phi}(f)+\lambda \lVert f \rVert ^{2}_{K} \right\} 
$$
