---
title: CommonJs规范以及nodejs的包管理工具
tags: ["nodejs","module","npm"]
date: 2017-07-19 09:06:00
categories:
- 后台
- nodejs
---
> 借助nodejs的生态开发也有不断的一段时间了，常用的一直是那几个常用的命令。如今，是时候好好理一理了...

<!-- more -->
## CommonJs规范

### 一览

module代表当前的模块，而module上的exports属性是这个模块对外暴露对象的接口。require其实就是导入某个模块的exports属性
```js
  /* 导出模块 */
  module.exports = {}
  /* 导入模块 */
  const xx = require('xx')
```

### 特点
- 模块可以多次加载，但是只会在第一次加载时运行一次，然后运行结果就被缓存了，以后再加载，就直接读取缓存结果。要想让模块再次运行，必须清除缓存
- 模块加载的顺序，按照其在代码中出现的顺序（这也是我们一般将导入模块的操作放到最顶部的原因）

### module.exports与exports
为了方便，Node为每个模块提供一个exports变量，指向module.exports。等同在每个模块头部，有一行这样的命令：`var exports = module.exports`。  
这样就有两个值得我们注意的点了：
1. 我们可以在exports变量上添加属性，但是不能直接给exports变量赋值
  ```js
    /* 正确做法 */
    exports.exp = function() {}
    /* 错误的做法 */
    exports = function exp() {}
  ```
2. 在给exports添加属性后不能给module.exports重新赋值
  ```js
    exports.exp = function() {}
    // 实际对外输出的就是module.exports属性，这样就切断了module.exports与exports的联系
    module.exports = 'Hello world'
  ```

### require
require的常规用法这里就不再多说了，这里只说以前忽略的点。  
目录加载规则：有些时候，我们在require的参数中只写目录而不写文件名。在没有特殊指定的情况下，node会加载该目录下的index.js或是index.node文件。当然我们也可以指定目录与文件名:
```js
  // package.json
  {
    "name" : "some-library",
    "main" : "./lib/some-library.js"
  }
```

### 模块加载机制
CommonJS模块的加载机制是，输入的是被输出的值的拷贝。也就是说，一旦输出一个值，模块内部的变化就影响不到这个值。

## npm

### 常用命令
- npm init：创建package.json文件
- npm login：登录npm
- npm whoami：告诉你当前登录用户是谁
- npm publish：发布自己的npm包
- npm unpublish --force：删除自己的npm包（每个版本都得删）
- npm config list：查看配置信息

### 踩过的坑
之前发布不成功，报了403的错误。解决方案是要讲淘宝的源切换会npm官方的源。然后还要重新登录。   
某些模块被墙的解决方案（以node-sass为例）：
```bash
  npm install --save node-sass --registry=https://registry.npm.taobao.org --disturl=https://npm.taobao.org/dist --sass-binary-site=http://npm.taobao.org/mirrors/node-sass
  # --registry=https://registry.npm.taobao.org 淘宝npm包镜像
  # --disturl=https://npm.taobao.org/dist 淘宝node源码镜像，一些二进制包编译时用
  # --sass-binary-site=http://npm.taobao.org/mirrors/node-sass 这个才是node-sass镜像
```

## yarn
yarn安装模块的速度着实把我给惊艳到了，这里必须要说道说道。

### 常用命令
- yarn
- yarn init
- yarn add：默认添加到开发依赖
- yarn upgrade
- yarn add --offline：指定离线安装（当前最新版本好像不太需要显式指定）
- yarn add --dev：添加到开发依赖
- yarn remove
- yarn publish
- yarn config set 淘宝源
- yarn global add：全局安装（yarn不推荐滥用全局安装）
- yarn self-update
- yarn run：运行package.json中的脚本
