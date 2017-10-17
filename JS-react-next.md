---
title: react ssr入门
tags: ["js","react","ssr"]
date: 2017-10-17 09:12:00
categories:
- 前端
- JS
- react
---
> react搭配nextjs走马观花...

<!-- more -->

## 因果
angular、react、vue等现代前端框架带领前端进入spa（单页面应用）时代。从此前端可以自己操控路由，页面与页面之间不再是一个个孤立的网页，而是一个完整的应用。  
但是但页面应用的盛行也带来了一些问题：  
①、不利于seo，所有内容都是通过后台异步获取，搜索引擎抓取内容的时候是抓不到有意义的内容的。（据说谷歌等搜索引擎已经能够通过ajax抓取有意义的内容了，但目前的百度无疑是不行的）；  
②、首屏加载时间过长。主要原因是我们打包出来的bundle文件过大，即使经过路由组件拆分，依旧不能达到用户的预期。  

如何解决spa带来的副作用，针对react的ssr，我们请出今天的主角nextjs.  

## 入门
### 安装依赖
```bash
# c创建项目目录并安装以下依赖
npm install --save next react react-dom
```

### 定义命令
运行`npm run dev`将会在本机的3000端口呈现nextjs渲染出来的内容
```js
// 在package.json中定义一下命令
{
  "scripts": {
    "dev": "next",
    "build": "next build",
    "start": "next start"
  }
}
```  
在项目中增加两个文件夹：pages、static。  
pages文件夹中的组件路径映射到URL对应目录上，static文件夹存放静态资源。  

## 页面之间的导航
### Link组件
我们可以通过nextjs提供的Link组件进行页面之间的跳转。  
```js
// pages/index.js
import Link from 'next/link'

const Index = () = (
  <div>
    <Link href="/about">
      <a>About Page</a>
    </Link>
  </div>
)

export default Index
```
### 给链接添加样式
Link组件是一个抽象的高阶组件，所以给它加样式是不起作用的。  
```html
<Link href="/about">
  <a style={{ fontSize: 20 }}>About Page</a>
</Link>
```

## 共享组件
共享组件存放的路径是可以自定义的，事实上，除了pages以及static文件夹，其他文件夹名称都可以自定义。  
```js
/* components/MyLayout.js */
import Header from './Header'

// 页面布局的样式定义在Layout组件中
const layoutStyle = {
  margin: 20,
  padding: 20,
  border: '1px solid #DDD'
}

const Layout = (props) => (
  <div style={layoutStyle}>
    <Header />
    {/* 相当于一个插槽 */}
    {props.children}
  </div>
)

export default Layout
```

## 传递消息，创建动态页面
很多时候，我们是根据不同的信息动态地渲染不同内容的页面，如给页面传入不同的ID从而渲染不同的详情内容。  

### 传递参数
```js
/* pages/index.js */
import Layout from '../components/MyLayout.js'
import Link from 'next/link'

const PostLink = (props) => (
  <li>
    {/* 将title属性值拼接到要跳转的URL地址后面，实际上就是通过URL传参 */}
    <Link href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)

export default () => (
  <Layout>
    <h1>My Blog</h1>
    <ul>
      <PostLink title="Hello Next.js"/>
      <PostLink title="Learn Next.js is awesome"/>
      <PostLink title="Deploy apps with Zeit"/>
    </ul>
  </Layout>
)
```

### 接受参数
```js
/* pages/post.js */
import Layout from '../components/MyLayout.js'

const Content = (props) => (
  <div>
    {/* 通过props下的url对象拿到URL上的查询字符串 */}
    <h1>{props.url.query.title}</h1>
    <p>This is the blog post content.</p>
  </div>
)

// props中的url对象默认只暴露给了根组件
export default (props) => (
    <Layout>
      {/* 将url传给子组件 */}
      <Content url={props.url} />
    </Layout>
)
```
此时，在浏览器地址栏中输入：http://localhost:3000/post?title=Hello%20Next.js 就可以看到效果了。  

## 路由掩码
路由掩码的作用实际上就是将真实丑陋的URL地址掩盖起来，呈现给用户一个清爽的URL。  
就像上节的栗子：我们的真实URL地址是“http://localhost:3000/post?title=Hello%20Next.js”，但是我们希望呈递给用户的URL是“http://localhost:3000/p/hello-nextjs”

### Link的as属性
```js
const PostLink = (props) => (
  <li>
    {/* href代表的是真实的URL地址，as代表的是我们呈递给用户看的假URL */}
    <Link as={`/p/${props.id}`} href={`/post?title=${props.title}`}>
      <a>{props.title}</a>
    </Link>
  </li>
)
```
整完之后，浏览器中的前进后退都是可以实现的，但是如果在“http://localhost:3000/p/hello-nextjs”时，直接刷新页面就会报页面404。接下来我们来看看如何通过nodejs修复这个bug。

### 服务器端配合
安装express  
```bash
# 这里我们使用express来处理
npm install --save express
```
创建服务  
```js
/* server.js */
const express = require('express')
const next = require('next')

const dev = process.env.NODE_ENV !== 'production'
const app = next({ dev })
const handle = app.getRequestHandler()

app.prepare()
.then(() => {
  const server = express()

  // 关键在此处
  server.get('/p/:id', (req, res) => {
    const actualPage = '/post'
    const queryParams = { title: req.params.id }
    app.render(req, res, actualPage, queryParams)
  })

  server.get('*', (req, res) => {
    return handle(req, res)
  })

  server.listen(3000, (err) => {
    if (err) throw err
    console.log('> Ready on http://localhost:3000')
  })
})
.catch((ex) => {
  console.error(ex.stack)
  process.exit(1)
})
```

### 更新启动命令
```js
/* package.js */
{
  "scripts": {
    "dev": "node server.js"
  }
}
```

## 页面加载前获取初始数据

### 直接刷新页面获取值
有时候我们在页面初始化时需要先从后台服务器中获取数据。此时我们就要祭出`getInitialProps`与`async`了。  
```js
import Layout from '../components/MyLayout.js'
import Link from 'next/link'
import fetch from 'isomorphic-unfetch'

const Index = (props) => (
  <Layout>
    <h1>Batman TV Shows</h1>
    <ul>
      {props.shows.map(({show}) => (
        <li key={show.id}>
          <Link as={`/p/${show.id}`} href={`/post?id=${show.id}`}>
            <a>{show.name}</a>
          </Link>
        </li>
      ))}
    </ul>
  </Layout>
)

/* 关键在此处 */
Index.getInitialProps = async function() {
  const res = await fetch('http://api.tvmaze.com/search/shows?q=batman')
  const data = await res.json()

  console.log(`Show data fetched. Count: ${data.length}`)

  return {
    shows: data
  }
}

export default Index
```
这段打印的信息只会在服务器端打印出来，浏览器控制来不会有这段信息。理当如此！  

### 从其他页面跳转后获取初始数据
```js
/* pages/post.js */
import Layout from '../components/MyLayout.js'
import fetch from 'isomorphic-unfetch'

const Post =  (props) => (
    <Layout>
       <h1>{props.show.name}</h1>
       <p>{props.show.summary.replace(/<[/]?p>/g, '')}</p>
       <img src={props.show.image.medium}/>
    </Layout>
)

// 通过context拿到ID值，根据ID异步获取数据，这个过程发生在浏览器中
Post.getInitialProps = async function (context) {
  const { id } = context.query
  const res = await fetch(`http://api.tvmaze.com/shows/${id}`)
  const show = await res.json()

  console.log(`Fetched show: ${show.name}`)

  return { show }
}

export default Post
```
通过其他页面跳转过来的是通过浏览器异步地获取数据，就像从前一样。  

## 部署
将nextjs项目部署到服务器上需要解决两个问题：①、项目的启动要随之操作系统的启动而启动；②、将80端口代理到nextjs开启的端口上。  

### 使用PM2来管理我们的Next.js进程
1. 启动next.js进程  
```bash
# 自定义Express服务器
# https://github.com/zeit/next.js/tree/master/examples/custom-server-express
NODE_ENV=production pm2 start ./server.js --interpreter ./node_modules/.bin/babel-node --watch src --name next-blog
# 默认Next.js内置的方式
NODE_ENV=production pm2 start npm --name "next-blog" -- start
```  

2. 保存启动信息  
```bash
pm2 save
```  

3. 创建系统启动服务  
```bash
# 以Ubuntu 16.04为例, 它会创建一个名为pm2-www.service的SYSTEMD服务.
pm2 startup
```  

4. 查看PM2管理的Next.js应用程序状态  
```bash
systemctl status pm2-www.service
``` 

### 代理端口  
1. 指定Next.js应用运行的端口  
```js
/* 首先配置 package.json */
"scripts": {
  "start": "next start -p $PORT"
}
```
```bash
PORT=8000 yarn start
```  

2. 使用Ngninx反向代理  
```bash
# 也可以不直接指定端口, 让Next.js 应用程序在Nginx反向代理后面跑.
location / {
  # default port, could be changed if you use next with custom server
  proxy_pass http://localhost:3000;

  proxy_http_version 1.1;
  proxy_set_header Upgrade $http_upgrade;
  proxy_set_header Connection 'upgrade';
  proxy_set_header Host $host;
  proxy_cache_bypass $http_upgrade;

  # if you have try_files like this, remove it from our block
  # otherwise next app will not work properly
  # try_files $uri $uri/ =404;
}
```
