---
title: react-router
tags: ["react","router"]
date: 2017-05-14 21:50:00
categories:
- 前端
- JS
- react
---
> 前几天苦修react大法，然后从github上拉了一个简单的react项目吗，准备挑个‘软柿子’捏一捏。结果一看人家代码，发现还有很多陌生的面孔，甚至连路由都是一知半解的...

<!-- more -->
### 路由的定义
#### 基本定义
```JS
  import { Router, Route, hashHistory } from 'react-router';
  // 路由本质上也是一个组件，这些组件在这里是作为根级组件来使用的。当然，我们也可以将它放在一个容器组件里面。任何时候都不要忘记，路由也是一个组件。
  render((
    <Router history={hashHistory}>
      <Route path="/" component={App}/>
      <Route path="/repos" component={Repos}/>
      <Route path="/about" component={About}/>
    </Router>
  ), document.getElementById('app'));
```
#### 嵌套路由
```JS
  <Router history={hashHistory}>
    <Route path="/" component={App}>
      // 下面的子组件要被通过"his.props.children"来呈现
      <Route path="/repos" component={Repos}/>
      <Route path="/about" component={About}/>
    </Route>
  </Router>
  // App组件要写成下面的样子。
  export default React.createClass({
    render() {
      return <div>
        // 子组件将被呈现在此处
        {this.props.children}
      </div>
    }
  })
```
#### 将路由配置拆分
子路由也可以不写在Router组件里面，单独传入Router组件的routes属性：
```JS
  let routes = <Route path="/" component={App}>
    <Route path="/repos" component={Repos}/>
    <Route path="/about" component={About}/>
  </Route>;

  <Router routes={routes} history={browserHistory}/>
```
#### path属性
Route组件的path属性指定路由的匹配规则。这个属性是可以省略的，这样的话，不管路径是否匹配，总是会加载指定组件。
```JS
  <Route path="inbox" component={Inbox}>
     <Route path="messages/:id" component={Message} />
  </Route>
  // 与下面这种等价
  <Route component={Inbox}> // 这里省略了path属性，所以不管怎么匹配都会加载Inbox组件
    <Route path="inbox/messages/:id" component={Message} />
  </Route>
```
#### 通配符
- paramName
:paramName匹配URL的一个部分，直到遇到下一个/、?、#为止。这个路径参数可以通过this.props.params.paramName取出。
- ()
()表示URL的这个部分是可选的。
- *
`*`匹配任意字符，直到模式里面的下一个字符为止。匹配方式是非贪婪模式。
- **
** 匹配任意字符，直到下一个/、?、#为止。匹配方式是贪婪模式。

注：path属性也可以使用相对路径（不以/开头），匹配时就会相对于父组件的路径，可以参考上一节的例子。嵌套路由如果想摆脱这个规则，可以使用绝对路由。
#### IndexRoute组件
首先看这段代码：
```JS
  // 这里访问根路径"/"，不会加载任何子组件。
  <Router>
    <Route path="/" component={App}>
      <Route path="accounts" component={Accounts}/>
      <Route path="statements" component={Statements}/>
    </Route>
  </Router>
  // 下面是解决方案，访问根路径"/"，会默认加载Home子组件。（这也是项目中推荐使用的一种方式）
  <Router>
    <Route path="/" component={App}>
      <IndexRoute component={Home}/>
      <Route path="accounts" component={Accounts}/>
      <Route path="statements" component={Statements}/>
    </Route>
  </Router>
```
#### Redirect组件
`<Redirect>`组件用于路由的跳转，即用户访问一个路由，会自动跳转到另一个路由。
```JS
  <Route path="inbox" component={Inbox}>
    /* 从 /inbox/messages/:id 跳转到 /messages/:id */
    ＜Redirect from="messages/:id" to="/messages/:id" />
  </Route>
```
#### IndexRedirect组件
IndexRedirect组件用于访问根路由的时候，将用户重定向到某个子组件。
```JS
  // 这跟IndexRoute组件是有区别的
  <Route path="/" component={App}>
    ＜IndexRedirect to="/welcome" />
    <Route path="welcome" component={Welcome} />
    <Route path="about" component={About} />
  </Route>
```
### 路由跳转
#### Link组件
```JS
  <li><Link to="/about">About</Link></li>

  <Link to="/about" activeStyle={{color: 'red'}}>About</Link>

  <Link to="/about" activeClassName="active">About</Link>
```
#### 在Router组件之外，导航到路由页面
```JS
  import { browserHistory } from 'react-router';
  browserHistory.push('/some/path');
```
#### IndexLink
如果链接到根路由/，不要使用Link组件，而要使用IndexLink组件。
这是因为对于根路由来说，activeStyle和activeClassName会失效，或者说总是生效，因为/会匹配任何子路由。而IndexLink组件会使用路径的精确匹配。
```JS
  // 根路由只会在精确匹配时，才具有activeClassName。
  <IndexLink to="/" activeClassName="active">
    Home
  </IndexLink>
  // 使用Link组件的onlyActiveOnIndex属性，也能达到同样效果。
  <Link to="/" activeClassName="active" onlyActiveOnIndex={true}>
    Home
  </Link>
```
### 周边
#### histroy属性
- browserHistory ：以hash的方式在URL上面呈递。
- hashHistory ：调用浏览器的historyAPI。但是，这种情况需要对服务器改造。
- createMemoryHistory ：主要用于服务器渲染。它创建一个内存中的history对象，不与浏览器URL互动。

#### JS跳转
- browserHistory.push():
```JS
  import { browserHistory } from 'react-router'
  const path = `/repos/${userName}/${repo}`
  browserHistory.push(path)
```
- 使用context对象
```JS
  export default React.createClass({
    // ask for `router` from context
    contextTypes: {
      router: React.PropTypes.object
    },
    handleSubmit(event) {
      // ...
      this.context.router.push(path)
    },
  })
```

#### 路由钩子
每个路由都有Enter和Leave钩子，用户进入或离开该路由时触发。
下面是一个例子，使用onEnter钩子替代<Redirect>组件：
```JS
  <Route path="inbox" component={Inbox}>
    <Route
      path="messages/:id"
      onEnter={
        ({params}, replace) => replace(`/messages/${params.id}`)
      }
    />
  </Route>
```
onEnter钩子还可以用来做认证：
```JS
  const requireAuth = (nextState, replace) => {
    if (!auth.isAdmin()) {
      // Redirect to Home page if not an Admin
      replace({ pathname: '/' })
    }
  }
  export const AdminRoutes = () => {
    return (
       <Route path="/admin" component={Admin} onEnter={requireAuth} />
    )
  }
```
