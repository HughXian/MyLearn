1、变换（Transformation）
2、缩放变换
	使用缩放矩阵对图片进行缩放；
	--对图片做镜像处理：本质上也是缩放变换；

---
3、错切变换（剪切变换）
	使用错切矩阵（shear matrix）;
	--剪切变换不会改变前后的几何体面积；
> [!tip] 三维剪切矩阵 Shear Matrix（4×4，OpenGL 列向量规范）
> 三维一共有 **6 种基础剪切系数**
> - $s_{xy}$：$y$ 影响 $x$；$s_{xz}$：$z$ 影响 $x$
> - $s_{yx}$：$x$ 影响 $y$；$s_{yz}$：$z$ 影响 $y$
> - $s_{zx}$：$x$ 影响 $z$；$s_{zy}$：$y$ 影响 $z$
>
> ### 1. X方向剪切（y、z 共同影响 x）
> $$
> S_x(s_{xy},s_{xz})=
> \begin{bmatrix}
> 1 & s_{xy} & s_{xz} & 0 \\
> 0 & 1 & 0 & 0 \\
> 0 & 0 & 1 & 0 \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$
> 变换方程组：
> $$
> \begin{cases}
> x' = x + s_{xy}\cdot y + s_{xz}\cdot z \\
> y' = y \\
> z' = z
> \end{cases}
> $$
>
> ### 2. Y方向剪切（x、z 共同影响 y）
> $$
> S_y(s_{yx},s_{yz})=
> \begin{bmatrix}
> 1 & 0 & 0 & 0 \\
> s_{yx} & 1 & s_{yz} & 0 \\
> 0 & 0 & 1 & 0 \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$
>
> ### 3. Z方向剪切（x、y 共同影响 z）
> $$
> S_z(s_{zx},s_{zy})=
> \begin{bmatrix}
> 1 & 0 & 0 & 0 \\
> 0 & 1 & 0 & 0 \\
> s_{zx} & s_{zy} & 1 & 0 \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$
>
> > [!tip]
> > 剪切变换不改变几何体体积；
> > DirectX 使用行向量，对应矩阵为本矩阵的**转置**。

---
4、旋转变换
	使用旋转矩阵（Rotation Matrix）；
	--根据下图：推断旋转角为$\theta$；
	--旋转前的$\boldsymbol{x}$轴为$\cos \theta$，  $\boldsymbol{y}$轴为$\sin \theta$；
	--旋转后的$\boldsymbol{x}$轴为$-\sin \theta$，$\boldsymbol{y}$轴为$\cos \theta$；
![[Pasted image 20260730110834.png]]

---
5、平移变换与齐次坐标
	--因为平移变换的特殊性，从而出现了==齐次坐标==；
> [!tip] 平移不属于线性变换
> 在没有齐次坐标的情况下，平移变换是这样计算：
> $$
> \begin{bmatrix}x'\\y'\end{bmatrix}=
> \begin{bmatrix}a & b\\c & d\end{bmatrix}
> \begin{bmatrix}x\\y\end{bmatrix}
> +
> \begin{bmatrix}t_x\\t_y\end{bmatrix}
> $$

---
	--引入齐次坐标，目的：*为了把所有的变换给写成一个矩阵乘以一个向量的形式*；
> [!tip] 二维齐次坐标（Homogeneous Coordinate）
> 增加第三维 $w$ 坐标，区分**点**与**向量**：
> - 二维点：$(x,\ y,\ \boldsymbol{1})^\mathrm{T}$
> - 二维向量：$(x,\ y,\ \boldsymbol{0})^\mathrm{T}$
>
> ### 平移变换矩阵（3×3）
> $$
> \begin{bmatrix} x' \\ y' \\ w' \end{bmatrix}=
> \begin{bmatrix}
> 1 & 0 & t_x \\
> 0 & 1 & t_y \\
> 0 & 0 & 1
> \end{bmatrix}
> \cdot
> \begin{bmatrix} x \\ y \\ 1 \end{bmatrix}=
> \begin{bmatrix} x+t_x \\ y+t_y \\ 1 \end{bmatrix}
> $$
>
> > [!important] 关键性质
> > 1. 点的 $w=1$，乘平移矩阵会产生位移；
> > 2. 向量的 $w=0$，代入平移矩阵后平移项失效：向量不受平移影响。
> > 3. 对于2维的点或向量来看，齐次坐标是3维的；
> > 4. 对于3维的点或向量来看，齐次坐标是4维的；

---
	--在齐次坐标下，$\boldsymbol{w}$决定了 点或向量的类型：$\boldsymbol{w}$为0是向量，$\boldsymbol{w}$为1是点；
	--在齐次坐标下，有4种计算：
> [!tip] 齐次坐标运算：向量 + 向量
> $\text{vector} + \text{vector} = \text{vector}$
>
> 二维向量齐次表达：$(x,y,0)$
> $$
> (x_1,y_1,0)+(x_2,y_2,0)=(x_1+x_2,\ y_1+y_2,\ 0)
> $$

> [!tip] 齐次坐标运算：点 − 点 = 向量
> $\text{point} - \text{point} = \text{vector}$
>
> 二维点齐次表达：$(x,y,1)$
> $$
> (x_1,y_1,1)-(x_2,y_2,1)=(x_1-x_2,\ y_1-y_2,\ 0)
> $$

> [!tip] 齐次坐标运算：点 + 向量 = 点
> $\text{point} + \text{vector} = \text{point}$
>
> $$
> (x_p,y_p,1)+(x_v,y_v,0)=(x_p+x_v,\ y_p+y_v,\ 1)
> $$
> 相当于点在平移；

> [!warning] 齐次坐标运算：点 + 点（无原生标准定义）
> $\text{point} + \text{point}$
>
> 二维点齐次表达：$(x,y,1)$
> $$
> (x_1,y_1,1)+(x_2,y_2,1)=(x_1+x_2,\ y_1+y_2,\ 2)
> $$
> > [!info] 解析
> > 相加结果 $w=2$，既不等于0也不等于1，不属于标准向量/点形式。
> > 根据齐次坐标归一化规则 $\begin{pmatrix}x\\y\\w\end{pmatrix} \implies \begin{pmatrix}\dfrac{x}{w}\\[4pt]\dfrac{y}{w}\\[4pt]1\end{pmatrix},\ w\neq0$
> > $$
> > (x_1+x_2,\ y_1+y_2,\ 2) \implies \left(\frac{x_1+x_2}{2},\ \frac{y_1+y_2}{2},\ 1\right)
> > $$
> > 归一化后几何意义：*两点连线中点*。
> > 注意：基础向量几何中**点 + 点不存在原生运算定义**，中点是齐次归一化衍生出的解释，不能直接当作通用规则使用。

---
	--归类：用了齐次坐标后的变换公式样式；
		$\boldsymbol{a}$、$\boldsymbol{b}$、$\boldsymbol{c}$、$\boldsymbol{d}$ 代表缩放、剪切、旋转；
		$\boldsymbol{t_x}$、$\boldsymbol{t_y}$ 代表平移；
		$0$、$0$、$1$是固定不变的；
![[Pasted image 20260730153849.png]]

---
6、3维变换和齐次坐标	
	--和2维的齐次坐标同理；
![[Pasted image 20260730160350.png]]
	--下图是：3维变换中的缩放和平移；
![[Pasted image 20260731152023.png]]
	下图是：3维的几何物体绕着某个轴旋转；比如$R_x(a)$是绕着X轴旋转；
![[Pasted image 20260731152439.png]]
