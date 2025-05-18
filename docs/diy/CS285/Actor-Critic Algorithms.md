
[slides](http://rail.eecs.berkeley.edu/deeprlcourse/deeprlcourse/static/slides/lec-6.pdf)

部分笔记参考：[强化学习CS285笔记【四】Actor-Critic 算法 - 知乎](https://zhuanlan.zhihu.com/p/678372136)

## Policy Gradient 

在 [Policy Gradients](Policy%20Gradients.md) 中引入了一个 $\hat{Q}$ 来描述在给定状态 $s_{t}$ 并且选择动作 $a_{t}$ 下未来情况的总和，也称为 "reward to go" ：

$$
\hat{Q}_{i,t} = \left( \sum_{t'=1}^{T}r(s_{i,t'},a_{i,t'})  \right) 
$$

但是实际上，由于我们的系统和策略存在一定的随机性，我们其实只是在众多可能性中选择了一条可能性，也就是一条轨迹上的一个样本，那么我们自然而然地会希望有一种能够估计所有可能发生未来的表现的方法，换句话说，我们希望得到的是如下的期望估计结果：

$$
{Q}(\mathbf{s}_{t},\mathbf{a}_{t})= \sum_{t=t'}^{T}  \mathbb{E}_{\pi_{\theta}}[r(\mathbf{s}_{t'},\mathbf{a}_{t'})|\mathbf{s}_{t},\mathbf{a}_{t}]
$$

实际上，Policy Gradient 对于 reward to go 的计算是对于一个非常复杂的期望进行一个单样本估计，因此你用于估计期望的样本越少，估计量的方差就会越大。如果我们由了上述的 $Q$ 函数，我们可以简单地将其替换掉 $\hat{Q}$ ，从而得到得到一个方差更小的策略梯度。

我们同样可以引入 baseline ，我们选择平均我们的 $Q$ 值，也就是：

$$
b = \frac{1}{N}\sum_{}^{} Q(\mathbf{s}_{i,t},\mathbf{a}_{i,t}) 
$$

我们将有一个特性：策略梯度将增加期望优于平均水平的动作的概率，并减少那些低于平均水平的概率。Baseline 可以进一步的降低反差，这是因为他并不依赖于会引起偏差的动作，而是基于我们选择的状态。我们定义 Value function 来描述 $b$ 的行为

$$
V^{}(\mathbf{s}_{t}) \approx \mathbb{E}_{\mathbf{a}_{t} \sim \pi}[Q(\mathbf{s}_{t},\mathbf{a}_{t})]
$$

我们的梯度就变为：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N}\Bigg[ \Big( \sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\Big) \Big( \underbrace{Q^{\pi  }(\mathbf{s}_{t},\mathbf{a}_{t})-V^{\pi}(\mathbf{s}_{t})}_{A^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t})}\Big)   \Bigg]  
$$

我们在 $Q$ 函数的上方添加了一个 $\pi$ 表示 $Q$ 函数的值依赖于策略，称 $Q^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t})-V^{\pi}(\mathbf{s}_{t})$ 为优势函数(Advantage Function) ，它的含义是，采取当前动作比平均水平“好”多少。这是一个更低方差的估计。

现在我们有三个函数，我们应该拟合哪个函数呢？

考虑将 $Q^{\pi}$ 展开称如下形式

$$
Q^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t} ) \approx r(\mathbf{s}_{t},\mathbf{a}_{t}) + \underbrace{ \sum_{t' = t+1}^{T} \mathbb{E}_{\pi_{\theta}}[r(\mathbf{s}_{t'},\mathbf{a}_{t'})|\mathbf{s}_{t},\mathbf{a}_{t}] }_{V^{\pi}(\mathbf{s}_{t+1})}
$$

也就是说我们将 $Q$ 函数拆分称当前时刻的奖励和按照策略 $\pi$  继续行动的期望总奖励，但是由于随机性，我们无法确定下一个状态 $s_{t+1}$ 是什么，因此我们采用的实际上 $\mathbb{E}_{\mathbf{s}_{t+1}\sim p(\mathbf{s}_{t+1}|\mathbf{s}_{t},\mathbf{a}_{t})}[V^{\pi}(\mathbf{s}_{t+1})]$ 来近似第二项，然后我们强行去掉期望，本质上还是用单样本来对原来的期望做一个估计（无偏但是高方差）。

实际上，上面那条公式更常写成 ：

$$
Q^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t} ) \approx r(\mathbf{s}_{t},\mathbf{a}_{t}) +\gamma \cdot{ \sum_{t' = t+1}^{T} \mathbb{E}_{\pi_{\theta}}[r(\mathbf{s}_{t'},\mathbf{a}_{t'})|\mathbf{s}_{t},\mathbf{a}_{t}] }
$$

$\gamma$ 称为 discount factor ，这个是强化学习中的 Bellman 方程，会在下面说明

将展开后的 $Q$ 函数带回到优势函数可得：

$$
A^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t}) \approx r(\mathbf{s}_{t},\mathbf{a}_{t})+V^{\pi }(\mathbf{s}_{t+1})-V^{\pi}(\mathbf{s}_{t})
$$

于是我们可以通过拟合 $V$ 来拟合剩下两个函数，这是因为 $A$ 和 $Q$ 依赖于两个变量，而 $V$ 只依赖于一个变量，拟合起来更为简便，我们可以使用一个神经网络来拟合。

## Policy evaluation 
通过 Policy Gradient 后，我们该如何评估我们得到的 policy 的好坏呢？

强化学习的目标本身可以表示为初始状态上价值函数的期望值，因此如果计算了价值函数，我们很容易在初始状态下给出对应的目标函数：

$$
J(\theta) = \mathbb{E}_{s_{1}\sim p(s_{1})}\left[ \mathbb{E}_{\pi}\left[ \sum_{t=1}^{T} r(\mathbf{s}_{t},\mathbf{a}_{t}) |\mathbf{s}_{1} \right]  \right]
$$

也就是

$$
J(\theta) = \mathbb{E}_{s_{1} \sim p(\mathbf{s}_{1})}[V^{\pi}(\mathbf{s}_{1})]
$$

因此，我们通过评估 value function 来评价一个 policy 的好坏。问题就转换成了如何估计 $V^{\pi}(s)$ ，一种方法就是使用 Monte Carlo 模拟，正如我们在 Policy Gradient 中做的事情。我们从 $\mathbf{s}_{t}$ 开始运行 N 次我们的策略，每次都走一条不同的轨迹，累加得到的所有rewards，最后取平均，也就是：

$$
V^{\pi}(\mathbf{s}_{t} )\approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t'=t}^{T} r(\mathbf{s}_{t},\mathbf{a}_{t})
$$

但是，这并不是一个很好的方法。这需要我们从相同状态重置模拟器（reset the simulator）。

[笔记](https://zhuanlan.zhihu.com/p/678372136) 中有一个对 reset the simulator 形象的描述：

> reset the simulator 简单来理解就是 你玩游戏的过程中可以在任意状态及你想存档，并且可以从这个存档重新开始。那么借助这个直观感受你就可以知道 有的系统是允许 reset the simulator 有的是不允许的，显然不允许的情况会更加困难。

在现实世界中，我们很难实现从相同状态重置模拟器，物理系统很难精准地复现同一个状态。

另一种方法是采用神经网络来近似学习一个 value function 。我们引入如下的神经网络函数逼近器：

$$
	\hat{V}_{\phi}^{\pi}(s)
$$

其中 $\phi$ 表示参数。我们训练一个出上面的神经网络，实际上，对 value function 的近似是一个监督学习的问题，我们可以采用用 Monte Carlo 采样获得的 True reward 来作为我们的标签，构建如下监督学习的样本对：

$$
\left( s_{i,t},\sum_{t'=t}^{T}r(s_{i,t'},a_{i,t'})  \right) 
$$

我们采用 MSE 损失来最小化预测值和真实 return 的差距。

在训练过程中，神经网络会逐渐“意识到”不同轨迹中存在相似的状态。也就是说，如果两条轨迹在某个时刻彼此靠得很近，即使它们之后分道扬镳，网络在估计这两个状态的价值时，一个状态的价值信息也会影响到另一个状态的估计。这种“信息的传递”正是泛化的体现：相近的状态会被赋予相似的值，从而使模型能够对未见过的状态做出合理的预测。举一个例子，对于没有见过的状态 $s'$ ，如果它和 $s$ 相似（（比如都是接近终点、都在危险区），那么训练出来的值函数应该差不多，网络并不会记住这个 $s'$ 的 return 是 3 （和 $s$ 的return 相同），而是学会“在这张情况下，return 一般是 3 ”

我们希望得到的理想标签是：

$$
y_{i,t} = \sum_{t=t'}^{T}\mathbb{E}_{\pi_{\theta}} [r(\mathbf{s}_{t'},\mathbf{a}_{t'})|\mathbf{s}_{i,t}] 
$$

但这并不好直接计算，所以我们取如下近似展开：

$$
\approx r(\mathbf{s}_{i,t},\mathbf{a}_{i,t})+V^{\pi}(\mathbf{s}_{i,t+1})
$$

这时一个无偏估计，但是真实的 $V^{\pi}(\mathbf{s}_{i,t+1})$ 我们并不知道，所以采用之前训练出来的神经网络来拟合：

$$
y_{i,t} \approx r(\mathbf{s}_{i,t},\mathbf{a}_{i,t})+\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i,t+1})
$$

这就叫做 bootstrapped estimate 因为它“引导”自己从当前的估计中前进一小步，而不是等全部轨迹走完。但这是一个有偏估计，这是因为我们训练出来的 $\hat{V}_{\phi}$ 与真实的 value function 存在偏差。

## Actor-critic 
!!! note "batch actor-critic algorithm"

    1. 从 $\pi_{\theta}(\mathbf{a}|\mathbf{s})$ 中采集样本 $\left\{ \mathbf{s}_{i},\mathbf{a}_{i} \right\}$
    2. 拟合 $\hat{V}_{\phi}^{\pi}(s)$  得到奖励和
    3. 评估近似优势函数 $\hat{A}_{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})=r(\mathbf{s}_{i},\mathbf{a}_{i})+\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i}')-\hat{V}_{\phi}^{\pi}(\mathbf{s}_{i})$ 
    4. 构造梯度 $\nabla_{\theta}J(\theta) \approx \sum_{i}^{}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i}|\mathbf{s}_{i})\hat{A}^{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})$
    5. 梯度上升：$\theta \approx \theta+\alpha \nabla_{\theta}J(\theta)$


考虑一个问题，我们之前所讨论的都是基于时间有限的情况下，那么对一个无限时域的问题，比如让一个机器人长时间的行走，这时候会面临一个问题：价值函数会无限增加至正无穷大使得整个算法不收敛，这并不是我们想要的。为了解决这个问题，一个简单的方法就是引入 discount 系数，也就是：

$$
y_{i,t} \approx r(\mathbf{s}_{i,t},\mathbf{a}_{i,t})+\gamma \cdot\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i,t+1})
$$

其中 $\gamma\in[0,1]$ 称为 discount 系数，（常取 $0.9\sim0.999$）。

理解这个方法的想法是：我们希望更早地获得奖励。你对于马上获得100美金和十年后获得100美金这两件事的看法是不一样的，或者从生物学上的角度来说，你更希望在生前获得奖励而非死后获得奖励。

折扣系数在MDP体现在：每个状态都会额外有一个转移概率连接到一个死亡状态，转移到死亡状态的概率为 $1-\gamma$ ，对应的 reward 就是 $0$ ，剩下的概率 $\gamma$ 就表示到其他非死亡状态的概率，那么自然非死亡状态的 reward 就需要乘以 $\gamma$ 。

将折扣系数引入蒙特卡洛模拟，可以得到 Actor-Critic 方法中的策略梯度估计：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\Big( \underbrace{r(\mathbf{s}_{i,t},\mathbf{a}_{t})+\gamma \hat{V}_{\phi}^{\pi}(\mathbf{s}_{i,t+1})-\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i,t})}_{\hat{A}^{\pi}(\mathbf{s}_{i,t},\mathbf{a}_{i,t})}\Big)   
$$

现在要思考的是在纯粹的 policy gradient 中，如何将折扣系数引入蒙特卡洛模拟。有如下两种选择：

- 选择 1：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \sum_{t'=t}^{T}\gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'})  \right)  
$$

- 选择 2：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \Bigg(\sum_{t=1}^{T}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\Bigg)\left( \sum_{t=1}^{T}\gamma^{t-1}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'})  \right) 
$$

这两个选择会导致一样的结果嘛？实际上，如果我们考虑因果效应，上面的式子最终可以展开成：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T}\gamma^{t-1}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \sum_{t'=t}^{T}\gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'})  \right)  
$$

显然，他们并不是相同的，对这个式子的理解就是：你不仅对未来更远的奖励的关注减少，对未来更远决策关注也减少了。这意味着在第一个时间步做出正确的决策比在第二个时间步做出的正确决策更为重要，因为第二个时间步的决策并不会影响你在第一个时间步上获得的奖励。这是你在真正的折扣环境中应当考虑的事情。但实际上，这通常不是我们真正想要的，我们通常用的是选项一。因为我们处理的并不是一个真正的折扣问题，我们只是想通过折扣问题来获得一个有限的平均奖励，我们真的需要一个在每个时间步都做正确事情的决策，而并不只是在早期时间步上。

折扣因子有助于减少梯度的方差。

!!! note  "batch actor-critic algorithm (discount)"
    1. 从 $\pi_{\theta}(\mathbf{a}|\mathbf{s})$ 中采集样本 $\left\{ \mathbf{s}_{i},\mathbf{a}_{i} \right\}$
    2. 拟合 $\hat{V}_{\phi}^{\pi}(s)$  得到奖励和
    3. 评估近似优势函数 $\hat{A}_{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})=r(\mathbf{s}_{i},\mathbf{a}_{i})+\gamma\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i}')-\hat{V}_{\phi}^{\pi}(\mathbf{s}_{i})$ 
    4. 构造梯度 $\nabla_{\theta}J(\theta) \approx \sum_{i}^{}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i}|\mathbf{s}_{i})\hat{A}^{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})$
    5. 梯度上升：$\theta \approx \theta+\alpha \nabla_{\theta}J(\theta)$


!!! note "online actor-critic algorithm"
    1. 实施动作 $\mathbf{a}\sim \pi_{\theta}(\mathbf{a}|\mathbf{s})$ ，得到 $(\mathbf{s},\mathbf{a},\mathbf{s}',r)$
    2. 用 $r+\gamma \hat{V}_{\phi}^{\pi}(\mathbf{s}')$ 更新 $\hat{V}_{\phi}^{\pi}$
    3. 估计 $\hat{A}_{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})=r(\mathbf{s}_{i},\mathbf{a}_{i})+\gamma\hat{V}^{\pi}_{\phi}(\mathbf{s}_{i}')-\hat{V}_{\phi}^{\pi}(\mathbf{s}_{i})$ 
    4. 构造梯度 $\nabla_{\theta}J(\theta) \approx \sum_{i}^{}\nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i}|\mathbf{s}_{i})\hat{A}^{\pi}(\mathbf{s}_{i},\mathbf{a}_{i})$
    5. 梯度上升：$\theta \approx \theta+\alpha \nabla_{\theta}J(\theta)$


Actor 是我们的策略函数，也就是决定在某个状态下选择什么动作的部分 ，而 Critic 的任务是评估 Actor 的表现如何，主要负责估计值函数，提供价值反馈信号引导 Actor 改进策略

实现 actor-critic 算法首先要建立其合适的神经网络，最简单的方式是建立两个神经网络，这两个神经网络的输入都是 $\mathbf{s}$ ，而输出分别是价值函数和决策的概率分布，这样做的好处是稳定和简单，但是坏处就是 actor 和 critic 之间没有共享的特征，这两个神经网络是相互独立的，于是就会想把这两个网络合并成一个同一个神经网络，让他们共享网络前几层的权值，但是这个过程会使训练过程变得更加复杂，更不稳定，调参过程也更为繁琐。

使用小批次样本更新样本数据的效果会比只使用一个样本的效果好，因而我们在算法中引用并行化。对于 synchronized parallel actor-critic ，假定我们有 32 个模拟器，他们使用不同的随机种子生成数据，然后将这些数据收集到一个批次中用来同步更新参数；对于 asynchronized parallel actor-critic 我们有不同的线程，他们以自己的速度运行，积攒一定数据后更新参数，比如说生成32个样本时更新数据， 汇集到批次的所有数据最好是只有轻微不同，这是因为对一个比较落后的线程生成的数据对更新几乎没有效果。异步算法的结果在数学上并不等同同步算法，但是其效率更快。

也可以采用 off-policy actor-cirtic 算法，其思想是假定我们有一个缓冲池来存储样本，缓冲池遵循先进先出原则，于是在训练过程中我们将生成的样本存入缓冲池，并从缓冲池提取样本进行更新。其和 asynchronized parallel actor-critic 算法不同在于每次用于更新的样本比较“旧”，而后者只是稍微“旧”。

对于off-policy算法，如果直接套用 online 算法，有一个问题是每次更新得到的结果并非是最新执行者采取动作的结果，而是其他执行者采取动作得到的结果。 由于 $Q$ 函数可以直接使用 buffer 中的样本，不存在错位问题， Actor 也可通过最大化 $Q^{\pi}$ 来优化原有的策略，所以采用 $Q$ 函数来更新我们的算法。 同时，在策略梯度的估计中，由于动作来自旧的策略，而非当前策略，所以得到的梯度并非是真正的策略梯度，因此我们对于每个 $\mathbf{s}_{i}$ 从当前策略重新采样 $\mathbf{a}_{i}^{\pi}$ ，然后估计梯度，同时我们采用 $Q$ 函数替代优势函数，尽管这个会提高原有的方差，但是节省了计算开销，所以是可接受的。最后，状态  $\mathbf{s}_{i}$ 也并非来自当前概率函数 $p_{\theta}(\mathbf{s})$ ，这个我们没有办法处理，只能接受。不过，我们最终得到的是一个比 $p(\theta)$ 更广一点的最优策略。

回顾Policy Gradient：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \left( \sum_{t'=t}^{T} \gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'}) \right) -b \right) 
$$

Actor-cirtic 算法能够明显降低方差，这是因为我们选用的函数逼近器 $\hat{V}_{\phi}^{\pi}(\mathbf{s}_{i,t+1})$ 和 critic 整合了所有可能发生的情况，而不是依赖于单一样本，但是这个算法并非是无偏算法，这是由于基于有限样本的训练的函数逼近器无法保证梯度会收敛到真实的策略梯度，换言之我们学习到的是一个近似值而非真实值。而原本的 Policy Gradient 算法尽管是高方差的，但是是一个无偏估计。

我们可以构建一个策略梯度估计器，它的方差略高于 Actor-critic 算法，但却是一个无偏估计。我们已经证明了对于policy gradient，减去任意常数 $b$ 仍是一个无偏估计，实际上这个可以扩展成：减去任意仅依赖于状态的任意函数，仍保持无偏估计。一个好的基线函数选择是价值函数，因为你期望的这个单样本估计器在期望中等于价值函数，同时方差也会偏小。

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \left( \sum_{t'=t}^{T} \gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'}) \right) -\hat{V}_{\phi}^{\pi}(\mathbf{s}_{i,t}) \right) 
$$

如果我们让基线依赖于状态和动作我们可以得到更低的方差，我们选择 $Q$ 函数作为基线：

$$
\nabla_{\theta}J(\theta) \approx \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \left( \sum_{t'=t}^{T} \gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'}) \right) -\hat{Q}_{\phi}^{\pi}(\mathbf{s}_{i,t}) \right) 
$$

但是选择 $Q$ 函数作为我们的基线时会带来一个问题，他并不会给出一个正确的梯度，因此需要补偿一个误差项 

$$
\begin{align}
\nabla_{\theta}J(\theta) \approx& \frac{1}{N}\sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta}\log \pi_{\theta}(\mathbf{a}_{i,t}|\mathbf{s}_{i,t})\left( \left( \sum_{t'=t}^{T} \gamma^{t'-t}r(\mathbf{s}_{i,t'},\mathbf{a}_{i,t'}) \right) -\hat{Q}_{\phi}^{\pi}(\mathbf{s}_{i,t}) \right)  \\
&+ \frac{1}{N} \sum_{i=1}^{N} \sum_{t=1}^{T} \nabla_{\theta}\mathbb{E}_{\mathbf{a}\sim \pi_{\theta}(\mathbf{a_{t}|\mathbf{s}_{i,t}})}[Q^{\pi}_{\phi}(\mathbf{s}_{i,t},\mathbf{a}_{t})]
\end{align}
$$

在很多情况下，第二项可以被很精确的估计，使其方差比较低，而第一项本身就是一个方差比较低的估计，因此这也是一个方差比较小的无偏估计。

我们希望将两种方法做一个结合实现对于bias/variance trade off。当我们考虑价值函数时，由于折扣系数的存在，在未来能够得到的回报会越来越低，因此，如果我们在未来的时间点使用价值函数，由于奖励比较小，偏差问题也随之变小；同时，在单样本估计器中，方差也会随着时间逐渐增大。因此我们希望先用单样本估计器估计，然后在方差变大之前替换成价值函数，这是因为价值函数的方差会变低，尽管这会带来一点偏差。我们可以通过构建所谓的 $n$ 步回报估计器（n-step return）来实现这一点:

$$
\hat{A}^{\pi}_{n}(\mathbf{s}_{t},\mathbf{a}_{t}) = \sum_{t'=t}^{t+n} \gamma^{t'-t}r(\mathbf{s}_{t'},\mathbf{a}_{t'})-\hat{V}_{\phi}^{\pi}(\mathbf{s}_{t})+\gamma^{n}\hat{V}_{\phi}^{\pi}(\mathbf{s}_{t+n})
$$

第一项贡献了方差，最后一项贡献了偏差，一般而言 $n$ 越大，偏差越小，但是对应的方差也越大。这是一种硬切换，但是我们可以不用单一的 $n$ ，我们可以构建如下融合估计器，称为 GAE （Generalized Advantage Estimation  广义优势估计）

$$
\hat{A}^{\pi}_{\mathrm{GAE}}(\mathbf{s}_{t},\mathbf{a}_{t}) = \sum_{n=1}^{\infty}w_{n}\hat{A}_{n}^{\pi}(\mathbf{s}_{t},\mathbf{a}_{t}) 
$$

权重满足指数衰减 $w_{n} \propto \lambda^{n-1}$ 越小的权重会有更多的偏差，而越大权重则会带来更多的方差，我们更希望减小偏差，如果权重取为 $\lambda^{n-1}$ ，则 GAE 可以写成如下形式：

$$
\hat{A}^{\pi}_{\mathrm{GAE}}(\mathbf{s}_{t},\mathbf{a}_{t}) = \sum_{n=1}^{\infty}(\gamma\lambda)^{t'-t}\delta_{t'}  \quad \delta_{t'} = r(\mathbf{s}_{t'},\mathbf{a}_{t'}) +\gamma \hat{V}^{\pi}_{\phi}(\mathbf{s}_{t'+1})-\hat{V}_{\phi}^{\pi}(\mathbf{s}_{t'})
$$

当然我们惊奇发现 discout 系数 $\gamma$ 出现的位置和 $\lambda$ 是完全一样的。反推回去就表示 $\gamma$ 本质上也是在 bias 和 variance 来 trade off 的系数。这一点就回答了之前留下的关于 为什么 discount 可以降低 variance 的原因。