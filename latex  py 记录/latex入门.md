### Typora + latex公式

- 行内公式 $ a + c = 2 $

```latex
$ a + c = 2 $
```

- 块级公式$$ E = mc^2 $$

``` latex
$$ E = mc^2 $$
```

- 上下标 $x^{ss}$

```latex
x^2 %上标
x _1 %下标 , 如果下标有多个字母需要使用花括号{}，比如x_{ss}	
```

- 分数  $$ \frac{a}{c} $$

```latex
$$\frac{a}{c}$$ %分子在前，分母在后
```

- 根号 $$\sqrt{x}$$

```latex
$$\sqrt{x}$$
```

- n次根号 $$\sqrt[n]{x}$$

```latex
$$\sqrt[n]{x}$$
```

- 乘号 $$a \cdot b $$

```latex
$$a \cdot b $$
```

- 除号 $$a \div b $$

```latex
$$a \div b $$
```

- 常用运算符

$\sum_{i=1}^{n} i^2  $

```latex
\sum_{i=1}^{n} i^2               % 求和符号
\prod_{k=1}^{n} k                % 连乘
\lim_{x \to 0} \frac{\sin x}{x}  % 极限
\int_a^b x^2 \, dx               % 积分
\iint, \iiint, \oint             % 二重、三重、曲线积分
```

- 矩阵与行列式

```latex
% 矩阵（无括号）
\begin{matrix}
a & b \\
c & d
\end{matrix}

% 方括号矩阵
\begin{bmatrix}
1 & 2 \\
3 & 4
\end{bmatrix}

% 括号矩阵
\begin{pmatrix}
a & b \\
c & d
\end{pmatrix}

% 行列式
\begin{vmatrix}
a & b \\
c & d
\end{vmatrix}
```

- 希腊字母

| 大写    | 小写    |
| ------- | ------- |
| \Alpha  | \alpha  |
| \Beta   | \beta   |
| \Gamma  | \gamma  |
| \Delta  | \delta  |
| \Theta  | \theta  |
| \Lambda | \lambda |
| \Pi     | \pi     |
| \Sigma  | \sigma  |
| \Phi    | \phi    |
| \Omega  | \omega  |

- 逻辑、集合、关系符号

```latex
\in, \notin, \subset, \subseteq, \supset
\cup, \cap, \setminus,	 \emptyset, \forall, \exists
\land, \lor, \neg, \Rightarrow, \Leftrightarrow
\le, \ge, \neq, \approx, \equiv
```

- 箭头

```latex
\leftarrow	%左箭头
\rightarrow	%右箭头
```

- 上划线$\tilde{x}$

```latex
\tilde{x}
```

- 换行等式

```latex
\begin{align}
a + b &= c \\
	  &= a - c \\
	  &= b - c
\end{align}
```

$\begin{align}
a + b &= c \\
	  &= a - c \\
	  &= b - c
\end{align}$

- 带帽上标$\hat{x}$

```latex
\hat{x}
```

- 向量（上箭头） $\vec{a}$

```latex
\vec{a}
```

- $A'$，上标带撇，直接'即可，不用^

```latex
A'
```

