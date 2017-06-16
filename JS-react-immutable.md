---
title: immutable在react中的应用
tags: ["js","react","性能"]
date: 2017-06-16 19:44:00
categories:
- 前端
- JS
- react
---
> 江湖传言，immutable-js能够对react应用的性能提升良多。那么就下来就是填坑经历了[参考](https://github.com/chvin/react-tetris/tree/master/src)...

<!-- more -->
## 为什么使用immutable
使用immutable创建的数据集合一旦被创建就无法被改变，每次对这个数据集合进行修改都会返回新的值。这点特性对redux这样的偏函数式框架非常重要。
immutable相似的数据集合能够共享他们之间相同的节点，这对于节省内存是非常有好处的。
## immutable的边界性问题
- 在React视图里，props其实就来自于Redux维护的全局的state的，所以props中的每一项一定是immutable的；
- 在React视图里，组件自己维护的局部state如果是用来提交到store的，必须为immutable的，否则不强；
- 从视图层向同步和异步action发送的数据(A/B)，必须是immutable；
- Action提交给reducer的数据(C/D)，必须是 immutable 的；
- reducer处理后所得state(E)当然一定是immutable的。

除了向服务端发送数据请求的时候，其他位置，不允许出现toJS的代码。而接收到服务端的数据后，在流转入全局 state 之前，统一转化为 immutable 数据。
## 集成 immutable 到流程中
### redux
reducers 我们用 redux-immutable 提供的 combineReducers 来处理，他可以将 immutable 类型的全局 state 进行分而治之。
```js
  import { combineReducers } from 'redux-immutable'
  const rootReducer = combineReducers({
  	routing: routingReducer,
  	a: immutableReducer,
  	b: immutableReducer
  })
  export default rootReducer
```
### initialState
```js
  const initialState = Immutable.Map()
  const store = createStore(rootReducer, initialState)
```
### mapStateToProps
```js
  const mapStateToProps = (state) => ({
    pause: state.get('pause'),
    music: state.get('music')
  })
  export default connect(mapStateToProps)(App)
```
## immutable常用API[更多](https://yq.aliyun.com/articles/69516)
### 原生js转换为immutableData
```js
  Immutable.fromJS([1,2]) // immutable的 list
  Immutable.fromJS({a: 1}) // immutable的 map
```
### 从immutableData回到JavaScript对象
```js
  immutableData.toJS()
```
### 判断两个immutable数据是否一致
```js
  Immutable.is(immutableA, immutableB)
```
### 判断是不是map或List
```js
  Immutable.Map.isMap(x)
  Immutable.Map.isList(x)
```
### 对象合并(注意是同个类型)
```js
  let immutableMaB = immutableMapA.merge(immutableMaC)
```
### Map的增删查改
#### 查
```js
  immutableData.get('a') // {a:1} 得到1。
  immutableData.getIn(['a', 'b']) // {a:{b:2}} 得到2。访问深层次的key
```
### 增和改(注意不会改变原来的值，返回新的值)
```js
  immutableData.set('a', 2) // {a:1} 得到1。
  immutableData.setIn(['a', 'b'], 3)
  immutableData.update('a',function(x){return x+1})
  immutableData.updateIn(['a', 'b'],function(x){return x+1})
```
#### 删
```js
  immutableData.delete('a')
  immutableData.deleteIn(['a', 'b'])
```
### List的增删查改
如同Map，不过参数变为数字索引。
```js
  immutableList.set(1, 2)
```
