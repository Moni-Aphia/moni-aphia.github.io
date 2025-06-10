# EM 算法

[EM 算法](https://zhuanlan.zhihu.com/p/78311644)

概率模型有时既含有观测变量，又含有隐变量或者潜在变量。EM算法用于含有隐变量的概率模型参数的极大似然估计或者加大后验概率估计，每次迭代有两步组成：E 步，用于求期望，M 步，用于求极大值。这一算法又称为期望极大算法，简称 EM 算法。

用 $Y$ 表示观测随机变量的数据，$Z$ 表示隐随机变量的数据，$Y$ 和 $Z$ 连在一起称为完全数据，观测数据 $Y$ 称为不完全数据

对于一个含有隐变量的概率模型，目标是极大化观测数据（不完全数据）$Y$  关于参数 $\theta$ 的对数似然函数，即极大化：

$$
\begin{align}
L(\theta) &= \log P(Y|\theta) =\log \sum_{Z}^{} P(Y,Z|\theta) \\
&= \log \left( \sum_{Z}^{} P(Y|Z,\theta)P(Z|\theta) \right) 
\end{align}
$$

这里 $P(\cdot|\theta)$ 表示变量与 $\theta$ 有关。

困难：式中有未观测数据并有包含和（或积分）的对数

EM 算法通过迭代逐步近似极大化 $L(\theta)$ ，假设第 $i$ 次迭代后 $\theta$ 的估计是 $\theta^{(i)}$ ，我们希望新估计值 $\theta$ 能使 $L(\theta)$ 增加，即 $L(\theta)>L(\theta^{(i)})$ ，并逐步达到极大值。考虑：

$$
L(\theta) -L(\theta^{(i)}) = \log\left(  \sum_{Z}^{} P(Y|Z,\theta)P(Z|\theta)\right) -\log P(Y|\theta^{(i)}) 
$$

利用 Jensen 不等式：

$$
\begin{align}
L(\theta) -L(\theta^{(i)}) &= \log\left(  \sum_{Z}^{}P(Z|Y,\theta^{(i)}) \frac{P(Y|Z,\theta^{})P(Z|\theta)}{P(Z|Y,\theta^{(i)})}\right) -\log P(Y|\theta^{(i)})  \\
&\geq \sum_{Z}^{}P(Z|Y,\theta^{(i)})\log\frac{P(Y|Z,\theta^{})P(Z|\theta)}{P(Z|Y,\theta^{(i)})} -\log P(Y|\theta^{(i)}) \\
&=\sum_{Z}^{}P(Z|Y,\theta^{(i)})\log\frac{P(Y|Z,\theta^{})P(Z|\theta)}{P(Z|Y,\theta^{(i)})P(Y|\theta^{(i)})} 
\end{align}
$$

这里运用了 $\sum_{Z}^{}P(Z|Y\theta^{(i)})=1$ 记

$$
B(\theta,\theta^{(i)}) \triangleq L(\theta^{(i)})+\sum_{Z}^{}P(Z|Y,\theta^{(i)})\log\frac{P(Y|Z,\theta^{})P(Z|\theta)}{P(Z|Y,\theta^{(i)})P(Y|\theta^{(i)})} 
$$

则 

$$
L(\theta ) \geq B(\theta,\theta^{(i)})
$$

即函数 $B(\theta,\theta^{(i)})$ 是 $L(\theta)$ 的一个下界


$$
\begin{align}
\theta^{(i+1)}& =\arg \max_{\theta} \left(L(\theta^{(i)})+\sum_{Z}^{}P(Z|Y,\theta^{(i)})\log\frac{P(Y|Z,\theta^{})P(Z|\theta)}{P(Z|Y,\theta^{(i)})P(Y|\theta^{(i)})}   \right)  \\
&=\arg \max_{\theta} \left( \sum_{Z}^{}P(Z|Y,\theta^{(i)})\log(P(Y|Z,\theta)P(Z|\theta))  \right)  \\
& = \arg \max_{\theta}\left( \sum_{Z}^{} P(Z|Y,\theta^{(i)})\log P(Y,Z|\theta) \right) \triangleq \arg\max_{\theta}Q(\theta,\theta^{(i)})
\end{align}
$$

上面等价于 EM 算法的一次迭代， $Q(\theta,\theta^{(i)})$ 是 EM 算法的核心，称为 $Q$ 函数，EM 算法通过不断求解下界的极大化来逼近对数似然函数的极大化。

!!! important "Q 函数"

    完全数据的对数似然函数 $\log P(Y,Z|\theta)$ 关于在给定观测数据 $Y$ 和当前参数 $\theta^{(i)}$ 下对未观测数据 $Z$ 的条件概率分布 $P(Z|Y,\theta^{(i)})$ 的期望称为 $Q$ 函数，即

    $$
    Q(\theta,\theta^{(i)})=\mathbb{E}_{Z} [\log P(Y,Z|\theta)|Y,\theta^{(i)}]
    $$





!!! note "EM算法"

    输入：观测变量数据 $Y$ ，隐变量数据 $Z$ ，联合分布 $P(Y,Z|\theta)$ ，条件分布 $P(Z|Y,\theta)$ 

    输出：模型参数 $\theta$

    (1) 选择参数的初值 $\theta^{(0)}$ ，开始迭代

    (2) E 步：记 $\theta^{(i)}$ 为第 $i$ 次迭代参数 $\theta$ 的估计值，在第 $i+1$ 次迭代的 E 步，计算

    $$
    \begin{align}
    Q(\theta,\theta^{(i)}) &=\mathbb{E}_{Z}[\log P(Y,Z|\theta)|Y,\theta^{(i)}]\\
    &=\sum_{Z}^{} \log P(Y,Z|\theta)P(Z|Y,\theta^{(i)})
    \end{align}
    $$

    (3) M 步：求使 $Q(\theta,\theta^{(i)})$ 极大化的 $\theta$ ，确定第 $i+1$ 次迭代的参数的估计值 $\theta^{(i+1)}$ 

    $$
    \theta^{(i+1)} = \arg \max_{\theta} Q(\theta,\theta^{(i)})
    $$

    重复第 2 步和第 3 步，直到收敛


==EM 算法不能保证找到全局最优值==

参数的初值可以任意选择，但 EM 算法对初值敏感

停止迭代一般有如下两种条件：对于较小的正数 $\varepsilon_{1},\varepsilon_{2}$ ，满足：

$$
\lVert \theta^{(i+1)}-\theta^{(i)} \rVert \leq \varepsilon_{1} \text{ or } \lVert Q(\theta^{(i+1)},\theta^{(i)})-Q(\theta^{(i)},\theta^{(i)}) \rVert <\varepsilon_{2}
$$


!!! note "Theorem"

    设 $P(Y|\theta)$ 为观测数据的似然函数，$\theta^{(i)}$ 为 EM 算法得到的参数估计序列，$P(Y|\theta^{(i)})$ 为对应的似然函数序列，则 $P(Y|\theta^{(i)})$ 是单调递增的，即：

    $$
    P(Y|\theta^{(i+1)})\geq P(Y|\theta^{(i)})
    $$


由于

$$
P(Y|\theta) = \frac{P(Y,Z|\theta)}{P(Z|Y,\theta)}\implies \log P(Y|\theta) = \log P(Y,Z|\theta)-\log P(Z|Y,\theta)
$$

令

$$
H(\theta,\theta^{(i)}) =\sum_{Z}^{} \log P(Z|Y,\theta)P(Z|Y,\theta^{(i)})
$$

于是对数似然函数可以写作：

$$
\log P(Y|\theta) = Q(\theta,\theta^{(i)})-H(\theta,\theta^{(i)})
$$

取 $\theta^{(i)}$ 和 $\theta^{(i+1)}$ 相减可得：

$$
\begin{align}
\log &P(Y|\theta^{(i+1)})-\log P(Y|\theta^{(i)})\\ &= [Q(\theta^{(i+1)},\theta^{(i)})-Q(\theta^{(i)},\theta^{(i)})]-[H(\theta^{(i+1)},\theta^{(i)})-H(\theta^{(i)},\theta^{(i)})]
\end{align}
$$

可以证明第一项恒为正，对于第二项：

$$\begin{align}
H(\theta^{(i+1)},\theta^{(i)})-H(\theta^{(i)},\theta^{(i)})&=\sum_{Z}^{} \left( \log \frac{P(Z|Y,\theta^{(i+1)})}{P(Z|Y,\theta^{(i)} )} \right) P(Z|Y,\theta^{(i)}) \\
&\leq \log \left( \sum_{Z}^{}  \frac{P(Z|Y,\theta^{(i+1)})}{P(Z|Y,\theta^{(i)} )} P(Z|Y,\theta^{(i)})\right)  \\
&= \log \left( \sum_{Z}^{} P(Z|Y,\theta^{(i+1)}) \right) =0
\end{align}
$$

所以这时非正的，因此，可以证明是单调递增。

!!! note "Theorem"

    设 $L(\theta)=\log P(Y|\theta)$ 为观测数据的对数似然函数，$\theta^{(i)}$ 为 EM 算法得到的参数估计序列，$L(\theta^{(i)})$ 为对应的对数似然函数序列。

    1. 如果 $P(Y|\theta)$ 有上界，则 $L(\theta^{(i)})=\log P(Y|\theta^{(i)})$ 收敛到某一值 $L^{*}$
    2. 在函数 $Q(\theta,\theta')$ 与 $L(\theta)$ 满足一定条件下，由 EM 算法得到的参数估计序列 $\theta^{(i)}$ 的收敛值 $\theta ^{*}$ 是 $L(\theta)$ 的稳定点


EM 算法的收敛性包含对数似然函数序列 $L(\theta^{(i)})$ 的收敛性和关于参数估计序列 $\theta^{(i)}$ 的收敛性两层意思，前者并不蕴涵后者。此外，定理只能保证参数估计序列收敛都对数似然函数序列的稳定点，不能保证收敛到极大值点。所以在应用中，初值的选择变得非常重要，常用的办法是选取几个不同的初值进行迭代，然后对得到的各个估计值加以比较，从中选择最好的。

## 高斯混合模型

!!! note "高斯混合模型"

    高斯混合模型是指具有如下形式的概率分布模型：

    $$
    P(y|\theta) =\sum_{k=1}^{K} \alpha_{k}\phi(y|\theta_{k})
    $$

    其中 $\alpha_{k}$ 是系数，$\phi(y|\theta_{k})$ 是高斯分布密度，$\theta_{k}=(\mu_{k},\sigma^{2}_{k})$

    $$
    \phi(y|\theta_{k}) = \frac{1}{\sqrt{ 2\pi }\sigma_{k}} \exp \left( -\frac{(y-\mu_{k})}{2\sigma_{k}^{2}} \right) 
    $$

    称为第 $k$ 个分模型。

假设观测数据 $y_{1},y_{2},\dots y_{N}$ 由高斯混合模型生成：

$$
P(y|\theta) = \sum_{k=1}^{K} \alpha_{k}\phi(y|\theta_{k}) 
$$

这里的参数 $\theta = (\alpha_{1},\alpha_{2},\dots,\alpha_{K};\theta_{1},\theta_{2},\dots,\theta_{K})$ 。

我们设想观测数据 $y_{i}$ 是依概率 $\alpha_{k}$ 选择第 $k$ 个高斯分模型 $\phi(y|\theta_{k})$ 生成的。这是观测数据是已知的，反应观测数据的模型数据是位置，用隐变量 $\gamma_{jk}$ 表示，定义如下：

$$
\gamma_{ik}= \begin{cases}
1 & 第 j个观测来自第  k个分模型 \\
0  & 
\end{cases}
$$

$\gamma_{jk}$ 是 $0-1$ 随机变量。因此，有了观测数据 $y_{j}$ 以及未观测数据 $\gamma_{jk}$ ，那么完全数据为：

$$
(y_{j},\gamma_{j1},\gamma_{j 2},\dots,\gamma_{jK})
$$

对应似然函数：

$$
P(y,\gamma|\theta) = \prod^{K}_{k=1}\alpha_{k}^{n_{k}} \prod_{j=1}^{N}\left[ \frac{1}{\sqrt{ 2\pi  }\sigma_{k}}\exp \left( -\frac{(y_{j}-\mu_{k})^{2}}{2\sigma_{k}^{2}} \right)  \right]^{\gamma_{jk}} 
$$

其中 $n_{k}= \sum_{j=1}^{N}\gamma_{jk},\sum_{k=1}^{K}n_{k}=N$，对数似然函数：

$$
\log P(y,\gamma|\theta) = \sum_{k=1}^{K}\left\{ n_{k}\log \alpha_{k} +\sum_{j=1}^{N}\gamma_{jk}\left[ \log \left( \frac{1}{\sqrt{ 2\pi }} \right) -\log\sigma_{k}-\frac{1}{2\sigma^{2}_{k}}(y_{j}-\mu_{k})^{2} \right] \right\}  
$$

确定 Q 函数：

$$
\begin{align}
Q(\theta,\theta^{(i)}) &= \mathbb{E}[\log P(y,\gamma|\theta)|y,\theta^{(i)}] \\
& = \sum_{k=1}^{K} \Bigg\{ \sum_{j=1 }^{N}(\mathbb{E}\gamma_{jk})\log\alpha_{k}  \\
&\quad+\sum_{j=1}^{N}(\mathbb{E}\gamma_{jk})\left[ \log \left( \frac{1}{\sqrt{ 2\pi }} \right) -\log\sigma_{k}-\frac{1}{2\sigma^{2}_{k}}(y_{j}-\mu_{k})^{2} \right]\Bigg\}
\end{align}
$$

现在需要计算 $\mathbb{E}(\gamma_{jk}|t,\theta)$ ，记为 $\hat{\gamma}_{jk}$

$$
\begin{align}
\hat{\gamma}_{jk}&= P(\gamma_{jk}=1|y,\theta) \\
&= \frac{P(@y_{jk}=1,y_{j}|\theta)}{\sum_{k=1}^{K} P(\gamma_{jk}=1,y_{i}|\theta)}  \\
&= \frac{P(@y_{jk}=1,y_{j}|\theta)P(\gamma_{jk}=1|\theta)}{\sum_{k=1}^{K} P(\gamma_{jk}=1,y_{i}|\theta)P(\gamma_{jk}=1|\theta)}  \\
&= \frac{\alpha_{k}\phi(y_{i}|\theta_{k})}{\sum_{k=1}^{K} \alpha_{k}\phi(y_{j}|\theta_{k})}
\end{align}
$$

$\hat{\gamma}_{jk}$ 是在当前模型参数下第 $j$ 个观测数据来自第 $k$ 个分模型的概率，称为分模型 $k$ 对观测数据 $y_{j}$ 的响应度。可得 $Q$ 函数

$$
Q(\theta,\theta^{(i)}) =\sum_{k=1}^{K} \left\{ \hat{n}_{k} \log\alpha_{k}+\sum_{j=1}^{N} \hat{\gamma}_{jk}\left[ \log \left( \frac{1}{\sqrt{ 2\pi }} \right)-\log\sigma_{k}-\frac{1}{2\sigma_{k}^{2}}  (y_{j}-\mu_{k})^{2}\right]  \right\} 
$$

迭代的 $M$  步是求函数 $Q(\theta,\theta^{(i)})$ 对 $\theta$ 的极大值，即求新一轮迭代的模型参数：

$$
\theta^{(i+1)} = \arg \max_{\theta}Q(\theta,\theta^{(i)})
$$

结果如下：

$$
\begin{align}
\hat{\mu}_{k} &=  \frac{\sum_{j=1}^{N} \hat{\gamma}_{ij}y_{j}}{\sum_{j=1}^{N} \hat{\gamma}_{jk} } \\
\hat{\sigma}_{k}^{2}& = \frac{\sum_{j=1}^{N} \hat{\gamma}_{jk}(y_{j}-\mu _{k})^{2}}{\sum_{j=1}^{N} \hat{\gamma}_{jk}} \\
\hat{\alpha}_{k}& = \frac{\sum_{j=1}^{N} \hat{\gamma}_{jk} }{N}
\end{align}
$$

!!! note "高斯混合模型参数估计的EM算法"

    输入：观测数据 $y_{1},y_{2},\dots y_{N}$ ，高斯混合模型

    输出：高斯混合模型参数

    (1) 取参数的初始值开始迭代
    (2) E 步：计算分模型 $k$ 对观测数据 $y_{j}$ 的响应度

    $$
    \hat{\gamma}_{jk} = \frac{\alpha_{k}\phi(y_{i}|\theta_{k})}{\sum_{k=1}^{K} \alpha_{k}\phi(y_{j}|\theta_{k})}
    $$

    (3) M 步：计算新一轮迭代的模型参数：

    $$
    \begin{align}
    \hat{\mu}_{k} &=  \frac{\sum_{j=1}^{N} \hat{\gamma}_{ij}y_{j}}{\sum_{j=1}^{N} \hat{\gamma}_{jk} } \\
    \hat{\sigma}_{k}^{2}& = \frac{\sum_{j=1}^{N} \hat{\gamma}_{jk}(y_{j}-\mu _{k})^{2}}{\sum_{j=1}^{N} \hat{\gamma}_{jk}} \\
    \hat{\alpha}_{k}& = \frac{\sum_{j=1}^{N} \hat{\gamma}_{jk} }{N}
    \end{align}
    $$

    (4) 重复 2 和 3 步，直到收敛

