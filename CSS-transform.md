---
title: CSS3转换
tags: ["CSS"]
date: 2017-05-16 19:56:00
categories:
- 前端
- CSS
- transform
---
> CSS3的转换是能够启用浏览器硬件加速的一个属性，它也能代替我们常用的一些css属性。但人总有惰性，不愿意舍弃自己熟悉的东西而去使用一个不太熟的事物...

<!-- more -->
### 前言
通过CSS3转换，我们能够对元素进行移动、缩放、转动、拉长或拉伸。
### 2D转换
#### 概述
2D转换的属性名为transform，使用方法为transform:method(value)。
2D转换方法有translate、scale、rotate、skew、matrix，还有基于这些分支出的translateX、scaleY等。
#### translate(x,y)
```CSS
  .demo {
    /* 向右移50px，向下移100px */
    /* 分支属性即在方法名后面加大写的“X”或“Y” */
    transform:translate(50px,100px);
  }
```
#### rotate()
```CSS
  .demo {
    /* 顺时针旋转30度 */
    transform: rotate(30deg);
  }
```
#### scale(x,y)
scale方法的参数表示缩放的倍数。
当参数值为负值的时候，就出现里翻转现象（展开收起中的上下箭头就可以用这种方式来做）。
```CSS
  .demo {
    /* 该元素将会沿纵轴翻转 */
    transform:scale(-1,1);
  }
```
#### skew()
首先纠正一个误区：参数是坐标轴旋转度数，skewX单独使x轴旋转逆时针，skewY单独使y轴旋转顺时针。参考坐标系x轴向下正，y向右正。旋转x轴时候，平行于y轴的直线方向不变，依旧水平，但逐渐变短，平行于x的直线跟随x轴转动依旧平行于x轴，但是逐渐变长。反之亦然。
### 3D转换
