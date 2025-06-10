#  AdaBoost

!!! note
    课程的编程作业要求实现一个决策树核SVM核的AdaBoost算法，从结果来说，Adaboost算法对弱学习器的提升效果非常好，而 SVM 本身就是一个强学习器，Adaboost 算法的提升效果并没有那么好，甚至延长了训练时间。

多分类 SAMMR 算法可以参见：[Datawhale](https://datawhalechina.github.io/ML-FTTI/01_tree_ensemble/03_ada.html)

在概率近似正确(probably approximately correct, PAC)学习的框架中，一个概念（类），如果存在一个多项式的学习算法能够学习它，并且正确率很高，称这个概念是强可学习的；一个概念（类），如果存在一个多项式的学习算法能够学习它，学习的正确率仅比随机猜测略好，则称这个概念是弱可学习的。

Schapire证明：

>在PAC学习的框架下，一个概念是强可学习的充分必要条件是这个概念是弱可学习。


Adaboost 起源于这个想法：只要找到一个比随机猜测略好的弱学习算法就可以直接将其提升为强学习算法，而不必直接去找很难获得的强学习算法


在训练过程中 Adaboost 提高那些被前一轮弱分类器错误分类样本的权值，降低那些被正确分类样本的权重。对于训练出来的多个弱分类器，Adaboost 采取加权多数表决，加大分类误差率小的弱分类器的权值，使其在表决中起较大的作用，减小分类误差率大的弱分类器的权值，使其在表决中起较小的作用。



!!! note "AdaBoost 算法"

    输入：二分类的训练数据集 $T =\left\{ (x_{1},y_{1}) ,(x_{2},y_{2}),\dots,(x_{N},y_{N})\right\},x_{i}\in \mathcal{X}\subseteq \mathbb{R}^{n},y_{i}\in \mathcal{Y}=\left\{ +1,-1 \right\}$

    输出：最终分类器 $G(x)$

    (1) 初始化训练数据的起始权值分布：

    $$
    \mathcal{D}_{1} = \left( w_{11},\dots,w_{1N} \right),w_{1i} = \frac{1}{N}  
    $$

    (2) 对 $m= 1,2,\dots M$

    在权值 $D_{m}$ 训练数据集，得到弱分类器

    $$
    G_{m}(x):\mathcal{X}\to \left\{ -1,+1 \right\}  
    $$

    计算 $G_{m}$ 的训练误差：

    $$
    e_{m}=\sum_{i=1}^{N}P(G_{m}(x_{i})\neq y_{i}) = \sum_{i=1}^{N} w_{mi}l(G_{m}(x_{i})\neq y_{i}) 
    $$

    计算分类器 $G_{m}$ 的系数：

    $$
    \alpha_{m} = \frac{1}{2}\log \frac{1-e_{m}}{e_{m}}
    $$

    更新训练数据集的权值分布

    $$
    \mathcal{D}_{m+1} = (w_{m+1,1},\dots,w_{m+1,N}) \quad w_{m+1,i} = \frac{w_{mi}}{Z_{m}}\exp (-\alpha_{m}y_{i}G_{m}(x_{i}))
    $$

    其中 $Z_{m}$ 称为规范化（归一化）因子：$Z_{m}=\sum_{}^{}w_{mi}\exp (-\alpha_{m}y_{i}G_{m}(x_{i}))$

    (3) 构建弱分类器的线性组合：

    $$
    f(x) = \sum_{m=1}^{M} \alpha_{m}G_{m}(x)
    $$

    得到最终分类器：

    $$
    G(x)= \mathrm{sign}(f(x)) =\mathrm{sign}\left( \sum_{m=1}^{M} \alpha_{m}G_{m}(x) \right)
    $$



可以证明，权重满足两个性质：

1. 当 $e_{m}< \frac{1}{2}$ 时， $\alpha_{m}>0$
2. $\alpha_{m}$ 关于 $e_{m}$ 单调递减

第一个性质是我们对弱学习器的要求，我们希望它的表现比随机分类要好一点（在二分类的情况下，误差为 $\frac{1}{2}$ ） 下面会证明 $\alpha_{m}$ 的表达式

!!! note "Theorem"

    AdaBoost 算法最终分类器的训练误差届为：

    $$
    \frac{1}{N}\sum_{i=1}^{N} I(G(x_{i})\neq y_{i})\leq \frac{1}{N}\sum_{i}^{} \exp \left( -y_{i}f(x_{i}) \right) = \prod_{m}Z_{m}
    $$



证明第二个等式，将左边展开成：

$$
\frac{1}{N} \sum_{i}^{} \exp \left( -\sum_{m=1}^{M} \alpha_{m}y_{i}G_{m}(x_{i}) \right) 
$$

因为初始权重 $w_{1i}=\frac{1}{N}$ ，所以上面可以写作如下形式：

$$
\sum_{i=1}^{} w_{1i}\prod _{m=1}^{M} \exp\left( - \alpha_{m}y_{i}G_{m}(x_{i}) \right) 
$$

利用性质：

$$
w_{mi} \exp(-\alpha_{m},y_{i}G_{m}(x_{i}))=Z_{m}w_{m+1,i}
$$

做如下递推：

$$
\begin{align}
&\sum_{i=1}^{} w_{1i}\prod _{m=1}^{M} \exp\left( - \alpha_{m}y_{i}G_{m}(x_{i}) \right)  \\
=&\,Z_{1} \sum_{i}^{} w_{2i}\prod^{M}_{m=2}\exp (-\alpha_{m}y_{i}G_{m}(x_{i})) \\
=&\, Z_{1}Z_{2}\sum_{i}^{} w_{3i}\prod^{M}_{m=3}\exp (-\alpha_{m}y_{i}G_{m}(x_{i})) \\
=&\dots \\
=&\,Z_{1}Z_{2}\dots Z_{M-1}\sum_{i}^{} w_{Mi}\exp (-\alpha_{m}y_{i}G_{M}(x_{i})) \\
=&\,\prod _{m=1}^{M}Z_{m}
\end{align}
$$

!!! note "Theorem"

    二分类问题 AdaBoost 的训练误差界为：

    $$
    \prod_{m=1}^{M}Z_{m} =\prod _{m=1}^{M}[2\sqrt{ e_{m}(1-e_{m}) }] = \prod_{m=1}^{M} \sqrt{ (1-4\gamma^{2}_{m}) }\leq \exp \left( -2 \sum_{m=1}^{M} \gamma^{2}_{m} \right) 
    $$

    其中 $\gamma_{m} = \frac{1}{2}e_{m}$

我们找到了一个误差上界，如果我们能求得这个上界的最小值，实际上就能求得最大误差的上界：

$$
\begin{align}
Z_{m} &= \sum_{i=1}^{N}w_{mi}\exp(-\alpha_{m}y_{i}G_{m}(x_{i}))  \\
& = \sum_{y_{i} = G_{m}(x_{i})}^{} w_{mi}e^{-{\alpha}_{m}}+\sum_{y_{i}\neq G_{m}(x_{i})}^{} w_{mi}e^{{\alpha}_{m}} \\
&=(1-e_{m})e^{-{\alpha}_{m}}+e_{m}e^{\alpha_{m}}
\end{align}
$$

我们希望对上式取最小值，对 $\alpha_{m}$ 求导并取零可得：

$$
-e^{-\alpha_{m}}(1-e_{m})+e_{m}e^{\alpha_{m}} = 0 \Leftrightarrow \alpha_{m} = \frac{1}{2}\log \frac{1-e_{m}}{e_{m}}
$$

我们就得到了  $\alpha_{m}$ 的表达式

将 $\alpha_{m}$ 的表达式带入可以求得第二个、第三个等式。

采用如下不等式：

$$
1-t\leq e^{-t}
$$

可以证明不等式

!!! Tip "Corollary"

    如果存在 $\gamma>0$ ，对所有 $m$ 有 $\gamma_{m}\geq \gamma$ ，则：

    $$
    \frac{1}{N}\sum_{i=1}^{N} I(G(x_{i})\neq y_{i})\leq \exp (-2M\gamma^{2})
    $$


---
AdaBoost 算法时模型为加法模型，损失函数为指数函数，学习算法为前向分步算法的二类分类学习算法


定义加法模型(additive model)

$$
f(x) = \sum_{m=1}^{M} \beta_{m}b(x;\gamma_{m})
$$

其中，$b(x;\gamma_{m})$ 为基函数，$\gamma_{m}$ 为基函数的参数， $\beta_{m}$ 为基函数的系数。在给定训练束及损失函数 $L(x,f(x))$ 的条件下，学习加法模型 $f(x)$ 称为经验风险极小化即损失函数极小化问题：

$$
\min_{\beta_{m},\gamma_{m}}\sum_{i=1}^{N} L\left( y_{i},\sum_{m=1}^{M} \beta_{m}b(x_{i};\gamma_{m})\right) 
$$

前向分布算法求解这一优化问题的想法是：因为学习是加法模型，如果能够从前向后，每一步只学习一个基函数及其系数，逐步逼近优化目标函数，具体地，每步只需优化如下损失函数：

$$
\min_{\beta,\gamma}\sum_{i=1}^{N} L(y_{i},\beta b(x_{i};\gamma))
$$

!!! note "前向分布算法"

    输入：训练数据集 $T =\left\{ (x_{1},y_{1}) ,(x_{2},y_{2}),\dots,(x_{N},y_{N})\right\}$；损失函数 $L(y,f(x))$；基函数集 $\left\{ b(x;\gamma) \right\}$

    输出：加法模型 $f(x)$

    (1) 初始化 $f_{0}(x)=0$
    (2) 对 $m=1,2,\dots,M$

    极小化损失函数：

    $$
    (\beta_{m},\gamma_{m}) =\arg \min_{\beta,\gamma}\sum_{i=1}^{N} L(y_{i},f_{m-1}(x_{i})+\beta b(x_{i};\gamma))
    $$

    得到参数 $\beta_{m},\gamma_{m}$

    更新

    $$
    f_{m}(x)=f_{m-1}(x) +\beta_{m}b(x;\gamma_{m})
    $$

    (3) 得到加法模型：

    $$
    f(x)=f_{M}(x) = \sum_{m=1}^{M} \beta_{m}b(x;\gamma_{m})
    $$


!!! note "Theorem"
    AdaBoost 算法是前向分步加法算法的特例。这时，模型是由基本分类器组成的加法模型，损失函数是指数函数


提升方法实际采用加法模型（即基函数的线性组合）与前向分布算法。以决策树为基函数的提升方法称为提升树。提升树模型表示为决策树的加法模型：

$$
f_{M}(x) = \sum_{m=1}^{M} T(x;\Theta_{m})
$$

第 $m$ 步模型：

$$
f_{m}(x) = f_{_{m-1}}(x)+T(x;\Theta_{m})
$$

通过经验风险极小化确定下一棵决策树的参数 $\Theta_{m}$ ：

$$
\hat{\Theta}_{m} = \arg \min_{\Theta_{m}}\sum_{i=1}^{N} L(y_{i},f_{m-1}(x_{i})+T(x_{i},\Theta_{m}))
$$


回归问题提升树使用以下前向分步算法：

$$
\begin{align}
f_{0}(x)&= 0 \\
f_{m}(x)& = f_{m-1}(x)+T(x;\Theta_{m}) \\
f_{M}(x)&= \sum_{m=1}^{M} T(x;\Theta_{m})
\end{align}
$$

!!! note "回归问题的提升树算法"

    输入：训练数据集 $T =\left\{ (x_{1},y_{1}) ,(x_{2},y_{2}),\dots,(x_{N},y_{N})\right\},x_{i}\in \mathcal{X}\subseteq \mathbb{R}^{n},y_{i}\subseteq \mathbb{R}$

    输出：提升树 $f_{M}(x)$

    (1) 初始化 $f_{0}(x)=0$

    (2) 对 $m=1,2,\dots,M$

    计算残差

    $$
    r_{mi} =y_{i}-f_{m-1}(x_{i})
    $$

    拟合残差 $r_{mi}$ 学习一个回归树，得到 $T(x;\Theta_{m})$

    更新 $f_{m}(x)=f_{m-1}(x)+T(x;\Theta_{m})$

    (3) 得到回归问题提升树：

    $$
    f_{M}(x)=\sum_{m=1}^{M} T(x;\Theta_{m})
    $$

!!! note "梯度提升算法"

    输入：训练数据集 $T =\left\{ (x_{1},y_{1}) ,(x_{2},y_{2}),\dots,(x_{N},y_{N})\right\},x_{i}\in \mathcal{X}\subseteq \mathbb{R}^{n},y_{i}\subseteq \mathbb{R}$，损失函数 $L(y,f(x))$

    输出：提升树 $f_{M}(x)$

    (1) 初始化

    $$
    f_{0}(x) = \arg \min_{c}\sum_{i=1}^{N} L(y_{i},c)
    $$

    (2) 对 $m=1,2,\dots,M$

    对 $i=1,2,\dots,N$ ，计算

    $$
    r_{mi}=-\left[ \frac{\partial L(y_{i},f(x_{i})) }{\partial f(x_{i}) }  \right] _{f(x)=f_{m-1}(x)}
    $$

    对 $r_{mi}$ 拟合一个回归树，得到第 $m$ 棵树的叶结点 $R_{mj}$

    对 $j=1,2,\dots,J$，计算：

    $$
    c_{mj} = \arg \min_{c}\sum_{x_{i}\in R_{mj}}^{} L(y_{i},f_{m-1}(x_{i})+c)
    $$

    更新 $f_{m}(x)=f_{m-1}(x)+\sum_{j=1}^{J}c_{mj}I(x\in R_{mj})$

    (3) 得到回归树：

    $$
    \hat{f}(x)=f_{M}(x)=\sum_{m=1}^{M} \sum_{j=1}^{J} c_{mj}I(x\in R_{mj})
    $$

