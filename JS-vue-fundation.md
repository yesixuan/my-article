---
title: VUE入门
tags: ["VUE"]
date: 2017-05-09 21:51:00
categories:
- 前端
- JS
- VUE
---
> 貌似最近耍react和redux耍得比较多，vue已经有了要淡忘的趋势。所以，赶紧的，vue耍起来...

<!-- more -->
### 前言
本文旨在提醒自己在vue中容易混淆和淡忘的点。所以此处略过vue的基础知识。
### Vue实例
Vue 实例暴露了一些有用的实例属性与方法。这些属性与方法都有前缀`$`，以便与代理的`data`属性区分。
### 插值语法
#### 添加含标签的插值
双大括号会将数据解释为纯文本，而非`HTML`。为了输出真正的`HTML`，你需要使用`v-html`指令。
#### 给标签属性绑定动态值
差值不能在标签里面使用，要在标签里面绑定动态值，需要使用`v-bind`指令。
```HTML
  <div v-bind:id="动态ID"></div>
```
### 指令
#### 指令的`:`
指令通过后面的`:`来接收参数（最常用的便是动态绑定标签属性）。
#### 指令的`.`
指令的`.`（修饰符）用于指出一个指令应该以某种特殊的方式绑定。
```HTML
  <form v-on:submit.prevent="onSubmit"></form>
```
#### 过滤器（管道）
过滤器允许用在两个地方：`mustache`插值和`v-bind`表达式。
它只适合处理文本转换，更加复杂的转换需要用到计算属性。
### 改变样式
#### 通过`class`改变样式
使用对象的形式绑定`class`
```JS
  // 这个对象可以直接写在ng-bind:class中，也可以写在data属性中，但是，放在计算属性中无疑是最强大的
  computed: {
    classObject: function () {
      return {
        active: this.isActive && !this.error,
        'text-danger': this.error && this.error.type === 'fatal',
      }
    }
  }
```
使用数组的形式绑定`class`
```HTML
  <!-- 数组里面使用三元表达式 -->
  <div v-bind:class="[isActive ? activeClass : '', errorClass]">
  <!-- 数组里面使用对象方式 -->
  <div v-bind:class="[{ active: isActive }, errorClass]">
```
### 通过内联样式改变样式
对象语法常常结合返回对象的计算属性使用。
`v-bind:style`的数组语法可以将多个样式对象应用到一个元素上。
### 列表渲染
#### 列表渲染中的参数
```HTML
  <!-- 循环数组 -->
  <div v-for="(item, index) in items">
    {{ index }} - {{ index  }}
  </div>
  <!-- 循环对象 -->
  <div v-for="(value, key, index) in object">
    {{ index }} - {{ key }} - {{ value }}
  </div>
```
#### 循环渲染大段内容
只需在这段内容的最外层包一层`<template> ~ </template>`标签即可。
#### 整数迭代 v-for
```HTML
    <span v-for="n in 10">{{ n }}</span>
```
#### 显示过滤/排序结果
```HTML
  <!-- evenNumbers是计算属性，这个属性过滤了某些值 -->
  <li v-for="n in evenNumbers">{{ n }}</li>
  <!-- even()这个方法过滤了某些值 -->
  <li v-for="n in even(numbers)">{{ n }}</li>
```
### 事件处理
#### 在事件处理函数中访问原生DOM对象
有时也需要在内联语句处理器中访问原生DOM事件。可以用特殊变量`$event`把它传入处理事件的回调函数。
#### 事件修饰符
直接上代码：
```HTML
  <!-- 阻止单击事件冒泡 -->
  <a v-on:click.stop="doThis"></a>
  <!-- 提交事件不再重载页面 -->
  <form v-on:submit.prevent="onSubmit"></form>
  <!-- 修饰符可以串联  -->
  <a v-on:click.stop.prevent="doThat"></a>
  <!-- 只有修饰符 -->
  <form v-on:submit.prevent></form>
  <!-- 添加事件侦听器时使用事件捕获模式 -->
  <div v-on:click.capture="doThis">...</div>
  <!-- 只当事件在该元素本身（而不是子元素）触发时触发回调 -->
  <div v-on:click.self="doThat">...</div>
  <!-- 点击事件将只会触发一次 -->
  <a v-on:click.once="doThis"></a>
```
#### 按键修饰符
```HTML
  <!-- 根据键值调用 vm.submit() -->
  <input @keyup.enter="submit">
  <!-- 这是一些别名.enter.tab.delete.esc.space.up.down.left.right -->
```
### 表单
#### 修饰符
```HTML
  <!-- 在 "change" 而不是 "input" 事件中更新 -->
  <input v-model.lazy="msg" >
  <!-- 将用户输入的值变为数字类型 -->
  <input v-model.number="age" type="number">
  <!-- 过滤用户输入的前后空格 -->
  <input v-model.trim="msg">
```
### 组件
#### 全局注册
```JS
  // 定义组件时推荐使用‘-’命名
  Vue.component('my-component', {
    // 选项
  })
```
#### 局部注册
```JS
  var Child = {
    template: '<div>A custom component!</div>'
  }
  new Vue({
    // ...
    components: {
      // <my-component> 将只在父模板可用
      'my-component': Child
    }
  })
```
#### `data`必须是函数
```JS
  // 每创建一个组件实例就返回新的data，不然会影响其他实例
  data: function () {
    return {
      counter: 0
    }
  }
```
#### 使用`Prop`传递数据
```JS
  Vue.component('child', {
    // 声明 props（这种方式与angular2蛮像的）
    props: ['message'],
    // 就像 data 一样，prop 可以用在模板内
    // 同样也可以在 vm 实例中像 “this.message” 这样使用
    template: '<span>{{ message }}</span>'
  })
```
#### `Prop`验证
```JS
  Vue.component('example', {
    props: {
      // 基础类型检测 （`null` 意思是任何类型都可以）
      propA: Number,
      // 多种类型
      propB: [String, Number],
      // 必传且是字符串
      propC: {
        type: String,
        required: true
      },
      // 数字，有默认值
      propD: {
        type: Number,
        default: 100
      },
      // 数组／对象的默认值应当由一个工厂函数返回
      propE: {
        type: Object,
        default: function () {
          return { message: 'hello' }
        }
      },
      // 自定义验证函数
      propF: {
        validator: function (value) {
          return value > 10
        }
      }
    }
  })
```
#### 使用`v-on`绑定自定义事件（子向父传参）
- 使用`$on(eventName)`监听事件。
- 使用`$emit(eventName)`触发事件。

#### 单个`Slot`
在父组件中传入一堆内容，这堆内容将会在子组件中的<slot></slot>标签位置上显示。这样可以让子组件呈现更多样化的展示。
#### 具名`Slot`
```HTML
  <!-- 这个h1标签将会被插入到name属性为header的slot标签上 -->
  <h1 slot="header">这里可能是一个页面标题</h1>
  <!-- 这个定义在自组件上 -->
  <slot name="header"></slot>
```
#### keep-alive
如果把切换出去的组件保留在内存中，可以保留它的状态或避免重新渲染。为此可以添加一个 keep-alive 指令参数。
