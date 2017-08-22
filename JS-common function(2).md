---
title: JS常用方法（二）
tags: ["js","常用方法"]
date: 2017-07-27 08:50:00
categories:
- 前端
- JS
- 常用函数
---
> 整理一下从别处看到的js常用函数[参考](https://segmentfault.com/a/1190000010225928)...

<!-- more -->
## 字符串的相关操作
### 去除字符串空格
```js
//去除空格  type 1-所有空格  2-前后空格  3-前空格 4-后空格
function trim(str,type){
  switch (type){
    case 1:return str.replace(/\s+/g,"");
    case 2:return str.replace(/(^\s*)|(\s*$)/g, "");
    case 3:return str.replace(/(^\s*)/g, "");
    case 4:return str.replace(/(\s*$)/g, "");
    default:return str;
  }
}
```
### 字母大小写切换
```js
/*type
1:首字母大写   
2：首页母小写
3：大小写转换
4：全部大写
5：全部小写
 **/
function changeCase(str, type) {
  function ToggleCase(str) {
    var itemText = ""
    str.split("").forEach(
      function(item) {
        if (/^([a-z]+)/.test(item)) {
          itemText += item.toUpperCase();
        } else if (/^([A-Z]+)/.test(item)) {
          itemText += item.toLowerCase();
        } else {
          itemText += item;
        }
      });
    return itemText;
  }

  switch (type) {
    case 1:
      return str.replace(/\b\w+\b/g, function(word) {
        return word.substring(0, 1).toUpperCase() + word.substring(1).toLowerCase();
      });
    case 2:
      return str.replace(/\b\w+\b/g, function(word) {
        return word.substring(0, 1).toLowerCase() + word.substring(1).toUpperCase();
      });
    case 3:
      return ToggleCase(str);
    case 4:
      return str.toUpperCase();
    case 5:
      return str.toLowerCase();
    default:
      return str;
  }
}
```
### 字符串替换
```js
// 字符串替换(字符串,要替换的字符,替换成什么)
function replaceAll(str, AFindText, ARepText) {　　　
  raRegExp = new RegExp(AFindText, "g");　　　
  return str.replace(raRegExp, ARepText);
}
```
### 将某些特定字符替换为'*'  
```js
// replaceStr(字符串,字符格式, 替换方式,替换的字符（默认*）)
function replaceStr(str, regArr, type, ARepText) {
  var regtext = '',
    Reg = null,
    replaceText = ARepText || '*';
  // replaceStr('18819322663',[3,5,3],0)
  // 188*****663
  // repeatStr是在上面定义过的（字符串循环复制），大家注意哦
  if (regArr.length === 3 && type === 0) {
    regtext = '(\\w{' + regArr[0] + '})\\w{' + regArr[1] + '}(\\w{' + regArr[2] + '})'
    Reg = new RegExp(regtext);
    var replaceCount = repeatStr(replaceText, regArr[1]);
    return str.replace(Reg, '$1' + replaceCount + '$2')
  }
  // replaceStr('asdasdasdaa',[3,5,3],1)
  // ***asdas***
  else if (regArr.length === 3 && type === 1) {
    regtext = '\\w{' + regArr[0] + '}(\\w{' + regArr[1] + '})\\w{' + regArr[2] + '}'
    Reg = new RegExp(regtext);
    var replaceCount1 = repeatSte(replaceText, regArr[0]);
    var replaceCount2 = repeatSte(replaceText, regArr[2]);
    return str.replace(Reg, replaceCount1 + '$1' + replaceCount2)
  }
  // replaceStr('1asd88465asdwqe3',[5],0)
  // *****8465asdwqe3
  else if (regArr.length === 1 && type == 0) {
    regtext = '(^\\w{' + regArr[0] + '})'
    Reg = new RegExp(regtext);
    var replaceCount = repeatSte(replaceText, regArr[0]);
    return str.replace(Reg, replaceCount)
  }
  // replaceStr('1asd88465asdwqe3',[5],1,'+')
  // "1asd88465as+++++"
  else if (regArr.length === 1 && type == 1) {
    regtext = '(\\w{' + regArr[0] + '}$)'
    Reg = new RegExp(regtext);
    var replaceCount = repeatSte(replaceText, regArr[0]);
    return str.replace(Reg, replaceCount)
  }
}
```
### 检测字符串
```js
// checkType('165226226326','phone')
// false
// 大家可以根据需要扩展
function checkType(str, type) {
  switch (type) {
    case 'email':
      return /^[\w-]+(\.[\w-]+)*@[\w-]+(\.[\w-]+)+$/.test(str);
    case 'phone':
      return /^1[3|4|5|7|8][0-9]{9}$/.test(str);
    case 'tel':
      return /^(0\d{2,3}-\d{7,8})(-\d{1,4})?$/.test(str);
    case 'number':
      return /^[0-9]$/.test(str);
    case 'english':
      return /^[a-zA-Z]+$/.test(str);
    case 'chinese':
      return /^[\u4E00-\u9FA5]+$/.test(str);
    case 'lower':
      return /^[a-z]+$/.test(str);
    case 'upper':
      return /^[A-Z]+$/.test(str);
    default:
      return true;
  }
}
```
### 检测密码强度
```js
// checkPwd('12asdASAD')
// 3(强度等级为3)
function checkPwd(str) {
  var nowLv = 0;
  if (str.length < 6) {
    return nowLv
  };
  if (/[0-9]/.test(str)) {
    nowLv++
  };
  if (/[a-z]/.test(str)) {
    nowLv++
  };
  if (/[A-Z]/.test(str)) {
    nowLv++
  };
  if (/[\.|-|_]/.test(str)) {
    nowLv++
  };
  return nowLv;
}
```
### 生成随机码
```js
// count取值范围0-36

// randomNumber(10)
// "2584316588472575"

// randomNumber(14)
// "9b405070dd00122640c192caab84537"

// Math.random().toString(36).substring(2);
// "83vhdx10rmjkyb9"
function randomNumber(count){
  return Math.random().toString(count).substring(2);
}
```
### 查找特定字符串出现的次数
```js
function countStr (str,strSplit){
  return str.split(strSplit).length-1
}
var strTest = 'sad44654blog5a1sd67as9dablog4s5d16zxc4sdweasjkblogwqepaskdkblogahseiuadbhjcibloguyeajzxkcabloguyiwezxc967'
// countStr(strTest,'blog')
// 6
```
## 数组操作
### 数组顺序打乱
```js
function upsetArr(arr){
  return arr.sort(function(){ return Math.random() - 0.5});
}
```
### 数组最大值最小值
```js
// 这一块的封装，主要是针对数字类型的数组
function maxArr(arr){
  return Math.max.apply(null,arr);
}
function minArr(arr){
  return Math.min.apply(null,arr);
}
```
### 数组求和，平均值
```js
// 这一块的封装，主要是针对数字类型的数组
// 求和
function sumArr(arr) {
  var sumText = 0;
  for (var i = 0, len = arr.length; i < len; i++) {
    sumText += arr[i];
  }
  return sumText
}
// 平均值,小数点可能会有很多位，这里不做处理，处理了使用就不灵活了！
function covArr(arr) {
  var sumText = sumArr(arr);
  var covText = sumText / length;
  return covText
}
```
### 从数组中随机获取元素
```js
function randomOne(arr) {
  return arr[Math.floor(Math.random() * arr.length)];
}
```
### 返回数组（字符串）一个元素出现的次数
```js
// getEleCount('asd56+asdasdwqe','a')
// 3
// getEleCount([1,2,3,4,5,66,77,22,55,22],22)
// 2
function getEleCount(obj, ele) {
  var num = 0;
  for (var i = 0, len = obj.length; i < len; i++) {
    if (ele == obj[i]) {
      num++;
    }
  }
  return num;
}
```
### 返回数组（字符串）出现最多的几次元素和出现次数
```js
// arr, rank->长度，默认为数组长度，ranktype，排序方式，默认降序
function getCount(arr, rank， ranktype) {
  var obj = {},
    k, arr1 = []
  // 记录每一元素出现的次数
  for (var i = 0, len = arr.length; i < len; i++) {
    k = arr[i];
    if (obj[k]) {
      obj[k]++;
    } else {
      obj[k] = 1;
    }
  }
  // 保存结果{el-'元素'，count-出现次数}
  for (var o in obj) {
    arr1.push({
      el: o,
      count: obj[o]
    });
  }
  // 排序（降序）
  arr1.sort(function(n1, n2) {
    return n2.count - n1.count
  });
  // 如果ranktype为1，则为升序，反转数组
  if (ranktype === 1) {
    arr1 = arr1.reverse();
  }
  var rank1 = rank || arr1.length;
  return arr1.slice(0, rank1);
}
```
