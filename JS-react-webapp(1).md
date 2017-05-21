---
title: React webapp(一)
tags: ["react"]
date: 2017-05-20 22:25:00
categories:
- 前端
- JS
- react
---
> 额，五月二十号这么特殊的一天。我还是写一篇技术博客吧...

<!-- more -->
### webpack
#### 自动打开浏览器
```JS
  var OpenBrowserPlugin = require('open-browser-webpack-plugin');
  // 插件配置
  plugins: [
    ...
    // 打开浏览器
    new OpenBrowserPlugin({
      url: 'http://localhost:8080'
    }),
    ...
  ],
```
#### 自动添加CSS兼容属性
```JS
  postcss: [
      require('autoprefixer') //调用autoprefixer插件，例如 display: flex
  ],
```
#### 将第三方依赖库打包在一起
```JS
entry: {
    app: path.resolve(__dirname, 'app/index.jsx'),
    // 将 第三方依赖 单独打包
    vendor: [
      'react',
      'react-dom',
      ...
    ]
  },
  output: {
    path: __dirname + "/build",
    filename: "[name].[chunkhash:8].js",
    publicPath: '/'
  },
  plugins: [
    ...
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      filename: '[name].[chunkhash:8].js'
    }),
  ]
```
#### 提高组件利用率
```JS
  plugins: [
    ...
    // 为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID
    new webpack.optimize.OccurenceOrderPlugin(),
  ]
```
#### 分离CSS文件
```JS
  var ExtractTextPlugin = require('extract-text-webpack-plugin');
  plugins: [
    ...
    // 为组件分配ID，通过这个插件webpack可以分析和优先考虑使用最多的模块，并为它们分配最小的ID
    new ExtractTextPlugin('[name].[chunkhash:8].css'),
  ]
```
### mock
#### 使用koa写接口
```JS
  // mock/server.js（使用nodejs启动这个koa服务）
  var app = require('koa')();
  var router = require('koa-router')();

  router.get('/', function *(next) {
      this.body = 'hello koa !'
  });
  router.get('/api', function *(next) {
      this.body = 'test data'
  });
  router.get('/api/1', function *(next) {
      this.body = 'test data 1'
  });
  app.use(router.routes())
     .use(router.allowedMethods());
  app.listen(3000);
```
#### 设置代理
```JS
  devServer: {
    proxy: {
      // 凡是 `/api` 开头的 http 请求，都会被代理到 localhost:3000 上，由 koa 提供 mock 数据。
      '/api': {
        target: 'http://localhost:3000',
        secure: false
      }
    },
    ...
  }
```
### localStorage
```JS
// util/localStorage.js
export default {
  getItem: function (key) {
    let value
    try {
      value = localStorage.getItem(key)
    } catch (ex) {
      // 开发环境下提示error
      if (__DEV__) {
        console.error('localStorage.getItem报错, ', ex.message)
      }
    } finally {
      return value
    }
  },
  setItem: function (key, value) {
    try {
      // ios safari 无痕模式下，直接使用 localStorage.setItem 会报错
      localStorage.setItem(key, value)
    } catch (ex) {
      // 开发环境下提示 error
      if (__DEV__) {
        console.error('localStorage.setItem报错, ', ex.message)
      }
    }
  }
}
```
### loading
在系统还没准备好的时候展示一些其他的组件：
```JS
// 当我们的app准备好了再改变initDone的值，重新渲染组件
render() {
  return (
    <div>
      {
        this.state.initDone
          ?this.props.children
          :<p>Loding...</p>
      }
    </div>
  )
}
```
