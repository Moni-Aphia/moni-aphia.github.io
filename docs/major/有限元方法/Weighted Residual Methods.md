## Weighted Residual Methods

微分方程的解要求处处满足物理方程和边界条件，但实际上这是一个非常严苛的要求，或者说解的强形式。实际上，找到一个满足上述要求的微分方程的解并不是一件容易的事情，因此，与其找到一个强解，不如找到一个满足如下条件的弱解：

$$
\int_{V} \left[ \sigma(\tilde{u}_{1},\tilde{u}_{2},\tilde{u}_{3})_{ij,j}+f_{i} \right] \mathrm{d}V =0
$$

$\tilde{u}_{k}$  为近似解。将积分内部的项称为残差（residual），此时放松了解的条件：

$$
R_{i} \neq 0 \quad \int_{V}R_{i} \mathrm{d}V=0
$$

如果希望进一步对残差有所限制，则引入权函数 $W$ ，要求

$$
\int_{V}WR \mathrm{d} V =0
$$

加权残差法有如下方法：

- Collocation 要求网格上每一个点 $R =0$ （实际上权函数为狄拉克函数）
- Subdomain $\int_{V}R\mathrm{d}V =0$
- Least Squares $\int_{V}R^{2} \mathrm{d}V=0$
- Galerkin $\int_{V}N_{i}R \mathrm{d}V =0$

考虑一个微分方程：

$$
\frac{{\rm{d}}  ^{2}u }{{\rm {d}} x^{2} } +u +x=0
$$

边界条件：

$$
u(0) = 0\quad u(1) =0 
$$

对应准确解：

$$
u = \frac{\sin x}{\sin 1}-x
$$

取一个满足边界条件的近似解：

$$
\bar{u} =x(1-x)(a_{1}+a_{2}x+a_{3}x^{2}+\dots)
$$

- 一阶近似：

$$
\bar{u} = a_{1}x(1-x)
$$

将近似解带入微分方程中可得残差

$$
R_{1} =x+a_{1}(-2+x-x^{2})
$$

- 二阶近似：

$$
\bar{u}=x(1-x)(a_{1}+a_{2}x)
$$

对应残差：

$$
R_{2} = x+a_{1}(-2+x-x^{2})+a_{2}(2-6x+x^{2}-x^{3})
$$

Collocation 方法要求选定节点 $R(x_{i}) =0$ ，对于一阶近似取中点 $x= 1/2$ ，可得 $a_{1} = \frac{2}{7}$ ；对于二阶近似，取三等分点 $x=1 /3 \quad x=2 /3$ 可得 $a_{1} =0.1948,a_{2}=0.1731$

对于 Galerkin 方法，先考虑一阶近似，由于近似解本身是给定基函数的线性组合 $\bar{u}=\sum N_{i}a_{i}$ ，所以取

$$
W_{1} = N_{1} =x(1-x)
$$

积分得

$$
\int_{0}^{1} W_{1} R\mathrm{d}x=\int^{1}_{0}x(1-x)(x+a_{1}(-2+x-x^{2})) = 0 \implies a_{1} = \frac{5}{18}
$$

再考虑二阶近似，取第二个权函数：

$$
W_{2} = x^{2}(1-x)
$$

分别积分可得近似解：

$$
\bar{u} = x(1-x )(0.1924+0.1707x)
$$

## Galerkin's Method 
用 Galerkin 方法求出刚度矩阵和待定方程

!!! note "Steps in Applying Galerkin’s Method" 

    1. 定出控制偏微分方程（PDE） 和边界条件 (BCs)
    2. 定出残差方程
    3. 计算分步积分
    4. 将插值函数带入残差方程
    5. 计算单元积分获得代数有限元形式方程



![alt text](<Source/Pasted image 20250607214148.png>)

考虑一根杆（只受横向作用 $f_{i},f_{j}$ ），其对应的微分方程满足：

$$
\frac{{\rm{d}}   }{{\rm {d}} x }\left( A(x)E(x)\frac{{\rm{d}} u  }{{\rm {d}}x  }  \right)   =0
$$

根据 Galekin 法，残差积分满足：

$$
\int_{0}^{L}\frac{{\rm{d}}   }{{\rm {d}} x }\left( A(x)E(x)\frac{{\rm{d}} u  }{{\rm {d}}x  }  \right) N_{i}\mathrm{d}x = 0
$$

分步积分可得：

$$
\left( N_{i}AE \frac{{\rm{d}}  u }{{\rm {d}} x } \right)\Bigg|^{L}_{0}-\int_{0}^{L} A(x)E(x)\frac{{\rm{d}}  u }{{\rm {d}} x } \frac{{\rm{d}}  N_{i} }{{\rm {d}} x } \mathrm{d}x =0
$$

分步积分的结果引入了边界条件。

杆单元中，$\frac{{\rm{d}}  u }{{\rm {d}} x }$ 可以写作：

$$
\frac{{\rm{d}}  u }{{\rm {d}}  x }=\begin{bmatrix}
-\frac{1}{L} & \frac{1}{L}
\end{bmatrix} \begin{Bmatrix}
u_{i} \\
u_{j}
\end{Bmatrix}
$$

带入分步积分可得（考虑横截面积和杨氏模量为常数）：

$$
AE\int_{0}^{L}\frac{{\rm{d}}  N_{i} }{{\rm {d}} x } \begin{bmatrix}
-\frac{1}{L} & \frac{1}{L}
\end{bmatrix}\mathrm{d}x \begin{Bmatrix}
u_{i} \\
u_{j}
\end{Bmatrix}=\left( N_{i}AE \frac{{\rm{d}}  u }{{\rm {d}} x } \right)\Bigg|^{L}_{0}
$$

因为在杆问题中，只有两个形函数，所以上面给出两个等式，取第一个形函数 $N_{1}$ 给出

$$
f_{i} = \frac{AE}{L}(u_{i}-u_{j})
$$

取第二个形函数，可得

$$
f_{j} = \frac{AE}{L}(u_{j}-u_{i})
$$

装配可得：

$$
\frac{AE}{L}\begin{bmatrix}
1 & -1 \\
-1 & 1
\end{bmatrix}\begin{Bmatrix}
u_{i} \\
u_{j}
\end{Bmatrix} = \left\{ \begin{align}
f_{i} \\
f_{j}
\end{align} \right\} 
$$

与直接法和能量法得到的方程一样。

![alt text](<Source/Pasted image 20250607214745.png>)

控制方程：

$$
EI\frac{{\rm{d}}  v^{4} }{{\rm {d}} x^{4} } +w =0
$$

伽辽金方法给出：

$$
\int_{0}^{L}\left( EI \frac{{\rm{d}}  ^{4}v }{{\rm {d}} x^{4} }+w  \right)N_{i}\mathrm{d}x =0\quad (i=1,2,3,4)
$$

对第一项积分做两次分步积分

$$
\int_{0}^{L}\left( EI \frac{{\rm{d}}  ^{4}v }{{\rm {d}} x^{4} }  \right)N_{i}\mathrm{dx=} \int_{0}^{L}EI \frac{{\rm{d}}  ^{2}v }{{\rm {d}} x^{2} } \frac{{\rm{d}}  ^{2}N_{i} }{{\rm {d}} x^{2} }  \mathrm{d}x+EI \left( N_{i}\frac{{\rm{d}}  ^{3}v }{{\rm {d}} x^{3} } -\frac{{\rm{d}}  N_{i} }{{\rm {d}} x } \frac{{\rm{d}}  ^{2}v }{{\rm {d}} x^{2} }  \right)\Bigg|^{L}_{0}
$$

对位移插值函数做微分

$$
\frac{{\rm{d}}  ^{2} v}{{\rm {d}} x^{2} }  = \begin{bmatrix}
\frac{12x-6L}{L^{3}} & \frac{6xL-4L^{2}}{L^{3}}  &  \frac{-12x+6L}{L^{3}} & \frac{6xL-2L^{2}}{L^{3}}
\end{bmatrix}\left\{ d \right\} =[B]\left\{ d \right\} 
$$

同时有：

$$
M(x) = EI\frac{{\rm{d}}  ^{2}v }{{\rm {d}} x^{2} }\quad F(x) = EI \frac{{\rm{d}} ^{3}v }{{\rm {d}} x^{3} } 
$$

带入可得

$$
\begin{align}
&\int_{0}^{L}\left( EI \frac{{\rm{d}}  ^{4}v }{{\rm {d}} x^{4} }+w  \right)N_{i}\mathrm{d}x  \\
=&\int_{0}^{L}\frac{{\rm{d}}  N_{i}^{2} }{{\rm {d}} x^{2} }EI [B]\mathrm{d}x\left\{ d \right\}  +\int_{0}^{L}N_{i}w\mathrm{d}x+\left[ N_{i}F(x)-\frac{{\rm{d}}  N_{i} }{{\rm {d}} x }M(x)  \right]\Bigg|^{L}_{0}  =0
\end{align} 
$$

将形函数带入就可以导出梁元素满足的方程：

$$
[K]\left\{ d \right\} = \begin{bmatrix}
F_{i} & M_{i} & F_{j} & M_{j}
\end{bmatrix} ^{T} -\int_{0}^{L}[N]^{T}w \mathrm{d}x
$$

其中刚度矩阵由如下式子导出：

$$
[K] = \int_{0}^{L}[B]^{T}EI[B]\mathrm{d}x = \frac{EI}{L^{3}}\begin{bmatrix}
12 & 6L & -12 & 6L \\
6L & 4L^{2} & -6L & 2L^{2} \\
-12 & -6L & 12 & -6L \\
6L & 2L^{2} & -6L & 4L^{2}
\end{bmatrix}
$$
