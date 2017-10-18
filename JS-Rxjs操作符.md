---
title: Rxjs操作符
tags: ["JS", "Rxjs"]
date: 2017-10-07 15:22:00
categories:
- 前端
- JS
---
> 在响应式编程的世界里，我们需要做的最多的便是处理应用在不同的携带数据以及逻辑的时间流中的变化规则，而这恰恰是Rxjs操作符在很大程度是帮我们简化和梳理的...

<!-- more -->

## 常见创建类操作符
创建类操作符可以说是连接传统数据与Rxjs的桥梁。其主要作用是将原始的数据类型转化为Observable类型。  

### from
可以把数组、Promise、以及Iterable转化为Observable.

```js
/* 如果是由Array转变为Observable，Array中的数据将会在一瞬间发射完成，没有时间间隔 */
Rx.Observable.from(xx: Array|Promise|Iterable)
```

### fromEvent
将事件转化为Observable.  

```js
/* 很多事件是一个无尽的事件流，所以永远不能激发completed状态 */
Rx.Observable.fromEvent(btn, 'eventName')
```

### of
接受一系列数据，并将它们发射出去。

### bindCallback
把回调API转化为返回Observable的函数。  
注意这里返回的还是一个函数，调用该函数才能得到Observable对象。

```js
// 这是一个接受回调的API
function hehe(x, callback) {
  setTimeout(() => {
    callback(x)
  }, 1000)
}
// 注意函数的参数'haha'是如何传递的
Rx.Observable.bindCallback(hehe)('haha')
  .subscribe(v => console.log(v))
// 一秒后输出：haha
```

### bindNodeCallback
就像是 bindCallback, 但是回调函数必须形如 callback(error, result)这样。

```js
Rx.Observable.bindNodeCallback(fs.readFile)('./roadNames.txt', 'utf8');
	.subscribe(x => console.log(x), e => console.error(e));
```
注意：如果回调函数能接受多个参数，则这些参数将会放到数组中被发射出去。

### interval
接受一个时间间隔，然后不停地从零开始累加发射数据。

```js
/* 拿到什么就发射什么，不管里边是对象还是函数（如果是函数的调用则返回函数调用后的结果） */
Rx.Observable.of(2, 8, [5,54], fn())
```


## 常见转换操作符

### map
用法非常灵活，许多其他的转换操作符都可以使用map来实现。  

```js
Rx.Observable.from([1,2,3,4,5])
  .map(v => v * 10)
```

### mapTo
有时候我们不关心Observable具体发射了什么数据，只需知道有一个数据被发射了就使用`mapTo`。它永远返回一个定值。  

```js
Rx.Observable.from([1,2,3,4,5])
  .mapTo(10)
```

### pluck
我们经常进行这样的操作，从一个大对象中取出其中的某个特定的字段。此时用到`pluck`.  

```js
Observable.pluck('data', 'id')
// 等价于下面这种写法
Observable.map(res => res.data.id)
```

## 常见累积操作符
许多时候，我们在监听到当前值时，还需要知道Observable之前发射的值。此时就需要用到累积操作符了。  

### scan
scan操作符的用法类似数组的reduce方法。  

```js
// scan不会改变流中数据的个数，只是让所有之前的数据参与当前的运算
Rx.Observable.from([1,2,3,4,5])
  .scan(((prev, curr) => prev + curr), 0)
  .subscribe(v => console.log(v))
// 1, 3, 6, 10, 15
```

### reduce

```js
// reduce操作符之后返回一个数据，并且这个数据只有在该数据流的状态变成completed时才会发射出
Rx.Observable.from([1,2,3,4,5])
  .reduce(((prev, curr) => prev + curr), 0)
  .subscribe(v => console.log(v))
// 15
```

## 过滤类操作符
过滤类操作符用来剔除我们不想关心的数据，例如输入框中为空以及长度小于2的值。  

### filter

```js
Rx.Observable.from([1,2,3,4,5])
  .filter(v => v % 2)
  .subscribe(v => console.log(v))
// 1, 3, 5
```

### take
take表示只取流中的前几个数据。  

```js
Rx.Observable.interval(500)
  .take(3)
  .subscribe(v => console.log(v))
// 0, 1, 2
```

### first/last
见名知意，无需多做解释。

```js
/* first */
Rx.Observable.interval(500)
  .first()
  .subscribe(v => console.log(v))
// 0

/* last */
Rx.Observable.interval(500)
  .take(3)
  .last()
  .subscribe(v => console.log(v))
// 2
```

### skip
跳过前面的多少个值。  

```js
Rx.Observable.interval(500)
  .skip(3)
  .subscribe(v => console.log(v))
// 3, 4, 5, ...
```

### skipWhile
忽略源Observable开头的一系列值，直到有一项符合条件，才开始从源Observable的该值开始，开始发射值。  

```js
Rx.Observable.interval(100)
  .skipWhile(v => v < 3)
  .subscribe(v => console.log(v))
// 3, 4, 5, ...
```

### debounce（对比sample）
debounce接受一个函数作为参数，而且该函数返回一个Observable流。  
将第二个流的数据发射的间隔时间当作第一个流数据发射的等待时间，只有在这个时间间隔内没有新的值发射时，数据才会最终被发射。（本质上是一种延时动作）

```js
/* 只有在按钮被点击时才从第一个流中取出一个最新值 */
Rx.Observable.interval(100)
  .debounce(() => Rx.Observable.fromEvent(btn, 'click'))
  .subscribe(v => console.log(v))
```

### debounceTime
debounceTime接受一个毫秒数作为参数，其作用是如果在一个值发射过后的指定时间间隔内没有新的值发射，该值才发射，否则舍弃掉之前的值。（本质上是一种延时动作）  
实际场景可以是，用户在输入搜索关键字时，只有停止输入了一个时间间隔，我们才拿到关键字匹配与之相关的内容。  

```js
Rx.Observable.fromEvent(input, 'keyup')
  .debounceTime(1000)
  .subscribe(v => console.log(v))
// 等同于
Rx.Observable.fromEvent(input, 'keyup')
  .debounce(() => Rx.Observable.interval(1000))
  .subscribe(v => console.log(v))
```

### sample
接受一个Observable作为它的定时发射时间（debounce是接受一个函数，该函数返回Observable）。本质是是一种定时动作。

```js
/* 该示例如果改成debounce则永远也不会输出值 */
Rx.Observable.interval(100)
  .sample(Rx.Observable.interval(1000))
  .subscribe(v => console.log(v))
```

### sampleTime
接受一个毫秒数最为它的定时发射时间，本质是是一种定时动作。  

```js
Rx.Observable.interval(100)
  .sample(1000)
  .subscribe(v => console.log(v))
```

### 去重复值 distinct / distinctUntilChanged / distinctKeyUntilChanged
distinct：流中不存在重复值。  
distinctUntilChanged：相邻两个值不等。  
distinctKeyUntilChanged：去除连续项中，拥有相同给予key值的value的项。  

```js
let items = [
  { age: 4, name: 'Foo'},
  { age: 7, name: 'Bar'},
  { age: 5, name: 'Foo'},
  { age: 6, name: 'Foo'}
]
Rx.Observable.of( ...items )
  .distinctUntilKeyChanged('name') 
  .subscribe( x => console.log( x ))
```

## 合并类操作符
将多个流合并为一个流，可以理解为流的加法运算。（如果是流中包含流，需用到高阶操作符，可以理解为流的乘法运算）  

### merge
接受多个流作为参数，将多个流打平成一个流。  

### concat
接受两个流作为参数，将两个流拼接起来（前面的流必须是有穷的流才有意义）。

### startWith
在流的前面增加一个值。  
使用场景：给流赋初始值。  

### combineLastest
接受两个流作为参数。取出两个流中的最新值，合并成一个流（任何一个流的改变多会引起流的重新组合）。  

### withLatestFrom
与combineLastest类似，不同之处在于这里有主流的概念，主流发射新值时才从另一个流中取出最新值进行组合，并且返回的是数组。

```js
length$.withLatestFrom(width$)
  .subscribe(v => console.log(v))
```

### zip
接受两个流作为参数。一一对应，讲究辈分相同，而不在乎实际年纪（与combineLastest对比）。


## 高阶操作符
何为流中流？ 点击事件是个流，点击事件后要调后台接口又是一个流。这就是典型的流中流的场景。  
高阶操作符是用来处理流中流的情况（即流的乘法）。

### mergeMap
所有主流下的支流全部保留，拍平。  

### switchMap
发射最新主流下的支流。  
一个典型的场景是：用户输入关键字，同时调接口匹配关键字相关内容。当关键字发生改变时，我们已经不关心后台匹配的上一个关键字的返回结果了。此时，就该是switchMap大放异彩了。