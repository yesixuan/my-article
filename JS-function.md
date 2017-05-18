---
title: JS中函数的生命周期
tags: ["JS"]
date: 2017-05-17J8 11:20:00
categories:
- 前端
- JS
---
> 本文意在将JS中函数从生到死的讲清楚。嗯，让后人能知晓JS函数的生平。是非功过，便听凭后人吧...

<!-- more -->
### 通俗地讲
#### 函数的创建
当函数被声明的时候，它的作用域链就已经被确定了。此时将保存作用域链到[[scope]]中（这也是函数能记住自己出生环境的原因）。
#### 函数的调用
1. 创建自己的执行上下文。
2. 复制[[scope]]属性，创建作用域链。
3. 用arguments创建活动对象，初始化变量。
4. 将活动对象压入作用域顶端。
5. 函数代码块消费作用域链(AO)中的值。

#### 函数的终结（本人臆断）
1. 执行上下文出栈。
2. 销毁未被引用的变量。
### 专业地讲
#### 上代码
```JS
  var scope = "global scope";
  function checkscope(){
      var scope2 = 'local scope';
      return scope2;
  }
  checkscope();
```
#### 执行过程
1. checkscope函数被创建，保存作用域链到[[scope]]。
```JS
  checkscope.[[scope]] = [
    globalContext.VO
  ];
```
2. 执行checkscope函数，创建checkscope函数执行上下文，checkscope函数执行上下文被压入执行上下文栈。
```JS
  ECStack = [
    checkscopeContext,
    globalContext
  ];
```
3. checkscope函数并不立刻执行，开始做准备工作，第一步：复制函数[[scope]]属性创建作用域链。
```JS
  checkscopeContext = {
    Scope: checkscope.[[scope]],
  }
```
4. 第二步：用arguments创建活动对象，随后初始化活动对象，加入形参、函数声明、变量声明。
```JS
  checkscopeContext = {
    AO: {
      arguments: {
        length: 0
      },
      scope2: undefined
    }
  }
```
5. 第三步：将活动对象压入checkscope作用域链顶端。
```JS
  checkscopeContext = {
    AO: {
      arguments: {
        length: 0
      },
      scope2: undefined
    },
    Scope: [AO, [[Scope]]]
  }
```
6. 准备工作做完，开始执行函数，随着函数的执行，修改AO的属性值。
