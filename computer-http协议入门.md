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

## Request

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

tip: 组合拳：  
- `If-Modified-Since`与服务器给的`Last-Modified`  
- `If-None-Match`与服务器给的`ETag`（Last-Modified是服务器给的一个时间戳，而ETag相当于是给了一个唯一ID）  
- `Expires`与`Max-age`前者指的是服务器给出的过期时间点，后者指定的是在某个时间段内缓存有效  

`Expires`与`Max-age`是强缓存，而`If-Modified-Since`与`If-None-Match`则属于弱缓存。  

### 空行

即使没有请求主体，也必须有空行。  

### 请求体

请求体并不是一个请求报文所必需的，它是用来承载数据的。

## Response

Response也由四个部分组成，分别是：状态行、消息报头、空行和响应体。  

### 响应行

响应行告诉我们的是发生了什么？  
由HTTP协议版本号， 状态码， 消息短语 三部分组成。  
最常用的状态吗：  
 - 200——表明该请求被成功地完成，所请求的资源发送回客户端
 - 304——自从上次请求后，请求的网页未修改过，请客户端使用本地缓存
 - 400——客户端请求有错（譬如可以是安全模块拦截）
 - 401——请求未经授权
 - 403——禁止访问（譬如可以是未登录时禁止）
 - 404——资源未找到
 - 500——服务器内部错误
 - 503——服务不可用

### 相应头

常用的响应头：  
  - Access-Control-Allow-Headers: 服务器端允许的请求Headers
  - Access-Control-Allow-Methods: 服务器端允许的请求方法
  - Access-Control-Allow-Origin: 服务器端允许的请求Origin头部（譬如为*）
  - Content-Type：服务端返回的实体内容的类型
  - Date：数据从服务器发送的时间
  - Cache-Control：告诉浏览器或其他客户，什么环境可以安全的缓存文档
  - Last-Modified：请求资源的最后修改时间
  - Expires：应该在什么时候认为文档已经过期,从而不再缓存它
  - Max-age：客户端的本地资源应该缓存多少秒，开启了Cache-Control后有效
  - ETag：请求变量的实体标签的当前值
  - Set-Cookie：设置和页面关联的cookie，服务器通过这个头部把cookie传给客户端
  - Keep-Alive：如果客户端有keep-alive，服务端也会有响应（如timeout=38）
  - Server：服务器的一些相关信息

### 空行

消息报头后面的空行是必须的。  

### 响应体

服务器返回给客户端的文本信息。  

## HTTP请求方法

GET: 请求指定的页面信息，并返回实体主体;  
HEAD: 类似于get请求，只不过返回的响应中没有具体的内容，用于获取报头;  
POST: 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST请求可能会导致新的资源的建立和/或已有资源的修改;  
PUT: 从客户端向服务器传送的数据取代指定的文档的内容;  
DELETE: 请求服务器删除指定的页面;  
CONNECT: HTTP/1.1协议中预留给能够将连接改为管道方式的代理服务器;  
OPTIONS: 允许客户端查看服务器的性能;  
TRACE: 回显服务器收到的请求，主要用于测试或诊断。  

## HTTP 请求/响应的步骤

1. 客户端连接到Web服务器  
一个HTTP客户端，通常是浏览器，与Web服务器的HTTP端口（默认为80）建立一个TCP套接字连接。  

2. 发送HTTP请求  
通过TCP套接字，客户端向Web服务器发送一个文本的请求报文，一个请求报文由请求行、请求头部、空行和请求数据4部分组成。  

3. 服务器接受请求并返回HTTP响应  
Web服务器解析请求，定位请求资源。服务器将资源复本写到TCP套接字，由客户端读取。一个响应由状态行、响应头部、空行和响应数据4部分组成。  

4. 释放连接TCP连接  
若connection 模式为close，则服务器主动关闭TCP连接，客户端被动关闭连接，释放TCP连接;若connection 模式为keepalive，则该连接会保持一段时间，在该时间内可以继续接收请求;  
