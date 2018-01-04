---
title: nginx静态伺服与反向代理
tags: ["nginx"]
date: 2018-01-04 19:17:00
categories:
- 后端
- nginx
---
> Nginx 是一个很强大的高性能Web和反向代理服务器...

<!-- more -->
Nginx 是一个很强大的高性能Web和反向代理服务器。
下面，我们将借助 nginx 实现前后端分离。

## 前期准备

- 下载 nginx 安装包（windows 下就是一个压缩包），解压到任意位置都行；
- 切换命令行至 nginx.exe 目录下，启动 nginx，`start nginx`；
- 浏览器打开 localhost，如果看到 nginx 的启动页，证明启动成功。

## 前后端分离

### 问题

当下的开发模式是后端提供数据接口，前端进行数据展示。前后端之间的桥梁就是ajax.
前端开发通常是借助于一个 node 服务器，我们假设它的主机名加端口是：http://localhost:8080.
后端接口通常是在 tomcat 服务器上，这里我们使用 koa + koa-router 创建后台接口，地址为：http://localhost:3001.
此时，前端通过 ajax 调用后台接口时，由于浏览器的同源策略会请求失败。

### 解决思路

使用 nginx 作为前后端中间的反向代理服务器。前端不是直接发送请求给后端，而是发给 nginx 服务器。nginx 服务器再去请求真正的接口地址（浏览器有跨域限制，服务器没有）。nginx 服务器获取到接口数据之后再将数据发送给前端（同时要设置`add_header 'Access-Control-Allow-Origin' '*';`）。
而上述转发的过程，对于前后端而言是没有感知的，它们并不知道 nginx 这个中间人干了什么，这就是反向代理的好处。

## 相关代码实现

### 前端

前端借助 axios 这个库来发送 ajax 请求：
```js
/* http://localhost:8080 */

// nginxServer 是 nginx 服务器监听的端口号
const nginxServer = 'http://localhost:8880';

axios.get(`${nginxServer}/apis/hehe?name=tom&age=22`)
  .then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

### 后端

后端是有 koa + koa-router 提供接口：
```js
// 相关代码
router.get('/apis/hehe', async(ctx, next) => {
  // 声明返回的数据类型为json
  ctx.set('Content-Type', 'application/json');
  ctx.body = JSON.stringify({
    code: 0,
    // 通过 ctx.query 拿到请求地址里的参数
    msg: `欢迎：${ctx.query.name}`
  })
})
```

### nginx 配置

我们约定好接口的命名都要以"/apis"开头，nginx 会拦截以"/apis"开头的请求，认为这样的请求就是在请求后台接口，并将其转发到后端服务器上。
```bash
server {
  listen       8880; # 默认是80端口，这里我改成了8880
  server_name  localhost;

  # 反向代理
  location /apis {
    # 重写请求路径（端口号后面的，查询参数前面的部分）
    rewrite  ^/(.*)$ /$1 break;
    # 请求真正的后台服务器
    proxy_pass http://localhost:3001;
    # 添加跨域访问头（前端请求 nginx 服务器实际上也是跨域）
    add_header 'Access-Control-Allow-Origin' '*';
  }

  # 代理到本地文件目录，这里我们可以指向前端打包后的 dist 目录
  location ~ \.(html|js|css|png|gif)$ {
    root E:\workspace\wxApp\dist;
  }

  error_page   500 502 503 504  /50x.html;
  location = /50x.html {
    root   html;
  }
}
```

## nginx 更多能力

### 开启 gzip 压缩

```bash
# 默认 gzip on; 是被注释掉的
gzip on;
  # 超过5k才进行压缩
  gzip_min_length 5k;
  gzip_buffers 4 16k;
  #gzip_http_version 1.0;
  # 压缩等级，等级越高，耗时越长，要权衡
  gzip_comp_level 2;
  # 要压缩文件的MIME类型
  gzip_types text/plain application/x-javascript application/javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
  gzip_vary off;
  # IE6以下就不用压缩了
  gzip_disable "MSIE [1-6]\.";
```
