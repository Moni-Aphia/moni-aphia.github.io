论文：[https://onlinelibrary.wiley.com/doi/10.1111/insr.12554](https://onlinelibrary.wiley.com/doi/10.1111/insr.12554)

---
考虑描述<font color="#ff0000">连续过程</font>演变的通用动力学方程（Dynamics Equation） $\{u(s,t):s\in D_s,t\in D)t\}$

$$
u_{t^{(J)}}(s,t) =M(u(x,t),u_x(s,t),u_y(s,t),\dots,u_{t^{(1)}}(s,t),\dots u_{t^{(J-1)}}(s,t),\omega(s,t))
$$

向量 $u(s,t)\in \mathbb{R}^N$ 表示系统 $t$ 时间在 $s$ 处的位移， $u_{t^{(j)}}$ 表示 $u(s,t)$ 对时间的 $j$ 阶导数，$J$ 表示最高阶导数， $M(\cdot)$ 表示进化函数(evolution function)， $\omega (s,t)$ 表示可能被包含在系统的协变量。$N$ 是系统的组成部分（比如说 $u(s,t)=[u(s,t,1),u(s,t,2),\dots,u()s,t,N]'$，有时候也被称作系统状态），$s\in \{s_1,\dots,s_S\} =D_s$ 是域中的离散位置， $t\in\{1,\dots,T\}=D_t$ 是系统在离散时间的实现。

上述方程通常是一个偏微分方程（partial differential equation，PDE），如果移除空间偏导数就是常微分方程（ordinary differential equation, ODE） 


$$
u_{t^{(J)}}(s,t) =M(u(t),u_{t^{(1)}}(t),\dots u_{t^{(J-1)}}(t),\omega(t))
$$

数据驱动方法的目标就是学习上述两种控制方程，尤其是(非)线性函数 $M$，在真实过程中u存在噪声影响，也就是说其真实导数是未知的。

我们一般将数据驱动方法分为三类

1. 稀疏回归
2. 符号方法
3. 将稀疏回归和符号方法结合的深度建模

当然，还是存在其他方法的。

## Sparse Regression

我们将方程改写成如下形式：

$$
u_{t^{(J)}}(s,t)  = f(u(s,t),\dots)M
$$

$M$ 是一个 $D\times N$ 的稀疏系数矩阵。$f(\cdot)$ 是长度为 D 的向量值非线性变换函数，称为特征库(feature library)，$f(·)$ 的参数输入是通用的，并且包含可能与系统相关的函数。稀疏回归识别就是为确定 $M$ 中的相关项，从而识别驱动系统 $f$ 的组成部分并发现控制的动力学系统。

我们将系统 $j$ 阶导数的所有数据用矩阵表示

$$
U_{t^{(j)}}=\begin{bmatrix}
u_{t^{(j)}}(s_1,1,1)&u_{t^{(j)}}(s_1,1,1)&\dots&u_{t^{(j)}}(s_1,1,1)\\
u_{t^{(j)}}(s_1,2,1)&u_{t^{(j)}}(s_1,2,2)&\dots&u_{t^{(j)}}(s_1,2,N)\\
\vdots&\vdots&\ddots&\vdots\\
u_{t^{(j)}}(s_S,T,1)&u_{t^{(j)}}(s_S,T,2)&\dots&u_{t^{(j)}}(s_S,T,N)\\
\end{bmatrix}
$$

响应矩阵 $U_{t^{(j)}}$ 的大小为 $(ST)\times N$，所以我们将特征库转换成如下形式

$$
F=[1,U_{t^{(0)}},\dots U_{t^{(J)}},U_{x},U_{y},U_{xx},\dots,\Omega]
$$

$\Omega$ 表示时间和空间中的相关协变量，$F$ 是一个 $(ST)\times D$ 的矩阵。

特征库还可能包含组成变量、偏导数和协变量的相互作用。

所以我们将线性系统写作

$$
U_{t^{(J)}} =FM
$$

但是无法之间观测系统的导数状态，所以常用数值方法来近似求得导数
，最经典的方法是有限差分法，但是这个方法对于噪声是非常<font color="#ff0000">敏感</font>的。于是当存在噪声时，我们要么先让数据平滑然后计算导数，要么适用<font color="#ff0000">全变分正则化</font>或者<font color="#ff0000">多项式插值方法</font>计算导数。

所以，由于噪声影响以及导数的数值计算，我们将线性系统修正为如下形式

$$
U_{t^{(J)}} =FM+ \epsilon
$$

误差满足i.i.d分布 $N(0,\sigma^2I_n)$，其中 $\sigma^2$ 是与模型近似和数值微分有关的方差。为了引出稀疏性和进一步识别相关方程，对于上述方程的解为

$$
M= \underset{\hat{M}}{\arg \min} ||U_{t^{(J)}}-F\hat{M}||^2_2 + Pen_{\theta} (\hat{M})
$$

其中 $Pen_{\theta}(\hat{M})$ 表示基于参数 $\theta$ 的惩罚项，比如说我们将 $Pen_{\theta}(\hat{M}) = \lambda ||\hat{M}||_1$ 其中 $\theta = \lambda$ 表示 LASSO 惩罚项

### Deterministic Approaches 

大多数方法由三个步骤组成
1. 对于数据的去噪和微分
2. 构建特征库
3. 稀疏回归

如果已经处理好我们的数据且建好特征库了，那么我们的任务就变成了寻找方程的解。最早期的稀疏回归的方法是由 Brunton 在 [SINDy](https://arxiv.org/abs/1509.03580) 提出的连续阈值最小二乘法（sequential threshold least-squares，STLS）。这种方法用来识别 ODE 方程。尽管 SINDy 并没有涉及惩罚项，但是它近似是 $l_0$ 惩罚，即删除小于某个设定阈值 $\kappa$ 的 $M$ 项。该方法的缺点是对阈值参数的敏感性以及对逼近导数的方法的依赖性。

在此基础上 [Rudy](https://arxiv.org/abs/1609.06401) 通过顺序阈值岭回归（Sequential Threshold Ridge Regression , STRidge）将 SINDy 方法拓展到了 PDE 中，在迭代过程其中 $M<\kappa$ 的值被设置为零，但增加了惩罚项 $Pen_{\theta} (\hat{M}) = \lambda ||\hat{M}||^2_2$ ，然后通过交叉验证来找到最合适的 $\kappa$ 和 $\lambda$ 的值。STRidge 方法在含有不同噪声的数据集上被认为是有效的，但和 SINDy 一样依赖于求导的方法。

STRidge 可以通过在空间或时间上对项进行分组来进行调整，以允许参数化偏微分方程，为了使参数化的偏微分方程纳入线性系统方程中，系数必须在空间或时间域上变换，即 $M(s),M(t)$。 并且 $F$ 也需要构造成合适的块对角矩阵（空间域或时间域）。 与 LASSO 组类似，通过将整个时域上的空间中的相同位置或整个空间域上的相同时间分成同一组组，将系数分配给集合 $g \in \mathcal{G}$ （对于时间域上 $g \equiv s,\mathcal{G} \equiv D_s$，对于空间域 $g\equiv t,\mathcal{G} \equiv D_t$）。在 STRidge 算法中，如果 $‖M(g)‖_2 < κ$，则具有相同组索引的<font color="#ff0000">所有系数</font>都设置为零。以这种方式，<font color="#ff0000">在空间和时间上识别相同的动态，并且仅允许系数估计在空间或时间上变化</font>

Champion 基于系数松弛正则回归方法(sparse relaxed regularized regression , SR3) 为 SINDy 框架提供了一个鲁棒性统一算法。SR3 方法引入了一个带有惩罚项的辅助变量矩阵，将 $Pen_{\theta}(\hat{M})$ 转化成 $Pen_{\theta}(\hat{M}) = \lambda R(W) + \frac{1}{2 \nu}||\hat{M}-W||$ ，其中 $R(\cdot)$ 表示另一种惩罚项（比如说 $\mathscr{l}_1$）辅助变量的添加提供了更有利的几何表面来优化算法。SR3 被证实可用来处理异常值（数值微分时噪声数据带来的潜在问题），适应参数公式，并允许库中的物理约束。

还有用稀疏方法来识别动态系统的其他应用和方法。比如说，将 SINDy 应用于随机微分方程以及动力学在不同坐标系上演化的系统进一步提高了 SINDy 的适用性。 Schaeffer没有使用有限差分或全变分正则化，而是使用谱方法来计算空间导数，并使用 Douglas-Rachford 算法来寻找稀疏解。对高度损坏信号、SINDy 算法的收敛特性以及去噪和微分方法的选择的进一步考虑在文献中也得到了处理。为了便于使用，Python的包 PySINDy 中提供了 SINDy 和一些相关变体。


