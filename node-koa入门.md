---
title: koa入门
tags: ["koa"]
date: 2017-08-14 15:29:00
categories:
- 后端
- node
- koa
password: 
---
> koa是由Express原班人马打造的，致力于成为一个更小、更富有表现力、更健壮的Web框架。...

<!-- more -->
## 基本用法
### 搭建最简单的服务
```js
const Koa = require('koa')
const app = new Koa()
app.listen(3000)
```

### Context对象
Koa提供一个Context对象，表示一次对话的上下文（包括HTTP和HTTP）。通过加工这个对象，就可以控制返回给用户的内容。  
常用对象：
Context.response.body：将你想返回给前端的数据赋值给该对象。  

### Content-Type
Koa返回的mine类型是是text/plain，如果想返回其他类型的内容，可以先用ctx.request.accepts判断一下，客户端希望接受什么数据（根据 HTTP Request 的Accept字段），然后使用ctx.response.type指定返回类型。

## 路由
通过ctx.request.path可以获取用户请求的路径，由此实现简单的路由。但如果你不是想做一个玩具的话，你是不会用这种方式来实现路由的。  
一般来说，我们会借助`koa-route`模块来实现路由。
### koa-route模块
```js
const route = require('koa-route')
const about = ctx => {
  ctx.response.type = 'html'
  ctx.response.body = '<a href="/">Index Page</a>'
}
const main = ctx => {
  ctx.response.body = 'Hello World'
}
app.use(route.get('/', main))
app.use(route.get('/about', about))
```

### 静态服务
express自身具备提供静态服务的功能，但是到了koa，这个功能被剥离出去，通过`koa-static`来实现。  
```js
const path = require('path')
const serve = require('koa-static')
// 将指定的目录静态呈递出去
const main = serve(path.join(__dirname))
app.use(main)
```
### 重定向
ctx.response.redirect()方法可以发出一个302跳转，将用户导向另一个路由。
```js
const redirect = ctx => {
  // 这个方法有点像乾坤大挪移
  ctx.response.redirect('/')
  ctx.response.body = '<a href="/">Index Page</a>'
}
app.use(route.get('/redirect', redirect))
```

## 中间件
中间件大概是koa中，最核心也是最重要的概念了。  
### 中间件栈
多个中间件串联起来就形成了中间件栈。最先添加的中间件最先入栈，一旦在中间件中调用next()，那么代码的执行环境会从当前中间件跳到其栈结构上面的中间件中。到了栈结构顶部的中间件时，顶层的中间件开始出栈，最终回到栈结构底部。  
这就类似于我们向天空抛出一颗石子，石子先上升，后掉落的过程。

### 异步中间件
异步中间件需要将中间件写成async函数。   
```js
const main = async function (ctx, next) {
  ctx.response.type = 'html'
  ctx.response.body = await fs.readFile('./demos/template.html', 'utf8')
}
```

### 中间件的合成
koa-compose模块可以将多个中件合成为一个。  
```js
const compose = require('koa-compose')
const logger = (ctx, next) => {
  console.log(`${Date.now()} ${ctx.request.method} ${ctx.request.url}`)
  next()
}
const main = ctx => {
  ctx.response.body = 'Hello World'
}
const middlewares = compose([logger, main])
app.use(middlewares)
```

## 错误处理
### 500错误
```js
const main = ctx => {
  ctx.throw(500)
}
```

### 404错误
如果将ctx.response.status设置成404，就相当于ctx.throw(404)，返回404错误。   
```js
const main = ctx => {
  ctx.response.status = 404
  ctx.response.body = 'Page Not Found'
}
```

### 抛出错误
为了方便处理错误，最好使用try...catch将其捕获。但是，为每个中间件都写try...catch太麻烦，我们可以让最里层的中间件，负责所有中间件的错误处理。  
```js
const handler = async (ctx, next) => {
  try {
    await next()
  } catch (err) {
    ctx.response.status = err.statusCode || err.status || 500
    ctx.response.body = {
      message: err.message
    }
  }
}
const main = ctx => {
  ctx.throw(500)
}
app.use(handler)
app.use(main)
```

### error事件的监听
运行过程中一旦出错，Koa会触发一个error事件。监听这个事件，也可以处理错误。  
```js
const main = ctx => {
  ctx.throw(500)
}
app.on('error', (err, ctx) =>
  console.error('server error', err)
)
```

### 释放error事件
需要注意的是，如果错误被try...catch捕获，就不会触发error事件。这时，必须调用ctx.app.emit()，手动释放error事件，才能让监听函数生效。  
```js
const handler = async (ctx, next) => {
  try {
    await next()
  } catch (err) {
    ctx.response.status = err.statusCode || err.status || 500
    ctx.response.type = 'html'
    ctx.response.body = '<p>Something wrong, please contact administrator.</p>'
    ctx.app.emit('error', err, ctx)
  }
}
const main = ctx => {
  ctx.throw(500)
}
app.on('error', function(err) {
  console.log('logging error ', err.message)
  console.log(err)
})
```

## Web App的功能
### Cookies
ctx.cookies用来读写Cookie。  
```js
const main = function(ctx) {
  // 读cookies
  const n = Number(ctx.cookies.get('view') || 0) + 1
  // 写cookies
  ctx.cookies.set('view', n)
  ctx.response.body = n + ' views'
}
```

### 表单
`koa-body`模块可以用来从POST请求的数据体里面提取键值对。  
```js
const koaBody = require('koa-body')
const main = async function(ctx) {
  const body = ctx.request.body
  if (!body.name) ctx.throw(400, '.name required')
  ctx.body = { name: body.name }
}
app.use(koaBody())
```

### 文件上传
`koa-body`模块还可以用来处理文件上传。  
```js
const os = require('os')
const path = require('path')
const koaBody = require('koa-body')
const main = async function(ctx) {
  const tmpdir = os.tmpdir()
  const filePaths = []
  const files = ctx.request.body.files || {}
  for (let key in files) {
    const file = files[key]
    const filePath = path.join(tmpdir, file.name)
    const reader = fs.createReadStream(file.path)
    const writer = fs.createWriteStream(filePath)
    reader.pipe(writer)
    filePaths.push(filePath)
  }
  ctx.body = filePaths
}
app.use(koaBody({ multipart: true }))
```

### 最后
本文大量参考阮一峰的网络日志。[原文链接](http://www.ruanyifeng.com/blog/2017/08/koa.html)
