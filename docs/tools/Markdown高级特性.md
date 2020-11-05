# 前言

Markdown还有很多高级用法，我们可以学习一下。

# 字体

对文字进行编辑

```markdown
<font face="黑体">黑体</font>
<font face="微软雅黑">微软雅黑</font>
<font face="STCAIYUN">华文彩云</font>
<font color=#0099FF size=5 face="黑体">黑体</font>
<font color=#00FFFF size=4>NULL</font>
<font color=gray size=3 face="微软雅黑">微软雅黑</font>
```

效果如下：

<font face="黑体">黑体</font>
<font face="微软雅黑">微软雅黑</font>
<font face="STCAIYUN">华文彩云</font>
<font color=#0099FF size=5 face="黑体">黑体</font>
<font color=#00FFFF size=4>NULL</font>
<font color=gray size=3 face="微软雅黑">微软雅黑</font>

Markdown本身不支持背景色，可以采用HTML实现：

```markdown
<table><tr><td bgcolor=red>背景色</td></tr></table>
```

效果如下：

<table><tr><td bgcolor=red>背景色</td></tr></table>

# 图片处理

Markdown插入图片的语法是： `![图片描述](图片链接)`。但是，有时候我们需要自定义图片的尺寸，就不能使用这种语法了，索性Markdown支持HTML，我们可以使用 `<img>` 来插入图片并自定义尺寸并居中处理：

```html
<div align=center>
	<img width='300' height='150' src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
u=702257389,1274025419&fm=27&gp=0.jpg" referrerpolicy="on-referrer" alt="区块链">
</div>
```

效果：

<div align=center>
	<img width='300' height='150' src="https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/
u=702257389,1274025419&fm=27&gp=0.jpg" referrerpolicy="on-referrer" alt="区块链">
</div>

# 插入音乐

在网易云音乐里找生成外连接，复制过来就OK。如果不想自动播放，可以把 `auto` 改成1。

```html
<div align=center><iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=30375690&auto=1&height=66"></iframe></div>
```

<div align=center><iframe frameborder="no" border="0" marginwidth="0" marginheight="0" width=330 height=86 src="//music.163.com/outchain/player?type=2&id=30375690&auto=1&height=66"></iframe></div>

# 插入视频

在视频分享附近找到嵌入代码或通用代码，复制过来就OK。

<iframe width="560" height="315" src="https://www.youtube.com/embed/Ilg3gGewQ5U" frameborder="0" allowfullscreen></iframe>

# 支持Latex

Markdown支持Latex，

# 流程图

# 时序图

# 甘特图