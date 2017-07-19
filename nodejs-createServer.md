---
title: 原生nodejs创建服务
tags: ["nodejs","server"]
date: 2017-07-19 10:19:00
categories:
- 后台
- nodejs
---
> 虽然现实开发中不会有人使用原生nodejs提供服务。但原生毕竟是基石，还是有必要温故一下的...

<!-- more -->
## 一览

### 最简单的创建服务
```js
  const http = require('http')
  const urlLib = require('url')
  const querystring = require('querystring')
  http.createServer((req, res) => {
    // 判断是get还是post请求
    if(req.url.indexOf('?') == -1) {
      var get = querystring(req.url, true)
    }else {
      var str = ''
      req.on('data', data => {
        str += data
      })
      req.on('end', () => {
        var post = querystring(str)
      })
    }
  }).listen(4001)
```

### 提供静态资源
这只是个简陋的静态资源服务
```js
  const http = require('http')
  const fs = require('fs')
  let server = http.createServer((req, res) => {
    let fileName = './www' + req.url
    fs.readFile(fileName, (err, data) => {
      if (err) {
        res.write('404')
      } else {
        res.write(data)
      }
      // 读文件是异步的，所以end该放在这里
      res.end()
    })
  }).listen('4001')
```

## 常用模块

### url模块（解析GET参数）
```js
  const urlLib = require('url')
  // ...
  req.on('end', () => {
    var obj = urlLib.parse(req.url, true)
    const url = obj.pathname
    const GET = obj.query // 重要的查询参数
  })
```

### querystring（解析POST数据）
```js
  const querystring = require('querystring')
  // ...
  req.on('data', data => {
    str += data
  })
  req.on('end', () => {
    const POST = querystring.parse(str)
  }
```

## 比较完整的服务

```js
const http = require('http')
const fs = require('fs')
const querystring = require('querystring')
const urlLib = require('url')

var users = {} // 存用户数据

http.createServer((req, res) => {
  // 解析数据
  var str = ''
  req.on('data', data => {
    str += data
  })
  req.on('end', () => {
    var obj = urlLib.parse(req.url, true)
    // console.log(obj)
    const url = obj.pathname
    const GET = obj.query
    const POST = querystring.parse(str)
    // 区分接口和文件
    if (!url) {
      return
    }
    if (url === '/user') { // 接口
      switch (GET.act) {
        case 'reg':
          // 检查用户是否存在
          if (users[GET.user]) {
            res.write('{"ok":false,"msg":"用户名已存在"}')
            // 插入数据
          } else {
            users[GET.user] = GET.pass
            res.write('{"ok":true,"msg":"注册成功"}')
          }
          break;
        case 'login':
          // 检查用户名是否存在
          if (users[GET.name] == null) {
            res.write('{"ok":false,"msg":"用户不存在"}')
            // 检查密码是否正确
          } else if (users[GET.name] != GET.pass) {
            res.write('{"ok":false,"msg":"密码有误"}')
          } else {
            res.write('{"ok":true,"msg":"登录成功"}')
          }
          break;
        default:
          res.write('{"ok":false,"msg":"未知的act"}')
      }
      res.end()
    } else { // 当文件来对待
      // 读取文件
      var file_name = './www' + url
      fs.readFile(file_name, (err, data) => {
        if (err) {
          res.write('404')
        } else {
          res.write(data)
        }
        res.end()
      })
    }
  })
}).listen(4001)
```
