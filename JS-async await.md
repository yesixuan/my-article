---
title: async/await 更好的异步解决方案
tags: ["js"]
date: 2017-08-01 14:53:00
categories:
- 前端
- JS
---
> 异步的解决方案有很多种。包括最传统的回调，后来蓬勃发展的Promise，到了今时今日，JS大爆发的的时代。让我们看看现代的异步解决方案...

<!-- more -->
## 整体感知
async/await提供给我们一种同步的方式来编写异步代码。如果去掉await关键字，下面这段异步代码就跟我们常见的同步代码别无二致了。
```js
// 定义一个返回Promise对象的函数
function fn() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve(30)
    }, 1000)
  })
}
// 然后利用async/await来完成代码
const foo = async() => {
  const t = await fn()
  console.log(t)
  console.log('next code')
}
foo()
// 30
// next code
```

## 更进一步
### 前世————Generator函数
async/await的前世是ES6提供的Generator函数。那么下面来瞧一瞧Generator函数是个什么鬼。  
```js
function* gen(x) {
  var y = yield x + 2
  return y
}
var g = gen(1)
g.next() // { value: 3, done: false }
g.next() // { value: undefined, done: true }
```

简单粗暴地说，Generator函数就是一种可以暂停执行的函数。我们在调用Generator函数时，会返回一个内部指针（即遍历器）。继续调用这个内部指针的next方法才会执行Generator函数体中的语句，然后遇到以yield关键字则会暂停执行，知道再一次调用next方法。  
调用遍历器的next方法会返回形如`{value: xx, done: bool}`的对象。value接收yield之后的值，done表示函数是否执行完毕。  
遍历器的next方法可以接收外部传入的参数，该参数将会被当作上一个yield的值参与运算。  
下面看看如何用Generator函数来实现异步操作：  
```js
var fetch = require('node-fetch')
function* gen(){
  var url = 'https://api.github.com/users/github'
  var result = yield fetch(url)
  console.log(result.bio)
}
/* 执行这段代码如下 */
var g = gen();
// result得到的是一个Promise对象
var result = g.next();
result.value.then(function(data){
  return data.json();
}).then(function(data){
  // 得到异步返回的数据之后调用下一个next，并且将数据传进next方法
  g.next(data);
});
```

### async/await
#### 为什么要用async
async函数是什么？一句话，它就是Generator函数的语法糖。  
上面我们利用Generator函数封装了异步操作，但是那种写法比较别扭。首先，我们要将异步操作用Promise封装起来，其次，当异步完成之后进行下一个操作时，需要手动地调用next方法。  
async函数的出现，就弥补了我们提到的不足：
```js
async function getStockPriceByName(name) {
  // 正常情况下，await命令后面是一个 Promise 对象。如果不是，会被转成一个立即resolve的 Promise 对象。
  var symbol = await getStockSymbol(name)
  var stockPrice = await getStockPrice(symbol)
  return stockPrice
}
// async函数返回一个Promise对象。async函数内部return语句返回的值，会成为then方法回调函数的参数。
getStockPriceByName('goog').then(function (result) {
  console.log(result)
})
```
#### 不让前面的错误影响后面的操作
有时，我们希望即使前一个异步操作失败，也不要中断后面的异步操作。这时可以将第一个await放在try...catch结构里面，这样不管这个异步操作是否成功，第二个await都会执行。  
```js
async function f() {
  try {
    await Promise.reject('出错了')
  } catch(e) {
  }
  // 即使await后面的语句报错，下面这个await还是会执行
  return await Promise.resolve('hello world')
}
f()
.then(v => console.log(v))
// hello world
```
#### 错误处理
await命令后面的Promise对象，运行结果可能是rejected，所以最好把await命令放在try...catch代码块中。如果await后面的异步操作出错，那么等同于async函数返回的 Promise 对象被reject。  
```js
async function myFunction() {
  try {
    await somethingThatReturnsAPromise()
  } catch (err) {
    console.log(err)
  }
}
// 另一种写法
async function myFunction() {
  await somethingThatReturnsAPromise()
  .catch(function (err) {
    console.log(err)
  });
}
```
#### 并发的异步
上面的写法都是处理继发状况，下面来说说并发异步的处理：  
```js
// 写法一
let [foo, bar] = await Promise.all([getFoo(), getBar()])
// 写法二
let fooPromise = getFoo()
let barPromise = getBar()
let foo = await fooPromise
let bar = await barPromise
```
