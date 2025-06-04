我们先推导局部(Local)坐标中杆单元方程

**Features of a Bar** 

- 只受轴向力（tension or compressin）
- 沿轴向变形
- 不受剪力和弯矩


![alt text](<Source/Pasted image 20250311134246.png>)

**位移函数**：

$$
\begin{align}
&u(x) = u_{i} + \frac{u_{j}-u_{i}}{L}x \\
\implies& u(x) = \left( 1-\frac{x}{L} \right) u_{i}+\frac{x}{L}u_{j} = \begin{bmatrix}
N_{i} & N_{j}
\end{bmatrix} \begin{Bmatrix}
u_{i} \\
u_{j}
\end{Bmatrix} \\
& \text{where} N_{i}=1-\frac{x}{L}\quad N_{j}= \frac{x}{L}
\end{align}
$$

$N_{i},N_{j}$ 称为形函数，表示在定义域上元素假定位移函数的形状

**应力和应变**：

$$
\begin{align}
&\varepsilon = \frac{{\rm{d}}  u }{{\rm {d}} x } =\frac{u_{j}-u_{i}}{L} \\
&\sigma = E\varepsilon = E \frac{u_{j}-u_{i}}{L}
\end{align}
$$



刚度矩阵：

$$
\frac{EA}{L}\begin{bmatrix}
1 & -1 \\
-1 &  1
\end{bmatrix}
$$

---
用能量法获取刚度矩阵：

- 位移

$$
u(x) =\begin{bmatrix}
N_{i} & N_{j}
\end{bmatrix} \begin{Bmatrix}
u_{i} \\
u_{j}
\end{Bmatrix} = [\mathbf{N}]\left\{\mathbf{u} \right\} 
$$

- 应变

$$
\varepsilon_{x} = \frac{{\rm{d}}  u(x) }{{\rm {d}} x } = \frac{{\rm{d}}  [\mathbf{N}] }{{\rm {d}} x }\left\{\mathbf{u} \right\} = \left[ \mathbf{B} \right] \left\{\mathbf{u} \right\}    
$$

- 应力

$$
\sigma_{x} =E\varepsilon_{x} = [\mathbf{D}][\mathbf{B}]\left\{\mathbf{u} \right\}  \quad [\mathbf{D}]=[E] 
$$

应变矩阵 $[\mathbf{B}]$ 计算了节点位移而产生的应变，材料性质矩阵 $[\mathbf{D}]$ 一般是一个对称矩阵。

杆的势能

$$
\Pi _{p}(u) = U(u)+\Omega(u)
$$

其中 $U$ 为应变能，在杆的问题中为：

$$
\begin{align}
U(u)&=\frac{1}{2}\iiint_{V}\sigma_{x}\varepsilon_{x}dV  \\
&=\frac{A}{2}\int_{0}^{L}\sigma_{x}^{T}\varepsilon_{x}\mathrm{d}x = \frac{A}{2}\int_{0}^{L} \left\{\mathbf{u} \right\}^{T}[\mathbf{B}]^{T}[\mathbf{D}]^{T}[\mathbf{B}]\left\{\mathbf{u}  \right\}\mathrm{d}x   \\
&=\frac{AL}{2} \left\{\mathbf{u} \right\}^{T}[\mathbf{B}]^{T}[\mathbf{D}]^{T}[\mathbf{B}]\left\{\mathbf{u}  \right\}
\end{align}
$$

$\Omega$ 是功，

$$
\begin{align}
\Omega &= -\left( f_{i}u_{i}+f_{j}u_{j} \right) -\underbrace{\iint_{S_{1}}u_{s}^{T}T_{x}dS}_{表面载荷作用} \\
&=-\left\{\mathbf{u} \right\}^{T}\left\{ \mathbf{P}  \right\}-\iint_{S_{1}}\left\{\mathbf{u} \right\}^{T}\left[ \mathbf{N}_{s}  \right]^{T}T_{x}\mathrm{d}S  \\
&=-\left\{\mathbf{u}  \right\}^{T}\left\{ \mathbf{f} \right\}      \\
&\quad \left\{ \mathbf{f} \right\}  = \left\{ \mathbf{{P}} \right\}+\underset{ 等效节点力 }{ \iint_{S_{1}}\left[ \mathbf{N}_{s}  \right]^{T}T_{x}\mathrm{d}S } 
\end{align}
$$

因此势能可写作

$$
\Pi_{p}=\frac{AL}{2} \left\{\mathbf{u} \right\}^{T}[\mathbf{B}]^{T}[\mathbf{D}]^{T}[\mathbf{B}]\left\{\mathbf{u}  \right\}-\left\{\mathbf{u}  \right\}^{T}\left\{ \mathbf{f} \right\} 
$$

由最小势能原理可得

$$
\frac{\partial \Pi_{p} }{\partial \left\{ \mathbf{u} \right\}  }  \implies AL \left[ \mathbf{B} \right] ^{T}\left[ \mathbf{D} \right] ^{T}\left[ \mathbf{B} \right] \left\{ \mathbf{u} \right\} =\left\{\mathbf{f}  \right\} \triangleq[\mathbf{k}] \left\{ \mathbf{u} \right\}=\left\{ \mathbf{f} \right\}  
$$
---

将均布载荷转换成节点上的集中力。

$$
\begin{align}
&\left\{ \mathbf{f }_{s} \right\} =\iint_{S_{1}}\left[ \mathbf{N}_{s} \right]^{T}T_{x}dS  \\
\text{Here }& \left[ \mathbf{N}_{s} \right] =\begin{bmatrix}
1-x/L  & x/L
\end{bmatrix},\quad T_{x} = q ,\quad dS =dx \\
\implies& \left\{ \mathbf{f}_{s} \right\} =\iint_{S_{1}} \left[ \mathbf{N}_{s} \right]^{T}T_{x}dS = q\int_{0}^{L}\begin{Bmatrix}
1-x/L \\
x/L 
\end{Bmatrix}  \, {\rm d} x = \begin{Bmatrix}
qL /2 \\
qL /2
\end{Bmatrix}
\end{align}
$$

这是为了在运用 $\mathbf{Ku=F}$ 的时候能确定节点上对应的集中力

在比如说，假定杆上的载荷服从线性关系，即 $\hat{T}_{x}= C \hat{x}$ ，那么作用在前后节点上的集中力为

$$
\left\{ \mathbf{f}_{s} \right\} =\iint_{S_{1}} \left[ \mathbf{N}_{s} \right]^{T}T_{x}dS = q\int_{0}^{L}\begin{Bmatrix}
1-x/L \\
x/L 
\end{Bmatrix}  Cx\, {\rm d} x = \begin{Bmatrix}
CL^{2} /6 \\
CL^{2} /3
\end{Bmatrix}
$$

!!! example

    ![alt text](<Source/Pasted image 20250311190432.png>)

    我们先考虑杆会不会接触到墙面，当右端没有墙时，$3$ 点位移为

    $$
    \Delta_{0} = \frac{PL}{EA} = 1.8\mathrm{mm}> 1.2 \mathrm{mm}
    $$

    所以杆会接触到墙壁。

    Global 刚度矩阵：

    $$
    \mathbf{K} =\frac{EA}{L}\begin{bmatrix}
    1 & -1 & 0  \\
    -1 & 2 & -1 \\
    0 & -1 & 1
    \end{bmatrix}
    $$

    所以待定方程为：

    $$
    \frac{EA}{L}\begin{bmatrix}
    1 & -1 & 0  \\
    -1 & 2 & -1 \\
    0 & -1 & 1
    \end{bmatrix}\begin{Bmatrix}
    u_{1}  \\
    u_{2} \\
    u_{3}
    \end{Bmatrix}= \begin{Bmatrix}
    F_{1} \\
    F_{2} \\
    F_{3}
    \end{Bmatrix}
    $$

    边界条件：

    $$
    u_{1}= 0,u_{3} =\Delta,F_{2} = P
    $$

    可以解得

    $$
    u_{2} = \frac{1}{2}\left( \frac{PL}{EA}+\Delta \right)=1.5 \mathrm{ mm} 
    $$




---
收敛性

- 位移在每个节点（node）上都是准确的，但是在节点之间不一定
- 每个元素(elements)所受的应力为常量，但是元素之间的应力不是连续的
	- 解的准确性会随着元素数量的增加而提高

在FEA 中，我们采用多项式来描述一个元素的位移。

$$
\begin{align}
u(x,y,z) &= a_{0} +a_{1} x+b_{1} y +c_{1}z+\dots \\
&= N_{1}(x,y,z) u_{1}+N_{2}(x,y,z)u_{2}+\dots \\
& = \sum_{i}^{} N_{i}u_{i}
\end{align}
$$

形函数的性质：
1. 在节点 $i,N_{i} = 1$，在其他节点 $N_{i}= 0$
2. 允许刚体移动：$\sum_{}^{}N_{i}=1$

<font color="#ff0000">位移函数的完整性和相容性确保 FEA 的收敛性</font>

- 完备性（Complete）：allow for a rigid-body displacement and a constant strain within the element
- 相容性（Compatible）：continuity between adjacent elements and within the elemen
	- $C^{m}$ 描述了多项式的连续程度，比如说 $C^{0}$ 表述位移式连续的，$C^{1}$ 表述位移和其一阶导数是连续的。


![alt text](<Source/Pasted image 20250311163143.png>)

---
将局部(Local)元素和全局(Global)元素联系起来

取元素与水平方向的夹角为 $\theta$ ，即定义

$$
C= \cos \theta = \frac{X_{j}-X_{i}}{L}\quad S =\sin \theta =\frac{Y_{j}-Y_{i}}{L}
$$

取 $u'$ 表示局部元素，$u$ 为全局元素，则坐标系的变换满足：

$$
\begin{Bmatrix}
u_{i}' \\
v_{i} '\\
u_{j} '\\
v_{j}'
\end{Bmatrix}
=\begin{bmatrix}
C & S & 0 & 0 \\
-S & C & 0 & 0 \\
0 & 0 & C & S \\
0 & 0 & -S & C
\end{bmatrix}\begin{Bmatrix}
u_{i} \\
v_{i} \\
u_{j} \\
v_{j}
\end{Bmatrix}\triangleq \mathbf{u}' = \mathbf{Tu}
$$

称 $\mathbf{T}$ 为变换矩阵，其满足 $\mathbf{T}^{-1}=\mathbf{T}^{T}$

节点上的力也遵循变换规则

$$
\mathbf{f}' = \mathbf{Tf}
$$

对应刚度矩阵

$$
\mathbf{k}=\mathbf{T}^{T}\mathbf{k}'\mathbf{T}
$$

展开为

$$
\mathbf{k}=\frac{EA}{L}\begin{bmatrix}
	C^{2} & CS & -C^{2} & -CS  \\
	CS  & S^{2}  & -CS & -S^{2} \\
	-C^{2} & -CS & C^{2} & CS  \\
	-CS & -S^{2} & CS & S^{2}
\end{bmatrix}
$$


<font color="#ff0000">注：每一根杆都有自己的旋转矩阵</font>


![alt text](<Source/Pasted image 20250311192331.png>)

如果全局元素是一个三维问题，则取

$$
\begin{align}
l& = \frac{X_{j}-X_{i}}{L}= \cos \theta_{x} \\
m& = \frac{Y_{j}-Y_{i}}{L}= \cos \theta_{y} \\
n& = \frac{Z_{j}-Z_{i}}{L}= \cos \theta_{z}
\end{align}
$$

则刚度矩阵为

$$
\left[ \mathbf{k} \right] = \frac{AE}{L}\begin{bmatrix}
l^{2} & lm & ln & -l^{2} & -lm & -ln \\
lm & m^{2} & mn & -lm & -m^{2} & -mn  \\
ln  & mn &  n^{2} & -ln & -mn & -n^{2} \\
-l^{2} & -lm & -ln &   l^{2} & lm & ln  \\
-lm & -m^{2} & -mn & lm & m^{2} & mn  \\
-ln & -mn & -n^{2} & ln  & mn &  n^{2}
\end{bmatrix}
$$

!!! example

    ![alt text](<Source/Pasted image 20250312101646.png>)


    对于杆 $1$ ，$\theta = 90^{\circ},C= 0,S=1$ ，对应的 Global 刚度矩阵：

    $$
    \mathbf{k}_{1} = \frac{EA}{L}\begin{bmatrix}
    0 & 0 & 0 & 0 \\
    0 & 1 & 0 & -1 \\
    0 & 0 & 0 & 0 \\
    0 & -1 & 0 & 1
    \end{bmatrix}
    $$

    对于杆 $2$ $\theta =0^{\circ},C=1,S=0$，对应的 Global 刚度矩阵：

    $$
    \mathbf{k}_{2} = \frac{EA}{L} \begin{bmatrix}
    1 & 0 & -1 & 0 \\
    0 & 0 & 0 & 0 \\
    -1 & 0 & 1 & 0 \\
    0 & 0 & 0 & 0
    \end{bmatrix}
    $$

    对于杆 $3,\theta =45^{\circ},C = \sqrt{ 2 } /2,S =\sqrt{ 2 } /2$ 对应的 Global 刚度矩阵

    $$
    \mathbf{k}_{3} =\frac{EA}{L}\begin{bmatrix}
    0.5 & 0.5 & -0.5 & -.05 \\
    0.5 & 0.5 & -0.5 & -0.5 \\
    -0.5 & -0.5 & 0.5 & 0.5 \\
    -0.5 & -0.5 & 0.5 & 0.5
    \end{bmatrix}
    $$

    装配后的矩阵

    $$
    \frac{EA}{L}
    \begin{bmatrix}
    0.5 & 0.5 & 0 & 0 & -0.5 & -0.5 \\
    0.5 & 1.5 & 0 & -1 & -0.5 & -0.5 \\
    0 & 0 & 1 & 0 & -1 & 0 \\
    0 & -1 & 0 & 1 & 0 & 0 \\
    -0.5 & -0.5 & -1 & 0 & 1.5 & 0.5 \\
    -0.5 & -0.5 & 0 & 0 & 0.5 & 0.5
    \end{bmatrix}
    \begin{Bmatrix}
    u_{1} \\
    v_{1} \\
    u_{2} \\
    v_{2} \\
    u_{3} \\
    v_{3} \\
    \end{Bmatrix}
    =\begin{Bmatrix}
    F_{1X} \\
    F_{1Y} \\
    F_{2X} \\
    F_{2Y} \\
    F_{3X} \\
    F_{3Y}
    \end{Bmatrix}
    $$

    引入边界条件，此时有两个特殊的边界条件：$v_{3}' =0,F_{3x'}=0$，对应几何关系

    $$
    \begin{align}
    u_{3}-v_{3} &= 0 \\
    F_{3X}-F_{3Y} &= 0 
    \end{align}
    $$

    其余边界条件： $u_{1} =v_{1}=v_{2} =0,F_{2X}=P$ ，对应待定方程：

    $$
    \frac{EA}{L}\begin{bmatrix}
    1 & -1 & 0  \\
    -1 & 1.5  & 0.5\\
    0 & 0.5 & 0.5
    \end{bmatrix}\begin{Bmatrix}
    u_{2}  \\
    u_{3} \\
    u_{3}
    \end{Bmatrix}
    =
    \begin{Bmatrix}
    P \\
    F_{3X} \\
    F_{3X}
    \end{Bmatrix}
    $$

    带入求解即可
