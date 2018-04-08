---
title: http协议入门
tags: ["协议"]
date: 2018-04-04 20:00:00
categories:
- 计算机
- 互联网协议
---
> http协议是浏览器与服务器通信的协议，也是前端工程师最熟悉的一个应用层协议...

<!-- more -->

## URL

HTTP使用统一资源标识符（Uniform Resource Identifiers, URI）来传输数据和建立连接。URL是一种**特殊类型的URI**，包含了用于查找某个资源的足够的信息。  

`http://www.aspxfans.com:8080/news/index.asp?boardID=5&ID=24618&page=1#name`  
一个完整的URL包含：协议名、域名（或主机名）、端口、虚拟目录、文件名、参数、锚。  

tip: URI强调怎样来标识一个资源、URL强调怎样来获取这个资源。  

## request

一个完整的request包含：请求行、请求头部、**空行**、请求体。  

### 请求行

`GET /562f25980001b1b106000338.jpg HTTP/1.1`
请求行回答了三个问题：我要通过什么样的方式？去到什么地方？我是谁？  

### 请求头部（用来说明服务器要使用的附加信息）

常用请求头部：  
  - Accept: 接收类型，表示浏览器支持的MIME类型
  - Accept-Encoding：浏览器支持的压缩类型,如gzip等,超出类型不能接收
  - Content-Type：客户端发送出去实体内容的类型
  - Cache-Control: 指定请求和响应遵循的缓存机制，如no-cache
  - If-Modified-Since：对应服务端的Last-Modified，用来匹配看文件是否变动，只能精确到1s之内，http1.0中
  - Expires：缓存控制，在这个时间内不会请求，直接使用缓存，http1.0，而且是服务端时间（过期时间）
  - Max-age：代表资源在本地缓存多少秒，有效时间内不会请求，而是使用缓存，http1.1中（有效时间段）
  - If-None-Match：对应服务端的ETag，用来匹配文件内容是否改变（非常精确），http1.1中
  - Cookie: 有cookie并且同域访问时会自动带上
  - Connection: 当浏览器与服务器通信时对于长连接如何进行处理,如keep-alive
  - Host：请求的服务器URL
  - Origin：最初的请求是从哪里发起的（只会精确到端口）,Origin比Referer更尊重隐私
  - Referer：该页面的来源URL(适用于所有类型的请求，会精确到详细页面地址，csrf拦截常用到这个字段)
  - User-Agent：用户客户端的一些必要信息，如UA头部等