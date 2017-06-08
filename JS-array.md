---
title: JS的数组方法
tags: ["JS"]
date: 2017-06-08 08:55:00
categories:
- 前端
- JS
---
> 数组是我们最常操作的JS对象之一，熟练使用各种JS方法，可以让我们的代码敲起来更爽...

<!-- more -->
## 数组基本方法
### join()
将数组以指定的字符连接起来输出字符串
```JS
  // 不改变原数组
  var newArr = arr.join('-')
```
### push()和pop()
在数组的屁股上做操作，改变原数组。
### shift() 和 unshift()
与上面一组方法类似，只是改为在数组的头部做操作。
### sort()
不传参数的情况下，按照元素的编码值排序。同时可以接受一个函数作为参数来自定义排序规则。
```js
  function compare(pre,next) {
    // 返回值如果为负数，则两者不调换位置
    return(pre-next)
  }
  // sort方法会改变原数组
  arr.sort(compare)
```
### reverse()
这个没什么好说的，将数组反转。该方法会改变原数组。
### concat()
将两个数组拼接为一个数组。该方法不改变原数组，返回新数组。
### slice()
该方法接收起始位置和终止位置（含头不含尾）。字符串也有这样的方法（类似字符串的substring()方法类似）。不改变原数组，返回新数组。
### splice()
这个方法是数组中的瑞士军刀。它接受多个参数，顺序依次为：起始位置，要删除多少项，要插入的元素...
该数组会改变原数组，但它同时也返回被删除的项组成的数组。
```js
  var arr = [2,1,6,5]
  var newArr = arr.splice(1,1,8)
  console.log(arr) // [2, 8, 6, 5]
  console.log(newArr) // [1]
```
### indexOf()和lastIndexOf()
检索数组中是否包含某值，返回检索到值的位置下标。
## ES5数组方法
### forEach()
对数组for循环的封装，接收一个函数作为参数，该函数默认有三个参数：当前遍历的这一项，当前遍历项的索引值，遍历的数组对象。
### map()
意为“映射”，即对数组的每一项进行指定的处理，最终得到一个新的数组（一定要接住这个新的数组）。
### filter()
接收一个函数，用来过滤掉数组中某些不符合要求的项。
```js
  var arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10];
  var arr2 = arr.filter(function(item, index) {
    return index % 3 === 0 || item >= 8;
  });
  console.log(arr2); // [1, 4, 7, 8, 9, 10]
```
### every()和some()
every(): 判断数组中每一项都是否满足条件，只有所有项都满足条件，才会返回true。
some(): 判断数组中是否存在满足条件的项，只要有一项满足条件，就会返回true。
### reduce()和reduceRight()
这两个方法都会实现迭代数组的所有项，然后构建一个最终返回的值。reduce()方法从数组的第一项开始，逐个遍历到最后。
这两个方法都接收两个参数：一个函数，给定迭代的初始值。
这个函数默认接受四个参数：前一次处理得到的值，遍历的当前项，当前项的索引，迭代的数组对象。
```js
  var values = [1,2,3,4,5];
  // 每处理一项的返回值将作为下一项的prev值
  var sum = values.reduceRight(function(prev, cur, index, array){
    return prev + cur;
  },10); // 这个10将作为第一项的prev值
  console.log(sum); //25
```
## ES6数组方法
### Array.from()
Array.from方法用于将两类对象转为真正的数组：类似数组的对象（array-like object）和可遍历（iterable）的对象（包括ES6新增的数据结构Set和Map）。
```js
  let arrayLike = {
    '0': 'a',
    '1': 'b',
    '2': 'c',
    length: 3
  }
  let arr2 = Array.from(arrayLike); // ['a', 'b', 'c']
```
### Array.of()
Array.of方法用于将一组值，转换为数组。
### find()和findIndex()
数组实例的find方法，用于找出第一个符合条件的数组成员。它的参数是一个回调函数，所有数组成员依次执行该回调函数，直到找出第一个返回值为true的成员，然后返回该成员。如果没有符合条件的成员，则返回undefined。
数组实例的findIndex方法的用法与find方法非常类似，返回第一个符合条件的数组成员的位置，如果所有成员都不符合条件，则返回-1。
### entries()，keys()和values()
ES6提供三个新的方法——entries()，keys()和values()——用于遍历数组。它们都返回一个遍历器对象，可以用for...of循环进行遍历，唯一的区别是keys()是对键名的遍历、values()是对键值的遍历，entries()是对键值对的遍历。
```js
  for(let index of ['a', 'b'].keys()) {
    console.log(index);
  }
  for(let elem of ['a', 'b'].values()) {
    console.log(elem);
  }
  for(let [index, elem] of ['a', 'b'].entries()) {
    console.log(index, elem);
  }
```
### includes()
该方法返回一个布尔值，表示某个数组是否包含给定的值，与字符串的includes方法类似（比indexOf()更强）。
## 扩展的数组方法
### 数组的去重方法
filter():
```js
  function unique(arr) {
    return (
      arr.filter((item,index,arr) => {
        return arr.indexOf(item) == index
      })
    )
  }
```
hash方法
```js
  function unique(arr) {
    var hash = {},result = []
    arr.forEach((item) => {
      if(!hash[item]) {
        hash[item] = 1
        result.push(item)
      }
    })
    return result
  }
```
sort()加filter()
```js
  function unique(arr) {
    // arr.concat()得到新的数组
    return arr.concat().sort().filter(function(item, index, arr) {
      return !index || item != arr[index - 1];
    });
  }
```
ES6的Set以及Array.from方法
```js
  function unique(arr) {
    return Array.from(new Set(arr));
  }
```
### 数组判断
```js
  // 自带的isArray方法
  var array = []
  Array.isArray(array) // true
  // 利用instanceof运算符
  array instanceof Array;//true
  // 利用toString的返回值
  function isArray(o) {
    return Object.prototype.toString.call(o) === '[object Array]';
  }
```
