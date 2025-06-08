
## Single DOF System 
振动方程：

$$
m \ddot{u} +c \dot{u} + ku = f(t)
$$

无阻尼和外加激励情况下可简写成：

$$
m \ddot{u} +k u =0
$$

假定解 $u =U\sin(\omega t)$ ，带入可得固有频率：

$$
\omega = \sqrt{ \frac{k}{m} }
$$

考虑阻尼作用，定义阻尼比：

$$
\xi = \frac{c}{c_{0}} \quad c_{0} = 2m \omega =2\sqrt{ km }
$$

此时对应固有频率：

$$
\omega_{d} = \omega \sqrt{ 1-\xi^{2} }
$$

当 $0\leq \xi\leq{0}.15$ 时（称为结构阻尼），近似取

$$
\omega_{d} \approx \omega
$$

这种情况下，在模态分析中可以忽略阻尼影响

## Multiple DOF System
整体结构：

$$
\mathbf{M}\ddot{\mathbf{u}}+\mathbf{C}\dot{\mathbf{u}} + \mathbf{Ku} =\mathbf{f}(t)
$$

其分别代表：惯性力，耗散力，弹性力和外加力。

根据质量在单元内的分布方式，质量矩阵主要有两种形式：集中质量矩阵(Lumped Mass Matrix) 和一致质量矩阵 (Consistent Mass Matrix) 。

利用动能导出一致质量矩阵：

$$
\begin{align}
& \frac{1}{2}\int_{V} \rho  \dot{u}^{2}\mathrm{d}V  = \frac{1}{2}\int \rho(\dot{u}^{T})\dot{u} \mathrm{d}V \\
=& \frac{1}{2}\int_{V}\rho (\mathbf{N} \dot{\mathbf{u}})^{T}\left( \mathbf{N} \mathbf{\dot{u}} \right) \mathrm{d}V \\
=&  \frac{1}{2} \dot{\mathbf{u}}^{T} \int \rho \mathbf{N}^{T}\mathbf{N } \mathrm{d}V \dot{\mathbf{u}}
\end{align}
$$


因此，**一致质量矩阵**可以描述为通过在单元体积内对形函数的乘积与密度进行积分得到更准确地表示连续体中的质量分布：

$$
\mathbf{M}_{e} =\int_{V_{e}}\rho \mathbf{N}^{T}\mathbf{N}\mathrm{d} V
$$

- 矩阵通常是对称的，包含非零的非对角项，表示不同自由度之间的质量耦合。
- 能更准确地表示结构的惯性特性，特别适用于需要精确动态响应的分析，如模态分析和隐式动力学分析。
- 计算和存储成本较高，尤其在大规模问题中

**集中质量矩阵**是一种更简单且通常更有效的方法，用于表示结构内的分布质量将单元质量集中分配到节点上,特点：

- 矩阵为对角矩阵，非对角项为零，计算和存储效率高。
- 适用于显式动力学分析和大规模问题，能显著减少计算时间。        
- 在某些情况下，可能会低估结构的惯性效应，影响高阶模态的准确性。

理论上，集中质量矩阵产生的精度低于一致质量矩阵。但在实践中，当网格足够细化时，集总质量近似的精度与一致质量的精度相当。对于结构的较低振动模式尤其如此。集中质量矩阵通常会产生低于精确值的固有频率。

??? example

    考虑一个一维简单杆元素，有两个节点，集中质量矩阵为：

    $$
    \mathbf{M} = \begin{bmatrix}
    \frac{\rho AL}{2}  & 0 \\
    0 &  \frac{\rho AL}{2}
    \end{bmatrix}
    $$

    一致质量矩阵：

    $$
    \mathbf{M} = \int \rho \begin{bmatrix}
    1-\xi \\
    \xi
    \end{bmatrix}\begin{bmatrix}
    1-\xi  & \xi 
    \end{bmatrix}AL \mathrm{d}\xi = \rho AL \begin{bmatrix}
    1/3 & 1/6 \\
    1/6 & 1/3   
    \end{bmatrix}
    $$


对于保守系统：

$$
\mathbf{M}\ddot{\mathbf{u}} + \mathbf{Ku} =0
$$

假定取周期解：$\mathbf{u}=\bar{\mathbf{u}}\sin(\omega t)$ ，$\bar{ \mathbf{u}}$ 表示节点位移的向量，可得：

$$
[\mathbf{K}-\omega^{2}\mathbf{M}]\bar{\mathbf{u}} =0
$$

这是一个广义特征值问题（EVP），则非奇异解要求：

$$
\lvert \mathbf{K}-\omega^{2} \mathbf{M}\rvert =0
$$

满足上述等式的解 $\omega_{i}$ 称为结构的固有频率，将最小的 $\omega_{1}$ 称为基频，每一个 $\omega_{i}$ 对应一个特征向量 $\bar{\mathbf{u}}_{i}$ 称为模态，满足正交性：

$$
\bar{\mathbf{u}}_{i}^{T}\mathbf{M}\bar{\mathbf{u}}_{j}=\left\{\begin{align}
0  \text{ i} \neq \text{j}\\
1  \text{ i} = \text{j}
\end{align} \right.
$$

$$
\quad\bar{\mathbf{u}}_{i}^{T}\mathbf{K}\bar{\mathbf{u}}_{j}= \left\{\begin{align}
0  \text{ i} \neq \text{j}\\
\omega_{i}^{2} \text{ i} = \text{j}
\end{align} \right.
$$

- 正常模态分析中，位移（模态）或应力的大小并没有物理意义，因为我们只考虑频率和对应的振动模式（模态形状）
- 对于正常模态分析，并不需要考虑外部约束
	-  $\omega_{i} =0 \Leftrightarrow$ 整个结构或结构的一部分做刚体运动
	- 如果 $\omega_{i}=0$ ，需要检查有限元模型，如模型中存在刚体运动或自由元件（Free Elements）。这可能是由于模型设置不当，例如没有正确地施加边界条件或支撑，导致模型可以自由移动而没有受到约束。
- 在有限元计算中，较低的模态比较高模态更准确。因此对于低模态而言，达到足够精度需要的有限元模型的元素数量较少（因为较低模态对应的波长比较长，我们可以使用较粗的网格）