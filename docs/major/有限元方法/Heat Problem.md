


## Thermal Stress

考虑热应变问题：

$$
\varepsilon_{ij}^{0 }= \alpha \Delta T \delta_{ij} 
$$

当弹性体发生温度变化时，弹性体体内各点的微小长度在不受约束的情况下发生正应变 $\alpha \Delta T$，其中 $\alpha$ 为线膨胀系数

热应变(thermal strains)并不会产生应力，想象一个受热自由膨胀的物体，其内部并没有应力的产生。

在受热情况下，将本构方程修改成如下形式

$$
{\sigma} = \mathbf{D}({\varepsilon}-{\varepsilon}^{0})
$$

其他物理方程保持不变：

$$
\begin{align}
&{\varepsilon} =\nabla \mathbf{u} \\
&{\sigma}=\mathbf{D}({\varepsilon}-{\varepsilon}^{0}) \\
&\nabla^{T}{\sigma}+\mathbf{f} =0
\end{align}
$$

利用最小势能原理导出刚度矩阵，势能满足：

$$
\Pi_{p} =\underset{ \text{Strain energy} }{  \int_{B} \frac{1}{2}\sigma_{ij}\left( \varepsilon_{ij}-\varepsilon_{ij}^{0} \right) \mathrm{d}V  }\underset{ \text{Potential of external forces} }{ -\int_{B} f_{i}u_{i}\mathrm{dV}-\int_{\partial B} \bar{T}_{i}u_{i} \mathrm{d}S }
$$

写成矩阵的形式：

$$
\Pi_{p} =\frac{1}{2}\int_{B}{\varepsilon}^{T}\mathbf{D}{\varepsilon}\mathrm{d}V-\int_{B}{\varepsilon}^{T}\mathbf{D}{\varepsilon}^{0}\mathrm{d}V-\int_{B}\mathbf{u}^{T}\mathbf{f}\mathrm{d}V -\int_{\partial B}\mathbf{u}^{T}\mathbf{T}\mathrm{d}S +\int_{B}({\varepsilon}^{0})^{T}\mathbf{D}{\varepsilon}^{0}\mathrm{d}V
$$

势能最小原理给出：

$$
\delta \Pi_{p} =0
$$

由于最后一项积分结果为常数，所以不纳入考虑，假定

$$
\mathbf{u} =\mathbf{Nd}_{e} \implies {\varepsilon} = \left( \nabla \mathbf{N} \right) \mathbf{d}_{e} = \mathbf{B}\mathbf{d}_{e}
$$

即满足我们最开始的假设带入后取对 $\mathbf{d}_{e}^{T}$ 的偏导为零可得

$$
\left( \int_{B}\mathbf{B}^{T}\mathbf{DB} \mathrm{d}V\right) \mathbf{d_{e}} = \left( \int_{B}\mathbf{N}^{T}\mathbf{f}\mathrm{d}V \right)+\left( \int_{\partial B}\mathbf{N}^{T}\mathbf{T}\mathrm{d}S \right) + \underset{ \mathbf{f}_{T} }{ \int_{B}\mathbf{B}^{T}\mathbf{D}{\varepsilon}^{0}\mathrm{d}V  }
$$

这就是元素满足的刚度方程，将其装配后可得全局刚度方程，他与原来的方程相比多了一项由温度变化引起的力记作 $\mathbf{f}_{T}$

如果考虑的是一维杆问题，则：

$$
\mathbf{f_{T}} = \begin{Bmatrix}
-E\alpha\Delta T A \\
E\alpha\Delta T A
\end{Bmatrix}
$$

$A$ 是横截面积。

二维情况：

- 平面应变：

$$
\left\{ \varepsilon^{0} \right\}  = (1+\nu )\left\{ \begin{align}
\alpha&\Delta T \\
\alpha&\Delta T  \\
&0
\end{align} \right\} 
$$

- 平面应力：

$$
\left\{ \varepsilon^{0} \right\}  = \left\{ \begin{align}
\alpha&\Delta T \\
\alpha&\Delta T  \\
&0
\end{align} \right\} 
$$



对于平面应力问题，我们考虑 CST 元素，此时热应力的影响被简化为：

$$
\left\{ \mathbf{f}_{T} \right\}  = \mathbf{B}^{T}\mathbf{D}{\varepsilon} tA 
$$

带入求解可得：

$$
\left\{ \mathbf{f}_{T} \right\}  = \frac{\alpha EAt \Delta T}{2(1-\nu)}\begin{bmatrix}
\beta_{i} & \gamma_{i} & \beta_{j} & \gamma_{j} & \beta_{m} & \gamma_{m}
\end{bmatrix}^{T}
$$

对于轴对称元素，热应力满足：

$$
\left\{ \mathbf{f}_{T} \right\} = 2\pi \bar{r}A \bar{\mathbf{B}}^{T}\mathbf{D}{\varepsilon}^{0} 
$$

其中 $\bar{r}$ 表示质心，$\bar{\mathbf{B}}^{T}$ 表示质心 $\bar{z}$ 对应的应变矩阵


## Heat Transfer

!!! note "多物理场的有限元方法"
    1. 寻找控制方程(Governing equations)和边界条件(boundary conditions)  
    2. 使用变分法建立控制方程的弱形式及系统总势能  
    3. 选择合适的单元类型，并据此确定基本变量的插值函数（形函数）  
    4. 利用基本变量的插值函数构造系统总势能  
    5. 最小化系统总势能，以得到单元刚度矩阵和节点“力”向量  
    6. 汇总单元刚度矩阵和节点“力”向量，建立全局线性方程组


一维Fourier定律：

$$
q_{x} =-K_{xx} \frac{{\rm{d}}   T }{{\rm {d}} x } 
$$

能量守恒方程：

$$
\begin{align}
&E_{\mathrm{in}}+E_{\mathrm{generate}} = \Delta U +E_{\mathrm{out}}\\
\implies& q_{x}A \mathrm{d}t +QA \mathrm{d}x \mathrm{d}t = \Delta U +(q_{x}+\mathrm{d}q_{x})A\mathrm{d}t
\end{align}
$$

内能变化可以表示：

$$
\Delta U = c \rho A \mathrm{d}x\mathrm{d}T
$$

将上述带入可得热传导方程：

$$
K_{xx} \frac{{\rm{d}}  ^{2}T }{{\rm {d}} x^{2} } +Q  = c\rho \frac{{\rm{d}}   T }{{\rm {d}} t } 
$$

稳定状态时退化成：

$$
K_{xx} \frac{{\rm{d}}   ^{2}T }{{\rm {d}} x^{2} } +Q =0
$$

如果在二维情况，可以拓展成：

$$
K_{xx}\frac{\partial^{2}T }{\partial  x^{2}} +K_{yy}\frac{\partial^{2}T }{\partial y^{2} } +Q =0  
$$

### 热问题有限元法
如果用有限元方法求解热传导问题，此时系统势能写作：

$$
\Pi_{p} = \underbrace{ \frac{1}{2}\int_{A} \left[ K_{xx} \left( \frac{\partial T }{\partial x }  \right) ^{2}+K_{yy} \left( \frac{\partial T }{\partial y }  \right)^{2}  \right]\mathrm{dA} }_{U} - \underbrace{\int_{A}QT \mathrm{d}A }_{\Omega _{Q}}- \underbrace{ \int_{\partial S}T\bar{q} \mathrm{d}s }_{\Omega_{q}}
$$

此时变量只有 $T$ ，变分原理给出：

$$
\delta \Pi_{p}= 0
$$

首先需要针对问题选择合适的元素，然后为温度选择合适的形函数：

$$
T(x,y) = \sum_{i=1}^{n}N_{i}T_{i}  \triangleq \mathbf{NT}_{d} 
$$

$\mathbf{T}_{d}$ 表示节点温度，将上述表达式带回势能，求出应变矩阵：

$$
\begin{align}
\Pi_{p} &= \frac{1}{2}\int_{A}\left[ K_{xx}\left( \frac{\partial T }{\partial x }  \right) ^{2}+K_{yy} \left( \frac{\partial T }{\partial y }  \right)^{2}  \right]\mathrm{d}A -\int_{A}QT \mathrm{d}A -\int_{\partial S} T \bar{q} \mathrm{d}S \\
&= \frac{1}{2}\int_{A} \begin{Bmatrix}
\frac{\partial T }{\partial x }  \\
\frac{\partial T }{\partial y } 
\end{Bmatrix}^{T}\begin{bmatrix}
K_{xx }  &  0 \\
0 & K_{yy}
\end{bmatrix}\begin{Bmatrix}
\frac{\partial T }{\partial x } \\
\frac{\partial T }{\partial y }  
\end{Bmatrix} \mathrm{d}A - \int _{A}QT \mathrm{d}A -\int_{\partial S}T \bar{q} \mathrm{d}S  \\
&= \frac{1}{2}\mathbf{T}_{d}^{T}\left( \int_{A}\mathbf{B}^{T}\mathbf{KB}\mathrm{d}A \right) \mathbf{T}_{d} -\mathbf{T}_{d}^{T}\left( \int_{A}Q \mathbf{N}^{T}\mathrm{d}A \right) -\mathbf{T}_{d}^{T}\left( \int_{\partial S} \bar{q} \mathbf{N}^{T}\mathrm{d}S \right) 
\end{align}
$$

其中：

$$
\begin{Bmatrix}
\frac{\partial T }{\partial x }  \\
\frac{\partial T }{\partial y } 
\end{Bmatrix} = \mathbf{B}\mathbf{T}_{d} \quad \mathbf{B} = \begin{bmatrix}
\frac{\partial N_{1} }{\partial x }  & \frac{\partial N_{2} }{\partial  x }  & \cdots \\
 \frac{\partial N_{1} }{\partial y }  & \frac{\partial N_{2} }{\partial  y }  & \cdots
\end{bmatrix}
$$

根据最小势能原理可得刚度矩阵（此时考虑一个厚度为 $h$  的板）：

$$
\mathbf{k}_{e} =h \int_{A} \mathbf{B}^{T}\mathbf{KB} \mathrm{d}x\mathrm{d}y
$$

转换成等参形式：

$$
\mathbf{k}_{e} =h \int^{1}_{-1}\int^{1}_{-1}\mathbf{B}^{T}\mathbf{KB}\lvert \mathbf{J} \rvert \mathrm{d} s \mathrm{d}t = \sum_{i=1}^{I_{p}} \sum_{j=1}^{I_{p}} \mathbf{B}^{T}(s_{i},t_{j})\mathbf{KB}(s_{i},t_{j})\lvert \mathbf{J}(s_{i},t_{i})\rvert\alpha_{i}\alpha_{j}
$$

等效外力矩阵：

$$
\mathbf{P}_{e} =\underset{ \text{Internal heat source} }{ \int_{A}Q \mathbf{N}^{T}\mathrm{d}A  }+\underset{\text{Surface heat flow into the element} }{ \int_{\partial S} \bar{q} \mathbf{N}^{T}\mathrm{d}S }
$$

所以得到此时的“刚度”方程：

$$
\mathbf{k}_{e} \mathbf{T}_{d} = \mathbf{P}_{e}
$$

---
现在讨论边界条件：考虑左端 $x_{A}$ 流入热流，右端 $x_{B}$ 置放在恒温 $\bar{T}$ 环境，对应第一种和第二种边界条件，给出：

- $x =x_{A}$

$$
-K_{xx} \frac{{\rm{d}}  T }{{\rm {d}} x } =\bar{q}_{x}
$$

- $x=x_{B}$

$$
T= \bar{T} 
$$

如果是二维情况，第二种边界条件为：

$$
\bar{q}_{n} = K_{x x} \frac{\partial T }{\partial x } n_{x}+K_{yy} \frac{\partial T }{\partial y } n_{y}
$$

$n_{x}$ 和 $n_{y}$ 分别为热流法向到 $x$ 轴和 $y$ 轴的投影