# 前言

盒子模型，英文即 `box model`，无论是 `div`、`span`、还是 `a` 都是盒子。  
但是，图片、表单元素一律看作是文本，它们并不是盒子。这个很好理解，比如说，一张图片里并不能放东西，它自己就是自己的内容。

# 概念

CSS盒子模型主要的属性有：`width`、`height`、`padding`、`border`、`margin`。如下：

- `width` 和 `height`：内容的宽度、高度（不是盒子的宽度、高度）。
- `padding`：内边距。
- `border`：边框。
- `margin`：外边距。

CSS盒子模型和IE盒子模型的区别：

- 在标准盒子模型中，width和height指的是内容区域的宽度和高度。增加内边距、边距和外边距不会影响内容区域的尺寸，但是会增加元素框的总尺寸。
- IE盒子模型中，width和height指的是内容区域+border+padding的宽度和高度。