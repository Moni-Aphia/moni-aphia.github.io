

## Introduction

大规模机器学习：

- large $d$ : 输入的维度
- large $n$ : 样本数量

期望的理想时间复杂度： $\mathcal{O}(nd)$ 

假定数据满足独立同分布条件，我们希望学习出来的映射满足：

$$
y = \theta^{T}\Phi(x)
$$

$\Phi(x)$ 是特征函数。也就是说，可以将映射视作是特征函数 $\Phi(x)$ 的线性组合

经验损失给出：

$$
\min_{\theta \in \mathbb{R}^{d}}\underset{ \mathrm{convex\,data \, fitting \,term} }{  \frac{1}{n}\sum_{i=1}^{n} \mathscr{l}(y_{i},\theta^{T}\Phi(x_{i})) }+\underset{ \mathrm{regularizer} }{ \mu \Omega(\theta) }
$$

对于回归问题，损失函数一般取为二次函数；对于分类问题，取 $0-1$ 损失，但由于 $0-1$ 损失并不是凸函数，因此更多时候取
- hinge 函数
- 平方函数
- Logistic 函数

使用正则化的目的是避免过拟合，常用的正则化项有欧几里得范数 （$\scr l_{2}$ 范数），这个方法在数值效果上表现良好；如果要求结果更为稀疏，可以取 $\scr l_{1}$ 范数.

优化问题的解为：

$$
\theta ^{*} = \underset{}{\arg\min} \int \mathscr{l}(y,\theta^{T}\Phi(x))\mathrm{d}\rho (x,y)
$$

真实解基于所有样本，无法解出。因此，更多时候我们是通过经验风险，基于训练集中所有样本点损失函数最小化，利用蒙特卡罗模拟得到：

$$
\hat{f}(\theta) = \frac{1}{n} \sum_{i=1}^{n} \mathscr{l}(y_{i},\theta^{T}\Phi(x_{i})) 
$$


直接求解代价比较高因此采用迭代或者其他方法求得逼近真实解的近似解。也就是说我们求出来的是一系列的迭代结果序列 $\left\{ \theta_{i} \right\}$ 得到近似解 $\hat{\theta}$ 并期望近似解逼近真实解 $\theta ^{*}$ 。

这些损失函数的特点是都满足在 $\mathbb{R}^{d}$ 的，这对优化解法有着极大帮助，除此之外，有更多的假设，如满足 Lipschitz 连续，强凸，这些性质可以使用效果更好的优化算法。

凸函数的定义很多，举几个例子：

!!!note "Convex Function"

    对于一个连续可微的函数如果它在上满足$\forall x,y \in \mathbb{R}^n$

    $$
    f(y) \ge f(x) + \langle \nabla f(x),y-x\rangle 
    $$

    我们称这个函数为凸函数。记作 $f(x) \in \mathcal{F}^1(\mathbb{R}^n)$如果 $-f(x)$ 为凸函数，那么 $f(x)$ 为凹函数


!!! note "Convex Function"


    一个连续可微函数 $f(x)$ 是 $\mathbb{R}^n$上的凸函数当且仅当对$\forall x,y \in \mathbb{R}^n, \alpha \in [0,1]$ 有

    $$
    f(\alpha x+(1-\alpha)y) \le \alpha f(x)+(1-\alpha) f(y)
    $$



!!! note "Convex Function"

    一个连续可微函数 $f(x)$ 是 $\mathbb{R}^n$上的凸函数当且仅当对$\forall x,y \in \mathbb{R}^n$

    $$
    \langle \nabla f(x)-\nabla f(y),x-y\rangle \ge 0
    $$


上面对凸函数的定义都要求可微，对于如不可微函数，有：

!!! note "Convex Function" 

    函数 $f$ 称为凸函数 (convex function)，如果其定义域为凸集，且对 $\forall x,y \in \text{dom } f,\alpha \in[0,1]$，有如下不等式成立：

    $$
    f(\alpha x+(1-\alpha)y)\le \alpha f(x)+(1-\alpha) f(y)
    $$

    这个定义没有出现任何的梯度，因此也就不局限于可微函数。如果不等号对于所有 $x≠y$ 都严格取 $<$ ，那么我们称 $f$ 为严格凸函数。我们称 $f$ 是凹函数（concave function）如果 $−f$ 是凸函数。



$s \in \mathbb{R}^{d}$ 称为 $g$ 在 $\theta$ 处的次梯度当且仅当其满足：

$$
\forall \theta' \in \mathbb{R}^{d},g(\theta)\geq g(\theta)+s^{T}(\theta'-\theta)
$$

次梯度的集合称为次导数 $\partial g(\theta)$ ，当 $g$ 是可微时，有 $\partial g(\theta)=\left\{ g'(\theta) \right\}$ 。可以证明次导数为非空集。

> Hilbert 曾经说过，一个数学问题能正确描述出来，那么就成功了 80% 。对于一个优化问题而言，如果能转化为凸优化问题，那么就成功了 90% 。——凌青

凸函数是为了解决如下困局：

对于一个光滑目标的无约束优化问题，在一阶优化条件的假设下，我们并不能做太多事情。即使我们能找到导数等于 $0$ 的解（驻点），但不能保证这是最优解（极小值），但是凸函数能保证我们能收敛到最优值。

常见的非凸问题有深度学习，凸优化方法的思想依旧可以用在深度学习上，只不过这个时候求出来的一般时函数的驻点，并不能确定是极小值点。

Lipschitz 连续条件也是一个对优化问题很有效的函数性质，对于一些非凸问题，如果能保证 Lipschitz 连续条件，那么能够给出一些不错的优化解法，如 [RSG](https://arxiv.org/abs/2111.13162) 算法。

!!! note "Lipschitz 连续"

    考虑满足一定阶导数的利普希茨条件（lipschitz condition）的可微函数。记 $Q$ 是 $\mathbb{R}^n$的子集，函数类 $C^{k,p}_L(Q)$ 满足

    - 在 $Q$ 上 $k$ 阶连续可微
    - 在 $Q$ 上的 $p$ 阶导数关于常数 $L$ 是Lipschitz连续，即

    $$
    \lVert f^{(p)}(x) - f^{(p)}(y) \rVert\le L\lVert x-y\rVert  \quad \forall x,y \in Q
    $$




在优化问题中，更关注的是一阶导数的 Lipschitz 连续和二阶导数的 Lipschitz 连续，Lipschitz 条件相当于对导数的约束，取欧几里得范数，则 Lipschitz 条件可以看作是一个球状的约束。

称满足一阶导数 Lipschitz 连续为 **$L$-光滑**。

!!! note  "strong convexity"

    如果存在常数 $\mu \ge 0$ 使得 $\forall x,y \in \mathbb{R}^n$  ，连续可微函数 $f$ 在 $\mathbb{R}^n$  有

    $$
    f(y)\ge f(x)+\langle\nabla f(x),y-x\rangle+\frac{1}{2}\mu\lVert y-x\rVert^2
    $$

    记 $f\in \mathcal{S}^1_\mu(\mathbb{R}^n)$ ，称常数 $\mu$ 为 $f$ 的凸参数（convexity parameter）。 该定义说明关于该强凸函数的[布雷格曼散度](https://en.wikipedia.org/wiki/Bregman_divergence)（Bregman divergence） $\mathcal{D}_f(x,y)$ 不小于 $\frac{1}{2}\mu \lVert y-x \rVert^2$ 



因为在高维空间里直接寻找近似解比较复杂，所以在给定一个空间 $\Theta =\left\{ \theta \in \mathbb{R}^{d},\Omega(\theta)\leq D \right\}$ 中寻找，此时对应的样本误差和逼近误差（空间逼近的能力）：

$$
f(\hat{\theta})-\min_{\theta \in \mathbb{R}^{d}}f(\theta)= \underset{ \mathrm{Estimation \,error} }{ \left[ f(\hat{\theta})-\min_{\theta\in\Theta}f(\theta) \right]  }+\underset{ \mathrm{Approxi tion \,error} }{ \left[\min_{\theta \in\Theta}f(\theta)-\min_{\theta \in \mathbb{R}^{d}}f(\theta)\right] }
$$

引用统一偏差界（uniform deviation）对误差进行放缩：

$$
\sup_{\theta\in\Theta} \lvert f(\theta)-\hat{f}(\theta) \rvert 
$$

衡量了整个参数空间上经验风险和真实风险之间的最大偏差。考虑 $\hat{\theta} \in \underset{\theta\in\Theta}{\arg\min}\hat{f}(\theta)$

$$
\begin{align}
f(\hat{\theta})-\min_{\theta \in\Theta} f(\theta) &= [f(\hat{\theta})-\hat{f}(\hat{\theta})]+[\hat{f}(\hat{\theta})-\hat{f}(\theta_{*})_{\Theta}] +[\hat{f}(\theta_{*})_{\Theta}-{f}(\theta_{*})_{\Theta}] \\
&\leq \sup_{\theta \in \Theta}\left\{ f(\theta)-\hat{f}(\theta) \right\} +0 +\sup_{\theta \in \Theta}\left\{ \hat{f}(\theta)-f(\theta)\right\}  \\
\implies&f(\hat{\theta})-\min_{\theta\in\Theta} f(\theta) \leq 2 \sup_{\theta \in \Theta}\lvert f(\theta)-\hat{f}(\theta) \rvert 
\end{align}
$$

收敛速度为 $\mathcal{O}(\sqrt{ 1 /n })$

假定我们选取的目标函数为最小二乘函数，即：

$$
\mathscr{l}(y,\theta^{T}\Phi(x)) = \frac{1}{2}(y-\theta^{T}\Phi(x))^{2} 
$$

损失函数：

$$
\begin{align}
f(\theta)-\hat{f} (\theta) = &\frac{1}{2}\theta^{T}\left( \frac{1}{n}\sum_{i=1}^{n} \Phi(x_{i})\Phi(x_{i})^{T}-\mathbb{E}\Phi(X)\Phi(X)^{T} \right) \theta  \\
&-\theta^{T}\left( \frac{1}{n}\sum_{i=1}^{n} y_{i}\Phi(x_{i})-\mathbb{E}Y\Phi(X) \right) +\frac{1}{2}\left( \frac{1}{n}\sum_{i=1}^{n} y_{i}^{2}-\mathbb{E}Y^{2} \right) 
\end{align}
$$

上界偏差：

$$
\begin{align}
\sup \lvert f(\theta)-\hat{f} (\theta)\rvert = &\frac{D^{2}}{2}\left\lVert  \frac{1}{n}\sum_{i=1}^{n} \Phi(x_{i})\Phi(x_{i})^{T}-\mathbb{E}\Phi(X)\Phi(X)^{T}   \right\rVert_{\mathrm{op}}   \\
&+D\left\lVert  \frac{1}{n}\sum_{i=1}^{n} y_{i}\Phi(x_{i})-\mathbb{E}Y\Phi(X)  \right\rVert_{2}  +\frac{1}{2}\left\lvert  \frac{1}{n}\sum_{i=1}^{n} y_{i}^{2}-\mathbb{E}Y^{2}  \right\rvert  
\end{align}
$$

根据集中不等式，他们的误差都收敛于 $\mathcal{O}_{P}\left( \frac{1}{\sqrt{ n }} \right)$

在上述讨论中，我们并没有考虑到函数的凸性，尽管这并不是一个很好的收敛速度（比较慢），但是给出了一套在一般假设情况下的收敛速度的分析：

!!! note "Slow rate for supervised learning"

    对于线性分类器 $\theta(x)=\theta^{T}\Phi(x),\lVert \Phi(x) \rVert_{2}\leq R$ ，采用欧几里得范数作为正则化项，期望损失和风险损失在 $\Theta=\left\{ \lVert \theta \rVert_{2}\leq D \right\}$ 上满足 GR-Lipschitz 连续，则：

    - 对于任意 $\delta\in(0,1)$ ，至少以概率 $1-\delta$ ，有：

    $$
    \sup_{\theta \in\Theta}\lvert \hat{f} (\theta)-f(\theta)\rvert \leq \frac{\mathscr{l}_{0}+GRD }{\sqrt{ n }}\left( 2+\sqrt{ 2\log \frac{2}{\delta} } \right) 
    $$

    - 对上界取期望：

    $$
    \mathbb{E}\left[ \sup_{\theta \in\Theta}\lvert \hat{f} (\theta)-f(\theta)\rvert \right]  \leq \frac{4\mathscr{l}_{0}+4GRD }{\sqrt{ n }}
    $$


!!! note "Fast rate for supervised learning"

    与上面假设相同，但是这个时候考虑函数的凸性，且： $f^{\mu}(\theta)=f(\theta)+\frac{\mu}{2}\lVert \theta \rVert^{2}_{2}$，$\hat{f}^{\mu}(\theta)=\hat{f}(\theta)+\frac{\mu}{2}\lVert \theta \rVert^{2}_{2}$ 则有：

    - 对于任意的 $a>0$ ，对于所有 $\theta\in \mathbb{R}^{d}$ ，至少以概率 $1-\delta$ ，有：

    $$
    f^{\mu}(\hat{\theta}) - \min_{\eta \in \mathbb{R}^{d}}f^{\mu}(\eta) \leq \frac{8G^{2}R^{2}\left( 32+\log \frac{1}{\delta} \right)}{\mu n}
    $$



也就是说这个时候收敛速度提升为 $\mathcal{O}\left( \frac{1}{n} \right)$ ，如果考虑强凸函数，收敛速度会更快

## Classical methods for convex optimization 

假定 $g$ 是 $\mathbb{R}^{d}$ 上的凸函数，经典的优化算法有：
 
- 梯度下降和加速梯度下降
- 牛顿法
- 次梯度法和椭球算法

### Gradient Descent 
对于满足 $L$-Lipschitz 连续的一般凸函数，采用如下的梯度下降方法：

$$
\theta_{t} = \theta_{t-1}- \frac{1}{L}g'(\theta_{t-1})
$$

假定 $\theta_{*}$ 为最优解，则

$$
g(\theta_{t})-g(\theta_{*}) \leq \frac{2L\lVert \theta_{0} -\theta_{* }\rVert }{t+4}
$$

可以看出这种情况下，收敛速度较慢

如果考虑 $\mu$ -强凸，有：

$$
g(\theta_{t})- g(\theta_{*}) \leq (1-\mu /L)^{t}[g(\theta_{0})-g(\theta_{*})]
$$

即满足线性收敛（或指数收敛）

利用二次函数来证明上述结论，考虑：

$$
g(\theta) = \frac{1}{2}\theta^{T}H\theta-c^{T}\theta
$$

$\mu$ 和 $L$ 分别是 $H$ 的最小和最大特征值，这个问题的解 $\theta_{*} = H^{-1}c$ 或者 $H^{+}c$ ，$H^{+}$ 表示伪逆。显然这需要计算矩阵的逆，并不是一件容易处理的事情，因此采用迭代算法。

$$
\theta_{t} = \theta_{t-1}-\frac{1}{L}(H\theta_{t-1}-c) = \theta_{t-1}-\frac{1}{L}(H\theta_{t-1}-H\theta_{*})
$$

所以

$$
\theta_{t}-\theta_{*} =\left( I-\frac{1}{L}H \right)(\theta_{t-1}-\theta_{*}) =\left( I- \frac{1}{L}H \right)^{t}(\theta_{0}-\theta_{*})
$$

$\left( I-\frac{1}{L}H \right)^{t}$ 的特征值在 $\left[ 0,\left( 1-\frac{\mu}{L} \right) ^{t}\right]$ 之间，可以得出此时迭代收敛的两个性质：

- 序列迭代的收敛性满足：

$$
\lVert \theta_{t}-\theta_{*} \rVert^{2}\leq \left( 1-\frac{\mu}{L} \right)^{2t}\lVert \theta_{0}-\theta_{* } \rVert ^{2} 
$$

- 函数值误差的收敛性：

$$
g(\theta_{t})-g(\theta_{*}) \leq \left( 1-\frac{\mu}{L} \right)^{2t}[g(\theta_{0})-g(\theta_{*})]
$$

满足线性收敛


如果 $\mu=0$ ，此时无法保证迭代序列能够收敛。 函数误差满足

$$
g(\theta_{t})-g(\theta_{*}) \leq \frac{L}{t}\lVert \theta_{0}-\theta_{* } \rVert^{2} 
$$

---
光滑凸函数有以下性质：

- 对于满足 $L$-光滑的凸函数 $g$ ，对所有的 $\theta,\eta \in \mathbb{R}^{d}$ 有：
	- $\lVert g'(\theta)-g'(\eta) \rVert\leq L\lVert \theta-\eta \rVert$ 
	- 二阶可微： $0 \preceq g''(x)\preceq LI$ 
- 二次上界约束：$0\leq g(\theta)-g(\eta)-g'(\eta)^{T}(\theta-\eta)\leq \frac{L}{2}\lVert \theta-\eta \rVert$
- Co-coercivity：$\frac{1}{L}\lVert g'(\theta)-g'(\eta) \rVert^{2}\leq[ g'(\theta)-g'(\eta)]^{T}(\theta-\eta)$
- 如果 $g$ 满足强凸有：$g(\theta)\leq g(\eta)+g'(\eta)^{T}(\theta-\eta)+\frac{1}{2\mu}\lVert g'(\theta)-g'(\eta) \rVert^{2}$
- “Distance” to optimum：$g(\theta)-g(\theta_{*})\leq g'(\theta)^{T}(\theta-\theta_{*})$

### Accelerated gradient method
凸函数 $g$ 满足 $L$-连续，最优点为 $\theta_{*}$。

并不直接使用存储的序列来迭代而是使用如下更新方法

$$
\theta_{t} =\eta_{t-1} - \frac{1}{L}g'(\eta_{t-1})
$$

同时，更新用于迭代的参数：

$$
\eta_{t} = \theta_{t}+ \frac{t-1}{t+2}(\theta_{t}-\theta_{t-1})
$$

在这种情况下，函数误差：

$$
g(\theta_{t})-g(\theta_{*})\leq \frac{2L\lVert \theta_{0} -\theta_{* }\rVert^{2} }{(t+1)^{2}}
$$

确实加快了收敛速度。

进一步考虑强凸性质可得：

$$
\begin{align}
\theta_{t}&= \eta_{t-1}-\frac{1}{L}g'(\eta_{t-1}) \\
\eta_{t}& =\theta_{t} + \frac{1-\sqrt{ \mu /L } }{1+\sqrt{ \mu /L }} (\theta_{t}-\theta_{t-1})
\end{align}
$$

此时对应函数误差：

$$
g(\theta_{t})-f(\theta_{* }) \leq L\lVert \theta_{0}-\theta_{* } \rVert ^{2}\left( 1-\sqrt{ {\mu} /L } \right)^{t}
$$

