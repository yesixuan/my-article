---
title: mock数据
tags: ["js","mock"]
date: 2017-05-31 15:58:00
categories:
- 前端
- JS
- mock
---
> 花了差不多半天的时间来探索在vue中mock数据的最佳方案。总结一下...

<!-- more -->
## 前言
本文以vue-cli命令行工具生成的webpack项目为例，实现在前后端分离的情况下前段工程师模拟后台接口的方案。
## 最粗暴的mock
webpack内部其实是启动了一个基于express的小型服务器。而express提供了一个很简单的api来将某个目录里面的资源静态呈递出去。所以，我们的思路很简单，创建一个文件夹存放一对json数据；用express的static()方法将这个文件夹静态呈递。
关键代码如下：
```JS
  /* build/dev-server.js */
  // 以mock打头的http请求将去到指定的文件夹里面寻找相对应的资源，这里我们存放json文件即可（严格的json格式）
  app.use('/mock',express.static('./src/mock-data'))
```
## 使用node的第三方模块写接口
使用上面的方法太过死板，而且只能使用get请求。作为一个有追求的前端渣渣，我们还得继续完善。
首先，我们使用express（webpack本身依赖express，所以不需要安装其他模块）模拟后台接口：
```JS
/* mock/server.js */
  let express = require('express')
  let app = express();
  app.get('/api',(req,res) => {
  	res.send('success！')
  })
  // 这个js暴露了一个json对象
  var homeAdData = require('./home/ad.js');
  app.get('/api/homead',(req,res) => {
  	res.send(homeAdData)
  })
  app.listen(3002)
```
大家可以看到这里我们监听的是本机的3002端口，而vue项目默认是在8080端口启动的（这两个端口不能相同，原因不解释）。webpack-dev-server官方提供了一个转发接口的配置项。
```JS
  /* config/index.js */
  // 将URL后面以api打头的接口转发到指定的域名上
  proxyTable: {
    '/api': {
      target: 'http://localhost:3002',
      changeOrigin: true, // 这里表示允许跨域
      pathRewrite: {
        '^/api': '/api'
      }
    }
  }
```
至此，我们开发之前都要敲两个命令来分别启动项目和我们写的接口，还可以更加简单
```JS
  /* build/dev-server.js */
  // 将这个express服务器跑起来
  require('../mock/server.js');
```
