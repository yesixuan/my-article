---
title: fetch
tags: ["fetch"]
date: 2017-05-10 22:18:00
categories:
- 前端
- fetch
---
> 认识到fetch是一次很偶然的时机。那是在阮一峰讲解redux中的异步操作中使用到了fetch，当时我很费解，这货怎么能直接使用then()方法呢...

<!-- more -->
### 简介
`fetch`是w3c推出来的用来取代ajax的一种标准。
### `fetch`的使用
####  一般构造请求的方法
使用 fetch 的构造函数请求数据后，返回一个 Promise 对象，处理即可。
```JS
  fetch(URL)
    .then(function(response){
       // do something...
  })
```
#### `fetch`构成函数的其他选项
```JS
  var myHeaders = new Headers();
  myHeaders.append("Content-Type", "text/plain");
  myHeaders.append("Content-Length", content.length.toString());
  myHeaders.append("X-Custom-Header", "ProcessThisImmediately");
  myHeaders.append("Cache-Control", "no-cache"); // 每次 API 的请求我们想不受缓存的影响

  var myInit = {
    method: 'GET',
    headers: myHeaders,
    mode: 'cors',
    cache: 'default'
  };

  fetch(URL, myInit)
  .then(function(response){
      // do something...
  })
```
#### 返回的数据结构
常用的数据有：
- `Response.status`也就是`StatusCode`，如成功就是`200`。
- `Response.statusText`是`StatusCode`的描述文本，如成功就是`OK`。
- `Response.ok`一个`Boolean`类型的值，判断是否正常返回，也就是`StatusCode`为`200-299`。
