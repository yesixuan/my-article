---
title: React webapp(二)
tags: ["react"]
date: 2017-05-24 10:55:00
categories:
- 前端
- JS
- react
---
> 写这篇博客的时候，刚刚将这个webapp的首页完成...

<!-- more -->
## 智能组件与木偶组件
我们将react项目中的组件分为智能组件和木偶组件两类，每个组件拥有一个文件夹，文件夹里面包含index.jsx、style.less（它的子组件在它的下级目录）。
### 智能组件
我们把智能组件放到containers文件夹下，containers文件夹下的一级目录放的是每个路由分页面。智能组件主要是作为一个容器，负责给木偶组件传递方法和数据。
### 木偶组件
木偶组件位于components文件夹下。它的作用主要是页面的展示，它是消费父组件（智能组件）传递给他的方法和数据。
## 字体图标
### 制作
[在线制作字体图标](https://icomoon.io/)
### 使用
生成的css文件放在'static/css'文件夹下，字体文件放在'static/fonts'文件夹下（相对路径不要搞错）。
## postcss
上代码：
```JS
  // 引入样式文件
  import styles from './style.less'
  // 使用
  <div className={styles["content-title"]}></div>
```
## fetch的使用
### mock接口
#### 写接口
```JS
// mock/server.js
  var app = require('koa')();
  var router = require('koa-router')();
  //首页广告
  var homeAdData = require('./home/ad.js'); // 这里其实是拿到一个json
  router.get('/api/homead',function *(next){
  	this.body = homeAdData;
  })
  // ...后面可以接着写
  //开启服务
  app.use(router.routes())
     .use(router.allowedMethods());
  app.listen(3002);
```
#### 定义json
```JS
  // mock/home/ad.js
  module.exports = [
    {
        title: '暑假5折',
        img: 'http://images2015.cnblogs.com/blog/138012/201610/138012-20161016191639092-2000037796.png',
        link: 'http://www.imooc.com/wap/index'
    },
    {
        title: '特价出国',
        img: 'http://images2015.cnblogs.com/blog/138012/201610/138012-20161016191648124-298129318.png',
        link: 'http://www.imooc.com/wap/index'
    }
  ]
```
#### 使用
```JS
import AdData from '../../../../mock/home/ad'
import {getAdData} from '../../../fetch/home/home'

componentDidMount() {
  // 第一步，调接口
  const result = getAdData();
  // 第二步，拿到结果，如果成功，将字符串转成JSON对象
  result.then(res => {
    if(res.ok) {
      return res.json()
    }else {
      return AdData // 如果调接口拿数据出错，则直接返回这个import进来的数据
    }
  }).then(json => { // 将转换后的数据跟UI组件的显示数据对接起来
    const data = json
    if(data.length) {
      this.setState({
        data: data
      })
    }
  }).catch(err => {
    console.log(err.message)
  })
}
```
## 滚动事件中的性能优化
```JS
  componentDidMount() {
    const wrapper = this.refs.wrapper
    const loadMoreFn = this.props.loadMoreFn
    // 这里是滚动事件被触发之后的回调函数
    function callback() {
      const top = wrapper.getBoundingClientRect().top
      const windowHeight = window.screen.height
      if(top && top < windowHeight) {
        loadMoreFn() // 从父组件传过来的加载更多数据的方法
      }
    }
    /*
    * 1. 外部定义变量用来存储定时器
    * 2. 在滚动事件里面判断那个变量有没有存定时器
    * 3. 定时器搞起来，用之前的变量存起来
    */
    let timeAction
    window.addEventListener('scroll',() => {
      if(this.props.isLoadingMore) {
        return
      }
      if(timeAction) {
        clearTimeout(timeAction)
      }
      timeAction = setTimeout(callback,50)
    })
  }
```
