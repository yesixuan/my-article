---
title: express
tags: ["nodejs","express"]
date: 2017-07-19 11:01:00
categories:
- 后台
- nodejs
---
> 曾经听说过这样一个说法：jQuery之于JS就好似express之于nodejs。如此这般，大家应该知道express的江湖地位了...  

<!-- more -->

在我看来，express最亮眼的便是它的中间件。通过各种中间件，我们对服务中最重要的两个参数`req`、`res`进行加工，加工过后的`req`、`res`就有我们经常需要用到的数据以及更加好用的方法。
## 一览

```js
  const express = require('express')
  const cookieParser = require('cookie-parser')
  const cookieSession = require('cookie-session')
  const bodyParser = require('body-parser')
  const multer = require('multer')
  const consolidate = require('consolidate')

  var server = express()
  server.listen(4001)

  // 1. 解析cookie
  server.use(cookieParser('nsdojgf45642norjes'))
  // 2. 使用session
  server.use(cookieSession({
    name: 'xuan_sess',
    keys: ['shd','dsr156er','nherisg','bdsrgfij'],
    maxAge: 1000*3600*20
  }))
  // 3. post数据
  server.use(bodyParser.urlencoded({extended: false}))
  server.use(multer({dest: './www/upload'}).any())
  // 4. 模板引擎
  server.set('view engine', 'html') // 要输出什么
  server.set('views', './views') // 指定模板目录
  server.engine('html', consolidate.ejs) // 指定引擎
  // 6. 接口处理
  server.get('/index', (req, res, next) => {
    res.render('1.ejs', {name: 'yesixuan'})
  })
  // 5. 静态数据
  server.use(express.static('./www'))
```

## 解析数据

### 解析get数据
express原生解析出get参数，并将其挂在req的query属性上。
```js
  // ...
  server.get('/user', (req, res) => {
  // req.query,直接可以拿到查询参数
    console.log(req.query)
  })
```

### 解析post数据
解析post数据需要借助`body-parser`提供的中间件。它能帮助我们解析出post数据，并将其挂在req的body属性上。
```js
  const bodyParser = require('body-parser')
  // ...
  server.use(bodyParser.urlencoded({extended: false})) // 取消扩展模式，免得它老是发出警告
  // ...
  console.log(req.body) // post数据
```

### 解析上传的二进制数据
解析上传的文件需要借助`multer`模块。它解析出来的数据挂在req.files上。
```js
  const fs = require('fs')
  const pathLib = require('path')
  const multer = require('multer')
  // ...
  server.use(multer({dest: './www/upload'}).any()) // 指定文件上传的路径，不让buffer数据大量占用内存
  // ...
  /* 使用pathLib.parse('路径带文件名')可以解析更多信息，也可以拿到文件后缀名 */
  var newName = req.files[0].path + pathLib.extname(req.files[0].originalname)
  // 文件重命名，这里只是改了后缀名
  fs.rename(req.files[0].path, newName, err => {
    if(err)
      res.send('上传失败！')
    else
      res.send('ok!')
  })
  // ...
  console.log(req.files) // 对象数组，每个元素包含该文件的各种信息
```

## cookie && session

### cookie
cookie是在浏览器保存一些数据，每次请求都会带过来。只能存4k的数据。  
使用cookie时要注意两点：① 空间小，精打细算；② 使用时要校验cookie是否被篡改。  
```js
  const cookieParser = require('cookie-parser')
  // ... 使用中间件，这里可以添加密钥作为中间件的参数
  server.use(cookieParser())
  // ... 写入cookie
  /* 指定只有在‘/aaa’下才有cookie，保质期为一个月，是否需要签名 */
  res.cookie(res.cookie('user', 'xuan', {path: '/aaa', maxAge: 30*24*3600*1000, signed: true}))
  // ... 读取cookies
  /* 读取,子级目录可以读根级。即树枝可以去寻根。例如/aaa/bbb可以读/aaa下的cookie */
  console.log(req.cookies)
  // ... 删除cookie
  res.clearCookie('user')
```
给cookie签个名。（签名虽然不能加密数据，但是可以保证数据一旦被篡改，我能够知道）  
```js
  // ...
  server.use(cookieParser('shdgopedfgh')) // 整个密钥
  res.cookie('user', 'xuan', {signed: true})
  console.log(req.cookies) // 没有签过名的cookie
  console.log(req.signedCookies) // 签过名的cookie
```

### session
cookie没太多必要加密，真正机密的东西往session中放就好了。一定要用的话，`cookie-encrypter`。下面上session。
```js
  const cookieSession = require('cookie-session')
  // ...
  // 这个要在cookieParser之下
  server.use(cookieSession({
    keys: ['aaa', 'bbb', 'ccc'], // 这个数组越长，越是安全
    name: 'sess', // 如果没这个参数，默认就是在cookie中的键名就是session
    maxAge: 1000*3600*2 // 两小时未操作，自动注销
  }))
  // ... 使用session
  server.use('/', (req, res) => {
    if(req.session['count'] == null) {
      req.session['count'] = 1
    }else {
      req.session['count']++
    }
    console.log(req.session['count'])
    res.send('ok')
  })
  // ... 删除session
  delete req.session // session是存储在服务器上的数据，所以我们可以使用JS原生的方法来删除session
```

## 后台模版
虽然后台模版渲染是与现代的开发方式背道而驰的，现在大家比较推崇的是后台提供数据，前端获取数据，渲染模版。  
但是我在使用后台模版的时候，竟然找到了像撸react时相似的感觉。估计很多前端渲染模版的灵感也是来自后台模版吧。  

### 模版渲染
```js
  // consolidate帮我们整合了各种后台模版，甚至包括react
  const consolidate = require('consolidate')
  server.set('view engine', 'html') // 要输出什么
  server.set('views', './templates') // 指定模板目录
  server.engine('html', consolidate.ejs) // 指定引擎
  // ... {}里面传入模版中需要的参数
  res.render('index.ejs', {})
```

### jade语法
- 属性，使用小括号：img(src="./xx.jpg",alt="xxx")
- 内容，空格往标签后面写：a 链接
- style属性的对象写法：div(style={width:'200px',...})
- class的数组写法：div(class=['aa',...])
- 写class与id可以使用类似emmet写法
- 标签后面加上&attributes，可以用对象方式写多个属性
- 在内容前加‘|’，表示原样输出内容（script标签里的多行代码）
- 在标签后加‘.’，表示里面的内容原样输出
- include可以引入一个外部文件，include a.js（引入的内容还是嵌在页面中的）
- 使用变量：#{变量}，变量定义在renderFile(,{},)方法的‘{}’中（还可以写表达式）
- 可以写两个class属性，jade自会处理好
- 以‘-’开头的，解析为js。前面一行加了‘-’，下面与它平级或下级的都不用加‘-’
- span #{name}与span=name，两者等价
- 不让变量里面的尖括号被转义，在‘=’前面加‘!’
- 原来的switch-case变成case-when结构。  

### ejs语法
- 变量：<%= name %>
- js语法：<% 脚本 %>
- 引入外部文件的内容：<% include 路径 %>
- include不是原生js的语法，所以使用include时，要单独起一行用“<% %>”包裹起来

## express与数据库

### 连接mysql
```JS
  const mysql = require('mysql')
  // 使用连接池而不是创建连接，以提高性能（会创建多个链接，以保持跟数据库的持久通话）
  const db = mysql.createPool({
    host: 'localhost',
    user: 'root',
    password: '123456',
    database: 'blog'
    // ... 默认是3306端口的话，就不用在此处配置
  })
  // ... 增删改查（查询语句中使用反引号是极好的）
  db.query('select ID,title,summery from article_table', (err, data) => {
    if(err) {
      // 这里的链式操作也是推荐写法
      res.status(500).send('database err!').end()
    }else {
      // 将文章数据挂到res上，传递到下一层去。
      res.articles = data
      next()
    }
  })
```

### 连接mongodb
```js
  const MongoClient = require('mongodb').MongoClient
  const databaseUrl = 'mongodb://localhost:27017/xuan'
  // ...
  MongoClient.connect(databaseUrl, (err, db) => {
    if(err) {
      res.status(500).send('database err!').end()
      return
    }
    res.send('数据库连接成功！')
    db.collection('teacher').insert({'name':'Mary'}, (err, result) => {
      if(err) {
        res.status(500).send('插入数据失败！').end()
        return
      }
      res.status(200).send('数据插入成功！').end()
      db.close()
    })
  })
```
