# Exploration I


[slide](https://rail.eecs.berkeley.edu/deeprlcourse/deeprlcourse/static/slides/lec-13.pdf)

参考：[ CS285 深度强化学习 (11): Exploration (1) - 知乎](https://zhuanlan.zhihu.com/p/28071376935)


对于 Montezuma's Revenge ，假定得到钥匙或打开门都能带来一定的奖励，但是被骷髅杀掉并不会带来任何损失，那么 RL 算法可能会重复一个行为：拿到钥匙，然后被骷髅杀掉。那么在这种循环结构中，我们的收益是稳定且正的，那么算法就没有利用去追求更复杂的“开门奖励”。

这与一个称为“毛”的游戏类似，我们并没有被告知游戏规则，但违反游戏规则时就会出局，我们只能通过不断试错（tria and error）来猜测这些规则，尽管有些规则没有意义。这些困难总结如下：

- How extended the task is. 有些高奖励的目标，需要一连串的动作才能实现。比如说在 Montezuma's Revenge 中你要先走过几个房间 → 跳过陷阱 → 拿钥匙 → 再走到另一扇门 → 用钥匙开门。整个过程很长，奖励只在最后才出现，而 Agent 很难靠随机行为完成整个过程， 
- How little you know about the rules. Agent 不知道哪些动作组合是有意义的，哪些是完全没用的。

Exploration problem 与一种情境相关：你有暂时延迟的奖励，但任务的结构并没有告诉你在未来获得更大的奖励需要做些什么事情。

Exploration 问题可以分为两种定义方式：

- How can an agent discover high-reward strategies that require a temporally extended sequence of complex behaviors that, individually, are not rewarding? 这是一个稀疏奖励问题，一些高奖励的目标（如开门）需要一长串动作才能实现，但是这一长串动作可能不包含奖励
- How can an agent decide whether to attempt new behaviors (to discover ones with higher reward) or continue to do the best thing it knows so far? Agent 已经知道了一些能够稳定奖励的策略（拿到钥匙被骷髅人杀死然后再拿到钥匙），但此时还存在“没有尝试过的动作”，其中可能有更高的奖励。

这两种说法本质上都是**探索-利用困境 (exploration-exploitation trade-off)** ，也就是如何在有限经验下，平衡“利用已知最优解”与“尝试新可能性”。

Exploration 在理论上和实践上都非常困难，因此希望得到一个最优的探索策略。

首先，将问题从在理论上可处理到理论上不可处理之间分成四个部分：

- **multi-armed bandits**: 相当于单步的 stateless MDP  
- **contextual bandits**: 相当于单步 MDP  
- **small, finite MDPs**: 例如 tractable planning, model-based RL  
- **large, infinite MDPs, continuous space**: 例如 deep RL

bandit 是 RL 中的一个简单模型，当我们拉动 bandit 时，他可能会返回一些奖励，因此这更像是一个奖励分布，multi-armed bandits 指有多台 bandit ，代表着不同的奖励分布。因此即不知道每个杠杆的奖励，也不知道每个拉杆的奖励分布。

对于每个 bandit ，假定：

$$
r(a_{i}) \sim p_{\theta_{i}}(r_{i})
$$

$\theta_{i}$ 是概率分布的参数，可以先假定其满足先验分布 $p(\theta)$ ，考虑 $\theta_{i}$ 是固定的，所以这是一个  Partially Observable Markov Decision Process 。求解这个过程得到了最优的 exploration strategy。这是一个理想的情况，如果用其他简单的 strategies ，我们希望在一定误差范围内（大 O 形式）来衡量其与最优策略的误差，定义：

$$
\mathrm{Reg}(T) = TE[r(a^{*})] - \sum_{t=1}^{T} r(a_{t})
$$

这个式子称为 regret ，前者是最优动作的期望，后者是实际获得的期望，我们希望 regret 越小越好。

## Three Classes of Exploration Methods 

存在很对相对简单的策略减小 regret ，这些策略在理论上能够获得在大 O 表示法意义下的最优 regret ，这些算法在最优性上仅受限于于一个常数因子，但是实际性能也许会有些许不同。这些策略启发了复杂 MDP 中探索策略 。

### Optimistic exploration 

 对于每一个动作 $a$ 记录其平均回报 $\hat{\mu}_{a}$ 。在 optimistic estimate 中，我们选取：

$$
a = \arg \max \hat{\mu}_{a} + C \sigma_{a}
$$

其中 $\sigma$ 表示标准差，也就是说，我们倾向于选择两种动作：

- 高平均回报
- 高偏差（意味着可能会产生更好的动作）

这个思想基于一个乐观想法：我们还未接触的动作可能产生不错的结果。intuition 就是：我们要尝试每一个 arm 直到我们能够确信它不好。一个例子（UCB）是：

$$
a = \arg \max \hat{\mu}_{a} +\sqrt{ \frac{2\ln T}{N(a)} }
$$

这个算法的 regret 是 $O(\log T)$ ，实际上 Optimistic estimate 是深度学习中面对不确定性的常用手段
### Probability matching/posterior sampling 

Optimistic exploration 是一个无模型方法，在实际中存在些许偏差，一种更接近 POMDP 的方式是维护一个 belief state $\hat{p}(\theta_{1},\dots,\theta_{n})$

我们从 $\hat{p}(\theta_{1},\dots,\theta_{n})$ 中抽取一个模型 $\theta_{1},\dots,\theta_{n}$ ，假定这个抽取出来的模型是正确的，然后采取最优动作，从结果上看，你要么抽取到了正确的模型，要么抽取到了其反例，那么根据结果重新更新模型。

这种方法称为 posterior sampling 或者 Thompson sampling 。这个方法从理论上很难分析，但是在实践中运行效果不错。

### Information gain 

这个方法基于贝叶斯实验设计。我们期望决定一些潜在变量 $z$ ，但是没有办法直接观测，因此定义 $z$ 的熵 $\mathcal{H}(\hat{p}(z))$ ，定义在给定 $y$ 后 $z$ 的熵为 $\mathcal{H}(\hat{p}(z)|y)$ 。熵表示一种不确定性，熵越低，我们对 $z$ 的了解程度越高。

定义信息增益

$$
IG(z,y|a) = \mathbb{E}_{y}[\mathcal{H} (\hat{p}(z))-\mathcal{H}(\hat{p}(z)|y)|a]
$$

表示给定动作 $a$ 后我们对 $z$ 的了解程度

一个例子是假定观测变量 $y = r_{a}$ ，估计量 $z = \theta_{a}$ ，其中 $\theta_{a}$ 是 action $a$ 的 reward 参数。我们定义两个量：$g(a) =IG(\theta_{a},r_{a}|a)$ 用于表征信息量，$\Delta(a) = \mathbb{E}[r(a^{*})-r(a)]$ 表示动作的次优性。直觉上来说，我们期望采取信息量大的动作，但不期望采取次优性高的动作。

我们的决策为：

$$
\arg \min_{a} \frac{\Delta(a)^{2}}{g(a)}
$$


## Exploration in Deep RL 

![alt text](<Source/Pasted image 20250902154420.png>)

回顾 UCB ：

$$
a = \arg \max_{a} \hat{\mu}_{a} + \sqrt{ \frac{{2 \ln T}}{N(a)} }
$$

最后影响称为探索奖励（exploration bonus），现在将 optimistic exploration 扩展到更为复杂的 MDP 过程中，我们通过状态动作对的访问次数 $N(s,a)$ 或状态的访问次数来添加探索奖励。

定义一个新的奖励函数：

$$
r^{+}(s,a) = r(s,a) + \mathcal{B}(N(s)) 
$$

$\mathcal{B}(N(s))$ 表示一个随着 $N(s)$ 增加而减小的奖励。用如上增强的奖励函数来替代原本无模型算法中的奖励函数。这个方法的优势在于可以简单添加到任意 RL 算法中，但缺点就是需要调整奖励加成的权重。

但是这里有一个记数上的问题：在小型离散的 MDP 中，计数的概念是有意义的，但在复杂的 MDP 中未必适用。一般来说，如果存在大量不同的变化因素，就会得到组合数量众多的状态，也就意味着再次访问完全相同状态的概率变得很低，在连续空间中这个情况更加严重。

于是这就需要利用状态之间的相似性，我们可以选择拟合一个密度模型 $p_{\theta}(\mathbf{s})$ 或者 $p_{\theta}(\mathbf{s},\mathbf{a})$
对于如果一个新的状态 $\mathbf{s}$ 与之间见到的状态相似，那么 $p_{\theta}(\mathbf{s})$ 的值会相对比较高。我们可以将密度模型视作某种形式上的计数。

对于一个比较小的 MDP ，可以列出如下表达式：

$$
P(\mathbf{s}) = \frac{N(\mathbf{s})}{n} \quad P'(\mathbf{s})= \frac{N(\mathbf{s})+1}{n+1}
$$

我们期望 $p_{\theta}(\mathbf{s})$ 和 $p_{\theta'}(\mathbf{s})$ 也能遵循上面两个等式：

$$
p_{\theta}(\mathbf{s}_{i}) = \frac{\hat{N}(\mathbf{s}_{i})}{\hat{n}} \quad p_{\theta'}(\mathbf{s}_{i}) = \frac{\hat{N}(\mathbf{s}_{i})+1}{\hat{n}+1}
$$

可以求解出：

$$
\hat{N}(\mathbf{s}_{i}) = \hat{n} p (\mathbf{s}_{i}) \quad \hat{n} = \frac{1-p_{\theta'}(\mathbf{s}_{i})}{p_{\theta'}(\mathbf{s}_{i})-p_{\theta}(\mathbf{s}_{i})}p_{\theta}(\mathbf{s}_{i})
$$

整个过程为：

!!! note "Exploring with pseudo-counts"

    1. 利用当前见过的所有 states $\mathcal{D}$ 拟合一个 density model $p_{\theta}(\mathbf{s})$,  
    2. 走一步 i观测到 $\mathbf{s}_{i}$  
    3. 用 $\mathcal{D}\cup \mathbf{s}_{i}$ 拟合一个新的 density model $p_{\theta'}(\mathbf{s})$
    4. 使用 $p_{\theta}(\mathbf{s})$ 与 $p_{\theta'}(\mathbf{s})$ 来更新 $\hat{N}(\mathbf{s})$
    5. 设置 $r^{+}(\mathbf{s},\mathbf{a}) = r(\mathbf{s},\mathbf{a})+\mathcal{B}(\hat{N}(\mathbf{s}))$


奖励函数的选择：

- UCB $\mathcal{B}(N(\mathbf{s})) = \sqrt{ \frac{2 \ln T}{N(\mathbf{s})} }$
- MBIE-EB $\mathcal{B}(N(\mathbf{s})) = \sqrt{ \frac{1}{N(\mathbf{s})} }$
- BEB $\mathcal{B}(N(\mathbf{s})) = \frac{1}{N(\mathbf{s})}$

密度模型的选择：我们只需要一个能输出密度的模型，并不需要能够从中采样或生成数据（甚至不需要归一化处理）

## More Novelty-Seeking Exploration 

### Counting with hashes 
我们依旧使用计数这个思想，但是我们利用某种编码器 $\phi(s)$ 将状态 $\mathbf{s}$ 压缩到一个 $k$ 字节的编码，如果状态的数量大于 $2^{k}$ ，那么我们就会得到一个有损的哈希映射，我们期望不同的状态得到不同的哈希值，而非常相似的状态映射到相同的哈希值。

### Implicit density modeling with exemplar models
既然我们并不要求密度模型 $p_{\theta}(\mathbf{s})$ 能够生成样本数据，那么就可以训练一个分类器，使其能够将新状态和原有的旧状态进行比较，直觉上来说，一个状态越“新奇”，分类器就越容易将其与历史数据区分开来。

对于每一个观测到的状态 $\mathbf{s}$ ，我们训练一个分类器区分其与所有旧的状态 $\mathcal{D}$ ，我们的正样本为 $\left\{ \mathbf{s} \right\}$  ，负样本为 $\mathcal{D}$ 利用分类器误差来获得密度：

$$
p_{\theta}(\mathbf{s}) = \frac{1 - D_{\mathbf{s}}(\mathbf{s})}{D_{\mathbf{s}}(\mathbf{s})}
$$

$D_{\mathbf{s}}(\mathbf{s})$ 是分类器判断状态为正样本的概率，这是一个有点奇怪的事情，因为我们正在做的事情是判断一个新状态是否等于正样本（本身）， $D_{\mathbf{s}}(\mathbf{s})$ 应当输出 $1$ 。但在计数这个过程中，$\mathcal{D}$ 实际上包含了一些状态 $\mathbf{s}$ ，这会使得输出的结果并不为 $1$ 

但是在现实中，每一个状态都是唯一的，因此为了防止分类器过拟合，我们需要添加正则化项，同时为了减少分类器的开销，我们采用训练一个 amortized model ，在每个时间步，我们将当前状态输入到这个网络中，它会输出一个分数来评估其新奇性。我们不断地用新观测到的状态来更新这个网络的参数，使其能够泛化到未见过的状态，同时避免过拟合。

### Heuristic estimation of counts via errors 
我们甚至可以不要求密度函数产生良好密度，而是告诉我们一个状态是否是新奇的。

假定我们有一个目标函数 $f^{*}(\mathbf{s},\mathbf{a})$ ，同时给定 $\mathcal{D}= \left\{ (\mathbf{s}_{i},\mathbf{a}_{i}) \right\}$ 来训练一个预测 $\hat{f}_{\theta}(\mathbf{s},\mathbf{a})$ ，利用 $\mathcal{E} = \lVert \hat{f}_{\theta}(\mathbf{s},\mathbf{a}) - f^{*}(\mathbf{s},\mathbf{a}) \rVert$ 作为误差，如果误差很大，则说明这个状态可能是新奇的。

对于目标函数，有两种选择：

1. 预测下一个状态 $f^{*}(\mathbf{s},\mathbf{a}) = \mathbf{s}'$ 。如果我们训练出来的模型对下一个状态的预测表现很差，那么这个状态就是新奇的，这个想法与信息增益有关
2. 更简单的选择是 $f^{*}(\mathbf{s},\mathbf{a}) = f_{\phi}(\mathbf{s},\mathbf{a})$ ，$\phi$ 是一个随机参数向量，也就是说我们训练 $\hat{f}_{\theta}$ 预测一个预先定义好且随机生成的特征

### Posterior Sampling in Deep RL 

对于 MDP，我们将 $\hat{p}$ 视作是 Q 函数的近似，也就是用一组模型来近似一个 Q 函数的后验分布 $p(Q)$ 。也就是说，我们考虑以下过程：

1. 从 $p(Q)$ 中采样的一个 Q 函数 $Q$
2. 依据 ${Q}$ 做一个 episode 的 action
3. 更新 $p(Q)$ 

这样做的好处在于 Q 学习是一个离线策略，我们并不关系收集数据的 $Q$ 函数是什么。那么问题就变成了如何在这些函数上表示我们的分布 $p(Q)$。

利用 bootstrap 的思想：

1. 给定数据集 $\mathcal{D}$ ，重新采样 $N$ 次得到数据集 $\mathcal{D}_{1},\dots,\mathcal{D}_{N}$
2. 在 $D_{i}$ 上拟合模型 $f_{\theta_{i}}$ 
3. 为了从  $p(\theta)$ 中采样，我们选择采样一个索引 $i$ ，然后利用模型 $f_{\theta_{i}}$

为了减少开销，一个方式是运用共享网络，但是运用多头策略。

- 在 $\epsilon$ 贪婪算法中，我们在一个 episode 中并不会坚持固定的某种行为，而是会来回震荡，这可能会导致我们走到一个有价值的未知
- 使用随机 $Q$ 函数进行探索时，我们可以在整个 episode 中坚持一个一致的策略。

但这这个方法的表现并不如计数方法，需要比较大的 bonuses 才能表现良好，但是它的好处是不需要对原始的奖励函数做改变。


## Information Gain in Deep RL

考虑选择什么作为信息增益的指标：

- 奖励 $r(\mathbf{s},\mathbf{a})$ ，这个指标并不是有效当奖励是稀疏的
- 状态密度 $p(\mathbf{s})$ ，这个指标有点奇怪，但是从某种意义上来说这可以表示状态的新奇程度，不过又会回到计数方法
- 状态 $p(\mathbf{s}'|\mathbf{s},\mathbf{a})$ ，这是一个很好的选择，但是本身依旧是启发式的。

但是，对于复杂的问题，直接精确使用信息增益是一个比较困难的事情，因此需要采用一些近似方法。

近似信息增益的好处是是我们有数学上更强的保证. 但是其缺点是 models 通常会更加复杂, 通常更难得到有效使用。

### predicition gain 

一个简单方法是采用 prediction gain ，即：

$$
\log p_{\theta'}(\mathbf{s})-\log p_{\theta}(\mathbf{s})
$$

这个方法的想法是如果 $\log p_{\theta'}(\mathbf{s})-\log p_{\theta}(\mathbf{s})$ 很大的话，也就意味着原先在 $\mathbf{s}$ 附近的不确定性很大，而此时我们降低了对 $\mathbf{s}$ 的不确定性。

### varational inference 
实际上，信息增益也可以写作 $D_{KL}(p(z|y)\Vert p(z))$ ，也就是说 $y$ 中包含的信息越多，那么 $p(z|y)$ 与 $p(z)$ 的 KL 散度越大。我们学习一个参数为 $\theta$ 的转移模型 $p_{\theta}(\mathbf{s}_{t+1}|\mathbf{s}_{t},\mathbf{a}_{t})$ ，并且考虑对参数 $z = \theta$ 的信息增益，我们的观测是 $y = (\mathbf{s}_{t},\mathbf{a}_{t},\mathbf{s}_{t+1})$ 于是我们想要最大化的就是 

$$
D_{KL}(p(\theta|h,\mathbf{s}_{t},\mathbf{a}_{t},\mathbf{s}_{t+1}))\Vert p(\theta|h)
$$

如果一个转移模型导致我们对模型参数的 belief 发生显著变化，那么这个模型就具有很高的信息量。

但是直接计算上面两个后验分布很困难，因此使用一个变分推断来近似这些分布：我们学习一个变分分布 $q(\theta|\phi)$ 并用其来近似真正的后验分布 $p(\theta|h)$ 。假设变分分布是多个独立同分布的高斯分布的乘积：

$$
q(\theta|\phi) = \prod_{i}\mathcal{N} (\theta_{i}|\mu_{i}.\sigma_{i}^{2})
$$

其中 $\phi = (\mu_{1},\sigma_{1},\dots \mu_{n},\sigma_{n})$ 是需要学习的变分参数，我们采用一个贝叶斯神经网络来学习。 

在训练过程中，VIME 的目标是完成两件事：

- 学习环境模型：让模型 $f(\mathbf{s},\mathbf{a})$ 准确预测下一个状态 $\mathbf{s}'$
- 学习不确定性：更新变分参数 $\phi$ ，使得变分分布 $q(\theta|\phi)$ 更好地近似真实的后验分布 $p(\theta|D)$

最小化如下 KL 散度以保证我们近似有效性：

$$
	D_{KL}(q_{\phi}(\theta)||p(\theta)p(h|\theta))
$$

计算如下更新前后的变分分布之间的 KL 散度：

$$
D_{KL}(q(\theta|\phi_{\mathrm{new}})\Vert q(\theta|\phi_{\mathrm{old}}))
$$

这个散度值衡量我们的 belief 在观察到新的数据后改变了多少，也被用作后面的奖励。
