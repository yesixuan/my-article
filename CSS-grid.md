---
title: 拥抱未来的web布局————grid
tags: ["css"]
date: 2017-10-07 10:09:00
categories:
- 前端
- CSS
---
> 一直以来，许多css框架都在帮我们实现一个栅格系统来帮助我们布局页面，当grid标准以一种更优雅的方式来帮我们实现栅格系统...

<!-- more -->
## 容器属性

### 声明
```css
.box {
	display: grid | inline-grid | subgrid
;}
```

### 分地
```css
.box {
	/* 可以在每个长度数值两边加上网格线的别名 */
	grid-template-columns: 40px 50px auto 50px 40px;
	grid-template-rows: 25% 100px auto;
}
/* 使用repeat简化代码 */
.box {
	grid-template-columns: repeat(3, 20px [col-start]) 5%;
}
/* 等价于 */
.box {
	grid-template-columns: 20px [col-start] 20px [col-start] 20px [col-start] 5%;
}
/* fr是一个特殊的单位，它可以类似于设定flex-grow时，给网格容器中的自由空间设置为一个份数，举个例子，下面的例子将把网格容器的每个子项设置为三分之一 */
.box {
	grid-template-columns: 1fr 50px 1fr 1fr;
}
```

### 网格间距
```css
.box {
	grid-column-gap: 10px;
	grid-row-gap: 10px;
}
```

### 内容相对于子项的布局
实际上在使用中可能出现这种情况：网格总大小比它的网格容器的容量小，导致这个问题的原因可能是所有网格子项都使用了固定值，比如px。justify-content属性定义了网格和网格容器列轴对齐方式（和align-content相反，它是和行轴对齐）。

```css
/* 水平方向 */
.box {
	justify-content: start | end | center | stretch | space-around | space-between | space-evenly;
}
/* 垂直方向 */
.box {
	align-content: start | end | center | stretch | space-around | space-between | space-evenly;
}
```

### 子项相对于其父元素的布局
```css
/* justify-items定义了网格子项的内容和列轴对齐方式，即水平方向上的对齐方式。 */
.box {
	justify-items: start | end | center | stretch（默认）;
}
/* 类似于justify-items，align-items定义了网格子项的内容和行轴对齐方式，即垂直方向上的对齐方式。 */
.box {
	align-items: start | end | center | stretch（默认）;
}
```


## 子项属性

### 子项占地
子项占地有两种方式：  
第一种方式是命名标记（我自己的表达），意思就是给子项取独一无二的名字（如grid-area: nav），然后在容器元素的单元格中打上不同子项的烙印，拥有相同标记的单元格自然隶属于同一个子项；  
第二种方式是边界标记（我自己的表达），同样是在单元格被划分好之后，子项通过划定横轴的跨度与纵轴跨度可以确定一个矩形区域，这个区域便是子项的地盘。

#### 命名标记
```css
/* 父元素中 */
.grid {
	display: grid;
	grid-template-columns: 100px 100px 100px 100px 100px;
	grid-template-rows: 100px 100px 100px 100px;
	grid-template-areas:  'title  title  title  title  aside'
												'nav    main   main   main   aside'
												'nav    main   main   main   aside'
												'footer footer footer footer footer';

/* 子项中 */
.item1 {
	grid-area: title;
}
.item2 {
	grid-area: nav;
}
.item3 {
	grid-area: main;
}
.item4 {
	grid-area: aside;
}
.item5 {
	grid-area: footer;
}
```

#### 边界标记
```css
.container {
  display: grid;
  /* 这里使用另一种方式来划分单元格 */
  /*grid-template-columns: 60px 60px 60px 60px 60px;
  grid-template-rows: 30px 30px;*/
  grid-auto-columns: 300px 300px 300px 300px;
  grid-auto-rows: 300px;
  /* 这个属性用来定义没有明确定义边界的子项是横排还是竖排 */
  grid-auto-flow: row;
}
.item-a{
	/* 这里的数字是网格线默认的名字，你也可以自定义网格线名字 */
	/* 当你的跨度仅为一个单元格时，可以只声明起始线， */
	/* 等同于grid-column: 1 / 2; */
  grid-column: 1;
  grid-row: 1 / 3;
}
.item-f{
  grid-column: 2 / 4;
  grid-row: 1 / 3;
}
.item-c{
  grid-column: 4;
  grid-row: 1 / 3;
}
```

### 子项内部的布局
`justify-content`与`align-content`是在父元素上宏观调控其下每个子元素中内容的布局方式，`justify-items`是精细化的针对每个子元素进行设定。

```css
.item-1 {
	justify-self: start | end | center | stretch;
	align-self: start | end | center | stretch;
}
```

## 更多

### 简写
grid-column与grid-row表示grid-column-start + grid-column-end，和grid-row-start + grid-row-end的简写形式。

```css
.item-c {
	/* 终止网格线可以用span来表示跨多少单元格 */
	grid-column: 3 / span 2;
	grid-row: third-line / 4;
}
/* 等同于 */
.item-c {
	grid-column-start: 3;
	grid-column-end: span 2;
	grid-row-start: third-line;
	grid-row-end: 4;
}
```


grid-area调用grid-template-areas属性创建的模板。同时，这个属性也可以是grid-row-start+ grid-column-start + grid-row-end+ grid-column-end的缩写（*推荐写法*）。

```css
.item {
	/* <name>是显示网格使用的值，搭配父容器的grid-template-areas属性来使用 */
	grid-area: <name> | <row-start> / <column-start> / <row-end> / <column-end>;
}
```
注意：它的参数的顺序为：<row-start> / <column-start> / <row-end> / <column-end>。

### 最佳实践（个人认为）
```css
/* 父容器划分网格（不推荐简写） */
.container {
	display: grid;
	grid-template-columns: 100px 100px 100px;
	grid-template-rows: 100px 100px 100px;
}
/* 子项占地推荐简写 */
.item-1 {
  grid-area: 1 / 1 / 2 / 3;
}
.item-2 {
  grid-area: 1 / 3 / 3 / 4;
}
.item-3 {
  grid-area: 3 / 2 / 3 / 4;
}
.item-4 {
  grid-area: 2 / 1 / 4 / 2;
}
.item-5 {
  grid-area: 2 / 2 / 3 / 3;
}
```
