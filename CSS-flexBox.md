---
title: Flex布局
tags: ["flex"]
date: 2017-05-16 09:29:00
categories:
- 前端
- CSS
---
> Flex布局是必然会代替现行Div+CSS布局的一种布局方式。在现代浏览器中基本都支持...

<!-- more -->
### 基本概念
采用Flex布局的元素，称为Flex容器（flex container），简称”容器”。它的所有子元素自动成为容器成员，称为Flex项目（flex item），简称”项目”。
![基本概念图](http://mmbiz.qpic.cn/mmbiz/zPh0erYjkib0PY55r4g0ADOFbKwLHgbrgZgf6JhicNWZA6MQZoDot2eNvXJjfCJrXVz95nsv909LY6Zw8UdjI24g/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1)
容器默认存在两根轴：水平的主轴（main axis）和垂直的交叉轴（cross axis）。主轴的开始位置（与边框的交叉点）叫做main start，结束位置叫做main end；交叉轴的开始位置叫做cross start，结束位置叫做cross end。
### 指定容器为Flex布局
```CSS
  /*设为Flex布局以后，子元素的float、clear和vertical-align属性将失效。*/
  .box{
    display: flex;
    display: inline-flex;
    display: -webkit-flex; /* Safari */
  }
```
### 容器的属性
#### flex-direction属性
flex-direction属性决定主轴的方向（即项目的排列方向）。
```CSS
  .box {
    /*默认是row*/
    flex-direction: column-reverse | column | row | row-reverse;
  }
```
![flex-direction属性](http://mmbiz.qpic.cn/mmbiz/zPh0erYjkib0PY55r4g0ADOFbKwLHgbrgr79qBwJlcJP5g1ojric6d74HFibWGRWcpuO0ycPXOnstHdIPnx75OodA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1)
#### flex-wrap属性
默认情况下，项目都排在一条线（又称”轴线”）上。flex-wrap属性定义，如果一条轴线排不下，如何换行。
```CSS
  .box{
    flex-wrap: nowrap(不换行，默认) | wrap(换行) | wrap-reverse(换行反向);
  }
```
#### flex-flow
flex-flow属性是flex-direction属性和flex-wrap属性的简写形式，默认值为row nowrap。
#### justify-content属性
justify-content属性定义了项目在主轴上的对齐方式。
```CSS
  .box {
    justify-content: flex-start(默认) | flex-end | center | space-between | space-around;
  }
```
![justify-content属性](http://mmbiz.qpic.cn/mmbiz/zPh0erYjkib0PY55r4g0ADOFbKwLHgbrgWgibPpIahp2micKibiaAbJRDpMQRuQoAI7sjuDYubWsX9tj3CyKyH46ejw/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1)
#### align-items属性
align-items属性定义项目在交叉轴上如何对齐。
```CSS
  .box {
    align-items: flex-start(默认) | flex-end | center | baseline | stretch;
  }
```
[](http://mmbiz.qpic.cn/mmbiz/zPh0erYjkib0PY55r4g0ADOFbKwLHgbrgIIkHnLoWhXmV1l2AWJIAgo81t5mGAbicyg8ibRVRso6j8PDjCD91L3yQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1)
#### align-content属性
align-content属性定义了多根轴线的对齐方式。如果项目只有一根轴线，该属性不起作用。（子项目占据超过一行）
```CSS
  .box {
    align-content: flex-start | flex-end | center | space-between | space-around | stretch;
  }
```
- flex-start：与交叉轴的起点对齐。
- flex-end：与交叉轴的终点对齐。
- center：与交叉轴的中点对齐。
- space-between：与交叉轴两端对齐，轴线之间的间隔平均分布。
- space-around：每根轴线两侧的间隔都相等。所以，轴线之间的间隔比轴线与边框的间隔大一倍。
- stretch（默认值）：轴线占满整个交叉轴。

### 项目的属性
#### order属性
order属性定义项目的排列顺序。数值越小，排列越靠前，默认为0。
#### flex-grow属性
flex-grow属性定义项目的放大比例，默认为0，即如果存在剩余空间，也不放大。
如果所有项目的flex-grow属性都为1，则它们将等分剩余空间（如果有的话）。如果一个项目的flex-grow属性为2，其他项目都为1，则前者占据的剩余空间将比其他项多一倍。
#### flex-shrink属性
flex-shrink属性定义了项目的缩小比例，默认为1，即如果空间不足，该项目将缩小。
如果所有项目的flex-shrink属性都为1，当空间不足时，都将等比例缩小。如果一个项目的flex-shrink属性为0，其他项目都为1，则空间不足时，前者不缩小。
负值对该属性无效。
#### flex-basis属性
flex-basis属性定义了在分配多余空间之前，项目占据的主轴空间（main size）。浏览器根据这个属性，计算主轴是否有多余空间。它的默认值为auto，即项目的本来大小。
```CSS
  .item {
    flex-basis: <length> | auto; /* default auto */
  }
```
它可以设为跟width或height属性一样的值（比如350px），则项目将占据固定空间。
#### flex属性
flex属性是flex-grow, flex-shrink 和 flex-basis的简写，默认值为0 1 auto。后两个属性可选。
```CSS
  .item {
    flex: none | [ <'flex-grow'> <'flex-shrink'>? || <'flex-basis'> ]
  }
```
该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。
#### align-self属性
align-self属性允许单个项目有与其他项目不一样的对齐方式，可覆盖align-items属性。默认值为auto，表示继承父元素的align-items属性，如果没有父元素，则等同于stretch。
```CSS
  .item {
    align-self: auto | flex-start | flex-end | center | baseline | stretch;
  }
```
![align-self属性](http://mmbiz.qpic.cn/mmbiz/zPh0erYjkib0PY55r4g0ADOFbKwLHgbrgibEKHCULaFNxmos63ichpsdCnavoCr49h1qIk7clz22Ev7TjFE5syib6g/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1)

### 最佳实践
1. 声明flex容器；  
2. 将项目排列方向与是否折行这两个属性合并到`flex-flow`中写；  
3. 定义所有子项目在容器中的横向排布以及纵向排布（只有项目超过一行，声明纵向布局才有意义）；  
4. 通过定义`align-items`属性来声明每个项目在各自所属的那行空间的纵向排布；  
5. 定义项目的`flex`属性来确定项目在空间多余、空间缺乏和自身的原始宽度；  
6. 定义`align-self`属性，其功效类似容器中的`align-items`属性。只不过前者作用于所有项目，后者针对当前子项目（如果需要某个子项的行为与与其一行的兄弟不同的话）。  

```css
.container{
  display: flex;
  /* 方向可选值：row(默认) | column-reverse | column | row | row-reverse */
  flex-flow: row no-wrap;
  /* 可选值为：flex-start(默认) | flex-end | center | space-between | space-around */
  justify-content: space-around;
  align-content: space-around;
  /* 可选值为：flex-start(默认) | flex-end | center | baseline | stretch */
  align-items: flex-start;
}
.item{
  /* 扩展默认值为0（不扩展），收缩默认值为1（收缩），宽度默认值为auto */
  flex: 1 0 20%;
  align-self: auto | flex-start | flex-end | center | baseline | stretch;
}
```

