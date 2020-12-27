# 数学模式

Latex使用一种特有的模式来排版数学（mathematics）公式。数学公式允许嵌入到文本段中，称为行内公式（inline），使用 `$...$` 标记。也可以将公式独立成为一个段落，称为行间公式（display），使用`$$...$$`标记。

下面我们写一个公式：

```latex
\begin{equation}
  \int_0^\infty \frac{x^3}{e^x-1}\,dx = \frac{\pi^4}{15}
\end{equation}
```

$$
\begin{equation}
  \int_0^\infty \frac{x^3}{e^x-1}\,dx = \frac{\pi^4}{15}
\end{equation}
$$

下面我们[总结][1]常用公式与符号↓

# 数学公式

我们总结一下数学中常用的公式。

## 空格

在数学公式中，我们需要用空格来保证公式中符号之间的间距。Latex能够自动处理大多数的空格，但有时候还是需要自己来控制。

`\quad`表示空格，密度为$m$。写法 `\alpha \quad\beta`：$\alpha \quad\beta$ 。

`\qquad`表示两个`\quad`，密度为$2m$。写法 `\alpha \qquad\beta`：$\alpha \quad\beta$。

大空格的密度是$\frac{m}{3}$。写法 `\alpha\ \beta`：$\alpha\ \beta$。

中等空格的密度是$\frac{2m}{7}$。写法 `\alpha\;\beta`：$\alpha\;\beta$。

小空格的密度是$\frac{m}{6}$。写法 `\alpha\,\beta`：$\alpha\,\beta$。

紧凑的密度是$-\frac{m}{6}$。写法 `\alpha\!\beta`：$\alpha\!\beta$。

## 上标 & 下标 

上标使用 `^` 符号标注，写法 `e^3`：$e^3$。如果指数超过一位或是一个式子，则使用 `{}` 包裹，写法 `e^{3^x+2x}` ：$e^{3^x+2x}$。
下标使用 `_` 符号标注，写法：`e_3` ：$e_3$，如果下标也超过一位或是一个式子，也使用 `{}` 包裹，写法：`e_{2x-2^y}` ：$e_{2x-2^y}$。

两者都存在时写法 `x_1^2` ：$x_1^2$。

## 分数

分数符号使用 `\frac{}{}` 表示。也可以使用 `分子 \over 分母` 命令来实现。

正常书写 `\frac{x+2}{3y-5} = a` ： $\frac{x+2}{3y-5} = a$。

小型分数 `\tfrac{x+2}{3y-5} = a` ：$\tfrac{x+2}{3y-5} = a$。

嵌套的大型分式 `\cfrac{x+2}{c + \cfrac{x}{d + \cfrac{2}{b}}} = a` ：$\cfrac{x+2}{c + \cfrac{x}{d + \cfrac{2}{b}}} = a$。

上述的大型分数可以使用紧缩记法：

```latex
\frac{x+2}{c+}
\frac{x}{d+}
\frac{2}{b} = a
```

效果：

$$
\frac{x+2}{c+}
\frac{x}{d+}
\frac{2}{b} = a
$$

不嵌套的大型分式 `\dfrac{2}{x} = 0.5 \qquad \dfrac{x+2}{c + \dfrac{x}{d + \dfrac{2}{b}}} = a`：$\dfrac{2}{x} = 0.5 \qquad \dfrac{x+2}{c + \dfrac{x}{d + \dfrac{2}{b}}} = a$。

## 平方根

使用 `\sqrt` 代表开方符号，有两种写法：

- 开平方根语法 `\sqrt{}`: $\sqrt{2}$
- 开任意次方根语法 `\sqrt[]{}` ：$\sqrt[3x]{2^e+7y}$

花括号 `{}` 代表要开方的数，方括号 `[]` 为要填入要开的方。

## 求和 & 平均值

使用 `\sum`：$\sum$ 表示求和。例子：`$\sum_{i=1}^{n}a_i$` ：$\sum_{i=1}^{n}a_i$

平均值：`$\bar{x}=\sum X/n$`，效果：$\bar{x}=\sum X/n$。

## 水平线

在 Latex 中，命令 `\overline` 和 `\underline` 表达式的上下方画出水平线

|上划线|`\overline{123}`：$\overline{123}$|
|:---:|:---:|
|下划线|`\underline{123}`：$\underline{123}$|

<br/>

## 删除线

在公式内使用 `\require{cancel}` 来显示片段删除线。  
声明片段删除线后，使用 `\cancel{...}`、`\bcancel{...}`、`\xcancel{...}` 和 `\cancelto{...}` 来实现各种片段删除线的效果。

```latex
\require{case}\begin{array}{rl}
\verb|y+\cancel{x}| & y+\cancel{x} \\
\verb|\cancel{y+x}| & \cancel{y+x} \\
\verb|y+\bcabcel{x}| & y+\bcancel{x} \\
\verb|y+\xcancel{x}| & y+\xcancel{x} \\
\verb|y+\cancelto{0}{x}| & y+\cancelto{0}{x} \\
\verb+\frac{1\cancel9}{\cancel95} = \frac15+& \frac{1\cancel9}{\cancel95} = \frac15 \\
\end{array}
```

效果：

$$
\require{case}\begin{array}{rl}
\verb|y+\cancel{x}| & y+\cancel{x} \\
\verb|\cancel{y+x}| & \cancel{y+x} \\
\verb|y+\bcabcel{x}| & y+\bcancel{x} \\
\verb|y+\xcancel{x}| & y+\xcancel{x} \\
\verb|y+\cancelto{0}{x}| & y+\cancelto{0}{x} \\
\verb+\frac{1\cancel9}{\cancel95} = \frac15+& \frac{1\cancel9}{\cancel95} = \frac15 \\
\end{array}
$$

## 乘积

使用 `\prod`：$\prod$ 表示乘积。例子：

`\prod_{i=1}^n x_i`：$\prod_{i=1}^n x_i$

## 对数

对数有三种表示形式：

- `\log_{}{}`表现形式。写法：`$x=\log_{2}{N}$`，效果$x=\log_{2}{N}$。读作`数x叫做以2为底N的对数`。
- `\lg_{}`表现形式。写法：`$x=\lg{N}$`，写可写作：`$x=\log_{10}{N}$`，效果：$x=\lg{N}$。读作`数x叫做以10为底N的对数`。
- `\ln_{}`表现形式。写法：`$x=\ln{N}$`，效果：$x=\ln{N}$。读作`数x叫做以无理数(e=2.71828...)为底N的自然对数`。

## 向量

在 Latex 中，我们可以使用命令 `\vec` 获取向量表示。此外，令两个命令 `\overleftarrow` 和 `\overrightarrow` 在定义从 $A$ 到 $B$ 的向量时非常有用

向量符号 `\vec{x}`：$\vec{x}$。  
多符号矢量符号 `\overrightarrow{xy}`：$\overrightarrow{xy}$。和 `\overleftarrow{xy}`：$\overleftarrow{xy}$。

## 三角函数

三角形符号的语法 `\Delta` ：$\Delta$。  
表示角的语法  `\angle A`：$\angle A$。读作角A  
正弦的语法 `\sin \theta`：$\sin \theta$。  
余弦的语法 `\cos \theta`：$\cos \theta$。  
正切的语法 `\tan \theta`：$\tan \theta$。  
余切的语法 `\cot \theta`：$\cot \theta$。  
正割的语法 `\csc \theta`：$\csc \theta$。  
余割的语法 `\sec \theta`：$\csc \theta$。  
角度的语法为 `$\sin 30^\circ$`：$\sin 30^\circ$。

## 极限

使用 `\lim` 表示极限符号，而将极限的下标放在极限符号的正下方则使用 `\limits` 命令。最终实现的极限公式：`\lim\limits_{x \to 0}x_n`:$\lim\limits_{x \to 0}x_n$。

## 微积分公式

积分符号 `\int`：$\int$。例子：  
`\int_{-N}^{N} e^x\,{d}x`：$\int_{-N}^{N} e^x\,{d}x$。  
`\begin{matrix} \int_{-N}^{N} e^x\, {d}x\end{matrix}`：$\begin{matrix} \int_{-N}^{N} e^x\, {d}x\end{matrix}$  
双重积分符号 `\iint`：$\iint$。例子：  
`\iint_{D}^{W} \, {d}x\, {d}y`：$\iint_{D}^{W} \, {d}x\, {d}y$。  
三重积分符号 `\iiint`：$\iiint$。例子：`\iiint_{E}^{V} \, dx\, dy\, dz`：$\iiiint_{F}^{U} \, dx\, dy\, dz\, dt$。  
四重积分符号 `\iiiint`：$\iiiint$。例子：`\iiiint_{F}^{U} \, dx\, dy\, dz\, dt`：$\iiiint_{F}^{U} \, dx\, dy\, dz\, dt$。  
闭合的曲线，曲面积分符号 `\oint`：$\oint$。例子：`\oint_{C} x^3\,dx + 4y^2\,dy`：$\oint_{C} x^3\,dx + 4y^2\,dy$。

求微分 `\mathrm{d}x`：$\mathrm{d}x$。  
求偏微分 `\partial x`，效果：$\partial x$。  
求一阶微分 `\dot x` ：$\dot x$。  
求二阶微分 `\ddot y`：$\ddot y$。

`f^\prime` 表示导数。效果：$f^\prime(x)$。

## 条件定义

条件表达式使用 `\begin{cases}...\end{cases}` 来创建一组条件表达式。而使用 `\text{...}` 来添加注释文字。

```latex
$$
f(n) = 
\begin{cases}
\frac{n}{2}, & \text{if $n$ is even} \\
3n+1, & \text{if $n$ is odd}
\end{cases}
$$
```

效果：

$$
f(n) = 
\begin{cases}
\frac{n}{2}, & \text{if $n$ is even} \\
3n+1, & \text{if $n$ is odd}
\end{cases}
$$

若是想要条件在左侧显示，则使用如下方式：

```latex
$$
\left.
\begin{array}{1}
\text{if $n$ is even:} & \frac{n}{2} \\
\text{if $n$ is odd:} & 3n+1
\end{array}
\right\}
=f(n)
$$
```

效果：

$$
\left.
\begin{array}{1}
\text{if $n$ is even:} & \frac{n}{2} \\
\text{if $n$ is odd:} & 3n+1
\end{array}
\right\}
=f(n)
$$

### 适配行高

在上述情况中，条件定义中分数行的行高为非标准高度，此时使用 \\[2ex] 语句代替该行末尾的 `\\` 来进行高度适配。

```latex
$$
f(n) = 
\begin{cases}
\frac{n}{2},  & \text{if $n$ is even} \\[2ex]
3n+1, & \text{if $n$ is odd}
\end{cases}
$$
```

效果：

$$
f(n) = 
\begin{cases}
\frac{n}{2},  & \text{if $n$ is even} \\[2ex]
3n+1, & \text{if $n$ is odd}
\end{cases}
$$

## 多行等式

一列整齐的多行等式在计算的时候使用的比较多，使用 `\begin{aligned}...\end{aligned}`。

```latex
$$
\begin{aligned}
f(x) &= (m-n)^2 \\
& = m^2 - 2mn + n^2 \\
\end{aligned}
$$
```

效果：

$$
\begin{align}
f(x) &= (m-n)^2 \\
& = m^2 - 2mn + n^2 \\
\end{align}
$$

当两个方程时，也可以使其对齐。

```latex
$$
\begin{alignat}{2}
f(x) &= (m-n)^2 \\
f(x,y) &= (-m+n)^2 \\
& = m^2 - 2mn + n^2 \\
\end{alignat}
$$
```

效果：

$$
\begin{alignat}{2}
f(x) &= (m-n)^2 \\
f(x,y) &= (-m+n)^2 \\
& = m^2 - 2mn + n^2 \\
\end{alignat}
$$

也可以实现左对齐

```latex
$$
\begin{array}{lcl}
f(x) & = & (m-n)^2 \\
f(x,y) & = & (-m+n)^2 \\
& = & m^2 - 2mn + n^2 \\
\end{array}
$$
```

效果：

$$
\begin{array}{lcl}
f(x) & = & (m-n)^2 \\
f(x,y) & = & (-m+n)^2 \\
& = & m^2 - 2mn + n^2 \\
\end{array}
$$

实现右对齐

```latex
$$
\begin{array}{lcr}
f(x) & = & (m-n)^2 \\
f(x,y) & = & (-m+n)^2 \\
& = & m^2 - 2mn + n^2 \\
\end{array}
$$
```

效果：

$$
\begin{array}{lcr}
f(x) & = & (m-n)^2 \\
f(x,y) & = & (-m+n)^2 \\
& = & m^2 - 2mn + n^2 \\
\end{array}
$$


## 方程组

方程组使用 `cases` 环境实现

```latex
\begin{cases}
3x + 5y + z \\
7x - 2y + 4z \\
-6x + 3y + 2z
\end{cases}
```

效果：

$$
\begin{cases}
3x + 5y + z \\
7x - 2y + 4z \\
-6x + 3y + 2z
\end{cases}
$$

<br/>

## 矩阵

### 矩阵的基本用法

以 `\begin{matrix}...\end{matrix}` 包裹，实现基本的矩阵。写法：

```latex
\begin{matrix}
1 & 0 & x \\
1 & 2 & x^2 \\
1 & 3 & x^3 \\
\end{matrix}
```

效果：

$$
\begin{matrix}
1 & 0 & x \\
1 & 2 & x^2 \\
1 & 3 & x^3 \\
\end{matrix}
$$



### 边框矩阵

上面的矩阵不带框，当我们需要将矩阵使用框给包裹起来，就可以使用 `pmatrix`、`bmatrix` 、 `Bmatrix` 、`vmatrix` 或 `Vmatrix` 代替。

#### 小括号

小括号框矩阵使用 `pmatrix` 代替 `matrix`。

```latex
\begin{pmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\
\end{pmatrix}
```

效果：

$$
\begin{pmatrix} 1 & 0 & x \\ 1 & 2 & x^2 \\ 1 & 3 & x^3 \\\end{pmatrix}
$$

<br/>

#### 中括号

中括号框矩阵使用 `bmatrix` 代替 `matrix`。

```latex
\begin{bmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\ 
\end{bmatrix}
```


$$
\begin{bmatrix} 1 & 0 & x \\ 1 & 2 & x^2 \\ 1 & 3 & x^3 \\ \end{bmatrix}
$$

<br/>

#### 大括号

大括号框矩阵使用 `Bmatrix` 代替 `matrix`。

```latex
\begin{Bmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\ 
\end{Bmatrix}
```

$$
\begin{Bmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\ 
\end{Bmatrix}
$$

<br/>

#### 单竖线框矩阵

这里使用 `vmatrix` 设置的是行列式：

```latex
\begin{vmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\ 
\end{vmatrix}
```

$$
\begin{vmatrix} 1 & 0 & x \\ 1 & 2 & x^2 \\ 1 & 3 & x^3 \\ \end{vmatrix}
$$

<br/>

#### 双竖线

```latex
\begin{Vmatrix} 
1 & 0 & x \\ 
1 & 2 & x^2 \\ 
1 & 3 & x^3 \\ 
\end{Vmatrix}
```

$$
\begin{Vmatrix} 1 & 0 & x \\ 1 & 2 & x^2 \\ 1 & 3 & x^3 \\ \end{Vmatrix}
$$

边框矩阵也可以使用 `\left ... \right`加括号的形式。


### 省略号的矩阵

省略号总共有三种：

- 横向省略 `\cdots`：$\cdots$。
- 纵向省略 `\vdots`：$\vdots$。
- 斜向省略 `\ddots`：$\ddots$。

```latex
\begin{pmatrix}
a_{11} & a_{12} & a_{13} & \cdots & a_{1n} \\
a_{21} & a_{22} & a_{23} & \cdots & a_{2n} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
a_{m1} & a_{m2} & a_{m3} & \cdots & a_{mn} \\
\end{pmatrix}
```

效果：

$$
\begin{pmatrix}
a_{11} & a_{12} & a_{13} & \cdots & a_{1n} \\
a_{21} & a_{22} & a_{23} & \cdots & a_{2n} \\
\vdots & \vdots & \vdots & \ddots & \vdots \\
a_{m1} & a_{m2} & a_{m3} & \cdots & a_{mn} \\
\end{pmatrix}
$$

<br/>

### 分块矩阵

```latex
\begin{pmatrix}
	\begin{matrix} 
		1&0\\0&1 \end{matrix} & \text{0}\\
		\text{0} & \begin{matrix} 1&0\\
		0&1 
	\end{matrix}
\end{pmatrix}
```

效果：

$$
\begin{pmatrix}
	\begin{matrix} 
		1&0\\0&1 \end{matrix} & \text{0}\\
		\text{0} & \begin{matrix} 1&0\\
		0&1 
	\end{matrix}
\end{pmatrix}
$$

<br/>

### 行内矩阵

当矩阵显示在一行内，我们需要使用 `\bigl(\begin{smallmatrix}...\end{smallmatrix}\bigr)`。写法：

```latex
\bigl( \begin{smallmatrix} 
a & b \\ 
c & d 
\end{smallmatrix} \bigr)
```

效果：

我们使用 $\bigl( \begin{smallmatrix} a & b \\ c & d \end{smallmatrix} \bigr)$ 矩阵...

## 数组

矩阵可以使用 `\begin{array}{}...\end{array}` 实现，如果需要矩阵有框，可以使用 `\left( ... \right)`表示。`()` 可以换成 `[]` 或者 `{}`。写法：

```latex
$$
\left \{
	\begin{array}{cc|c}
		1 & 0 & x \\
		1 & 2 & x^2 \\
		1 & 3 & x^3 \\
	\end{array}
\right\}
$$
```

我们由上的写法得出：

- 列由 `&` 分隔；行由 `\\` 分隔。
- 列样式有n(列数)个表示列对齐方式的字母组成，在字母中间加入竖线可以实现分割线。字母的意义：
	- `l` —— 该列左对齐排列
	- `c`——该列居中排列
	- `r`——该列右对齐排列

效果如下：

$$
\left \{
	\begin{array}{cc|c}
		1 & 0 & x \\
		1 & 2 & x^2 \\
		1 & 3 & x^3 \\
	\end{array}
\right\}
$$



`\begin{array}...\end{array}` 也可以表示表格。写法：

```latex
$$
\begin{array}{|c|lcr|}
	\hline
	n & \text{左对齐} & \text{居中对齐} & \text{右对齐} \\
	\hline
	1 & 0.24 & 1 & 125 \\
	2 & -1 & 189 & -8 \\
	3 & -20 & 2000 & 1+10i \\
	\hline
\end{array}
$$
```

在这里，`\hline` 表示水平线，`|`表示竖线。

效果：

$$
\begin{array}{|c|lcr|}
	\hline
	n & \text{左对齐} & \text{居中对齐} & \text{右对齐} \\
	\hline
	1 & 0.24 & 1 & 125 \\
	2 & -1 & 189 & -8 \\
	3 & -20 & 2000 & 1+10i \\
	\hline
\end{array}
$$

<br/>

## 方程组

使用 `\begin{array}...\end{array}` 和 `\left{...\right.` 来创建方程组

```latex
$$
\left\{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\ 
a_2x+b_2y+c_2z=d_2 \\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right. 
$$
```

效果：

$$
\left\{ 
\begin{array}{c}
a_1x+b_1y+c_1z=d_1 \\ 
a_2x+b_2y+c_2z=d_2 \\ 
a_3x+b_3y+c_3z=d_3
\end{array}
\right.
$$

<br/>

## 交换图表

使用一行 `$\require{AMScd}$`语句来允许交换图表的显示。

声明交换图表后，语法语矩阵相似，在开头使用 `\begin{CD}`，在结尾使用 `\end{CD}`，在中间插入图表元素，每个元素之间插入 `&`，并在每行结尾处使用 `\\`。

例子：

```latex
$$
$\require{AMScd}$
\begin{CD}
    A @>a>> B\\
    @V b V V\# @VV c V\\
    C @>>d> D
\end{CD}
$$
```

效果如下：

$$
$\require{AMScd}$
\begin{CD}
    A @>a>> B\\
    @V b V V\# @VV c V\\
    C @>>d> D
\end{CD}
$$

其中，`@>>>` 代表右箭头、`@<<<`代表左箭头、`@VVV`代表下箭头、`@AAA`代表上箭头、`@=`代表水平实线、`@|`代表竖直双实线、`@.`代表没有箭头。

在 `@>>>` 的 `>>>` 之间任意插入文字即代表该箭头的注释文字。

例子：

```latex
$$
\begin{CD}
    A @>>> B @>{\text{very long label}}>> C \\
    @. @AAA @| \\
    D @= E @<<< F
\end{CD}
$$
```

效果如下：

$$
\begin{CD}
    A @>>> B @>{\text{very long label}}>> C \\
    @. @AAA @| \\
    D @= E @<<< F
\end{CD}
$$

在本例中，"very long label" 自动延长了它所在箭头以及对应箭头的长度。

# 数学符号

当我们书写数学公式的时候，我们就需要一些符号来表达数学的一些思想。下面列举一些常用的符号。

## 关系符号

**关系符号是表示数与数、式与式之间的某种关系的特定记号。**

所有的二元关系符都可以加 `\not` 前缀得到相反意义的关系符，例如 `\not=` 就得到不等号 $\not=$（同 `\ne`）。冒号 `:` 与 \反斜杠都是关系符号

|小于等于|`\leq or \le`：$\leq$|大于等于| `\geq or \ge`：$\geq$|恒等于|`\equiv`：$\equiv$|
|:---:|:---:|:---:|:---:|:---:|:---:|
|远小于|`\ll`：$\ll$|远大于|`\gg`：$\gg$|约等于|`\doteq`：$\doteq$|
||`\prec`:$\prec$||`\succ`:$\succ$|相似|`\sim`：$\sim$|
||`\preceq`:$\preceq$||`\succeq`:$\succeq$|渐进等于|`\simeq`：$\simeq$|
|约等于|`\approx`：$\approx$|全等|`\cong`：$\cong$||`\Join`:$\Join$|
|不等于|`\neq or \ne`：$\neq$||`\bowtie`：$\bowtie$|正比|`\propto`：$\propto$|
||`\vdash`：$\vdash$||`\dashv`：$\dashv$||`\models`：$\models$|
||`\mid`：$\mid$||`\parallel`：$\parallel$|垂直|`\perp`：$\perp$|
||`\smile`：$\smile$||`\frown`：$\frown$|`\asymp`：$\asymp$|

<br/>

<center><code><strong>AMS二元关系符↓</strong></code></center>

<br/>

|`\lessdot`:$\lessdot$|`\gtrdot`:$\gtrdot$|`\doteqdot`:$\doteqdot$|
|:---:|:---:|:---:|
|`\leqslant`:$\leqslant$|`\geqslant`:$\geqslant$|`\risingdotseq`:$\risingdotseq$|
|`\eqslantless`:$\eqslantless$|`\eqslantgtr`:$\eqslantgtr$|`\fallingdotseq`:$\fallingdotseq$|
|`\leqq`:$\leqq$|`\geqq`:$\geqq$|`\eqcirc`:$\eqcirc$|
|`\lll or \llless`:$\lll$|`\ggg`:$\ggg$|`\circeq`:$\circeq$|
|`\lesssim`:$\lesssim$|`\gtrsim`:$\gtrsim$|`\triangleq`:$\triangleq$|
|`\lessapprox`:$\lessapprox$|`\gtrapprox`:$\gtrapprox$|`\bumpeq`:$\bumpeq$|
|`\lessgtr`:$\lessgtr$|`\gtrless`:$\gtrless$|`\Bumpeq`:$\Bumpeq$|
|`\lesseqgtr`:$\lesseqgtr$|`\gtreqless`:$\gtreqless$|`\thicksim`:$\thicksim$|
|`\lesseqqgtr`:$\lesseqqgtr$|`\gtreqqless`:$\gtreqqless$|`\thickapprox`:$\thickapprox$|
|`\preccurlyeq`:$\preccurlyeq$|`\succcurlyeq`:$\succcurlyeq$|`\approxeq`:$\approxeq$|
|`\curlyeqprec`:$\curlyeqprec$|`\curlyeqsucc`:$\curlyeqsucc$|`\backsim`:$\backsim$|
|`\precsim`:$\precsim$|`\succsim`:$\succsim$|`\backsimeq`:$\backsimeq$|
|`\precapprox`:$\precapprox$|`\succapprox`:$\succapprox$|`\vDash`:$\vDash$|
|`\shortparallel`:$\shortparallel$|`\Supset`:$\Supset$|`\Vvdash`:$\Vvdash$|
|`\blacktriangleleft`:$\blacktriangleleft$|`\sqsupset`:$\sqsupset$|`\backepsilon`:$\backepsilon$|
|`\vartriangleright`:$\vartriangleright$|`\because`:$\because$|`\varpropto`:$\varpropto$|
|`\blacktriangleright`:$\blacktriangleright$|`\Subset`:$\Subset$|`\between`:$\between$|
|`\trianglerighteq`:$\trianglerighteq$|`\smallfrown`:$\smallfrown$|`\pitchfork`:$\pitchfork$|
|`\vartriangleleft`:$\vartriangleleft$|`\shortmid`:$\shortmid$|`\smallsmile`:$\smallsmile$|
|`\trianglelefteq`:$\trianglelefteq$|`\therefore`:$\therefore$|`\sqsubset`:$\sqsubset$|

<br/>

## 运算符号

<br/>

|`\pm`：$\pm$|`\mp`：$\mp$|`\triangleleft`：$\triangleleft$|
|:---:|:---:|:---:|
|`\cdot`：$\cdot$|`\div`：$\div$|`\triangleright`：$\triangleright$|
|乘号`\times`：$\times$|`\setminus`：$\setminus$|`\star`：$\star$|
|`\ast`：$\ast$|
|`\circ`：$\circ$|
|或`\vee or \lor`：$\vee$|且`\wedge or \land`：$\wedge$|`\bullet`：$\bullet$|
|`\oplus`：$\oplus$|`\omonus`：$\ominus$|`\diamond`：$\diamond$|
|`\odot`：$\odot$|`\oslash`：$\oslash$|`\uplus`：$\uplus$|
|`\otimes`：$\otimes$|`\bigcirc`：$\bigcirc$|`\amalg`：$\amalg$|
|`\bigtriangleup`：$\bigtriangleup$|`\bigtriangledown`：$\bigtriangledown$|`\dagger`：$\dagger$|
|`\lhd`：$\lhd$|`\rhd`：$\rhd$|`\ddagger`：$\ddagger$|
|`unlhd`：$\unlhd$|`unrhd`：$\unrhd$|`\wr`：$\wr$|

<br/>

<center><code><strong>大尺寸运算符↓</strong></code></center>

<br/>

|求和|`\sum`：$\sum$|大号并集| `\bigcup`：$\bigcup$|或|`\bigvee`：$\bigvee$|
|:---:|:---:|:---:|:---:|:---:|:---:|
|乘积|`\prod`：$\prod$|大号交集|`\bigcap`：$\bigcap$|且|`\bigwedge`：$\bigwedge$|
||`\coprod`：$\coprod$||`\bigsqcup`：$\bigsqcup$|多重集|`\biguplus`：$\biguplus$|
|积分|`\int`：$\int$|曲面积分|`\oint`：$\oint$|||
||`\bigodot`：$\bigodot$||`\bigoplus`：$\bigoplus$||`\bigotimes`：$\bigotimes$|

<br/>

<center><code><strong>AMS二元运算符↓</strong></code></center>

<br/>

|`\dotplus`|$\dotplus$|`\centerdot`|$\centerdot$|
|:---:|:---:|:---:|:---:|:---:|:---:|
|`\ltimes`|$\ltimes$|`\rtimes`|$\rtimes$|`\divideontimes`|$\divideontimes$|
|`\doublecup`|$\doublecup$|`\doublecap`|$\doublecap$|`\smallsetminus`|$\smallsetminus$|
|`\veebar`|$\veebar$|`\barwedge`|$\barwedge$|`\doublebarwedge`|$\doublebarwedge$|
|`\boxplus`|$\boxplus$|`\boxminus`|$\boxminus$|`\circleddash`|$\circleddash$|
|`\boxtimes`|$\boxtimes$|`\boxdot`|$\boxdot$|`\circledcirc`|$\circledcirc$|
|`\intercal`|$\intercal$|`\circledast`|$\circledast$|`\rightthreetimes`|$\rightthreetimes$|
|`\curlyvee`|$\curlyvee$|`\curlywedge`|$\curlywedge$|`\leftthreetimes`|$\leftthreetimes$|

## 集合符号

<br/>

<center><code><strong>下列集合符号属于关系符↓</strong></code></center>

<br/>

|名称|Latex符号|
|:---:|:---:|
|属于|`\in`：$\in$|
|反属于|`\ni or \owns`：$\ni$|
|不属于|`\not\in or \notin`：$\not\in$|
|不反属于|`\not\ni`：$\not\ni$|
|包含|`\supset`：$\supset$|
|包含于|`\subset`：$\subset$|
|包含有等于|`\supseteq`：$\supseteq$|
|包含于有等于|`\subseteq`：$\subseteq$|
||`\sqsubset`：$\sqsubset$|
||`\sqsupset`：$\sqsupset$|
||`\sqsubseteq`:$\sqsubseteq$|
||`\sqsupseteq`：$\sqsupseteq$|

<br/>

<center><code><strong>下列集合符号属于运算符↓</strong></code></center>

<br/>

|名称|Latex符号|
|:---:|:---:|
|空集|`\empty or \emptyset`：$\emptyset$|
|并集| `\cup`：$\cup$|
||`\sqcup`：$\sqcup$|
||`\sqcap`：$\sqcap$|
|交集|`\cap`：$\cap$|
|大号空集|`\varnothing`：$\varnothing$|

<br/>

## 箭头

<br/>

|`\leftarrow or \gets`：$\leftarrow$|`\Leftarrow`：$\Leftarrow$|
|:---:|:---:|
|`\rightarrow or \to`：$\rightarrow$|`\Rightarrow`：$\Rightarrow$|
|`\longleftarrow`：$\longleftarrow$|`\Longleftarrow`：$\Longleftarrow$|
|`\longrightarrow`：$\longrightarrow$|`\Longrightarrow`：$\Longrightarrow$|
|`\leftrightarrow`：$\leftrightarrow$|`\Leftrightarrow`：$\Leftrightarrow$|
|`\longleftrightarrow`：$\longleftrightarrow$|`\Longleftrightarrow`：$\Longleftrightarrow$|
|`\mapsto`：$\mapsto$|`\longmapsto`：$\longmapsto$|
|`\hookleftarrow`：$\hookleftarrow$|`\hookrightarrow`：$\hookrightarrow$|
|`\leftharpoonup`：$\leftharpoonup$|`\rightharpoonup`：$\rightharpoonup$|
|`\leftharpoondown`：$\leftharpoondown$|`\rightharpoondown`：$\rightharpoondown$|
|`\rightleftharpoons`：$\rightleftharpoons$|`\iff`：$\iff$|
|`\uparrow`：$\uparrow$|`\downarrow`：$\downarrow$|
|`\updownarrow`：$\updownarrow$|`\Uparrow`：$\Uparrow$|
|`\Downarrow`：$\Downarrow$|`\Updownarrow`：$\Updownarrow$|
|`\nearrow`：$\nearrow$|`\searrow`：$\searrow$|
|`\swarrow`：$\swarrow$|`\nwarrow`：$\nwarrow$|
|`\leadsto`：$\leadsto$|

<br/>

<center><code><strong>AMS箭头↓</strong></code></center>

<br/>

|Latex符号|Latex符号|
|:---:|:---:|
|`\dashleftarrow`：$\dashleftarrow$|`\dashrightarrow`：$\dashrightarrow$|
|`\leftleftarrows`：$\leftleftarrows$|`\rightrightarrows`：$\rightrightarrows$|
|`\leftrightarrows`：$\leftrightarrows$|`\rightleftarrows`：$\rightleftarrows$|
|`\Lleftarrow`：$\Lleftarrow$|`\Rrightarrow`：$\Rrightarrow$|
|`\twoheadleftarrow`：$\twoheadleftarrow$|`\twoheadrightarrow`：$\twoheadrightarrow$|
|`\leftarrowtail`：$\leftarrowtail$|`\rightarrowtail`：$\rightarrowtail$|
|`\leftrightharpoons`：$\leftrightharpoons$|`\rightleftharpoons`：$\rightleftharpoons$|
|`\Lsh`：$\Lsh$|`\Rsh`：$\Rsh$|
|`\looparrowleft`：$\looparrowleft$|`\looparrowright`：$\looparrowright$|
|`\curvearrowleft`：$\curvearrowleft$|`\curvearrowright`：$\curvearrowright$|
|`\circlearrowleft`：$\circlearrowleft$|`\circlearrowright`：$\circlearrowright$|
|`\multimap`：$\multimap$|`\upuparrows`：$\upuparrows$|
|`\downdownarrows`：$\downdownarrows$|`\upharpoonleft`：$\upharpoonleft$|
|`\upharpoonright`：$\upharpoonright$|`\downharpoonright`：$\downharpoonright$|
|`\rightsquigarrow`：$\rightsquigarrow$|`\leftrightsquigarrow`：$\leftrightsquigarrow$|

<br/>

## 定界符

短括号写法 `(\frac{a}{b})`：$(\frac{a}{b})$。  
长括号写法 `\left(\frac{a}{b} \right)`：$\left( \frac{a}{b} \right)$。  
你可以使用 `\left` 和 `\right` 来显示不同的括号。

小括号(圆括号，括弧) `(`：$($ 与 `)`：$)$。  
中括号(方括号) `[ or \lbrack`：$\lbrack$ 与 `] or \rbrack`：$\rbrack$。  
大括号(花括号) `\{ or \lbrace`：$\lbrace$ 与 `\} or \rbrace`：$\rbrace$。  
角括号(尖括号) `\langle`：$\langle$ 与 `\rangle`：$\rangle$。  
单竖线 `| or \vert`：$\vert$；双竖线 `\| or \Vert`：$\Vert$。  
取顶符号(上取整函数符号) `\lceil`：$\lceil$ 与 `\rceil`：$\rceil$  
高斯符号(下取整函数符号) `\lfloor`：$\lfloor$ 与 `\rfloor`：$\rfloor$  
斜杠 `/`：$/$；反斜线转义符 `\backslash`：$\backslash$。

单括号的两种写法：

- 左单括号写法 `\left \{ \frac{a}{b} \right.`：$\left \{ \frac{a}{b} \right.$。
- 右单括号写法 `\left. \frac{a}{b} \right \}`：$\left. \frac{a}{b} \right \}$。

上括号写法 `\overbrace{}^{}`：$\overbrace{1,2,3...}^{sample}$。

下括号写法 `\underbrace{}_{}`：$\underbrace{1,2,3...}_{sample}$。

> 可以使用 `\big`，`\Big`，`\bigg`，`\Bigg` 控制括号的大小 <br/>
> 写法 ：`\Bigg ( \bigg [ \Big \{ \big \langle \left | \| \frac{a}{b} \| \right | \big \rangle \Big \} \bigg ] \Bigg )` <br/>
> $\Bigg ( \bigg [ \Big \{ \big \langle \left | \| \frac{a}{b} \| \right | \big \rangle \Big \} \bigg ] \Bigg )$。

<br/>

<center><code><strong>其它定界符↓</strong></code></center>

<br/>

|`\uparrow`：$\uparrow$|`\downarrow`：$\downarrow$|
|:---:|:---:|
|`\Uparrow`：$\Uparrow$|`\Downarrow`：$\Downarrow$|
|`\updownarrow`：$\updownarrow$|`\Updownarrow`：$\Updownarrow$|

<br/>

<center><code><strong>大界定符↓</strong></code></center>

<br/>

|`\lgroup`：$\lgroup$|`\rgroup`：$\rgroup$|`\lmoustache`：$\lmoustache$|
|:---:|:---:|:---:|
|`\arrowvert`：$\arrowvert$|`\Arrowvert`：$\Arrowvert$|`\rmoustache`：$\rmoustache$|
|`\bracevert`：$\bracevert$|

<br/>

<center><code><strong>AMS定界符↓</strong></code></center>

<br/>

|`\ulcorner`：$\ulcorner$|`\urcorner`：$\urcorner$|`\llcorner`：$\llcorner$|`\lrcorner`：$\lrcorner$|
|:---:|:---:|:---:|:---:|
|`\lvert`：$\rvert$|`\rvert`：$\rvert$`\lVert`：$\lVert$|`\rVert`：$\rVert$|


## 省略号

代表省略号有以下： 

- `\dots`：$\dots$ 
- `\cdots`：$\cdots$
- `\vdots`：$\vdots$
- `\ddots`：$\ddots$

<br/>

## 其它符号

|||||
|:---:|:---:|:---:|:---:|
|`\hbar`：$\hbar$|`\imath`：$\imath$|`\jmath`：$\jmath$|`\ell`：$\ell$|
|`\Re`：$\Re$|`\Im`：$\Im$|`\aleph`：$\aleph$|`\wp`：$\wp$|
|任意 `\forall`：$\forall$|存在 `\exists`：$\exists$|`\mho`：$\mho$|`\partial`：$\partial$|
|'：$'$|`\prime`：$\prime$|空集 `\emptyset`：$\emptyset$|无穷大`\infty`：$\infty$|
|`\nabla`：$\nabla$|`\triangle`：$\triangle$|`\Box`：$\Box$|`\Diamond`：$\Diamond$|
|`\bot`：$\bot$|`\top`：$\top$|`\angle`：$\angle$|`\surd`：$\surd$|
|`\diamondsuit`：$\diamondsuit$|`\heartsuit`：$\heartsuit$|`\clubsuit`：$\clubsuit$|`\spadesuit`：$\spadesuit$|
|`\neg or \lnot`：$\neg$|`\flat`：$\flat$|`\natural`：$\natural$|`\sharp`：$\sharp$|

<br/>

## 重音符号

在数学公式的排版中，我们有可能需要使用重音符号

|\hat{a}：$\hat{a}$|\grave{a}：$\grave{a}$|\bar{a}：$\bar{a}$|\acute{a}：$\acute{a}$|
|:---:|:---:|:---:|:---:|
|\check{a}：$\check{a}$|\dot{a}：$\dot{a}$|\vec{a}：$\vec{a}$|\breve{a}：$\breve{a}$|
|\tilde{a}：$\tilde{a}$|\ddot{a}：$\ddot{a}$|\widehat{a}：$\widehat{a}$|\widetilde{a}：$\widetilde{a}$|

## 字母。

<center><code><strong>希腊字母↓</strong></code></center>

24个希腊字母表是书写数学表达式不可缺少的符号，区分大小写。

|中文名称|Latex小写|LaTex大写|
|:---:|:---:|:---:|
|阿尔法|`\alpha`:$\alpha$|`\A`:$A$|
|贝塔|`\beta`:$\beta$|`\B`:$B$|
|伽马|`\gamma`:$\gamma$|`\Gamma`:$\Gamma$|
|德尔塔|`\delta`:$\delta$|`\Delta`:$\Delta$|
|伊普西隆|`\epsilon`:$\epsilon$|`\Epsilon$`:$E$|
|泽塔|`\zeta`:$\zeta$|`Z`:$Z$|
|伊塔|`\eta`:$\eta$|`H`:$H$|
|锡塔|`\theta`:$\theta$|`\Theta`:$\Theta$|
|约塔|`\iota`:$\iota$|`I`:$I$|
|卡帕|`\kappa`:$\kappa$|`K`:$K$|
|兰姆达|`\lambda`:$\lambda$|`\Lambda`:$\Lambda$|
|缪|`\mu`:$\mu$|`M`:$M$|
|纽|`nu`:$\nu$|`N`:$N$|
|克赛|`\xi`:$\xi$|`\Xi`:$\Xi$|
|欧麦克戎|`\omicron`:$o$|`O`:$O$|
|派|`\pi`:$\pi$|`\Pi`:$\Pi$|
|若|`\rho`:$\rho$|`P`:$P$|
|西格玛|`\sigma`:$\sigma$|`\Sigma`:$\Sigma$|
|套|`\tau`:$\tau$|`T`:$T$|
|尤普西隆|`\upsilon`:$\upsilon$|`\Upsilon`:$\Upsilon$|
|斐 fei|`\phi`:$\phi$|`\Phi`:$\Phi$|
|凯|`\chi`:$\chi$|`X`:$X$|
|普赛|`\psi`:$\psi$|`\Psi`:$\Psi$|
|欧米茄|`\omega`:$\omega$|`\Omega`:$\Omega$|

部分字母有变量专用形式，以 `\var-` 开头。

|\epsilon|E|\varepsilon|$\epsilon \| E \| \varepsilon$|
|:---:|:---:|:---:|:---:|
|\theta|\Theta|\vartheta|$\theta \| \Theta \| \vartheta$|
|\rho|P|\varrho|$\rho \| P \| \varrho$|
|\sigma|\Sigma|\varsigma|$\sigma \| \Sigma \| \varsigma$|
|\phi|\Phi|\varphi|$\phi \| \Phi \| \varphi$|

<br/>

<center><code><strong>AMS希腊和希伯来符号↓</strong></code></center>

<br/>

|`\digamma`：$\digamma$|`\varkappa`：$\varkappa$|
|:--:|:--:|:--:|
|`\beth`：$\beth$|`\gimel`：$\gimel$|`\daleth`：$\daleth$|

<br/>

## AMS二元否定关系符和箭头

|`\nless`：$\nless$|`\ngtr`：$\ngtr$|`\varsubsetneqq`：$\varsubsetneqq$|
|:---:|:---:|:---:|
|`\lneq`：$\lneq$|`\gneq`：$\gneq$|`\varsupsetneqq`：$\varsupsetneqq$|
|`\nleq`：$\nleq$|`\ngeq`：$\ngeq$|`\nsubseteqq`：$\nsubseteqq$|
|`\nleqslant`：$\nleqslant$|`\gneqq`：$\gneqq$|`\nmid`：$\nmid$|
|`\lvertneqq`：$\lvertneqq$|`\gvertneqq`：$\gvertneqq$`|\nparallel`：$\nparallel$|
|`\nleqq`：$\nleqq$|`\ngeqq`：$\ngeqq$|`\nshortmid`：$\nshortmid$|
|`\lnsim`：$\lnsim$|`\gnsim`：$\gnsim$|`\nshortparallel`：$\nshortparallel$|
|`\lnapprox`：$\lnapprox$|`\gnapprox`：$\gnapprox$|`\nsim`：$\nsim$|
|`\nprec`：$\nprec$|`\nsucc`：$\nsucc$|`\ncong`：$\ncong$|
|`\npreceq`：$\npreceq$|`\nsucceq`：$\nsucceq$|`\nvdash`：$\nvdash$|
|`\precneqq`：$\precneqq$|`\succneqq`：$\succneqq$|`\nvDash`：$\nvDash$|
|`\precnsim`：$\precnsim$|`\succnsim`：$\succnsim$|`\nVdash`：$\nVdash$|
|`\precnapprox`：$\precnapprox$|`\succnapprox`：$\succnapprox$|`\nVDash`：$\nVDash$|
|`\subsetneq`：$\subsetneq$|`\supsetneq`：$\supsetneq$|`\ntriangleleft`：$\ntriangleleft$|
|`\varsubsetneq`：$\varsubsetneq$|`\varsupsetneq`：$\varsupsetneq$|`\ntriangleright`：$\ntriangleright$|
|`\nsubseteq`：$\nsubseteq$|`\nsupseteq`：$\nsupseteq$|`\ntrianglelefteq`：$\ntrianglelefteq$|
|`\subsetneqq`：$\subsetneqq$|`\supsetneqq`：$\supsetneqq$|`\ntrianglerighteq`：$\ntrianglerighteq$|
|`\nleftarrow`：$\nleftarrow$|`\nrightarrow`：$\nrightarrow$|`\nleftrightarrow`：$\nleftrightarrow$|
|`\nLeftarrow`：$\nLeftarrow$|`\nRightarrow`：$\nRightarrow$|`\nLeftrightarrow`：$\nLeftrightarrow$|

<br/>

## AMS其它符号

|`\hbar`：$\hbar$|`\hslash`：$\hslash$|`\Bbbk`：$\Bbbk$|
|:---:|:---:|:---:|
|`\square`：$\square$|`\blacksquare`：$\blacksquare$|`\circledS`：$\circledS$|
|`\vartriangle`：$\vartriangle$|`\blacktriangle`：$\blacktriangle$|`\complement`：$\complement$|
|`\triangledown`：$\triangledown$|`\blacktriangledown`：$\blacktriangledown$|`\Game`：$\Game$|
|`\lozenge`：$\lozenge$|`\blacklozenge`：$\blacklozenge$|`\bigstar`：$\bigstar$|
|`\angle`：$\angle$|`\measuredangle`：$\measuredangle$|
|`\diagup`：$\diagup$|`\diagdown`：$\diagdown$|`\backprime`：$\backprime$|
|`\nexists`：$\nexists$|`\Finv`：$\Finv$|`\varnothing`：$\varnothing$|
|`\eth`：$\eth$|`\sphericalangle`：$\sphericalangle$|`\mho`：$\mho$|

<br/>

## 颜色

Latex可以给文字设置颜色，语法 `{\color{色调} 表达式}`。

使用颜色语法写一个公式：`{\color{PineGreen} x^2} + {\color{ProcessBlue} 2x} - {\color{NavyBlue}1}`：${\color{PineGreen} x^2} + {\color{ProcessBlue} 2x} - {\color{NavyBlue}1}$。

<center><code><strong>支持的颜色对照表↓</strong></code></center>

|${\color{Apricot}Apricot}$|${\color{Aquamarine}Aquamarine}$|${\color{Bittersweet}Bittersweet}$|${\color{Black}Black}$|
|:---:|:---:|:---:|:---:|
|${\color{Blue}Blue}$|${\color{BlueGreen}BlueGreen}$|${\color{BlueViolet}BlueViolet}$|${\color{BrickRed}BrickRed}$|
|${\color{Brown}Brown}$|${\color{BurntOrange}BurntOrange}$|${\color{CadetBlue}CadetBlue}$|${\color{CarnationPink}CarnationPink}$|
|${\color{Cerulean}Cerulean}$|${\color{CornflowerBlue}CornflowerBlue}$|${\color{Cyan}Cyan}$|${\color{Dandelion}Dandelion}$|
|${\color{DarkOrchid}DarkOrchid}$|${\color{Emerald}Emerald}$|${\color{ForestGreen}ForestGreen}$|${\color{Fuchsia}Fuchsia}$|
|${\color{Goldenrod}Goldenrod}$|${\color{Gray}Gray}$|${\color{Green}Green}$|${\color{GreenYellow}GreenYellow}$|
|${\color{JungleGreen}JungleGreen}$|${\color{Lavender}Lavender}$|${\color{LimeGreen}LimeGreen}$|${\color{Magenta}Magenta}$|
|${\color{Mahogany}Mahogany}$|${\color{Maroon}Maroon}$|${\color{Melon}Melon}$|${\color{MidnightBlue}MidnightBlue}$|
|${\color{Mulberry}Mulberry}$|${\color{NavyBlue}NavyBlue}$|${\color{OliveGreen}OliveGreen}$|${\color{Orange}Orange}$|
|${\color{OrangeRed}OrangeRed}$|${\color{Orchid}Orchid}$|${\color{Peach}Peach}$|${\color{Periwinkle}Periwinkle}$|
|${\color{PineGreen}PineGreen}$|${\color{Plum}Plum}$|${\color{ProcessBlue}ProcessBlue}$|${\color{Purple}Purple}$|
|${\color{RawSienna}RawSienna}$|${\color{Red}Red}$|${\color{RedOrange}RedOrange}$|${\color{RedViolet}RedViolet}$|
|${\color{Rhodamine}Rhodamine}$|${\color{RoyalBlue}RoyalBlue}$|${\color{RoyalPurple}RoyalPurple}$|${\color{RubineRed}RubineRed}$|
|${\color{Salmon}Salmon}$|${\color{SeaGreen}SeaGreen}$|${\color{Sepla}Sepla}$|${\color{SkyBlue}SkyBlue}$|
|${\color{SpringGreen}SpringGreen}$|${\color{Tan}Tan}$|${\color{TealBlue}TealBlue}$|${\color{Thistle}Thistle}$|
|${\color{Turquoise}Turquoise}$|${\color{Violet}Violet}$|${\color{VioletRed}VioletRed}$|${\color{White}White}$|
|${\color{WildStrawberry}WildStrawberry}$|${\color{Yellow}Yellow}$|${\color{YellowGreen}YellowGreen}$|${\color{YellowOrange}YellowOrange}$|

<br/>

# 参考资料

-  一份不太简短的LATEX2介绍 作者：Tobias Oetiker