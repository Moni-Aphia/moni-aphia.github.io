论文：[https://doi.org/10.1016/j.cma.2024.116822](https://doi.org/10.1016/j.cma.2024.116822)

---
## Introduction

将系统识别领域分为两个不同的类别非常重要：一种方法是使用黑箱函数（例如神经网络）准确反映观察到的动态的方法，另一种方法是将系统恢复到能够以常微分方程和偏微分方程形式（ODEs 和 PDEs）表达的动态封闭形式与可解释表达式。这种二元性反映了布雷曼所倡行的机器学习和经典统计学的两种文化叙述（This duality reflects the two cultures narrative of machine learning and classical statistics made popular by Breiman）

一方面，研究可能会为具有已知机制（known mechanism）的数据假设一个特定模型，而另一方面，研究对算法模型感兴趣，这些算法模型虽然不一定反映真实机制，但预测准确。

微分方程的项通常含有特定的物理含义(interpretations)与原因(motivation)，比如说，扩散（diffusion）和平流(advection)普遍存在于大多数物理系统的，他们在偏微分方程中具有特定的表达式。对于一阶主导数难以处理的系统，我们可以根据已识别的偏微分方程中的项深入了解系统的底层物理原理。

我们认为提取保守方程的过程比将黑箱模型拟合到特定数据集更具有普遍性。具体来说，改变初始条件不会改变控制方程，但可能会破坏机器学习的黑盒求解器。

我们提出一个稀疏回归框架，用来识别具有非常数系数的偏微分方程，我们允许系数随时间或空间而变化，但是必须和偏微分方程中的活动一致。物理系统的参数在测量过程中经常发生变化，因此参数依赖关系可以在PDE中消除歧义。

这篇[文章](https://arxiv.org/abs/1801.06637)中提出的用神经网络从噪声数据中获取更为精确的时间微分而计算数值微分方式是一种颇有前景的方法。任何封闭式模型表达式都必须可以用一组有限的构建块来表示。在这里，我们仅在数据及其导数中使用单项式，因为这些是物理学中常见的项，但库中包含的项没有限制。

## Methods 

### Group Sparsity 

一种经过充分研究的解决具有群体稀疏性的回归问题的方法是群体 LASSO([Group LASSO](https://arxiv.org/abs/1001.0736))

$$
\hat{x} = \underset{w}{\arg \min} \frac{1}{2n}\left\lVert b-\sum_{g\in \mathcal{G}}A_{(g)}w_{(g)}\right\rVert^{2}_{2}+\lambda\sum_{g\in \mathcal{G}}||w_{(g)}||_{2}  
$$

这个方程的最优解满足 Karush-Kuhn-Tucker (KKT) 条件，即对每个 $g' \in \mathcal{G}$，解必须满足

$$
\frac{1}{n}A_{(g')}^T\left(b-\sum_{g \ne g'}A_{(g)}x_{(g)}\right)- \frac{1}{n}A_{(g')}^TA_{(g')}x_{(g')} \in \lambda \partial||x_{(g')}||_2
$$


其中次梯度是一个集值函数 $\partial ||x_{(g)'}||_2 = \{x_{(g')}||x_{(g')}||^{-1}_2\}$ KKT 条件解释了，当 $||r_{(g')}||_2\le \lambda$ 时，存在解 $x_{(g')}=0$

尽管群体稀疏性已经被运用于识别常微分方程动力学问题，但是群体 LASSO 在识别偏微分方程的效果很差。因此我们使用岭回归来替代，算法如下

![SGTR](<Source/Pasted image 20241026082147.png>)


在训练过程中， $\mathcal{G}$ 追踪含有非零系数的群组，并且当我们设置相关性足够小的阈值系数（由 $f$ 测量）时，它会被配对。也就是说，如果组 $g \in \mathcal{G}$ 满足阈值标准 $f(x_{(g)})<\varepsilon$ ，那么 $x_{(g)}$ 设置为零并且在接下来的算法迭代中不予以考虑。在 $f$ 中，我们采用2-范数，也可以考虑任意函数，特别是，对于每组内的系数具有自然排序的问题（就像我们的例子中的时间序列或空间函数一样），可以考虑函数的平滑度或其他属性。在实践中，我们对 A 和 b 的每一列进行归一化，以便组之间的尺度差异不会影响算法的结果。

### Data-driven identification of parametric partial differential equations 

考虑如下参数形式的偏微分方程

$$
u_t = N(u,u_x,\dots,\mu_{(t)}) = \sum^d_{j=1}N_j(u,u_x,\dots)\xi_j(t)
$$

如果需要捕获系数的空间变化，我们只需要将 $\xi(t)$ 换成 $\xi(x)$

我们假设PDE中包含少量的活动项，每个活动项都有一个随时间变化的系数 $\xi(t)$ ，我们试图解决两个问题：1.确定哪些系数非零 2.找到的已有数据时间域或空间域上 $\xi_j$ 的系数

一组适当的基函数的先验知识对于算法的准确性可能至关重要。使用不完整的库进行回归会产生不可预测的结果，如果不存在正确模型项，稀疏识别通常会以刻板失败，从而产生动力系统的非简约表示。


对于时间相关问题，我们为每个时间步长构建一个单独的回归，允许时间步长之间的偏微分方程存在变化。我们使用数据中的单项式及其导数构建偏微分方程的候选函数库

$$
\Theta\left(u^{(j)}\right)=\begin{pmatrix}
|&|&|&|\\
1&u^{(j)}&\dots &u^3u^{(j)}_{xxx}\\
|&|&|&|
\end{pmatrix}
$$

所以有
$$
u^{(j)}_t = \Theta\left(u^{(j)}\right)\xi^{(j)}
$$
我们的目标就是求解这样的方程组，约束条件 $\xi^{(i)}$ 是稀疏的且共享相同的稀疏模式。所以我们可以将我们的系统表示成如下形式

$$
\begin{pmatrix}
u^{(1)}_t\\
u^{(2)}_t\\
\vdots\\
u^{(m)}_t
\end{pmatrix}=
\begin{pmatrix}
\Theta\left(u^{(1)}\right)&&&\\
&\Theta\left(u^{(2)}\right)&&\\
&&\ddots&\\
&&&\Theta\left(u^{(m)}\right)
\end{pmatrix}
\begin{pmatrix}
\xi^{(1)}\\
\xi^{(2)}\\
\vdots\\
\xi^{(m)}
\end{pmatrix}
$$

我们使用 SGTR 算法求解上述方程。

而对于空间分组，我们使用空间标签来分类。

### Model selection

令 $\overset{\sim}{\Theta}$ 表示对角矩阵，所有的列都被标准化成单位长度，$\overset{\sim}{u}_t$ 是已标准化为单位长度的时间导数向量。$||\overset{\sim}{u}_t|| = \sqrt{m}$ 对于 LASSO 组，我们找到将所有系数设置为零的 $\lambda$ 最小值，由下式给出

$$
\lambda_\max = \underset{g \in \mathcal{G}}{\max}\frac{1}{n}||\overset{\sim}{\Theta}_{(g)^T}\overset{\sim}{u}_t||_2
$$

对于 SGTR，我们搜索 $\varepsilon_\min$ 和$\varepsilon_\max$之间的公差范围，给出如下定义

$$
\varepsilon_{\max/\min} = \underset{g \in \mathcal{G}}{\max/\min}||\xi_{\text{ridge,(g)}}||_2
$$

其中 $\xi_{\text{ridge}} = (\overset{\sim}{\Theta}^T \overset{\sim }{\Theta}+\lambda I)^{-1}\overset{\sim}{\Theta}^T \overset{\sim}{u}_t$ 

其中 $\varepsilon_\min$ 是稀疏性最小限度，$\varepsilon_\max$ 是保证所有系数都为零的最小容差