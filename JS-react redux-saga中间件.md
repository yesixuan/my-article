---
title: 使用redux-saga中间件处理redux中的异步
tags: ["js","redux"]
date: 2017-07-29 20:41:00
categories:
- 前端
- redux
---
> 记得某一天，第一次看到有人说到redux-saga。他说：用了redux-saga之后就再也回不去redux-thunk了...

<!-- more -->
## 整体感知
### 使用redux-saga封装异步操作
```js
import { call, put } from 'redux-saga/effects'
import { takeEvery } from 'redux-saga'
// 这个函数就封装了我们的异步操作，每一个yield都要等上一个yield完成之后再执行
function* fetchData(action) {
   try {
     // 这里其实是用声明式的方式调用Api.fetchUser方法，并传入参数
      const data = yield call(Api.fetchUser, action.payload.url);
      yield put({type: "FETCH_SUCCEEDED", data});
   } catch (error) {
      yield put({type: "FETCH_FAILED", error});
   }
}
// 监听INCREMENT_ASYNC action的调用，然后调用fetchData这个封装了异步的generate函数
export function* watchFetchData() {
  yield* takeEvery('FETCH_REQUESTED', fetchData)
}
```

### 将`watchFetchData`这个Saga连接至Store
```js
import { fetchData, watchFetchData } from './sagas'
const store = createStore(
  reducer,
  applyMiddleware(createSagaMiddleware(watchFetchData))
)
```

## 细节展示
### Saga辅助函数
`takeEvery`是最常见的，它提供了类似`redux-thunk`的行为；`takeLatest `则只执行最后一次fetchData
```js
import { takeEvery } from 'redux-saga'
function* watchFetchData() {
  // 只要监听到FETCH_REQUESTED这个action被派发，就一定会调用fetchData。不管上一次的fetchData有没有完成
  yield* takeEvery('FETCH_REQUESTED', fetchData)
  // 可以作为节流函数使用，还可以避免上一次输入已经清空，但结果还是返回了上次的输入得到的结果
  yield* takeLatest('FETCH_REQUESTED', fetchData)
}
```

### 声明式Effects
在 redux-saga 的世界里，Sagas 都用 Generator 函数实现。我们从 Generator 里 yield 纯 JavaScript 对象以表达 Saga 逻辑。 我们称呼那些对象为 Effect。Effect 是一个简单的对象，这个对象包含了一些给 middleware 解释执行的信息。 你可以把 Effect 看作是发送给 middleware 的指令以执行某些操作（调用某些异步函数，发起一个 action 到 store）。
#### call
为什么我们使用call声明式地调用一个函数而不是用“函数()”的方式？这是为了方便我们做断言测试。  
call 同样支持调用对象方法，你可以使用以下形式，为调用的函数提供一个 this 上下文：  
```js
yield call([obj, obj.method], arg1, arg2, ...) // 如同 obj.method(arg1, arg2 ...)
```  
#### apply
apply 提供了另外一种调用的方式：  
```js
yield apply(obj, obj.method, [arg1, arg2, ...])
```
#### cps
call 和 apply 非常适合返回 Promise 结果的函数。另外一个函数 cps 可以用来处理 Node 风格的函数 （例如，fn(...args, callback) 中的 callback 是 (error, result) => () 这样的形式，cps 表示的是延续传递风格（Continuation Passing Style））。
```js
import { cps } from 'redux-saga'
const content = yield cps(readFile, '/path/to/file')
```
#### put
同样的，如果我们想在获取到异步数据之后派发一个action也不能直接`dispatch({ type: 'PRODUCTS_RECEIVED', products })`而是要用`yield put({ type: 'PRODUCTS_RECEIVED', products })`这样声明式的调用以便测试。  

### 错误处理
我们可以使用熟悉的 try/catch 语法在 Saga 中捕获错误。
```js
import Api from './path/to/api'
import { call, put } from 'redux-saga/effects'
function* fetchProducts() {
  try {
    const products = yield call(Api.fetch, '/products')
    yield put({ type: 'PRODUCTS_RECEIVED', products })
  }
  catch(error) {
    yield put({ type: 'PRODUCTS_REQUEST_FAILED', error })
  }
}
```
当然了，你并不一定得在 try/catch 区块中处理错误，你也可以让你的 API 服务返回一个正常的含有错误标识的值。例如， 你可以捕捉 Promise 的拒绝操作，并将它们映射到一个错误字段对象。  
```js
import Api from './path/to/api'
import { take, put } from 'redux-saga/effects'
function fetchProductsApi() {
  return Api.fetch('/products')
    .then(response => {response})
    .catch(error => {error})
}
function* fetchProducts() {
  const { response, error } = yield call(fetchProductsApi)
  if(response)
    yield put({ type: 'PRODUCTS_RECEIVED', products: response })
  else
    yield put({ type: 'PRODUCTS_REQUEST_FAILED', error })
}
```

## 高级（[官方中文文档](http://leonshi.com/redux-saga-in-chinese/docs/advanced/FutureActions.html)）
### 监听未来的action
之前我们有用`takeEvery`来对每一个相同的action进行无差别对待，但其实我们也有`take`可以对每一次的action进行细微调控。比如说，我们可以在观察到用户完成了3个任务之后，派发一个向用户表示祝贺的action。  

### 同时执行多个任务
yield 指令可以很简单的将异步控制流以同步的写法表现出来，但与此同时我们将也会需要同时执行多个任务，我们不能直接这样写：
```js
// 错误写法，effects 将按照顺序执行
const users = yield call(fetch, '/users'),
      repos = yield call(fetch, '/repos')
      import { call } from 'redux-saga/effects'

// 正确写法, effects 将会同步执行
const [users, repos] = yield [
  call(fetch, '/users'),
  call(fetch, '/repos')
]
```
当我们需要 yield 一个包含 effects 的数组， generator 会被阻塞直到所有的 effects 都执行完毕，或者当一个 effect 被拒绝 （就像 Promise.all 的行为）。  

### 同时启动多个任务，择其优者取值
有时候我们同时启动多个任务，但又不想等待所有任务完成，我们只希望拿到 胜利者：即第一个被 resolve（或 reject）的任务。 race Effect 提供了一个方法，在多个 Effects 之间触发一个竞赛（race）。  
下面的示例演示了触发一个远程的获取请求，并且限制了 1 秒内响应，否则作超时处理。  
```js
import { race, take, put } from 'redux-saga/effects'
function* fetchPostsWithTimeout() {
  const {posts, timeout} = yield race({
    posts   : call(fetchApi, '/posts'),
    timeout : call(delay, 1000)
  })
  if(posts)
    put({type: 'POSTS_RECEIVED', posts})
  else
    put({type: 'TIMEOUT_ERROR'})
}
```
race 的另一个有用的功能是，它会自动取消那些失败的 Effects。具体实例参看官方文档
