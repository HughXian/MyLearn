1、向量的点乘$\quad \vec{a} \cdot \vec{b}$
	--向量的点乘可以判断两个向量是否 *同向* 或者 *反向*；假设向量$\vec{A}$为基本方向，有向量$\vec{B}$和向量$\vec{C}$；如果$\vec{B} \cdot \vec{A}$大于0，则表示向量$\vec{B}$与向量$\vec{A}$是同向；如果$\vec{C}  \cdot \vec{A}$小于0，则表示向量$\vec{C}$与向量$\vec{A}$是反向；
	--如果两个向量的点乘为0，则该两个向量垂直；
	--如果两个向量的点乘从1到0，表示两个向量的夹角越大；从0到-1，表示两个向量的夹角仍然增大，直到夹角最大为180°；

---
2、向量的叉乘$\quad \vec{a} \times \vec{b}$
	--右手螺旋定则（空我点赞手势）：
		假设有向量$\vec{A}$和$\vec{B}$，$\vec{A} \times \vec{B}$，向量$\vec{A}$旋转到向量$\vec{B}$为手掌的4指旋转方向（手掌的4指从$\vec{A}$的方向 向 $\vec{B}$的方向握紧），大拇指代表$\vec{A} \times \vec{B}$得出的向量$\vec{C}$，向量$\vec{C}$的方向为大拇指的方向；
	--应用：
		**如何判断两个向量的左右关系**：$\vec{A} \times \vec{B}$得到的结果和z轴同向，是正的，则向量$\vec{A}$在$\vec{B}$的右侧；
		**如何判断一个点是否在三角行内部（做光栅化，给三角形内部的像素着色需要用到）**：
		
> [!important] 如何判断一个点是否在三角行内部
>$\overrightarrow{AB} \times \overrightarrow{AP} > 0$ 说明p在ab的左侧；
>$\overrightarrow{BC} \times \overrightarrow{BP} > 0$ 说明p在bc的左侧；
>$\overrightarrow{CA} \times \overrightarrow{CP} > 0$ 说明p在ca的左侧；
>同时成立时（要么都>0，要么都<0），点 $P$ 在 $\triangle ABC$ 内部；
>$\overrightarrow{AB} \times \overrightarrow{AP} = 0$ 说明p在ab边上；
>$\overrightarrow{AB} \times \overrightarrow{AP} < 0$ 说明p在ab的右侧；

---
3、矩阵
	--矩阵是用()或[]包裹起来的几行几列的样式；
	--向量的点乘（Dot product）和叉乘（Cross product）可以用矩阵表示；	
	![[Pasted image 20260729104629.png]]

---
4、矩阵的乘积
	--**满足两个矩阵能够相乘的必要条件**：
		第一个矩阵是**列数** = 第二个矩阵的**行数**；
		假设有2个矩阵，$\boldsymbol{A}$：$m$ 行，$n$ 列，$\boldsymbol{B}$：$n$ 行，$p$ 列；矩阵$\boldsymbol{A}$ 乘 矩阵$\boldsymbol{B}$得出的矩阵$\boldsymbol{C}$为：$m$行，$p$列；
	--**新矩阵的$m$行$p$列的元素如何得出**：
		第一个矩阵的$m$行 和 第二个矩阵的$p$列 做点积运算；

---