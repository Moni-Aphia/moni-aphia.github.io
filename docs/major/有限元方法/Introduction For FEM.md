

工程问题一般都能化成求解如下方程：

$$
\begin{align}
\Delta u(x,y) &= f(x,y) \quad \mathrm{ in\,\Omega \subset \mathbb{R}^{2}}\\ 
u(x,y) &= 0 \quad \mathrm{ on \,\partial \Omega}
\end{align}
$$

这个方程的解一般是函数 $u(x,y)$ ，“知道”一个函数意味着我们知道其在无限点 $(x,y)$ 对应的值，但并不一定总是能给出其所对应的解析表达式，此时会采用数值解。但是，计算机一般只能存储有限个数值解，计算过程也只是有限的。因此，我们不能真正求解一个 PDE ，我们所能做的其实只是近似这个解。

所以这就带来了两个问题： 

- 如何在有限数据和计算的情况下近似这个解 
- 如何在不求出解析解的情况下近似PDE的解 
 
常用的近似函数有：
 
- 傅里叶级数（Fourier series）
- 全局多项式（global polynomial） 
- 在定义域划分的网格上定义的局部多项式

傅里叶级数方法在 $u$ 是全局光滑近似良好，而第二种办法一般只适用于部分邻域。有限元方法用的就是第三种方法。

!!! note "[斯通-魏尔施特拉斯逼近定理(Stone-Weierstrass theorem)](https://en.wikipedia.org/wiki/Stone%E2%80%93Weierstrass_theorem)"

    闭区间上的连续函数可用多项式级数一致逼近。

PDEs 的解有如下特点：

- 在定义域上的大部分地方都是光滑的 
- 在定义域上的小部分地方变化非常迅速 
- 边界奇异点 
- 也许有褶皱（May have kinks）


有限元方法（FEM）或有限元分析（FEA）基于用简单的网格构建复杂对象或者将复杂对象划分为易于分析的网格。其过程可以简述为：将物理域（定义域）划分称若干个简单的元素，找到每个元素对应的方程，最后将这些元素方程整合起来，得到：

$$
\underset{ \mathrm{Property} }{ \mathbf{K} }\underset{ \mathrm{Behavior} }{ \mathbf{U} } = \underset{ \mathrm{Action} }{ \mathbf{F} }
$$

有限元方法步骤可以总结如下： 

- 离散并选择元素类型 (Discretize and select the element type)
- 选定位移函数 (Select a displacement function)
- 定义物理关系（应力-应变、位移-应变）$\varepsilon =\nabla u \quad \sigma = D\varepsilon$ (Define the strain/displacement and stress/strain relationships)
- 获取元素的刚度矩阵和其对应的等式 $k_{e}u_{e}=f_{e}$ (Derive the element stiffness matrix and equations) 
- 组合元素方程以获得全局方程，并引入边界条件 (Assemble the element equations to obtain the global equations and introduce boundary conditions)
- 求解自由度 (Solve for the unknown degrees of freedom)
- 求解元素应力应变(Solve for the element strains and stresses)
- 后处理(Interpret the results)

获取元素的刚度矩阵和等式有如下方法 

- 直接法：我们可以直接计算这个等式，但是这种方法比较适用于 1 维元素 
- 能量法：比如说能量最低原理、卡氏定理（适用于弹性材料）虚功原理（适用于各种材 料） 
- 加权残差法：如伽辽金方法，适用于各种微分方程

有限元方法的误差(error)有 

- 离散误差(Discretization error)；比如说定义域的结构被简化从而会产生一定的误差。 
- 模型误差(Modeling error)；比如说我们采用多项式去逼近。 
- 数值误差(Numerical error)；比如说我们采用比较简单的积分方法会产生一定的误差