---
title: SASS的基本用法
tags: ["sass"]
date: 2017-05-16 14:50:00
categories:
- 前端
- CSS
- sass
---
> SCSS是目前最流行的一门CSS方言。没办法，只能耍耍了...

<!-- more -->
## 基本用法
### 变量
```CSS
  /* 定义变量 */
  $blue : #1875e7;
　div {
　　color : $blue;
　}
  /* 如果变量要用在字符串中，需要用“#{}”包裹起来。类似ES6的语法 */
  $side : left;
    .rounded {
    border-#{$side}-radius: 5px;
  }
```
### 计算功能
```CSS
  /* 类似cals计算，单位可以混用 */
  body {
  　margin: (14px/2);
  　top: 50px + 100px;
  　right: $var * 10%;
  }
```
### 嵌套
```CSS
  /* 这种在小组件中再好不过了 */
  div {
  　hi {
  　　color:red;
  　}
  }
```
#### 属性嵌套
```CSS
  p {
    /* 注意：border后面有冒号 */
  　border: {
  　　color: red;
  　}
  }
```
在嵌套的代码块内，可以使用&引用父元素。比如a:hover伪类，可以写成：
```CSS
  a {
  　&:hover { color: #ffb3ff; }
  }
```
### 注释
- 标准的CSS注释 `/* comment */` ，会保留到编译后的文件。
- 单行注释 `// comment`，只保留在SASS源文件中，编译后被省略。
- `/*! comment */` 表示这是"重要注释"。即使是压缩模式编译，也会保留这行注释，通常可以用于声明版权信息。

## 代码的重用
### 继承
SASS允许一个选择器，继承另一个选择器。比如，现有class1：
```CSS
  .class1 {
  　border: 1px solid #ddd;
  }
  /* class2要继承class1，就要使用@extend命令 */
  .class2 {
  　@extend .class1;
  　font-size:120%;
  }
```
### Mixin
Mixin有点像C语言的宏（macro），是可以重用的代码块。
```CSS
  /* 使用@mixin命令，定义一个代码块（可以指定参数和缺省值） */
  @mixin left($value: 10px) {
　　float: left;
　　margin-right: $value;
　}
  /* 使用@include命令，调用这个mixin */
  div {
　　@include left(20px);
　}
```
下面是一个mixin的实例，用来生成浏览器前缀：
```CSS
  @mixin rounded($vert, $horz, $radius: 10px) {
  　border-#{$vert}-#{$horz}-radius: $radius;
  　-moz-border-radius-#{$vert}#{$horz}: $radius;
  　-webkit-border-#{$vert}-#{$horz}-radius: $radius;
  }
  /* 调用 */
  #navbar li { @include rounded(top, left); }
  #footer { @include rounded(top, left, 5px); }
```
### 颜色函数
SASS提供了一些内置的颜色函数，以便生成系列颜色。
- lighten(#cc3, 10%) // #d6d65c
- darken(#cc3, 10%) // #a3a329
- grayscale(#cc3) // #808080
- complement(#cc3) // #33c

### 插入文件
```CSS
  @import "path/filename.scss";
  /* 如果插入的是.css文件，则等同于css的import命令 */
  @import "foo.css";
```
## 高级用法
### 条件语句
```CSS
  @if lightness($color) > 30% {
  　background-color: #000;
  } @else {
  　background-color: #fff;
  }
```
### 循环语句
```CSS
  /* for循环 */
  @for $i from 1 to 10 {
  　.border-#{$i} {
  　border: #{$i}px solid blue;
  　}
  }
  /* while循环 */
  $i: 6;
　@while $i > 0 {
　　.item-#{$i} { width: 2em * $i; }
　　$i: $i - 2;
　}
  /* each命令，作用与for类似 */
  @each $member in a, b, c, d {
　　.#{$member} {
　　　background-image: url("/image/#{$member}.jpg");
　　}
　}
```
### 自定义函数
```CSS
  @function double($n) {
　　@return $n * 2;
　}
  /* 调用 */
　#sidebar {
　　width: double(5px);
　}
```
