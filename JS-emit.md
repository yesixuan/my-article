---
title: JS观察者模式
tags: ["js","设计模式"]
date: 2017-07-11 09:52:00
categories:
- 前端
- JS
- 设计模式
---
> 观察者模式是一种非常经典的设计模式，本文是JS方式的实现...

<!-- more -->
## 自定义事件对象
```js
// 用于创建事件对象的类
function Person(name) {
  this.name = name
  // 私有属性，用于管理事件名称对应的回调列表
  this._events = {}
}
/* 如果有这个事件名，则将传进来的回调放至回调数组尾部；如果没有，则创建 */
Person.prototype.on = function(eventName, callback) {
  if(this._events[eventName]) {
    this._events[eventName].push(callback)
  }else {
    this._events[eventName] = [callback]
  }
}
/* 拿到事件名称对应的回调列表，依次执行这些回调 */
Person.prototype.emit = function(eventName) {
  // 拿到除事件名以外的其他参数，传至每一个回调
  var args = Array.prototype.slice.call(arguments, 1)
  var callbacks = this._events[eventName]
  var self = this
  callbacks.forEach(function(callback) {
    callback.apply(self, args)
  })
}
/* 解除订阅，这里的回调需要是引用外部的函数，而不是直接写函数体 */
Person.prototype.off = function(eventName, callback) {
  this._events[eventName] = this._events[eventName].filter(function(item) {
    return item != callback
  })
}
var girl = new Person()
function test(he) {
  console.log('记得撩起',he)
}
girl.on('待你长发及腰', function(he) {
  console.log('娶你可好？',he)
})
girl.on('待你长发及腰', test)
girl.emit('待你长发及腰','hehe')
girl.off('待你长发及腰', test)
girl.emit('待你长发及腰','hehe')
```
