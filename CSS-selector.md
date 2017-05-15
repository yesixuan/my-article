---
title: CSS选择器
tags: ["CSS"]
date: 2017-05-15 12:24:00
categories:
- 前端
- CSS
- selector
---
> 高级的css选择器虽说效率不怎么高，但是确实很强大。在一些不用太过在意性能的情况下用起来还是很爽的...

<!-- more -->
### 属性选择器
示例：
```CSS
  p[name='test'] {
    //
  }
```
### 伪选择器
#### nth-child
示例：
```CSS
  ul li:nth-child(1) {
    //
  }
```
> nth-child()括号里面可以是数字、n、odd、even，个人感觉还是n最强大。还有两个与之相关的选择器：first-child和last-child。

#### nth-of-type
:nth-of-type类似于:nth-child，不同的是他只计算选择器中指定的那个元素,其实我们前面的实例都是指定了具体的元素，这个选择器主要对用来定位元素中包含了好多不同类型的元素是很有用处，比如说，我们div.demo下有好多p元素，li元素，img元素等，但我只需要选择p元素，并让他每隔一个p元素就有不同的样式，那我们就可以简单的写成：
```CSS
  .demo p:nth-of-type(even) {background-color: lime;}
```
#### 动态伪类
动态伪类，因为这些伪类并不存在于HTML中,而只有当用户和网站交互的时候才能体现出来，动态伪类包含两种，第一种是我们在链接中常看到的锚点伪类，如":link",":visited";另外一种被称作用户行为伪类，如“:hover”,":active"和":focus"。
示例如下：
```CSS
  .demo a:link {color:gray;}/*链接没有被访问时前景色为灰色*/
  .demo a:visited{color:yellow;}/*链接被访问过后前景色为黄色*/
  .demo a:hover{color:green;}/*鼠标悬浮在链接上时前景色为绿色*/
  .demo a:active{color:blue;}/*鼠标点中激活链接那一下前景色为蓝色*/
```
#### UI元素状态伪类
:checked、:unchecked、:disabled、:enabled
这几个选择器在我们作表单验证时改变表单样式时特别有用。
#### 否定选择器
示例：
```CSS
  input:not([type="submit"]) {border: 1px solid red;}
```
### 伪元素
#### ::first-line
选择元素的第一行。
比如说改变每个段落的第一行文本的样式，我们就可以使用这个：
```CSS
  p::first-line {font-weight:bold;}
```
#### ::first-letter
选择文本块的第一个字母。
比如说首字下沉：
```CSS
  p::first-letter {font-size: 56px;float:left;margin-right:3px;}
```
#### ::before和::after
这两个主要用来给元素的前面或后面插入内容，这两个常用"content"配合使用。
比如清除浮动：
```CSS
  .clearfix:before,
  .clearfix:after {
     content: ".";
     display: block;
     height: 0;
     visibility: hidden;
  }
  .clearfix:after {clear: both;}
  .clearfix {zoom: 1;}
```
