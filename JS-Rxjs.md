---
title: Rxjs入门
tags: ["JS","编程思想"]
date: 2017-06-28 08:37:00
categories:
- 前端
- JS
---
> 初识Rxjs是在angular2的学习过程中。那时的理解起来还是非常晦涩，现在也不能说理解有多深，姑且写下来吧...

<!-- more -->
## 概况
Rxjs是响应式编程思想在JS中的一种实现。那么Rxjs到底是个什么东西呢？装逼地讲，Rxjs是一种融合了函数式编程、观察者模式的以操作`流`为核心的一种编程思想。
Rxjs的适用场景为异步数据流。针对异步的处理是采用观察者模式。针对数据流，我们使用函数式编程中的状态集中管理的理念。
简单说说对面向对象编程与函数式编程的理解吧：
面向对象就是对数据的封装，将具有相关性的数据和方法封装到一个个对象中，以便管理；
函数式编程实际上就是对数据操作的封装，我们只关心数据的输入与输出，至于输入的数据经过了一些什么样的操作最终变成输出的数据，我们是不必在乎的。

## 核心概念一览
### Observable
Observable是事件流的源，相当于观察者模式中的被观察者。可以发射一个个的事件、数据等流。
### Operator
Operator是Observable的操作符。体现了函数式编程和迭代器模式的思想。通过各种转变，将Observable流转变为新的Observable流。
### Observer
对Observable对象发出的每个事件进行响应。
### subscribe()
subscribe()方法用来订阅Observable流。该方法会接受一个observe作为参数，每当observable完成并发送一个事件时，该事件就会被observer所捕获，进入到observer对应的回调函数中。
### Subscription
Observable对象被订阅后返回的Subscription实例（可取消订阅）。
### Subject
EventEmitter的等价数据结构，可以当做Observable被监听，也可以作为Observer发送新的事件。

## 一个完整的Rx流
### 创建Observable对象
将一系列的异步事件封装到Observable流里边。
常用的Observable对象创建方式除了new Observable()还有实用的Observable.fromEvent()和Observable.create()。
Observable.fromEvent()：
```js
  let button = document.querySelector('button')
  Rx.Observable.fromEvent(button, 'click') // 返回一个Observable对象
  .subscribe(() => console.log('Clicked!'))
```  
Observable.create()：
```js
  Rx.Observable.create(observer => {
    getData(data => {
      observer.next(data)
      observer.complete()
    })
  })
  .subscribe(data => { doSomething(data) })
```
### 对Observable流进行各种花式操作
#### map变换操作符
只返回我们所关心的数据
```js
  observable.map(res => {
    return res.data
  }).subscribe(data => {
    doSomething(data)
  })
```
#### filter过滤操作符
过滤掉一些无用数据
```js
  /* 值为true时，才输出该值 */
  observable.filter(res => {
    return !!res.data && res.status == 200
  })
```
#### forkJoin组合操作符
某些场景是需要等到两个操作都完成之后才进行的
```js
  Rx.Observable.forkJoin(getFirstDatas, getSecondDatas)
  .subscribe(datas => {
    // datas[0]是getFirstDatas的数据
    // datas[1]是getSecondDatas的数据
  })
```
#### concatMap组合操作符
某次数据请求依赖前一次请求的结果
```js
  let getFirstDatas = Rx.Observable.create(observer => {
    // next可以执行一个异步操作，也可以在异步操作后next出异步操作的结果
    observer.next(getFirstData())
    observer.complete()
  })
  let createSecondDatas = function(firstData) {
    return Rx.Observable.create(observer => {
      getSecondData(firstData, secondData) => {
        observer.next(secondData)
        observer.complete()
      }
    })
  }
  getFirstDatas.concatMap(fristData => {
    return createSecondDatas(firstData)
  }).subscribe(secondData => {
    doSomething(secondData)
  })
```
#### 工具操作符
- timeout(): 超过指定的时间没有拿到数据就抛出异常
- debounceTime(): 防止抖动
- switchMap(): 保证前端拿到的数据是有序的
```js
  // ...
  .switchMap(event => getRecommend(event.target.value))
```

## 参考文档
[RxJS中文文档](http://cn.rx.js.org/)
