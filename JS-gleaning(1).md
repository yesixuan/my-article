---
title: JS gleaning
tags: ["JS 拾遗","js"]
date: 2017-04-21 15:34:42
categories:
- 前端
- JS
---
### JS数据类型
* 分为三大类：原始型、合成型、特殊型
* 合成型包含狭义对象、数组、函数
<!-- more -->

### JS数据类型的判断
* `typeof` 运算符检测对象、数组、null都是返回的`obj`
* `obj.prototype.toString.call(arg) === '[object Array]'`可以区别对象与数组
* `String()` 强制类型转换可以区别对象与数组
* `JSON.stringify()` 可判断一个对象是不是空对象

### JS函数
* 常规函数会申明提前，`var`出来的函数不会
* 函数a、b都是在外部申明，如果调用a时传入b，b用了a里面的变量，此时会报错
* 函数参数如果是原始类型，则传值方式为按值传递，可以用`window`属性的方式来绕过
* `arguments`有一个`callee`属性，指向它对应的原函数
* `JS`中，一切皆对象。运行环境也是对象，所以函数都是在某个对象之中运行，`this`就是这个对象（环境）
* `bind`方法用于将函数体内的`this`绑定到某个对象，然后返回一个新函数
