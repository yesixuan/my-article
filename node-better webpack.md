---
title: webpack2优化
tags: ["webpack"]
date: 2017-07-26 15:38:00
categories:
- 前端
- webpack
---
> webpack是现代前端开发中最常用的的构建打包工具之一，或许连“之一”都可以去掉。它的构建打包速度将直接影响我们的开发效率...  

<!-- more -->
## 优化输出
### 压缩css
css-loader在webpack2里默认是没有开启压缩的，最后生成的css文件里面有很多空格。通过配置`css-loader?minimize`参数可以开启压缩输出最小的css。css的压缩实际是通过cssnano实现的。  
### tree-shaking
tree-shaking是指借助es6 import export语法静态性的特点来删掉export但是没有import过的东西。  
让tree-shaking工作需要注意以下几点：
- 配置babel让它在编译转化ES6代码时不把ES6的模块语法转化成commonJS的模块语法
```js
"preset": [
  [
    "es2015",
    {
      "modules": false
    }
  ]
]
```
- 大多数npm包的代码是ES5的，但是也有一部分库（redux,react-router等等）开始支持tree-shaking。这些库既包含ES5又包含ES6模块化语法。如redux库中的package.json中会有这两个配置：
```json
"main": "lib/index.js",
"jsnext:main": "es/index.js"
```
我们需要让webpack去到es目录读取代码。为此，我们需要这样配置：
```js
module.exports = {
  resolve: {
    mainFields: ['jsnext:main','main'],
  }
}
```
这样可以让webpack先使用`jsnext:main`查找，在没有时使用main指定的路径。
### 优化UglifyJsPlugin
webpack --optimize-minimize选项会开启UglifyJsPlugin来压缩输出的js，但默认的UglifyJsPlugin的配置还是保留了注释和空格，要覆盖默认配置，要这样写：
```js
new UglifyJsPlugin({
  // 最紧凑输出
  beautify: fase,
  // 删除注释
  comments: fase,
  compress: {
    // 在UglifyJs删除没有用到代码是不输出警告
    warning: false,
    // 删除所有‘console’语句
    drop_console: true,
    // 内嵌定义了但是只用到一次的变量
    collapse_vars: true,
    // 提取出现多次但是没有定义成变量去引用的静态值
    reduce_vars: true
  }
})
```
### 定义环境变量NODE_ENV=production
很多库（比如react）有部分代码是这样的：
```js
if(process.env.NODE_ENV!=='production') {
  // 开发环境才需要用到的代码
}
```
在环境变量NODE_ENV等于production的时候UglifyJs会删掉这块代码。  
### 使用CommonsChunkPlugin抽取公共代码
CommonsChunkPlugin可以提取出多个代码快都依赖的模块形成一个单独的模块。要发挥CommonsChunkPlugin的威力，还要配合浏览器的缓存机制。  
### 生产环境按照文件内容md5打hash
webpack编译在生产环境出来的js、css等资源应该放到CDN上，再根据文件内容的md5命名文件，利用缓存机制，用户只需加载第一次。如此配置便好：
```js
{
  output: {
    publicPath: CDN_URL,
    filename: '[name]_[chunkhash].js',
  }
}
```
配合CommonsChunkPlugin还可以这样玩：
```js
import 'react'
import 'react-dom'
// ...
/* webpack配置 */
{
  entry: {
    vendor: './path/to/vendor.js',
  }
}
```

## 更快的构建
### 缩小文件搜索范围
- 排除node_modules文件夹
```js
module.exports = {
  resolve: {
    modules: [path.resolve(__dirname,'node_modules')]
  }
}
```
- 简化正则表达式(当项目只有js文件是就不要写成/\.jsx?$/)
- 只对项目目录下的代码进行babel编译
```js
{
  test: /\.js$/,
  loader: 'babel-loader',
  include: path.resolve(__dirname,'src')
}
```
### 开启babel-loader缓存
babel编译过程很耗时，好在babel-loader提供缓存编译结果选项，在重启webpack时，不需要重新编译而是复用缓存结果。打开babel-loader缓存配置如下：
```js
module.exports = {
  module: {
    loaders: [{
      test: /.js$/,
      loader: 'babel-loader?cacheDirectory',
    }]
  }
}
```
### 使用noParse
module.noParse可以配置哪些文件脱离webpack的解析（如jquery等）。  
```js
module.exports = {
  module: {
    noParse: /node_modules\/(jquery|chart)\.js/
  }
}
```

## 其他
- happypack
- DllPlugin  
