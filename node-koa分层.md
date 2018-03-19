---
title: koa中的mvc分层
tags: ["koa"]
date: 2018-01-04 19:14:00
categories:
- 后端
- node
- koa
---
> 在koa中实现mvc分层...

<!-- more -->
## app.js（koa入口）

koa入口文件主要做三件事：创建服务、装载middleware、将路由控制权交给router.js模块。
```js
const Koa = require('koa');
const router = require('./router/router.js');
const middleware = require('./middleware/index');

const app = new Koa();

middleware(app); // 给koa实例增加某些超能力
router(app); // 路由处理

app.listen(3000, () => {
  console.log('server is running at http://localhost:3000');
})
```

## route（路由）

router模块相当于现实生活中的交警，指挥不同的请求流入不同的通道（即控制器）。
```js
const router = require('koa-router')();
const HomeController = require('../controller/home');

module.exports = app => {
  router.get('/', HomeController.index)
  router.get('/home', HomeController.home)
  router.get('/user', HomeController.user)
  router.get('/user/:id', HomeController.homeParams)
  router.post('/user/register', HomeController.register)
  router.all('*', async(ctx, next) => {
    ctx.body = `<h1>404 NOT FOUND<h1>`
  })

  app.use(router.routes());
}
```

## controller（控制器）

控制器是路由下面的一员大将，他能够掌控一些大方向。但是涉及到一些琐碎的（如业务逻辑）事情，他会将这些事情委派给他的手下——service.
```js
const HomeService = require('../service/home');

// 简单来看，他仅仅是暴露了一堆函数
module.exports = {
  index: async(ctx, next) => {
    ctx.body = `<h1>index page</h1>`
  },
  home: async(ctx, next) => {
    ctx.body = `<h1>HOME page</h1>`
  },
  user: async(ctx, next) => {
    ctx.body = `
      <h1>
        <form action="/user/register" method="post">
          <input name="name" type="text" placeholder="用户名"/><br />
          <input name="password" type="password" placeholder="密码"/><br />
          <button type="submit">gogo</button>
        </form>
      </h1>
    `
  },
  homeParams: async(ctx, next) => {
    ctx.body = `<h1>您的id是：${ctx.params.id}</h1>`
  },
  register: HomeService.register, // 注册任务，委派给service
}
```

## service（服务）

service用来处理一些琐碎的的活儿（涉及到具体业务的）。
```js
/* 他也只是暴露了一堆的处理函数 */
module.exports = {
  register: async(ctx, next) => {
    console.log(ctx.request.body);
    let { name, password } = ctx.request.body;
    if(name === 'hehe' && password === '123') {
      ctx.body = `<h1>欢迎${name}，登录成功！</h1>`
    }else {
      ctx.body = `<h1>帐号信息有误！</h1>`
    }
  },
}
```

## middleware（中间件）

中间件的出现是为了给koa实例增加一些能力，比如：静态文件呈现、解析post数据、直接给前端发送json对象...
```js
/* middleware/index.js */
const path = require('path');
const staticFiles = require('koa-static');
const bodyParser = require('koa-bodyparser');

const miSend = require('./mi-send/index'); // 自定义中间件

module.exports = app => {
  app.use(staticFiles(path.resolve(__dirname, '../public')));
  app.use(bodyParser());
  app.use(miSend());
}
```

```js
/* middleware/mi-send/index.js */
module.exports = () => {
  function render(json) {
    this.set('Content-Type', 'application/json');
    this.body = JSON.stringify(json);
  }
  return async(ctx, next) => {
    ctx.send = render.bind(ctx);
    await next();
  }
}
```
