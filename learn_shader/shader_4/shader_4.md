1、视图变换（View/Camera transformation）
	--就相当于相机，如何摆放相机的角度；
		1.决定相机的位置；
		2.决定相机看向的方向；
		3.决定相机头朝上的方向；

---
2、投影变换（Projection transformation）
	投影分为：==正交投影==（Orthographic projection）、==透视投影==（Perspective projection）；

---
3、正交投影
	--以下所有目的：为了得出正交投影的矩阵，以便运算；
	--首先，对于正交投影来说，视口是个$[l,r][b,t][f,n]$的长方体；
	要让该长方体变成$[-1,1]^3$的正方体（NDC，GPU裁剪器只识别**NDC**）：
		先将长方体的中心平移到坐标原点；
		再将长方体缩放到$[-1,1]^3$中；
> [!tip] 相机空间（采用右手系空间坐标，相机看向 $-Z$，$y$ 向上）
> l = left：视口左边界 $x$ 最小值；
> r = right：视口右边界 $x$ 最大值；
> b = bottom：视口下边界 $y$ 最小值；
> t = top：视口上边界 $y$ 最大值；
> n = near：近裁剪平面 $z$ 坐标（负数！相机在原点看向 $-Z$，近平面离相机更近，$n>f$）
> f = far：远裁剪平面 $z$ 坐标；
> 	--注意：$z$ 越小离相机越远；$n$，$f$ 都是负值，$0>n>f$ ；

---
	--先平移，再缩放：
> [!tip] ### 1. 平移矩阵
> 作用：把视盒中心点移动到坐标原点
> $$
> T=
> \begin{bmatrix}
> 1 & 0 & 0 & -\frac{r+l}{2} \\
> 0 & 1 & 0 & -\frac{t+b}{2} \\
> 0 & 0 & 1 & -\frac{f+n}{2} \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$
> 原理：x 方向中心 $\frac{l+r}{2}$，减去中心值，区间 $[l,r]$ 平移为 $\big[\frac{l-r}{2},\frac{r-l}{2}\big]$，中心归零；y、z 同理。

> [!tip] ### 2. 缩放矩阵
> 作用：将中心对齐原点的视盒缩放至区间 $[-1,1]^3$
> $$
> S=
> \begin{bmatrix}
> \frac{2}{r-l} & 0 & 0 & 0 \\
> 0 & \frac{2}{t-b} & 0 & 0 \\
> 0 & 0 & \frac{2}{f-n} & 0 \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$

> [!important] ### 3. 正交投影矩阵 $M_{ortho}=S\cdot T$
> 正交投影矩阵 = 缩放矩阵 乘以 平移矩阵（矩阵的运算是从右往左）
> $$
> M_{ortho}=
> \begin{bmatrix}
> \frac{2}{r-l} & 0 & 0 & -\frac{r+l}{r-l} \\
> 0 & \frac{2}{t-b} & 0 & -\frac{t+b}{t-b} \\
> 0 & 0 & \frac{2}{f-n} & -\frac{f+n}{f-n} \\
> 0 & 0 & 0 & 1
> \end{bmatrix}
> $$
> 
> > [!important] 关键适配提醒
> > 1. 该矩阵适配 **OpenGL 标准 NDC**：$x,y,z\in[-1,1]$；
> > 2. DirectX NDC z范围为 $[0,1]$，不能直接套用该矩阵，需要单独修改z轴表达式。

---
4、透视投影
	--以下的所有目的：为了得出透视投影的矩阵，以便运算；
	--有近大远小的现象；
	--先看下图：
		在Frustum这个几何体中，小的平面就是 屏幕，大的平面就是 FOV；
![[Pasted image 20260802112307.png]]

> [!tip] Frustum这样的锥体几何体 变为 Cuboid这样的方体
> 1. 因为这样可以得出 透视投影的矩阵；
> 2. 注意：GPU硬件（GPU裁剪器只能处理 方体（NDC））
>3. 具体操作： 将Frustum远平面及远平面到近平面之间的所有平面挤压到近平面大小，
变成Cuboid的样子，然后做一次正交投影

---
	--如何挤压（相似三角形）
![[Pasted image 20260802113037.png]]
![[Pasted image 20260802113119.png]]

---
> [!tip] 透视投影完整推导
> 
> 坐标系约定（右手坐标系）
> 相机看向 $-Z$；近裁剪面 $z=n$，远裁剪面 $z=f$；满足 $n,f < 0$。
> 
> 透视投影核心流程
> 透视视锥（Frustum）$\xrightarrow{M_{persp\rightarrow ortho}}$ 长方体正交视体 $\xrightarrow{M_{ortho}}$ NDC 标准立方体 $[-1,1]^{3}$
$$\boldsymbol{M_{persp}} = M_{ortho} \cdot M_{persp\rightarrow ortho}$$
> 两步策略：先挤压视锥为长方体，再做正交投影。
> ### 1. 相似三角形 —— 挤压规则
> 对任意空间点 $(x,y,z),\ z<0$，投影到近平面 $z=n$：
> $$x' = \frac{n}{z}x,\quad y' = \frac{n}{z}y$$
> 将投影点齐次化并同乘 $z$：
> $$
>     \begin{pmatrix} x' \\ y' \\ ? \\ 1 \end{pmatrix}
>     =
>     \begin{pmatrix} \dfrac{n}{z}x \\[4pt] \dfrac{n}{z}y \\[4pt] z' \\[4pt] 1 
>      \end{pmatrix}
>     \xrightarrow{\text{同乘}z}
>     \begin{pmatrix} nx \\ ny \\ z'z \\ z \end{pmatrix}
>     $$
> 得出透视挤压矩阵：
> $$
>    M_{persp\rightarrow ortho}
>    \begin{pmatrix}x\\y\\z\\1\end{pmatrix}
>     =
>     \begin{pmatrix}nx\\ny\\Az+B\\z\end{pmatrix}
>     $$
> 得到待定矩阵：
> $$
>     M_{persp\rightarrow ortho}=
>     \begin{bmatrix}
>     n & 0 & 0 & 0\\
>     0 & n & 0 & 0\\
>     0 & 0 & A & B\\
>     0 & 0 & 1 & 0
>     \end{bmatrix}
>     $$
> ### 2. 边界约束求解 $A,B$
> 约束 1：近平面 $z=n$ 坐标不变
> 代入点 $(x,y,n,1)^T$：
> $$
>     \begin{bmatrix}
>     n & 0 & 0 & 0\\
>     0 & n & 0 & 0\\
>     0 & 0 & A & B\\
>     0 & 0 & 1 & 0
>     \end{bmatrix}
>     \begin{pmatrix}x\\y\\n\\1\end{pmatrix}
>     =
>     \begin{pmatrix}nx\\ny\\An+B\\n\end{pmatrix}
>     $$
> 齐次除法后 Z 坐标必须等于 $n$：
> $$\frac{An+B}{n}=n \implies An+B=n^2 \tag{1}$$
> 约束 2：远平面中心 $z=f$ 坐标不变
> 代入中心点 $(0,0,f,1)^T$：
> $$
>     \begin{bmatrix}
>     n & 0 & 0 & 0\\
>     0 & n & 0 & 0\\
>     0 & 0 & A & B\\
>     0 & 0 & 1 & 0
>     \end{bmatrix}
>     \begin{pmatrix}0\\0\\f\\1\end{pmatrix}
>     =
>     \begin{pmatrix}0\\0\\Af+B\\f\end{pmatrix}
>     $$
> 齐次除法后 Z 坐标必须等于 $f$：
> $$\frac{Af+B}{f}=f \implies Af+B=f^2 \tag{2}$$
> ### 3. 方程组求解结果
> 联立：
> $$
>     \begin{cases}
>     An+B = n^2 \\
>     Af+B = f^2
>     \end{cases}
>     $$
> 解得：
> $$A = n+f,\quad B = -nf$$
> ### 4. 最终透视挤压矩阵
> $$
>     M_{persp\rightarrow ortho}=
>     \begin{bmatrix}
>     n & 0 & 0 & 0\\
>     0 & n & 0 & 0\\
>     0 & 0 & n+f & -nf\\
>     0 & 0 & 1 & 0
>     \end{bmatrix}
>     $$
> 完整==透视投影==矩阵关系
> $$M_{persp} = M_{ortho} \cdot M_{persp\rightarrow ortho}$$
> $$ M_{persp}= \begin{bmatrix} \dfrac{2n}{r-l} & 0 & -\dfrac{r+l}{r-l} & 0\\[4pt] 0 & \dfrac{2n}{t-b} & -\dfrac{t+b}{t-b} & 0\\[4pt] 0 & 0 & \dfrac{n+f}{n-f} & -\dfrac{2nf}{n-f}\\[4pt] 0 & 0 & 1 & 0 \end{bmatrix} $$

---
