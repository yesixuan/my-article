---
title: angular4生命周期
tags: ["js","angular","生命周期"]
date: 2017-06-05 08:45:00
categories:
- 前端
- JS
- angular
---
> angular4官方提供了许多的生命周期钩子函数。在合适的钩子函数中做合适的事儿...

<!-- more -->
## 写在前头
angular4的生命周期钩子是其官方提供的接口。所以当我们要定义这些钩子函数时需要在模块中引入相应的接口，然后在组件的类中实现该接口。
钩子函数的名字是在接口的前面加上`on`。
## 数据准备阶段
### ngOnInit
当Angular初始化完成数据绑定的输入属性后，用来初始化指令或者组件。
### ngOnChanges
当Angular设置了一个被绑定的输入属性（输入属性）后触发。该回调方法会收到一个包含当前值和原值的changes对象。
### ngDoCheck
用来检测所有变化（无论是Angular本身能检测还是无法检测的），并作出相应行动。在每次执行“变更检测”时被调用。
注意：点击、调后台接口、表单处理、定时器都会触发这个钩子，所以我们在实现这个接口时必须要非常高效。
## 视图准备阶段
### ngAfterContentInit
当Angular把外来内容投影进自己的视图之后调用。
### ngAfterContentChecked
当Angular检查完那些投影到自己视图中的外来内容的数据绑定之后调用。
### ngAfterViewInit
在Angular创建完组件的视图后调用。
### ngAfterViewChecked
在Angular检查完组件视图中的绑定后调用。
