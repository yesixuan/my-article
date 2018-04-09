---
title: http协议缓存
tags: ["协议"]
date: 2018-04-09 21:00:00
categories:
- 计算机
- 互联网协议
---
> 在用户体验变得越来越重要的今天，充分利用缓存无疑是提升用户体验的一个重点...

<!-- more -->

## 先上两张图

浏览器第一次请求：  
![浏览器第一次请求](https://upload-images.jianshu.io/upload_images/2231755-0dd9e4e332f4c934.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/411)  

浏览器再次请求：  
![浏览器再次请求](https://upload-images.jianshu.io/upload_images/2231755-e1b5019e8d0eb9ce.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/554)

## 强缓存

缓存可以简单的划分成两种类型：强缓存（200 from cache）与协商缓存（304）。  
强缓存在将检测到缓存还未失效时，不发出 http 请求，而是直接得到 200 状态码。  

### Expires

Expires是Web服务器响应消息头字段，在响应http请求时告诉浏览器在过期时间前浏览器可以直接从浏览器缓存取数据，而无需再次请求。不过Expires 是HTTP 1.0的东西，现在默认浏览器均默认使用HTTP 1.1，所以它的作用基本忽略。  
Expires 的一个缺点就是，返回的到期时间是服务器端的时间，这样存在一个问题，如果客户端的时间与服务器的时间相差很大（比如时钟不同步，或者跨时区），那么误差就很大，所以在HTTP 1.1版开始，使用Cache-Control: max-age=秒替代。  

### Cache-control

Cache-Control与Expires的作用一致，都是指明当前资源的有效期，控制浏览器是否直接从浏览器缓存取数据还是重新发请求到服务器取数据。只不过Cache-Control的选择更多，设置更细致，如果同时设置的话，其优先级高于Expires。  
tip: 很多时候我们会使用"Cache-Control: true"与"Max-age: xx秒"来设置有效时间段。  

## 协商缓存

协商缓存（304）时，浏览器会向服务端发起http请求，然后服务端告诉浏览器文件未改变，让浏览器使用本地缓存
对于协商缓存，使用Ctrl + F5强制刷新可以使得缓存无效。  
但是对于强缓存，在未过期时，必须更新资源路径才能发起新的请求（更改了路径相当于是另一个资源了，这也是前端工程化中常用到的技巧）。

### Last-Modified / If-Modified-Sinc

Last-Modified/If-Modified-Since要配合Cache-Control使用。  
Last-Modified 是服务器给出的资源最后修改的时间点，浏览器再次请求这个资源时会通过 If-Modified-Since 携带这个时间点，然会服务器会判断这个文件是否被修改过而给出相应的 200 或 304 状态码。  

### Etag / If-None-Match

Etag/If-None-Match也要配合Cache-Control使用。  

Etag 是服务器生成的资源唯一标识（Apache中，ETag的值，默认是对文件的索引节（INode），大小（Size）和最后修改时间（MTime）进行Hash后得到的）。  
浏览器再次请求这个资源时会通过 If-None-Match 携带着这标识，余者与 Last-Modified 流程相似。  

## 用户行为与缓存

|  用户操作  |  Expires / Cache-Control  |  Last-Modified / Etag  |
|:-:|:-:|:-:|
|  地址栏回车  |  有效  |  有效  |
|  页面链接跳转  |  有效  |  有效  |
|  新开窗口  |  有效  |  有效  |
|  前进后退  |  有效  |  有效  |
|  F5刷新  |  无效(max-age=0)  |  有效  |
|  Ctrl+F5刷新  |  无效(CC=no-catche)  |  无效（请求头丢弃该选项）  |

