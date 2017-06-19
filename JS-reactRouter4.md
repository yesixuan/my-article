---
title: react-router4笔记
tags: ["js","react","router"]
date: 2017-06-19 09:55:00
categories:
- 前端
- JS
- react
---
> 前两天在写react项目的时候发现在路由上面走了很多的弯路。正所谓磨刀不误砍柴工，还是先系统地学习一下吧...

<!-- more -->
## 重要API一览
### 路由容器组件
- BrowserRouter: 浏览器自带的API，restful风格（需要后台做相应的调整）；
- HashRouter: 使用hash方式进行路由；
- MemoryRouter: 在内存中管理history，地址栏不会变化。在reactNative中使用。

### Route标签
- 该标签有三种渲染方式component、render、children（绝大多数情况使用component组件就好了）；
- 三种渲染方式都会得到三个属性match、history、location；
- 渲染组件时，route props跟着一起渲染；
- children方式渲染会不管地址栏是否匹配都渲染一些内容，在这里加动画一时很常见的做法。

### Link标签
- to: 后面可以接字符串，也可以跟对象（对象可以是动态地添加搜索的信息）；
- replace: 当设置为true时，点击链接后将使用新地址替换掉访问历史记录里面的原地址。

### NavLink标签
- <NavLink>是<Link>的一个特定版本, 会在匹配上当前URL的时候会给已经渲染的元素添加样式参数；
- activeClassName，当地址匹配时添加相应class；
- activeStyle，当地址匹配时添加相应style；
- exact，当地址完全匹配时，才生效；
- isActive，添加额外逻辑判断是否生效。

### Prompt标签
- when: when的属性值为true时启用防止转换；
- message: 后面可以跟简单的提示语，也可以跟函数，函数是有默认参数的。

### Redirect标签
- <Redirect/>可以写在<Route/>的render属性里面，也可以跟<Route/>平级；
- to: 依旧是可以跟字符串或对象；
- push: 添加该属性时，地址不会被覆盖，而是添加一条新纪录；
- from: 重定向，与<Route/>平级时。

### match
- params: 通过解析URL中动态的部分获得的键值对；
- isExact: 当为true时，整个URL都需要匹配；
- path: 在需要嵌套<Route/>的时候用到；
- url: 在需要嵌套<Link/>的时候会用到；
- 获取方式: 以this.props.match方式。

```js
  import {
    BrowserRouter as Router, // 或者是HashRouter、MemoryRouter
    Route,   // 这是基本的路由块
    Link,    // 这是a标签
    Switch   // 这是监听空路由的
    Redirect // 这是重定向
    Prompt   // 防止转换  
  } from 'react-router-dom'
```
## 权限控制
利用组件内的Redirect标签。
```js
  const PrivateRoute = ({ component: Component, ...rest }) => (
    <Route {...rest} render={props => (
      fakeAuth.isAuthenticated ? (
        <Component {...props}/>
      ) : (
        <Redirect to={{
          pathname: '/login',
          state: { from: props.location }
        }}/>
      )
    )}/>
  )
```
## 阻止离开当前路由
在组件内部添加Prompt标签来进行权限控制。
```html
  <Prompt
    when={isBlocking}
    message={location => (
      `你真的要跳转到 ${location.pathname}么？`
    )}
  />
```
## 过渡动画
样式分别定义：.example-enter、.example-enter.example-enter-active、.example-leave、.example-leave.example-leave-active。
[实例](https://segmentfault.com/a/1190000009687861)
```html
<ReactCSSTransitionGroup
  transitionName="fade"
  transitionEnterTimeout={300}
  transitionLeaveTimeout={300}
>
  <!-- 这里和使用 ReactCSSTransitionGroup 没有区别，唯一需要注意的是要把你的地址（location）传入「Route」里使它可以在动画切换的时候匹配之前的地址。 -->
  <Route
    location={location}
    key={location.key}
    path="/:h/:s/:l"
    component={HSL}
  />
</ReactCSSTransitionGroup>
```
## 按需加载
### 官方方法
借助`bundle-loader `实现按需加载。
新建一个bundle.js文件：
```js
  import React, { Component } from 'react'
  export default class Bundle extends React.Component {
    state = {
      // short for "module" but that's a keyword in js, so "mod"
      mod: null
    }
    componentWillMount() {
      this.load(this.props)
    }
    componentWillReceiveProps(nextProps) {
      if (nextProps.load !== this.props.load) {
        this.load(nextProps)
      }
    }
    load(props) {
      this.setState({
        mod: null
      })
      props.load((mod) => {
        this.setState({
          // handle both es imports and cjs
          mod: mod.default ? mod.default : mod
        })
      })
    }
    render() {
      if (!this.state.mod)
        return false
      return this.props.children(this.state.mod)
    }
  }
```
在入口处使用按需加载：
```js
  // bundle模型用来异步加载组件
  import Bundle from './bundle.js';
  // 引入单个页面（包括嵌套的子页面）
  // 同步引入
  import Index from './app/index.js';
  // 异步引入
  import ListContainer from 'bundle-loader?lazy&name=app-[name]!./app/list.js';
  const List = () => (
    <Bundle load={ListContainer}>
      {(List) => <List />}
    </Bundle>
  )
  <HashRouter>
    <Router basename="/">
      <div>
        <Route exact path="/" component={Index} />
        <Route path="/list" component={List} />
      </div>
    </Router>
  </HashRouter>
```
webpack.config.js文件配置：
```js
  output: {
    path: path.resolve(__dirname, './output'),
    filename: '[name].[chunkhash:8].bundle.js',
    chunkFilename: '[name]-[id].[chunkhash:8].bundle.js',
  },
```
### 个人觉得更好用的写法
```js
  import Loadable from 'react-loadable'
  import Loading from './my-loading-component'
  Loadable({
    loader: () => import(`views/AsyncView`),
    // 如果没有loading动画，就返回null
    LoadingComponent: () => null,
    // 如果有loading动画，则如下
    loading: Loading
  })
```
webpack配置：
```js
  // 添加插件babel-plugin-import-inspector
  {
    "plugins": [
      ["import-inspector", {
        "serverSideRequirePath": true,
        "webpackRequireWeakId": true,
      }]
    ]
  }
```
