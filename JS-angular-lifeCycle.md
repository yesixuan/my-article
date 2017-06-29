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
- 当Angular设置了一个被绑定的输入属性（输入属性）后触发。该回调方法会收到一个包含当前值和原值的changes对象；
- 当输入属性值发生变化，但是视图没有响应时，需要用到这个钩子；
- 父组件初始化子组件的输入属性时会被调用一次；
- 构造函数阶段输入属性为空，ngOnInit()时才会有值；
- 子组件的输入属性（基础数据类型）变化时触发这个钩子（引用类型不会触发）；
- 虽然引用类型不会触发钩子，但是angular的变更监测机制会检测到，并且同步数据；
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

### afterViewInit()与afterViewChecked()
- 子组件试图初始化并且试图变更监测完之后，父组件再进行试图初始化和试图变更监测；
- 父组件通过模版本地变量调用子组件中的方法时被调用；
- 调用子组件方法时也许不会触发视图初始化，但肯定会调用检测钩子；
- 不能在这两个钩子中改变视图中绑定的东西（可以通过定时器来解决）
### afterContentInit()与afterContentChecked()
- 与上面两个钩子类似，一个是说的整个视图，一个是说的投影内容；
- 在父组件的子组件标签中间可以定义要投影的内容（用class来区分多个），子组件用[ngCntent]来占位；
- 投影内容只能绑定父组件中的值；
- 调用顺序是父组件投影内容-->子组件初始化-->父组件初始化；
- 在这个钩子里还可以改变模版中绑定的内容，因为此时视图还为初始化