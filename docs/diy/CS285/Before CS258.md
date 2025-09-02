# Reinforce Learning
!!! tips
    这个内容是补充一些 CS285 中的基础知识，也就是强化学习的基础知识，内容源自《动手学强化学习这本书》（个人感觉一般，很基础但有代码，理论偏弱一点）

## 多臂老虎机
多臂老虎机问题是一种简化版本的强化学习问题。与强化学习不同的是，多臂老虎机不存在状态信息，只有动作和奖励，算是最简单的“和环境交互中的学习”的一种形式。

!!! note "多臂老虎机"
    有一个拥有 $K$ 根拉杆的老虎机，拉动一根拉杆都对应一个关于奖励的**概率分布** $\symcal{R}$ 。每次拉动一根拉杆就可以从拉杆对应的奖励概率分布中获得一个奖励。

    在各根拉杆的奖励概率分布位置的情况下开始尝试，目标是操作 $T$ 次拉杆后获得尽可能高的累计奖励。

    由于奖励的概率是未知的，因此需要在“探索拉杆的获奖概率”和“根据经验选择获奖最多的拉杆”中进行权衡


定义期望奖励为 $Q(a) = \mathbb{E}_{r \sim \mathcal{R}(\cdot|\mathbf{a})}[r]$。算法计算期望奖励估值有两种方法：

- 直接求和

$$
\hat{Q}_{k} = \frac{1}{k}\sum r_{i} 
$$

- 增量公式

$$
\hat{Q}_{k} = \hat{Q}_{k-1} + \frac{1}{k}[r_{k}-\hat{Q}_{k-1}]
$$

第一种方法的时间复杂度和空间复杂度均为 $\mathcal{O}(n)$ ，而第二种增量方法的时间复杂度和空间复杂度均为 $\mathcal{O}(1)$

在多臂老虎机问题中，一个经典的问题就是 exploration 与 exploitation 的平衡的问题：explore 是指尝试拉动更多可能的拉杆，这根拉杆不一定会获得最大的奖励，但是能摸清楚所有拉杆的获奖情况。exploitation 是指拉动**已知期望**将最大的拉杆。由于已知的信息仅仅来自有限次的交互观测，所以当前的拉杆不一定是最优的。

## 策略
### $\epsilon$-贪婪算法

完全贪婪算法在每一时刻采取期望奖励估值最大的动作，这是纯粹的利用而没有探索。$\epsilon$-贪婪算法在完全算法的基础上添加了噪声，每次以概率 $1-\epsilon$ 选择以往经验中期望奖励估值最大的拉杆，以概率 $\epsilon$ 随机选择一根拉杆。

$$
\mathbf{a}_{t} = \begin{cases}
 \arg \max_{\mathbf{a} \in \mathcal{A} } \hat{Q} (\mathbf{a})  & 1-\varepsilon \\
\mathrm{random\,sample}  & \varepsilon
\end{cases}
$$

随着探索次数的不断增加，对各个动作的奖励估计得越来越准，此时需要减少探索：令 $\varepsilon$ 随时间衰减，即探索的概率将会不断降低，但 $\varepsilon$ 不会在有限步数内衰减至 $0$ ，因为基于有限步数观测的灌完贪婪算法仍然是一个局部信息的贪婪算法。 

### 上置信界算法

一根拉杆的不确定性越大，就越具有 explore 的价值，因为在探索之后我们可能发现它的期望奖励很大。引入不确定性度量 $U(\mathbf{a})$ ，其随动作被尝试次数的增加而减小。我们可以使用一种基于不确定性的策略来总和考虑现有的期望奖励估值和不确定性，其核心问题是如何估计不确定性。

!!! note "Hoeffding's inequality"
    令 $X_{1},\dots,X_{n}$ 为 $n$ 个独立同分布的随机变量，取值范围为 $[0,1]$ ，其经验期望为 $\bar{x}_{n} = \frac{1}{n} \sum_{j=1}^{n}X_{j}$，则有：

    $$
    P(\mathbb{E}[X]\geq \bar{x}_t + u) \leq e^{-2nu^{2}}
    $$

基于 Hoeffding's inequality 提出 upper confidence bound(UCB) 算法。取 $\bar{x}_{t}$ 为平均期望 $\hat{Q}$ ，$u$ 为不确定性度量 $\hat{U}$  ，因此 $Q<\hat{Q}+\hat{U}$ 至少以概率 $1-p$ 成立，其中 $p = e^{-2NU^{2}}$
。当 $p$ 很小时，$Q<\hat{Q}+\hat{U}$ 就以很大概率成立，即期望奖励上界，此时上置信界算法便选取期望奖励上界最大的动作，即 $\mathbf{a}_{t} = \arg \max_{\mathbf{a}\in \mathcal{A}}[\hat{Q}(\mathbf{a})+\hat{U}(\mathbf{a})]$ ，对应不确定度 $\hat{U}(\mathbf{a})=\sqrt{ \frac{-\ln p}{2N(\mathbf{a}_{t})} }$

因此，设定一个概率 $P$ 后就可以计算相应的不确定性度量，换言之，算法在每次选择拉杆前，先估计拉动每根拉杆的期望奖励上界，使得拉动每根拉杆的期望奖励只有一个较小的概率 $p$ 超过上界，接着选出期望奖励上界最大的拉杆，从而选择最优可能获得最大期望奖励的拉杆。

### Thompson sampling 
Thompson sampling 使用采样的方式，即根据当前每个动作 $\mathbf{a}$ 的奖励概率分布进行一轮采样，得到一组各根拉杆的奖励样本，在选择样本奖励最大的动作。实际上 Thompson sampling 是一种计算所有拉杆的最高奖励概率的蒙特卡洛采样方法。

在实际情况中，我们通常用 Beta 分布对当前每个动作的奖励概率分布进行建模，也就是说，某拉杆被选择了 $k$ 次，其中 $m_{1}$ 次奖励为 $1$ ，$m_{2}$ 次奖励为 $0$ ，则该拉杆的奖励服从参数为 $(m_{1}+1,m_{2}+1)$ 的 Beta 分布。

## Bellman 方程

### Markov 奖励过程
一个马尔可夫奖励过程由 $\langle \mathcal{S},\mathcal{P},r,\gamma \rangle$ 构成，其中：

- $\mathcal{S}$ 是有限状态的集合
- $\symcal{P}$ 是状态转移矩阵
- $r$ 是奖励函数
- $\gamma$ 是折扣因子，取值为 $[0,1)$ 。

引入折扣因子是因为远期利益具有一定的不确定性，有时我们更希望能够获得一些奖励。接近 $1$ 的 $\gamma$ 更关注长期的累积奖励，更接近 $0$ 的 $\gamma$ 更考虑短期奖励。

在一个 Markov 奖励过程中，Return 定义为从 $t$ 时刻状态 $\mathbf{s}_{t}$ 开始，到终止状态时所有奖励的衰减之和：

$$
Q_{t} = \sum_{k=0}^{\infty}\gamma^{k}r_{t+k} 
$$

用正奖励鼓励进入某种状态，用负奖励阻止进入某种状态

状态的 Value 定义为一个 state 的期望回报，Value function 定义为所有 state 的价值。

$$
\begin{align}
V(\mathbf{s})& = \mathbb{E}[G_{t}|\mathbf{s}_{t}] \\
&=\mathbb{E}[r_{t}+\gamma r_{t+1}+\gamma^{2}r_{t+2}+\cdots|\mathbf{s}_{t}] \\
&=\mathbb{E}[r_{t}+\gamma(r_{t+1}+\gamma r_{t+2}+\cdots)|\mathbf{s}_{t}] \\
&= \mathbb{E}[r_{t}+\gamma G_{t+1}|\mathbf{s}_{t}] \\
&= \mathbb{E}[r_{t}|\mathbf{s}_{t}] + \gamma\mathbb{E}[G_{t+1}|\mathbf{s}_{t}] \\
\end{align}
$$

上式的最后一个等式中，第一项的即时奖励的期望实际上奖励函数的输出。

在计算第二项之前，先推导一个等式，利用全概率公式可得：

$$
\mathbb{E}[G_{t+1}|\mathbf{s}_{t}] = \mathbb{E}_{\mathbf{s}_{t+1}\sim p(\cdot|\mathbf{s}_{t},\pi)} [\mathbb{E}[G_{t+1}|\mathbf{s}_{t+1}]]= \mathbb{E}_{\mathbf{s}_{t+1} \sim p(\cdot|\mathbf{s}_{t}, \pi)} [V^{\pi}(\mathbf{s}_{t+1})]
$$

这是因为 $G_{t+1}$ 是一个随机变量，它取决于我们进入下一个状态 $\mathbf{s}_{t+1}$ 和之后所采取的策略。由于期望是可以互换的，所以我们可以用 $V^{\pi}$ 来替换内部的 $\mathbb{E}[G_{t+1}|\mathbf{s}_{t+1}]$

将期望展开可以得到：

$$
\mathbb{E}[V(\mathbf{s}_{t+1})|\mathbf{s}_{t}] = \sum_{\mathbf{s}_{t+1} \in \mathcal{S}} P(\mathbf{s}_{t+1}|\mathbf{s}_{t}) V(\mathbf{s}_{t+1})
$$

即得 Bellman 等式：

$$
V(\mathbf{s}) =r(\mathbf{s} ) +\gamma \sum_{\mathbf{s}'\in \mathcal{S} }P(\mathbf{s'}|\mathbf{s})V(\mathbf{s}') 
$$ 

!!! note
    这里还是写的有点不太清楚，可以看看赵老师的讲解。

### Markov 决策过程
如果引入策略，则 Markov 奖励过程变成了 Markov 决策过程。

在 Markov 决策过程中，有两类价值函数：

- 状态价值函数：

$$
V^{\pi}(\mathbf{s}) = \mathbb{E}_{\pi}[G_{t}|\mathbf{s}_{t}]
$$

- 动作价值函数：

$$
Q^{\pi} (\mathbf{s},\mathbf{a}) =\mathbb{E}_{\pi}[G_{t}|\mathbf{s}_{t},\mathbf{a}_{t}]
$$


状态价值函数和动作价值函数之间的关系为：在使用策略 $\pi$ 时，状态 $\mathbf{s}$ 的价值等于在该状态下策略 $\pi$ 采取所有动作的概率与相应的价值相乘再求和的结果：

$$
V^{\pi}(\mathbf{s}) = \sum_{\mathbf{a}\in \mathcal{A} }\pi(\mathbf{a}|\mathbf{s})Q^{\pi}(\mathbf{s,\mathbf{a}})
$$

使用策略 $\pi$ 时，在状态 $\mathbf{s}$ 下采取动作 $\mathbf{a}$ 的价值等于即时奖励加上经过衰减的所有可能的下一个状态的转移概率与响应的价值的乘积： 

$$
Q^{\pi}(\mathbf{s},\mathbf{a}) = r(\mathbf{s},\mathbf{a}) +\gamma \sum_{\mathbf{s}'\in \mathcal{S} }P(\mathbf{s}'|\mathbf{s},\mathbf{a})V^{\pi}(\mathbf{s}')
$$

Bellman 期望方程：

$$
\begin{align}
Q^{\pi} = r(\mathbf{s},\mathbf{a})+\gamma \sum_{\mathbf{s}'\in \mathcal{S} }P(\mathbf{s}'|\mathbf{s},\mathbf{a}) \sum_{a'\in \mathcal{A} }\pi(\mathbf{a}'|\mathbf{s}')Q^{\pi}(\mathbf{s}',\mathbf{a}')
 \\ 
V^{\pi} = \sum_{\mathbf{a}\in \mathcal{A} }\pi(\mathbf{a}|\mathbf{s})\left( r(\mathbf{s},\mathbf{a})+\gamma \sum_{\mathbf{s}'\in \mathcal{S } }P(\mathbf{s}'|\mathbf{s},\mathbf{a})V^{\pi} (\mathbf{s}') \right) 
\end{align}
$$
## 动态规划算法

动态规划的基本思想是将待求解问题分解成若干个子问题，先求解子问题，然后从这些子问题的解得到目标问题的解。

基于动态规划的强化算法主要两种

- 策略迭代 policy iteration 
- 价值迭代 value iteration 

具体来说策略迭代中的策略评估使用 **Bellman 期望方程**得到一个策略的状态价值函数，这是一个动态规划的过程；而价值迭代直接使用 **Bellman 最优方程**来进行动态规划，得到最终的最优状态价值函数。

算法的局限：

- 基于动态规划的强化学习算法要求事先指导环境的状态转移函数和奖励函数，也就是需要这样整个 Markov 决策过程。在这样一个白盒环境中，不需要通过智能体和环境的大量交互学习，可以直接求解，但是这种白盒环境很少
- 策略迭代和价值迭代通常只适用于有限 Markov 决策过程，即状态空间和动作空间是离散且有限的。

### 策略迭代算法
策略迭代是策略评估和策略提升不断循环交替，指导最后得到最优策略的过程。

策略评估：计算一个策略的状态价值函数

$$
V^{k+1}(\mathbf{s} ) = \sum_{\mathbf{a \in \mathcal{A} }} \pi(\mathbf{a}|\mathbf{s}) \left( r(\mathbf{s},\mathbf{a})+\gamma \sum_{\mathbf{s' \in \mathcal{S} }}^{} P(\mathbf{s}'|\mathbf{s},\mathbf{a})V^{k}(\mathbf{s}') \right) 
$$


策略提升：使用策略评估计算得到当前策略的状态价值函数后，据此来改进策略。

$$
\pi'(\mathbf{s}) = \arg \max_{\mathbf{a}}\left\{ r(\mathbf{s},\mathbf{a})+\gamma \sum_{\mathbf{s}'}^{} P(\mathbf{s' |\mathbf{s},\mathbf{a}})V^{\pi}(\mathbf{s}') \right\} 
$$

我们所构造出来的贪婪策略 $\pi'$ 满足策略提升定理（policy inprovement theorem），它比原本所采取的策略 $\pi'$ 更好或者至少一样好。

因此策略迭代算法的过程简述如下：对当前的策略进行策略评估，得到其状态价值函数，然后根据该状态价值函数进行策略提升以得到一个更好的新策略，接着继续评估新策略、提升策略。

### 价值迭代算法

策略迭代算法中的策略评估需要进行很多轮才能收敛到某一策略的状态函数，这需要很大的计算量，尤其是在状态和动作空间比较大的情况下。因此我们希望在策略评估中进行一轮价值更新，然后直接根据更新后的价值进行策略提升。价值迭代算法可以视作是一种策略评估只进行了一轮更新的策略迭代算法，我们只需要维护状态价值函数。

迭代更新公式：

$$
V^{k+1}(\mathbf{s}) = \max_{\mathbf{a}\in \mathcal{A} }\left\{ r(\mathbf{s},\mathbf{a})+\gamma \sum_{ \mathbf{s}' \in \mathcal{S} }P(\mathbf{s}'|\mathbf{s},\mathbf{a})V^{k}(\mathbf{s}') \right\} 
$$


## 时序差分

时序差分方法结合了Monte Carlo和动态规划算法的思想，其使我们可以从样本数据中学习，不需要事先知道环境，并利用后续状态的价值估计来更新当前状态的价值估计。

时序差分算法用当前获得的奖励加上下一个状态的价值估计来作为在当前状态会获得的回报，即：

$$
V(\mathbf{s}_{t}) \leftarrow V(\mathbf{s}_{t} ) + \alpha[r_{t}+\gamma V(\mathbf{s}_{t+1})-V(\mathbf{s}_{t})]
$$

其中 $r_t+\gamma V(\mathbf{s}_{t+1})-V(\mathbf{s}_{t})$ 称为时序差分（temporal difference）。

## Sarsa 算法

利用时序差分算法估计动作价值函数 $Q$ 

$$
Q(\mathbf{s}_{t},\mathbf{a}_{t}) \leftarrow Q(\mathbf{s}_{t},\mathbf{a}_{t}) +\alpha[r_{t}+\gamma Q(\mathbf{s}_{t+1},\mathbf{a}_{t+1})-Q(\mathbf{s}_{t},\mathbf{a}_{t})]
$$

然后使用贪婪算法来选取在某个状态下动作价值最大的那个动作，即 $\arg \max_{\mathbf{a}}Q(\mathbf{s},\mathbf{a})$。上述过程可概括为：使用贪婪算法根据动作价值选取动作来和环境交互，再根据得到的数据用时序差分算法更新动作价值估计。

但是这个过程有两个问题

- 需要大量样本来更新，但根据广义策略迭代（Generalized Policy Iteration）的思想，我们可以只用少量样本
- 使用贪婪算法可能会使某些动作对没有在序列中出现。

解决方法是采用 $\epsilon$ -贪婪策略：

$$
\pi(\mathbf{a}|\mathbf{s}) =  \begin{cases}
\epsilon /\lvert A \rvert +1 - \epsilon  & \mathrm{if }\ \arg \max_{\mathbf{a'}} Q(\mathbf{s},\mathbf{a}') \\
\epsilon/\lvert A \rvert  & \mathrm{others}
\end{cases}
$$

于是就得到了 Sarsa 算法。

### 多步 Sarsa 算法

时序估计算法具有非常小的方差，但是因其只关注了一步状态转移和奖励，这是一个有偏估计。我们更希望得到一个无偏（或低偏差）低方差的算法，一个方法是采用多部时序差分，其中我们使用 $n$ 步的奖励，然后使用之后状态的价值估计：

$$
G_{t} = r_{t} +\gamma r_{t+1}+\dots+\gamma^{n}Q(\mathbf{s}_{t+n},\mathbf{a}_{t+n})
$$

对应多步 Sarsa 算法：

$$
Q(\mathbf{s}_{t},\mathbf{a}_{t}) \leftarrow Q(\mathbf{s}_{t},\mathbf{a}_{t}) +\alpha[r_{t} +\gamma r_{t+1}+\dots+\gamma^{n}Q(\mathbf{s}_{t+n},\mathbf{a}_{t+n})-Q(\mathbf{s}_{t},\mathbf{a}_{t})]
$$

