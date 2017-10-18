---
title: JS实现继承的几种方式
tags: ["js"]
date: 2017-07-28 13:42:00
categories:
- 前端
- JS
---
> 多年以来，JS开发者一直期待JS能够实现像java那样的继承...

<!-- more -->
## 原型链继承
原型链继承基本思想就是让一个原型对象指向另一个类型的实例。  
```js
// 父类构造函数
function SuperType() {
  this.prop = true
}
// 父类原型对象
SuperType.prototype.getSuperValue = function () {
  return this.prop
}
// 子类构造函数
function SubType() {
  this.subprop = false
}
// 实现继承
SubType.prototype = new SuperType()
// 子类原型对象的自有方法
SubType.prototype.getSubValue = function () {
  return this.subprop
}
// 创建子类实例
var instance = new SubType()
console.log(instance.getSuperValue()) // true
```   

小蝌蚪找妈妈：子类实例-->子类构造函数-->子类原型对象-->父类实例-->父类构造函数-->父类原型对象。  
注意点：给子类原型对象添加自有方法时只能用打点的方式添加，而不能用对象字面亮的方式添加。不然会切断子类原型与父类实例的联系，也就不能认贼做父了。  
缺陷：所有子类实例都将共享父类中的属性，而当这个属性值是引用类型值时，一处改变将影响每一处。

## 构造函数继承
此方法为了解决原型中包含引用类型值所带来的问题。  
这种方法的思想就是在子类构造函数的内部调用父类构造函数，可以借助apply()和call()方法来改变对象的执行上下文。  
  
```js
function SuperType() {
  this.colors = ['red', 'blue', 'green']
}
function SubType() {
  // 继承SuperType
  SuperType.call(this)
}
var instance1 = new SubType()
var instance2 = new SubType()
instance1.colors.push('black')
console.log(instance1.colors)  // ["red", "blue", "green", "black"]
console.log(instance2.colors) // ["red", "blue", "green"]
```
缺陷：如果仅仅借助构造函数，方法都在构造函数中定义，因此函数无法达到复用。    

## 组合继承(原型链+构造函数)
使用原型链实现对原型属性和方法的继承，而通过借用构造函数来实现对实例属性的继承。  
这样，既通过在原型上定义方法实现了函数复用，又能够保证每个实例都有它自己的属性。
```js
// 父类
function SuperType(name) {
  this.name = name
  this.colors = ['red', 'blue', 'green']
}
// 父类原型对象
SuperType.prototype.sayName = function () {
  console.log(this.name)
}
// 子类
function SubType(name, job) {
  // 继承属性
  SuperType.call(this, name)
  // 子有属性
  this.job = job
}
/* 实现继承 */
// 子类原型与父类实例勾搭
SubType.prototype = new SuperType()
// 子类原型的构造函数与父类构造函数勾搭
SubType.prototype.constructor = SuperType
// 子类原型的自有方法
SubType.prototype.sayJob = function() {
  console.log(this.job)
}
var instance1 = new SubType('Jiang', 'student')
instance1.colors.push('black')
console.log(instance1.colors) //["red", "blue", "green", "black"]
instance1.sayName() // 'Jiang'
instance1.sayJob()  // 'student'
var instance2 = new SubType('J', 'doctor')
console.log(instance2.colors) // //["red", "blue", "green"]
instance2.sayName()  // 'J'
instance2.sayJob()  // 'doctor'
```

## 寄生继承的函数封装
```js
function inheritPrototype(subType, superType) {
  var prototype = Object.create(superType.prototype)
  prototype.constructor = subType
  subType.prototype = prototype
}
```
该函数实现了寄生组合继承的最简单形式。  
这个函数接受两个参数，一个子类，一个父类。  
第一步创建父类原型的副本，第二步将创建的副本添加constructor属性，第三部将子类的原型指向这个副本。

## ES6的 Object.setPrototypeOf 方法实现继承
```js
// 继承
Object.setPrototypeOf(SubType.prototype, SuperType.prototype)
console.log(SubType.prototype.constructor === SubType) // true
```
