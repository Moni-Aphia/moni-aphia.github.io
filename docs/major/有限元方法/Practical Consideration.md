长宽比定义为四边形单元最长尺度与最短尺度之比，在多数情况下，有限元方法给出的解的精度会随着长宽比的增大而降低，因此，为元素选择一个好的形状是至关重要的。对于一个四个节点的元素，我们要避免选择以下形状：

1. 有很大的长宽比
2. 形状接近三角形
3. 内角相差过大
4. 退化成为三角形的四边形

---
如果在几何、载荷或材料性质中发生了突变，需要考虑为突变的地方做一个（或多个）节点。

有时候，一个模型需要混合不同类型的单元，比如梁和平面单元。混合这些单元的问题在于每一个节点有不同的自由度，梁在每一个节点允许横向位移和旋转，但是对于平面单元，其只允许面内位移。梁可以承受一个节点处的集中力矩，而平面单元则不能，因此如果一个梁单元通过一个节点连接至平面单元，此时只能通过此点在不同类型单元之间传递力，这使得刚度矩阵为奇异矩阵。这种问题需要通过添加一个或多个梁单元，将梁拓展到平面单元解决。

---
适当运用对称性能加速问题的模拟，但在振动和屈曲问题中使用对称性要小心，因为并不是所有的振动模型或屈曲模型都对称。

---
精力凝聚（Static Condensation）是四边形单元经常采用的一种方法，一般而言，我们会在四边形对角线相交处临时引进一个想象的节点 $5$ 以生成四个三角形，然后叠加这四个三角形创建四边形单元的刚度矩阵，那么此时我们所想象的内部节点其实是被凝聚掉了，并不会出现在最后的方程中，因此，只有与实际外部焦点相关的自由度进入方程。简而言之，通过消除内部（局部）自由度，将系统方程的维度降低。

考虑平衡方程：

$$
\begin{bmatrix}
k_{11}  & k_{12} \\
k_{21} & k_{22}
\end{bmatrix}\begin{Bmatrix}
d_{a} \\
d_{i}
\end{Bmatrix}=\begin{Bmatrix}
F_{a} \\
F_{i}
\end{Bmatrix}
$$

假定 $d_{i}$ 与 $F_{i}$ 是我们人为想象的内部节点对应的内部位移和荷载， $d_{a}$ 与 $F_{a}$ 是实际节点自由度和荷载。求解可得：

$$
\left\{ d_{i}  \right\} = -[k_{22}]^{-1}[k_{21}]\left\{ d_{a} \right\} +[k_{22}]^{-1}\left\{ F_{i} \right\} 
$$

回代可得：

$$
[k_{11}]-[k_{12}][k_{22}]^{-1}[k_{21}] \left\{ d_{a} \right\} =  \left\{ F_{a} \right\}-[k_{12}][k_{22}]^{-1}\left\{ F_{i} \right\}   \triangleq \boxed{ [k_{c}]\left\{ d_{a} \right\} =\left\{ F_{c} \right\}  }
$$

这个新的方程组维度较小，仅涉及边界自由度，求解效率更高。

---
**子结构法（Substructuring）** 是一种将大型结构划分为多个（自然的）组成部分进行分析的过程。  这些组成部分的有限元模型被称为**子结构（substructures）** 或 **超单元（superelements, SE）** 。

也就是说将原始结构划分成多个子结构，每个子结构单独建立有限元模型，在主结构分析中只用保留边界节点，从而极大降低了自由度。

---

方程解法：

- 直接法：
	- 复杂度为 $\mathcal{O}(NB^{2}),N$ 为矩阵维度 $B$ 为方程条数
	- 适合中小问题，或者比较薄的结构
	- 容易处理复杂载荷情况
- 迭代法：
	- 解的复杂度不知
	- 减少存储需求
	- 适合大型问题或者大型矩阵
	- 需要根据不同载荷重新求解
---
有限元解的特点：

 - FE Model – A mathematical model of the real structure, based on many approximations. 
 - Real Structure – Infinite number of nodes (physical points or particles), thus infinite number of DOF’s. 
 - FE Model – finite number of nodes, thus finite number of DOF’s

根据给定有限节点的位移约束位移场。

有限元模型比真实结构更为严苛，给出的位移一般偏小。实际上有限元模型给出了真实解的下限

---
Convergence: As the mesh in an FE model is “refined” repeatedly, the FE solution will converge to the exact solution of the mathematical model of the problem (the model based on bar, beam, plane stress/strain, plate, shell, or 3-D elasticity theories or assumptions)

- 网格加密
	- h-加密：减小单元尺寸（网格内部划分网格）
	- p-加密：提高单元上多项式阶数（网格边上加密节点）
	- r-加密：重新排列网格中的节点（局部加密）
	- hp-加密：结合 h-加密与 p-加密
