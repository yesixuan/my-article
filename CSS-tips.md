---
title: CSS小技巧
tags: ["CSS"]
date: 2017-05-17 18:08:00
categories:
- 前端
- CSS
- skill
---
> 一些CSS的小技巧往往能够发挥出巨大的能量。本文转载至[前端开发者应该知道的CSS小技巧](http://mp.weixin.qq.com/s?__biz=MzAxODE2MjM1MA==&mid=402270468&idx=1&sn=d3d25897a6135f764b3fd5bc764fbc77&scene=21#wechat_redirect)...

<!-- more -->
### 使用:not()去除导航上不需要的边框
```CSS
  /* 只保留导航列表最后一项的右边框 */
  .nav li:not(:last-child) {
     border-right: 1px solid #666;
   }
```
### 为body添加行高
```CSS
  /* 这种方式下,文本元素可以很容易从body继承 */
  body {
    line-height: 1;
  }
```
### 垂直居中任何元素
```CSS
  html, body {
    height: 100%;
    margin: 0;
  }
  body {
    -webkit-align-items: center;  
    -ms-flex-align: center;  
    align-items: center;
    display: -webkit-flex;
    display: flex;
  }
```
### 逗号分离的列表
```CSS
  /* 使用伪类:not() ，这样最后一个元素不会被添加逗号 */
  ul > li:not(:last-child)::after {
    content: ",";
  }
```
### 使用负nth-child选择元素
```CSS
  /* 选择1到3的元素并显示 */
  li:not(:nth-child(-n+3)){
    display: none;
  }
```
### 使用SVG图标
SVG对所有分辨率类型具有良好的伸缩性，IE9以上的所有浏览器都支持。
```CSS
  /* 如果你使用SVG图标按钮，同时SVG加载失败，下面能帮助你保持可访问性 */
  .no-svg .icon-only:after {
    content: attr(aria-label);
  }
```
### 文本显示优化
```CSS
  html {
    -moz-osx-font-smoothing: grayscale;
    -webkit-font-smoothing: antialiased;
    text-rendering: optimizeLegibility;
  }
```
### 继承box-sizing
```CSS
  /* 从html继承box-sizing */
  html {
    box-sizing: border-box;
  }
  , :before, *:after {
    box-sizing: inherit;
  }
```
### 表格单元格等宽
```CSS
  /* 无痛表格布局 */
  .calendar {
    table-layout: fixed;
  }
```
