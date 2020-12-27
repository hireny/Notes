# XY-pic

xy 是绘制流程图的专用宏包。要想使用它，只需在导言区加上：

```latex
\usepackage[options]{xy}
```

options 列出你需要载入的 XY-pic 的选项。这些选项基本上被用于调试这个宏包的使用。建议你使用 all，可以让 LaTex 载入 XY 的所有命令。

XY-pic 流程图被绘制在一副以矩阵定位的画布上，每一个流程图元素被放在矩阵的一个单元中：

```latex
\begin{displaymath}
\xymatrix{A & B \\
          C & D }
\end{displaymath}
```

命令 `\xymatrix` 必须置于数学模式中。这里，我们设定了一个两行两列的矩阵。为了画出流程，我们只需要使用命令 `\ar` 增加带方向的箭头即可。

```latex
\begin{displaymath}
\xymatrix{ A \ar[r] & B \ar[d] \\
D \ar[u] & C \ar[l] }
\end{displaymath}
```

箭头命令要放在其触发的那个单元里。参量是箭头的方向（u：上，d：下，r：右以及 l：左）。

```latex
\begin{displaymath}
\xymatrix{
A \ar[d] \ar[dr] \ar[r] & B \\
D & C }
\end{displaymath}
```

要画对角线，可以指出不只一个方向参量。实际上，你还可以重复同一个方向来得到更大的箭头。

```latex
\begin{displaymath}
\xymatrix{
A \ar[d] \ar[dr] \ar[drr] &&\\
B & C & D }
\end{displaymath}
```

我们还可以绘制一些更有趣的流程图，给箭头加上标签，只需要使用普通的上标和下标。

```latex
\begin{displaymath}
\xymatrix{
A \ar[r]^f \ar[d]_g &
B \ar[d]^{g’} \\
D \ar[r]_{f’} & C }
\end{displaymath}
```

如图所示，就像数学模式里一样使用上下标。唯一的区别在于：上标表示在 “箭头的上方”，下标表示放在 “箭头的下方”。把文本放到箭头上可以用 |。

```latex
\begin{displaymath}
\xymatrix{
A \ar[r]|f \ar[d]|g &
B \ar[d]|{g’} \\
D \ar[r]|{f’} & C }
\end{displaymath}
```

绘制空心箭头的命令是 `\ar[...]|\hole`。

某些情况下，需要区分不同类型的箭头。可以给它们标上标签，或者使用不同的外观来实现：

```latex
\shorthandoff{”}
\begin{displaymath}
\xymatrix{
\bullet\ar@{->}[rr] && \bullet\\
\bullet\ar@{.<}[rr] && \bullet\\
\bullet\ar@{~)}[rr] && \bullet\\
\bullet\ar@{=(}[rr] && \bullet\\
\bullet\ar@{~/}[rr] && \bullet\\
\bullet\ar@{^{(}->}[rr] &&
\bullet\\
\bullet\ar@2{->}[rr] && \bullet\\
\bullet\ar@3{->}[rr] && \bullet\\
\bullet\ar@{=+}[rr] && \bullet
}
\end{displaymath}
\shorthandon{”}
```

注意下面两幅流程图的区别：

```latex
\begin{displaymath}
\xymatrix{
\bullet \ar[r]
\ar@{.>}[r] &
\bullet
}
\end{displaymath}
```

```latex
\begin{displaymath}
\xymatrix{
\bullet \ar@/^/[r]
\ar@/_/@{.>}[r] &
\bullet
}
\end{displaymath}
```

两条斜线间的修饰元素决定了曲线应该如何被画出。XY-pic 提供了很多办法来改变曲线的形状。

