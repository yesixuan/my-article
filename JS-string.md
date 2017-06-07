---
title: JS中的字符串
tags: ["js","字符串"]
date: 2017-06-05 12:07:00
categories:
- 前端
- JS
---
> 拒绝字符串中的各种模糊的方法...

<!-- more -->
## 字符串的转换
```js
  var num= 19 // 19
  // 方法一
  var myStr = num.toString() // "19"
  // 方法二
  var myStr = String(num) // "19"
  // 方法三(这个我喜欢)
  var myStr = "" +num // "19"
```
## 字符串分割(split)
```JS
  var myStr = "I,Love,You,Do,you,love,me"
  // 以","为分割点，将字符串分割为多个片段组成的数组
  var substrArray = myStr .split(",") // ["I", "Love", "You", "Do", "you", "love", "me"];
  // 加上第二个参数，指定切割后数组的最大长度
  var arrayLimited = myStr .split(",", 3) // ["I", "Love", "You"];
```
## 查询子字符串
```js
  var myStr = "I,Love,you,Do,you,love,me"
  // indexOf()如果匹配成功，则返回这个匹配上的位置，匹配不成功返回-1
  var index = myStr.indexOf("you") // 7 ,基于0开始,找不到返回-1
  // 从字符串的末尾开始查找
  var index = myStr.lastIndexOf("you") // 14
  /* 以上两个函数同样接收第二个可选的参数，表示开始查找的位置。 */
```
## 字符串替换
```js
  var myStr = "I,love,you,Do,you,love,me";
  var replacedStr = myStr.replace("love","hate") // "I,hate,you,Do,you,love,me"
  /* 默认只替换第一次查找到的，想要全局替换，需要置上正则全局标识 */
  var replacedStr = myStr.replace(/love/g,"hate") //"I,hate,you,Do,you,hate,me"
```
## 查找给定位置的字符或其字符编码值
```js
  var myStr = "I,love,you,Do,you,love,me"
  var theChar = myStr.charAt(8) // "o",同样从0开始
  /* 查找对应位置的字符编码值 */
  var theChar = myStr.charCodeAt(8) // 111
```
## 字符串连接
```js
  /* 除了使用"+"，JS还提供了专门的函数来处理 */
  var str1 = "I,love,you!"
  var str2 = "Do,you,love,me?"
  // concat()函数可以有多个参数，传递多个字符串，拼接多个字符串
  var str = str1.concat(str2) // "I,love,you!Do,you,love,me?"
```
## 字符串切割和提取
```js
  var myStr = "I,love,you,Do,you,love,me"
  // slice()含头不含尾
  var subStr = myStr.slice(1,5) // ",lov"
  // substring()跟上面效果一样
  var subStr = myStr.substring(1,5) // ",lov"
  // 从哪个位置开始截取，最多截取多少个
  var subStr = myStr.substr(1,5) // ",love"
```
## 字符串大小写转换
```js
  var myStr = "I,love,you,Do,you,love,me"
  var lowCaseStr = myStr.toLowerCase() // "i,love,you,do,you,love,me";
  var upCaseStr = myStr.toUpperCase() // "I,LOVE,YOU,DO,YOU,LOVE,ME"
```
## 字符串匹配
### 字符串方法match()
```js
  var myStr = "I,love,you,Do,you,love,me"
  var pattern = /love/
  var result = myStr.match(pattern) // ["love"]
  console.log(result .index) // 2
  console.log(result.input ) // I,love,you,Do,you,love,me
```
### 正则方法exec()
```js
  var myStr = "I,love,you,Do,you,love,me"
  var pattern = /love/
  /* 其实就是将match()方法反过来用 */
  var result = pattern .exec(myStr) // ["love"]
  console.log(result .index) // 2
  console.log(result.input ) // I,love,you,Do,you,love,me
```
对于以上两个方法，匹配的结果都是返回第一个匹配成功的字符串，如果匹配失败则返回null。
### search()
```js
  var myStr = "I,love,you,Do,you,love,me";
  var pattern = /love/;
  /* 返回查到的匹配的下标，如果匹配失败则返回-1 */
  var result = myStr.search(pattern) // 2
```
