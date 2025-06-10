# Logistic 回归和最大熵模型

## Logistic 回归

先定义 Logistic 分布：

!!! note "Logistic 分布"

    设 $X$ 是连续随机变量，$X$ 服从 logistic 分布是指 $X$ 具有下列分布函数和密度函数：

    $$
    \begin{align}
    F(x) = P(X\leq x) = \frac{1}{1+e^{-(x-\mu)/\gamma}}
    \end{align}
    $$

    $$
    f(x)= F'(x)= \frac{e^{-(x-\mu)/\gamma}}{\gamma(1+e^{-(x-\mu)/\gamma})^{2}}
    $$

    其中 $\mu$ 为位置参数，$\gamma>0$ 为形状参数

定义二项 Logistic 回归模型：

!!! note "逻辑斯谛回归模型"

    二项 logistic 回归模型是如下的条件概率分布：

    $$
    P(Y=1|x) = \frac{\exp(w\cdot x+b)}{1+\exp(w\cdot x+b)}
    $$

    $$
    P(Y=0|x) = \frac{1}{1+\exp(w\cdot x+b)}
    $$

    这里 $x\in \mathbb{R}^{n}$ 是输入空间，$Y\in \left\{ 0,1 \right\}$ 是输出，$w\in \mathbb{R}^{n}$ 和 $b\in \mathbb{R}^{n}$ 是参数，$w$ 称为权值向量，$b$ 称为偏置


对于给定的输入实例 $x$ ，可以求得 $P(Y=1|x)$ 和 $P(Y=0|x)$ 。logistic 回归比较两个条件概率值的大小，将实例 $x$ 分到概率值较大的那一类

为了方便，可以将偏置纳入权重向量，则

$$
\begin{align}
{w} &= \left( w^{(1)},w^{(2)},\dots,w^{(n)},b \right)^{T} \\
{x} &=\left( x^{(1)},x^{(2)},\dots,x^{(n)},1 \right)^{T}
\end{align}
$$

此时，对应的 logistic 回归模型：

$$
\begin{align}
P(Y=1|x) = \frac{\exp(w\cdot x)}{1+\exp(w\cdot x)}\\
P(Y=0|x) = \frac{1}{1+\exp(w\cdot x)}
\end{align}
$$

logistic 回归模型的特点：定义几率为事件发生的概率与发生的概率的比值，对数几率定义为：

$$
\mathrm{logit}(p) = \log \frac{p}{1-p}
$$

在 logistic 回归中：

$$
\log \frac{P(Y=1|x)}{1-P(Y=1|x)} = w\cdot x
$$

对于给定训练数据集 $T = \left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots(x_{N},y_{N}) \right\}$ 可以应用极大似然估计法估计模型参数，从而得到 logistic 回归模型。

取

$$
P(Y=1|x) = \pi(x)\quad P(Y=0|x)= 1-\pi(x)
$$

似然函数：

$$
\prod^{N}_{i=1} \left[ \pi(x_{i}) \right] ^{y_{i}}[1-\pi(x_{i})]
$$

对数似然函数形式（方便计算）：

$$
\begin{align}
L(w) &= \sum_{i=1 }^{N} \left[ y_{i}\log \pi(x_{i})+ (1-y_{i})\log(1-\pi(x_{i}))\right]  \\
&=\sum_{i=1}^{N} \left[ y_{i}\log \frac{\pi(x_{i})}{1-\pi(x_{i})}+\log(1-\pi(x_{i})) \right]  \\
&= \sum_{i=1}^{N} [y_{i}(w\cdot x_{i})-\log(1+\exp(w\cdot x_{i}))]
\end{align}
$$

对 $L(w)$ 求极大值，得到 $w$ 的估计值

这样问题就变成了以对数似然函数为目标函数的最优化问题，logistic 回归学习中通常采用的方法是梯度下降法和拟牛顿法。

最后定义多项 Logistic 回归模型：


!!!note "多项逻辑斯谛回归"

    假设离散型随机变量 $Y$ 的取值集合是 $\{1,2,\dots,K\}$ ，则多项 logistic 回归

    $$
    P(Y=k|x) = \frac{\exp(w_{k}\cdot x)}{1+\sum_{}^{} \exp(w_{k}\cdot x)}
    $$

    $$
    P(Y=K|x) = \frac{1}{1+\sum_{}^{} \exp(w_{k}\cdot x)}
    $$

    最后一项是为了保证概率累计结果为 $1$ 

## 最大熵原理

### KL 散度

[解读KL散度：从定义到优化方法 | 机器之心](https://www.jiqizhixin.com/articles/2018-07-24-10)


KL 散度（**Kullback-Leibler divergence**），称为相对熵，是两个概率分布间差异的非对称度量（它不是两个分布之间真实的“距离”度量，因为它不是对称的）。在离散随机变量的情形下：设 $P(x)$ 和 $Q(x)$ 是随机变量 $X$ 上的两个概率分布，假定 $P$ 为真实分布， $Q$ 为近似分布，则对应的 KL 散度为：

$$
\mathrm{KL}(P||Q) = \sum_{}^{} P(x) \log \frac{P(x)}{Q(x)}
$$

性质：

1.  $\mathrm{KL}(P||Q)\geq 0$
2.  $P=Q$ 时，$\mathrm{KL}(P||Q) = 0$
3. $\mathrm{KL}(P||Q) \neq \mathrm{KL}(Q||P)$ 非对称性

证明第一个性质，依旧利用 Jensen 不等式

$$
\begin{align}
\mathrm{KL}(P||Q) &= \sum_{}^{} P(x) \log \frac{P(x)}{Q(x)} = -\sum_{}^{} P(x) \log \frac{Q(x)}{P(x)} \\
&\geq -\log \left(\sum_{}^{}  P(x)\cdot \frac{Q(x)}{P(x)}  \right) =-\log \left( \sum_{}^{} Q(x) \right) =0  
\end{align}
$$

定义交叉熵：

$$
H(P,Q) = \sum_{}^{} P(x)\log Q(x)
$$

所以 KL 散度可以视作是熵和交叉熵之和

### 最大熵模型

假设离散随机变量 $X$ 的概率分布是 $P(x)$ ，熵

$$
H(P) = -\sum_{x}^{}P(x)\log P(x) 
$$

熵满足不等式：

$$
0\leq H(P)\leq \log \lvert X \rvert 
$$

当 $X$ 均布分布时，右边的等号成立，也就是说，当 $X$ 服从均匀分布时，熵最大。

最大熵原理认为要选择的概率模型首先满足已有的事实，即约束条件，**在没有信息的情况下，那些不确定的部分都是“等可能的”**。最大熵原理通过熵的最大化来表示等可能性。这是因为“等可能”不容易操作，但是熵是一个可优化的数值。

---
现在研究最大熵模型的约束条件：

假定分类模型是一个条件概率分布 $P(Y|X)$ ，$X \in \mathcal{X}\subseteq \mathbb{R}^{n}$ 表示输入，$Y\in \mathcal{Y}$ 表示输出，$\mathcal{X}$ 和 $\mathcal{Y}$ 分别是输入和输出的集合。这个模型表示的是对于给定的输入 $X$ ，以条件概率 $P(Y|X)$ 输出 $Y$

给定训练数据集 

$$
T = \left\{ (x_{1},y_{1}),(x_{2},y_{2}),\dots,(x_{N},y_{N}) \right\} 
$$

可以确定联合分布 $P(X,Y)$ 的经验分布和边缘分布 $P(X)$ 的经验分布，分别以 $\tilde{P}(X,Y)$ 和 $\tilde{P}(X)$ 表示：

$$
\begin{align}
&\tilde{P}(X=x,Y=y) = \frac{\nu(X=x,Y=y)}{N} \\
&\tilde{P}(X=x) = \frac{\nu(X=x)}{N}
\end{align}
$$

其中 $\nu$ 表示频数。

我们用特征函数(feature function) $f(x,y)$ 描述输入 $x$ 和输出 $y$ 之间的某一个关系，定义为

$$
f(x,y) = \left\{\begin{aligned} 
1,&\quad x与y 满足某一事实\\
0,&\quad 否则 
\end{aligned}\right.
$$

这是一个二值函数，当 $x$ 和 $y$ 满足这个事实取值为 $1$ ，否则为 $0$ 。


特征函数 $f(x,y)$ 关于经验分布 $\tilde{P}(X,Y)$ 的期望值，

$$
\mathbb{E}_{\tilde{P}}(f) = \sum_{x,y}\tilde{P}(x,y)f(x,y)
$$

这个是从训练数据中得到得到的经验期望

特征函数 $f(x,y)$ 关于模型 $P(Y|X)$ 与经验分布 $\tilde{P}(X)$ 的期望值，

$$
\mathbb{E}_{P}(f) = \sum_{x,y}\tilde{P}(x)P(y|x)f(x,y) 
$$

这个是模型给出的期望

如果模型能够获取训练数据中的信息，那么就可以假设这两个期望值相等

$$
\begin{align}
\mathbb{E}_{P}(f)= \mathbb{E}_{\tilde{P}}(f) 
\Leftrightarrow \sum_{x,y}^{} \tilde{P}(x)P(y|x)f_{i}(x,y)=\sum_{x,y}^{} \tilde{P}(x,y)f_{i}(x,y)
\end{align}
$$

我们称其为模型学习的约束条件，假如有 $n$ 个特征函数 $f_{i}(x,y)$ ，那么就有 $n$ 个约束条件

因此可以定义最大熵模型


!!! note "最大熵模型"

    设满足所有约束条件的模型集合为

    $$
    \mathcal{C}\equiv \left( P\in \mathcal{P} |\mathbb{E}_{P}(f_{i})=\mathbb{E}_{\tilde{P}}(f_{i}),\ i=1,2,\dots,n \right)  
    $$

    定义在概率分布 $P(Y|X)$ 上的条件熵：

    $$
    H(P) = -\sum_{x,y}^{}\tilde{P}(x)P(y|x)\log P(y|x) 
    $$

    则模型集合 $\mathcal{C}$ 中条件熵 $H(P)$ 最大的模型称为最大熵模型。

最大熵模型的学习等价于一个约束最优化问题:


$$
\begin{align}
\min_{P\in \mathcal{C} }\quad& -H(P) = \sum_{x,y}^{} \tilde{P}(x)P(y|x)\log P(y|x) \\
\mathrm{s.t.}\quad& \mathbb{E}_{P}(f_{i})-\mathbb{E}_{\tilde{P}}(f_{i})=0\quad i=1,2,\dots,n \\
&\sum_{y}^{} P(y|x)=1
\end{align}
$$

对于一般的约束优化问题，采用拉格朗日乘子法将其转化成无约束最优化对偶问题，对偶问题会在 SVM 中详细介绍

记

$$
\Psi(w)= \min_{P \in \mathcal{C} }L(P,w) = L(P,w)
$$

称作对偶函数，将其解记作 

$$
P_{w} =\arg \min_{P \in \mathcal{C} } L(P,w) = P_{w}(y|x)
$$

具体地，求 $L(P,w)$ 对 $P(y|x)$ 的偏导数

$$
\begin{align}
\frac{\partial L(P,w) }{\partial P(y|x) }  &= \sum_{x,y}^{} \tilde{P}(x)(\log P(y|x)+1)-\sum_{y}^{} w_{0}-\sum_{x,y}^{} \left( \tilde{P}(x)\sum_{i=1}^{n} w_{i}f_{i}(x,y) \right)  \\
&=\sum_{x,y }^{}\tilde{P}(x)\left( \log P(y|x) +1-w_{0}\sum_{i=1}^{n} w_{i}f_{i}(x,y)\right)  
\end{align}
$$

令偏导数结果为零，解得

$$
P(y|x) = \frac{\exp \left( \underset{ i=1 }{ \overset{ n }{ \sum } }w_{i}f_{i}(x,y) \right) }{\exp(1-w_{0})}
$$

由于  $\underset{ y }{ \overset{  }{ \sum } }P(y|x)=1$ 可得

$$
P_{w}(y|x) = \frac{1}{Z_{w}(x)} \exp \left( \sum_{i=1}^{n} w_{i}f_{i}(x,y) \right) 
$$

其中 

$$
Z_{w}(x) = \sum_{y}^{} \exp \left( \sum_{i=1}^{n} w_{i}f_{i}(x,y) \right) 
$$

称为规范化因子。（消去了 $\exp(1-w_{0})$）

之后求解对偶问题的极大化问题：

$$
\max_{w}\Psi(w)
$$

将其解记为 $w^{*}$ ，即

$$
w^{*} = \arg \max_{w}\Psi(w)
$$

$P^{*}=P_{w^{*}}=P_{w^{*}}(y|x)$ 是学习到的最优模型，也就是说最大熵模型的学习归结为对偶函数 $\Psi(w)$ 的极大化


对偶函数的极大化等价于最大熵模型的极大似然估计。证明如下：已知训练数据的经验概率分布 $\tilde{P}(X,Y)$ ，条件概率分布 $P(Y|X)$ 的对数似然函数表示为

$$
L_{\tilde{P}}(P_{w}) = \log \prod^{}_{x,y}P(y|x)^{\tilde{P}(x,y)} = \sum_{x,y}^{} \tilde{P}(x,y)\log P(y|x)
$$

当条件概率分布 $P(y|x)$ 满足最大熵模型时

$$
\begin{align}
L_{\tilde{P}}(P_{w})&= \sum_{x,y}^{} \tilde{P}(x,y) \log P(y|x) \\
&=\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} w_{i}f_{i}(x,y)-\sum_{x,y}^{} \tilde{P}(x,y)\log Z_{w}(x) \\
&=\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} w_{i}f_{i}(x,y)-\sum_{x}^{} \tilde{P}(x)\log Z_{w}(x)
\end{align}
$$

考虑对偶函数

$$
\begin{align}
\Psi(w) &= \sum_{x,y} \sum_{n}^{n} \tilde{P}(x) P_w(y|x) \log P_w(y|x) + \nonumber \\
&\quad \sum_{i=1}^{n} w_i \left( \sum_{x,y} \tilde{P}(x,y) f_i(x,y) - \sum_{x,y} \tilde{P}(x) P_w(y|x) f_i(x,y) \right) \nonumber \\
&= \sum_{x,y} \tilde{P}(x,y) \sum_{i=1}^{n} w_i f_i(x,y) + \sum_{x,y} \tilde{P}(x) P_w(y|x) \left( \log P_w(y|x) - \sum_{i=1}^{n} w_i f_i(x,y) \right) \nonumber \\
&= \sum_{x,y} \tilde{P}(x,y) \sum_{i=1}^{n} w_i f_i(x,y) - \sum_{x,y} \tilde{P}(x) P_w(y|x) \log Z_w(x) \nonumber \\
&= \sum_{x,y} \tilde{P}(x,y) \sum_{i=1}^{n} w_i f_i(x,y) - \sum_{x} \tilde{P}(x) \log Z_w(x)
\end{align}
$$

最后一步用到 $\underset{ y }{ \overset{  }{ \sum } }P(y|x)=1$


## 模型学习的最优化算法

!!! tip
    理论分析，优化的东西就是这么复杂且头疼

考虑最大熵模型的对数似然函数：

$$
L(w) = \sum_{x,y}^{} \tilde{P}(x,y) \sum_{i=1}^{n} w_{i}f_{i}(x,j)-\sum_{x}^{}\tilde{P}(x)\log Z_{w}(x) 
$$

改进的迭代尺度法(improved iterative scaling,IIS)的想法是：假设最大熵模型当前的参数向量是 $w = \left( w_{1} ,w_{2},\dots,w_{n}\right)^{T}$，我们希望找到一个新的参数向量 $w+\delta = \left( w_{1}+\delta_{1} ,w_{2}+\delta_{2},\dots,w_{n}+\delta_{n}\right)^{T}$，使得模型的对数似然函数值增大。如果能有这样一种参数向量更新的方法：$\tau:w\to w+\delta$ ，那么就可以重复使用这一方法，直至找到对数似然函数的最大值。

对于给定的经验分布 $\tilde{P}(x,y)$ ，模型参数从 $w$ 到 $w+\delta$ ，对数似然函数的改变量：

$$
\begin{align}
L(w+\delta)-L(w) &= \sum_{x,y}^{} \tilde{P}(x,y)\log P_{w+\delta}(y|x)-\sum_{x,y}^{} \tilde{P}(x,y)\log P_{w}(y|x) \\
& = \sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} \delta_{i}f_{i}(x,y)-\sum_{x}^{} \tilde{P}(x)\log \frac{Z_{w+\delta}(x)}{Z_{w}(x)}
\end{align}
$$

考虑不等式：

$$
-\log\alpha \geq 1-\alpha
$$

可得对数似然函数的下界：

$$
\begin{align}
L(w+\delta)-L(w) &\geq \sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} \delta_{i}f_{i}(x,y)+1-\sum_{x}^{}\tilde{P}(x)  \frac{Z_{w+\delta}(x)}{Z_{w}(x)} \\
& = \sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} \delta_{i}f_{i}(x,y)+1-\sum_{x}^{}\tilde{P}(x)  \sum_{y}^{} P_{w}(y|x)\exp \sum_{i=1}^{n} \delta_{i}f_{i}(x,y) 
\end{align}
$$

记作

$$
L(w+\delta)-L(w) \geq A(\delta|w)
$$

即 $A(\delta|w)$ 是对数似然函数该变量的一个下界

如果能找到适当的 $\delta$ 使下界 $A(\delta|w)$ 提高，那么对数似然函数也会提高。然而，函数 $A(\delta|w)$ 的 $\delta$ 是一个向量，含有多个变量，不易同时优化。IIS 试图一次只优化其中一个变量 $\delta_{i}$ ，而固定其他变量 $\delta_{j}$方法如下：

引进 

$$
f^{\#}(x,y) = \sum_{i}^{} f_{i}(x,y)
$$

$f^{\#}(x,y)$ 表示所有特征在 $(x,y)$ 出现的次数。这样, $A(\delta|w)$ 可以写为

$$
\begin{align}
A(\delta|w) =&\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} \delta_{i}f_{i}(x,y)+1- \\
&\sum_{x}^{}\tilde{P}(x)\sum_{y}^{} P_{w}(y|x)\exp \left( f^{\# }(x,y)\sum_{i=1}^{n} \frac{\delta_{i}f_{i}(x,y)}{f^{\#}(x,y)} \right)  
\end{align}
$$

受 $\underset{ i=1 }{ \overset{ n }{ \sum } }\frac{f_{i}(x,y)}{f^{\#}(x,y)}=1$  启发，引入 Jensen 不等式

$$
\exp \left( \sum_{i=1}^{n} \frac{f_{i}(x,y)}{f^{\#}(x,y)} \delta_{i}f^{\# }(x,y)\right) \leq \sum_{i=1}^{n} \frac{f_{i}(x,y)}{f^{\#}(x,y)} \exp(\delta_{i}f^{\#}(x,y)) 
$$

于是上式可以改写为

$$
\begin{align}
A(\delta|w) \geq&\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} \delta_{i}f_{i}(x,y)+1- \\
&\sum_{x}^{}\tilde{P}(x)\sum_{y}^{} P_{w}(y|x)\sum_{i=1}^{n} \frac{f_{i}(x,y)}{f^{\#}(x,y)} \exp(\delta_{i}f^{\#}(x,y))   
\end{align}
$$

将不等式右端记为 $B(\delta|w)$ 则得到一个对数似然函数的一个新的（相对不紧的）下界：

$$
L(w+\delta)-L(w) \geq B(\delta|w)
$$

对 $B(\delta|w)$ 求偏导：

$$
\frac{\partial B(\delta|w) }{\partial \delta_{i} } =\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} f_{i}(x,y)-\sum_{x}^{}\tilde{P}(x)\sum_{y}^{} P_{w}(y|x){f_{i}(x,y)} \exp(\delta_{i}f^{\#}(x,y)) 
$$

取偏导数为零

$$
\sum_{x,y}^{}\tilde{P}(x) P_{w}(y|x){f_{i}(x,y)} \exp(\delta_{i}f^{\#}(x,y)) = \mathbb{E}_{\tilde{P}}(f_{i})
$$

于是，依次对 $\delta_{i}$ 求解上述方程可以求出 $\delta$ 

!!! note "改进的迭代尺度算法 IIS"

    输入：特征函数 $f_{1},f_{2},\dots,f_{n}$；经验分布 $\tilde{P}(X,Y)$，模型 $P_w(y|x)$ 

    输出：最优参数值 $w^{*}_{i}$ ；最优模型 $P_{w^{*}}$ 

    (1) 对所有 $i\in \left( 1,2,\dots,n \right)$，取初值 $w_{i}=0$ 

    (2) 对每一 $i\in \left\{ 1,2,\dots,n \right\}$

    (2.1) 令 $\delta_{i}$ 是方程

    $$
    \sum_{x,y}^{}\tilde{P}(x) P_{w}(y|x){f_{i}(x,y)} \exp(\delta_{i}f^{\#}(x,y)) = \mathbb{E}_{\tilde{P}}(f_{i})
    $$

    的解    

    (2.2) 更新 $w_{i}:w_{i }\leftarrow w_{i}+\delta$ 

    (3) 如果不是所有 $w_{i}$ 都收敛，重复 $(2)$ 


最大熵模型：

$$
P_{w}(y|x) = \frac{\exp \left( \underset{ i=1 }{ \overset{ n }{ \sum } } w_{i}f_{i}(x,y) \right) }{ \underset{ y }{ \overset{  }{ \sum } }\exp \left( \underset{ i=1 }{ \overset{ n }{ \sum } } w_{i}f_{i}(x,y) \right) } 
$$

目标函数：

$$
\underset{ w\in \mathbb{R}^{n} }{ \min } f(w) = \sum_{x}^{} \tilde{P}(x)\log \sum_{y}\exp^{}\left( \sum_{i=1}^{n} w_{i}f_{i}(x,y) \right) -\sum_{x,y}^{} \tilde{P}(x,y)\sum_{i=1}^{n} w_{i}f_{i}(x,y) 
$$

梯度：

$$
g(w)= \left( \frac{\partial f(w) }{\partial w_{1} } ,\frac{\partial f(w) }{\partial w_{2} } ,\dots,\frac{\partial f(w) }{\partial w_{n} }  \right)^{T}
$$

其中 

$$
\frac{\partial f(w) }{\partial w_{i} }  =\sum_{x,y}^{}\tilde{P}(x)P_{w}(y|x)f_{i}(x,y)-\mathbb{E}_{\tilde{P}}(f_{i}) 
$$

IIS 有：收敛慢、对特征要求严格、计算量大等缺点，实际上更常用的算法是 BFGS 算法

BFGS 是一种无须计算二阶导数（Hessian 矩阵）的优化方法，但通过近似更新来逼近 Hessian 的逆矩阵，从而实现比普通梯度下降更快的收敛。

!!! note "最大熵模型学习的 BFGS 算法"

    输入：特征函数 $f_{1},f_{2},\dots,f_{n}$ ；经验分布 $\tilde{P}(x,y)$ 目标函数 $f(w)$ ，梯度 $g(w)=\nabla f(w)$，精度 $\varepsilon$ 

    输出：最优参数值 $w^{*}$ ；最优模型 $P_{w^{*}}(y|x)$ 

    (1) 选定初始点 $w^{(0)}$ ,取正定对称矩阵 $B_{0}$，置 $k=0$ 

    (2) 计算 $g_{k}=g(w^{(k)})$ ，若 $\lVert g_{k} \rVert<\varepsilon$ 则停止计算，$w^{*} =w^{(k)}$ 否则转 $(3)$

    (3) 有 $B_{k}p_{k}=-g_{k}$ 求出 $p_{k}$

    (4) 一维搜索：求 $\lambda_{k}$ 使得：

    $$
    f(w^{(k)}+\lambda_{k}p_{k}) = \min_{\lambda\geq 0} f(w^{(k)}+\lambda p_{k})
    $$

    (5) 置 $w^{(k+1) }=w^{(k)}+\lambda_{k}g_{k}$

    (6) 计算 $g_{k+1}=g(w^{k+1})$ ，若 $\lVert g_{k+1} \rVert< \varepsilon$ ，则停止计算，得 $w^{*} = w^{k+1}$ 否则，计算：

    $$
    B_{k+1} = B_{k} +\frac{y_{k}y_{k}^{T}}{y_{k}^{T}\delta_{k}} - \frac{B_{k}\delta_{k}\delta_{k}^{T}B_{k}}{\delta_{k}^{T}B_{k}\delta_{k}}
    $$

    其中 

    $$
    y_{k} =g_{k+1}-g_{k}\quad \delta_{k } = w^{(k+1)}-w^{(k)}
    $$

    (7) 置 $k=k+1$ 转 3

