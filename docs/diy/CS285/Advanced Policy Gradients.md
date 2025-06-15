# Advanced Policy Gradients

[slides](http://rail.eecs.berkeley.edu/deeprlcourse/deeprlcourse/static/slides/lec-9.pdf)

笔记参考：[ CS285 深度强化学习 (7): Advanced Policy Gradients - 知乎](https://zhuanlan.zhihu.com/p/23688028368)

## Policy gradient as policy iteration 

先回顾 REINFORCE  算法：

!!! note "REINFORCE algorithm" 

    1. 从 $\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})$ 中采样 $\left\{ \tau^{i} \right\}$
    2. 计算 $\nabla_{\theta}J(\theta)=\frac{1}{N}\sum_{i}\sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \sum_{t'=t}^{T}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'}) \right)$
    3. 更新 $\theta \leftarrow \theta+\alpha \nabla_{\theta}J(\theta)$ 
    


实际上，梯度下降过程中采用的梯度为：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N}\Bigg[ \Big( \sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\Big) \Big( \underbrace{Q^{\pi  }(\mathbf{s}_{t},\mathbf{a}_{t})-V^{\pi}(\mathbf{s}_{t})}_{A^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t})}\Big)   \Bigg]  
$$

从概念上理解这个过程就是

1. 用当前策略 $\pi$ 估计 $\hat{A}^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t})$
2. 利用 $\hat{A}^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t})$ 获得改进后的 $\pi'$

这个和之前所讨论的poilcy iteration 算法很相似：

1. 评估 $A^{\pi}(\mathbf{s},\mathbf{a})$ 
2. 更新$\pi \leftarrow \pi'$

实际上，Policy gradient 可以看作是 Policy iteration 的一个“软版本”，在 Policy iteration 过程中，我们进行了 $\arg \max$ 操作，将概率全部赋予找到最大优势函数的策略；但在 Policy gradient 中，我们朝着给出比较大优势函数的策略方向改进，远离那些给出比较小优势函数策略。

简化 $J(\theta)$ 的表达式：

$$
J(\theta) = \mathbb{E}_{\tau \sim p_{\theta}(\tau)}\left[ \sum_{t}\gamma^{t}r(\mathbf{s}_{t},\mathbf{a}_{t}) \right] 
$$

在此基础上做如下主张：

!!! tip "claim"

    $$
    J(\theta') - J(\theta) = \mathbb{E}_{\tau \sim p_{\theta'}(\tau)}\left[ \sum_{t=1}^{T} \gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right] 
    $$

也就是两者的差异是新策略的轨迹分布下，旧策略优势函数的期望。也就是说在改变 $\theta'$ 的情况，我们使得 $J(\theta')-J(\theta)$ 变大，那么我们就极大地改进了原有的策略，这听起来有点奇怪，因为在这个过程中 $J(\theta)$ 是无关紧要的，这是正确的看法。于是如果我们能够最大化方程右侧，我们实际上是在做最大化 $J(\theta')$ 。

这个主张说明，如果我们能够优化旧策略优势函数在新策略的期望着，那么确实优化了强化学习目标的改进，那么我们就证明了利用旧策略优势函数并在新策略下最大化它，是优化新策略的正确方法。

我们来证明这个主张

$$
\begin{align}
J(\theta')-J(\theta) &= J(\theta')-\mathbb{E}_{\mathbf{s}_{0}\sim p(\mathbf{s}_{0})}[V^{\pi_{\theta}}(\mathbf{s}_{0})] \\
&=J(\theta')-\mathbb{E}_{\tau\sim p_{\theta'}(\tau)} [V^{\pi_{\theta}}(\mathbf{s}_{0})] \\
&=J(\theta')-\mathbb{E}_{\tau\sim p_{\theta'}(\tau))}\left[ \sum_{t=0}^{\infty} \gamma^{t}V^{\pi_{\theta}}(\mathbf{s}_{t}) -\sum_{t=1}^{\infty} \gamma^{t}V^{\pi_{\theta}}(\mathbf{s}_{t}) \right]  \\
&=J(\theta')+\mathbb{E}_{\tau\sim p_{\theta'}(\tau)}\left[ \sum_{t=0}^{\infty} \gamma^{t}(\gamma V^{\pi_{\theta}}(\mathbf{s}_{t+1}) - V^{\pi_{\theta}}(\mathbf{s}_{t})) \right]  \\
&=\mathbb{E}_{\tau \sim p_{\theta'}(\tau)}\left[ \sum_{t=0}^{\infty}\gamma^{t}r(\mathbf{s}_{t},\mathbf{a}_{t})  \right]+\mathbb{E}_{\tau \sim p_{\theta'}(\tau)} \left[ \sum_{t=0}^{\infty}\gamma^{t}(\gamma V^{\pi_{\theta}}(\mathbf{s}_{t+1})-V^{\pi_{\theta}}(\mathbf{s}_{t}))  \right]  \\
&=\mathbb{E}_{\tau \sim p_{\theta'}(\tau)}\left[ \sum_{t=0}^{\infty} \gamma^{t}(r(\mathbf{s}_{t},\mathbf{a}_{t})+\gamma V^{\pi_{\theta}}(\mathbf{s}_{t+1})-V^{\pi_{\theta}}(\mathbf{s})_{t}) \right]  \\
&= \mathbb{E}_{\tau \sim p_{\theta'}(\tau)}\left[ \sum_{t=0}^{\infty}\gamma^{t}A^{\pi_{\theta}}  (\mathbf{s}_{t},\mathbf{a}_{t})\right] 
\end{align}
$$

首先将参数 $\theta$ 从期望中提取出来，然后将期望下的分布转换为新目标参数（注意他们的初始状态需要相同），这是因为不同 policy 采样的轨迹关于 $\mathbf{s}_{0}$ 的边缘分布式相同的。然后将优势函数表示成两项和的差，提取折扣系数并变号，将 $J(\theta')$ 也展开并合并可得优势函数。

利用重要性采样可以进一步转化表达式：

$$
\begin{align}
\mathbb{E}_{\tau \sim p_{\theta'}(\tau)}\left[ \sum_{t}^{}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t})  \right] &=\sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}'(\mathbf{s}_{t})}[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}[\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t})]] \\
&=\sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}'(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right]
\end{align}
$$

但现在还有一个问题是状态的分布是关于 $\theta'$ 的而不是 $\theta$ ，我们不知道 $p'_{\theta}(\mathbf{s}_{t})$ 因此无法实现采样。为此又提出一个主张：

!!! tip "claim"

    $p_{\theta}(\mathbf{s}_{t})$ 近似于 $p_{\theta'}(\mathbf{s}_{t})$ 当 $\pi_{\theta}$ 近似于 $\pi_{\theta'}$

那么此时就可以直接 $p_{\theta'}(\mathbf{s}_{t})$ 替换成 $p_{\theta}(\mathbf{s}_{t})$，那么：

$$
J(\theta')-J(\theta) \approx\underbrace{  \sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right] }_{\bar{A}(\theta')}
$$

现在内部只有一个参数与 $\pi_{\theta'}$ 有关，微分时，我们就能恢复策略梯度，也可以使用如下方式最大化 $J(\theta')$

$$
\theta \leftarrow \arg \max_{\theta'}\bar{A}(\theta')
$$


## Bounding the Distribution Change 

我们讨论一下在什么能够用 $p_{\theta}$ 来替代 $p_{\theta'}$ 并仍然有一个合理的优化目标，能够准确地近似新策略的回报。

首先假定 $\pi_{\theta}$ 为确定性的分布，意味每一个状态策略确定地选择摸一个动作： $\mathbf{a}_{t}=\pi_{\theta}(\mathbf{s}_{t})$ ，当 $\pi_{\theta'}(\mathbf{a}_{t}\neq \pi_{\theta}(\mathbf{s}_{t})|\mathbf{s}_{t})\leq \varepsilon$ ，可以认为 $\pi_{\theta'}$ 近似于 $\pi_{\theta}$ ，于是可以将概率分布做如下拆解：

$$
p_{\theta'}(\mathbf{s}_{t}) = (1-\varepsilon)^{t}p_{\theta}(\mathbf{s}_{t})+(1-(1-\varepsilon)^{t})p_{\mathrm{mistake}}(\mathbf{s}_{t})
$$

这个形式很像之前讨论过的走钢丝的情形。我们关心  $p_{\theta}$ 和 $p_{\theta'}$ 之间的差异，则

$$
\lvert p_{\theta'}(\mathbf{s}_{t})-p_{\theta}(\mathbf{s}_{t}) \rvert  = (1-(1-\varepsilon)^{t})\lvert p_{\mathrm{mistake}}(\mathbf{s}_{t})-p_{\theta}(\mathbf{s}_{t}) \rvert  \leq 2(1-(1-\varepsilon)^{t}) \leq 2\varepsilon t
$$

第二个不等式使用了放缩，显然这并不是一个很好的约束，因为随着时间的增长，误差也随之变大，正如我们在走钢丝情形讨论过的一样，但这终究给出了一个界限。

上面地讨论基于确定性分布，接下来关注给出任意分布 $\pi_{\theta}$

给出引理：

!!! tip "lemma"

    如果 $\lvert p_{X}(x)-p_{Y}(x) \rvert = \varepsilon$ ，存在 $p(x)=p_{X}(x)$  和 $p(y)=p_{Y}(y)$ 和 $p(x=y)=1-\varepsilon$ 

也就是说如果你有两个关于同一变量的概率分布，那么可以构造一个关于两个变量的联合分布，使得该联合分布关于第一个和第二个参数的边缘分布为假设的两个分布，且参数相同的概率为 $1-\varepsilon$ 。这个引理帮助我们将前面的结果拓展到策略是随机的情况。

- $p_{X}(x)$ 和 $p_Y(y)$ 相同的概率为 $\varepsilon$ 
- $\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})$ 采取不同于 $\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})$ 的动作的概率至多为 $\varepsilon$ 

因此，对于任意分布 $\pi_{\theta}$ ，总是有：

$$
\lvert p_{\theta'}(\mathbf{s}_{t})-p_{\theta}(\mathbf{s}_{t}) \rvert  \leq 2\varepsilon t
$$

考虑：

$$
\begin{align}
\mathbb{E}_{p_{\theta'}(\mathbf{s}_{t})} [f(\mathbf{s}_{t})] =\sum_{\mathbf{s}_{t}}p_{\theta'}(\mathbf{s}_{t})f(\mathbf{s}_{t})&\geq \sum_{\mathbf{s}_{t}}p_{\theta}(\mathbf{s}_{t})f(\mathbf{s}_{t})-\lvert p_{\theta}(\mathbf{s_{t}})-p_{\theta'}(\mathbf{s_{t}}) \rvert \max_{\mathbf{s}_{t}}f(\mathbf{s}_{t}) \\
&\geq \mathbb{E}_{p_{\theta}(\mathbf{s}_{t})}[f(\mathbf{s}_{t})]-2\varepsilon t \max_{\mathbf{s}_{t}}f(\mathbf{s}_{t})
\end{align}
$$

将 $p_{\theta'}(\mathbf{s}_{t})$ 写作：$p_{\theta}(\mathbf{s}_{t})-p_{\theta}(\mathbf{s}_{t})+p_{\theta'}(\mathbf{s}_{t})$  并对于概率求和可得到第一个不等式。尽管第二项有一个取最大的操作，但只要我们的 $\varepsilon$ 足够小，并不会有太大的影响 

回到我们所关心的方程：

$$
\sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}'(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right]
$$

利用上面的推导可以得出一个下界：

$$
\sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right]-\sum_{t}^{}2\varepsilon t C
$$

其中 $C = \mathcal{O}(T r_{max})$ ，这个最大值其实很简单，因为我们所关心的是优势函数，那么优势函数能取到的最大值就是时间步数乘以最大奖励。对于无限时间，则为 $\mathcal{O}\left( \frac{r_{max}}{1-\gamma} \right)$

有些时候，计算 $\lvert p_{\theta'}(\mathbf{s}_{t})-p_{\theta}(\mathbf{s}_{t}) \rvert$ 并不是一件容易的事情，而且绝对值函数也不是一个处处可微的函数，因此，我们采用更方便的界。引用 KL 散度：

$$
D_{KL} (p_{1}(x)||p_{2}(x)) = \mathbb{E}_{x \sim p_{1}(x)}\left[ \log \frac{p_{1}(x)}{p_{2}(x)} \right] 
$$

KL 散度有很不错的性质，给出：

$$
\lvert \pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t}) -\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t}) \rvert \leq \sqrt{ \frac{1}{2}D_{\mathrm{KL}}(\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})||\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})) }
$$

因此要求 

$$
D_{\mathrm{KL}}(\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})||\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t}))\leq \varepsilon
$$

利用拉格朗日函数求解约束优化问题：

$$
\begin{align}
\mathcal{L}(\theta',\lambda) =&  \sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right] \\
& -\lambda(D_{\mathrm{KL}}(\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})||\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t}))-\varepsilon)
\end{align}
$$

对偶算法的思路是：

1. 对 $\theta'$ 最大化 $\mathcal{L}(\theta',\lambda)$ 
2. $\lambda \leftarrow \lambda+\alpha(D_{\mathrm{KL}}(\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})||\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t}))-\varepsilon)$

## Natural Gradient

总结一下我们想要优化的目标函数：

$$
\theta' \leftarrow \arg \max_{\theta'} \sum_{_{t}}\mathbb{E}_{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right]  
$$

约束条件：$D_{\mathrm{KL}}(\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})||\pi_{\theta} (\mathbf{a}_{t}|\mathbf{s}_{t}))\leq \varepsilon$  $\varepsilon$ 是用于提升 $J(\theta')-J(\theta)$ 的小量。

如果想要优化一个复杂的非线性函数，一种方法是在一定区域内做线性近似（一阶泰勒展开），于是我们的任务就变成了优化这个线性函数，而这个区域实际上是对我们线性近似的约束，称为信任区域。一般而言，这个区域的划定由我们设定的约束条件给出，例如上面的 KL 散度，因此，优化方式就变成了：

$$
\theta' \leftarrow \arg \max_{\theta'} \nabla_{\theta}\bar{A}(\theta)^{T}(\theta'-\theta)
$$ 

这使我们得到了一个尤为简单的算法。

对于梯度

$$
\nabla_{\theta}\bar{A}(\theta )= \sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[ \frac{\pi_{\theta'}(\mathbf{a}_{t}|\mathbf{s}_{t})}{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\gamma^{t}\nabla_{\theta'}\log{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t}) \right]\right]
$$

如果我们在 $\theta'=\theta$ 的点上评估时，重要性采样得到的权重会相互抵消，得到：

$$
\nabla_{\theta}\bar{A}(\theta )= \sum_{t}\mathbb{E} _{\mathbf{s}_{t}\sim p_{\theta}(\mathbf{s}_{t})}\left[\mathbb{E}_{\mathbf{a}_{t}\sim \pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}\left[\gamma^{t}\nabla_{\theta'}\log{\pi_{\theta}(\mathbf{a}_{t}|\mathbf{s}_{t})}A^{\pi_{\theta}}(\mathbf{s}_{t},\mathbf{a}_{t})\right]\right]= \nabla_{\theta}J(\theta)
$$

这个就是我们之前所讨论的 policy gradient

如果采用简单的梯度上升的方法：

$$
\theta \leftarrow \theta +\alpha \nabla_{\theta}J(\theta)
$$

一个可能的问题就是参数变化会引起较大的概率变化从而违背了原来的 KL 散度约束。

gradient ascent 实际上做的事是：

$$
\theta' \leftarrow \arg \max_{\theta'} \nabla_{\theta}J_{\theta}(\theta'-\theta)
$$

在 $\lVert \theta-\theta' \rVert^{2}\leq \varepsilon$ 

注意我们此时仍用线性函数，于是梯度上升更像是朝着 $\nabla_{\theta}J_{\theta}$ 的方向迈了一步，而这一步的大小正是 $\varepsilon$ 。因此，我们更像是以如下方式进行梯度上升：

$$
\theta' = \theta +\sqrt{ \frac{\varepsilon}{\lVert \nabla_{\theta}J(\theta) \rVert ^{2}} } \nabla_{\theta}J(\theta)
$$

原本简单的梯度上升确实解决了一个约束优化问题，不过这是一个错误的约束，这个约束在 $\theta$（参数） 空间而不是分布空间中。我们的信赖域约束，**本质上是加在策略的分布空间（policy space）上的**，只不过我们的约束需要在参数空间上去实现。直观上约束形状对于梯度下降而言是一个圆，但是我们更希望这是一个椭圆，这个椭圆沿着高度敏感的方向被压扁，我们希望在策略分布变化比较大的方向上有一个更紧的约束，因为这个方向会导致概率的大变化（并不是我们希望做的），而那些即使参数做大幅度调整也只会导致分布发生比较小的方向上放宽我们的约束。

对 $KL$ 散度做二次泰勒展开，不做一次泰勒展开的原因是 $KL$ 散度在 $\theta'$ 上是平坦的，但是其二阶导数不为零：

$$
D_{\mathrm{KL}} (\pi_{\theta'}||\pi_{\theta})\approx \frac{1}{2}(\theta'-\theta)\mathbf{F}(\theta'-\theta)
$$

可以证明 $\mathbf{F}$ 是 Fisher 信息矩阵，由如下表达式给出：

$$
\mathbf{F}= \mathbb{E}_{\pi_{\theta}}[\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}|\mathbf{s})\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}|\mathbf{s})^{T}]
$$

使用 Fisher 信息的好处是它是对某个量的期望，因此我们可以使用采样的方法来模拟近似。这是设定的二次约束，将约束带入 $\theta$ 空间中，可以将原来的圆变成由 $\mathbf{F}$ 控制的椭圆，这个椭圆告诉我们哪些方向会导致概率的大变化，哪些方向又会导致概率的小变化。采用如下更新方法：

$$
\theta' = \theta +\alpha \mathbf{F}^{-1}\nabla_{\theta}J(\theta)
$$

为了使约束能够被强制满足，取：

$$
\alpha = \sqrt{ \frac{2\varepsilon}{\nabla_{\theta }J(\theta)^{T}\mathbf{F}\nabla_{\theta}J(\theta)} }
$$

这个梯度格式被称为自然梯度。

在实践中，自然梯度方法比常规的梯度方法提供了一个条件好更多的梯度方向，并且是学习率的调整更为方便。

![alt text](<Source/Pasted image 20250613195511.png>)

更多详细内容见参考笔记