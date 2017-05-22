---
title: redux
tags: ["应用状态","redux"]
date: 2017-05-08 12:32:00
categories:
- 前端
- JS
- react
---
> 又是一个无味的星期一。看了一上午的redux相关知识，每次去看，几乎每次都有新的收获。这让我既是开心又是无奈...

<!-- more -->
### redux核心
#### 整体感知
用我自己浅陋的理解来叙一叙吧。
redux是一个专门用来管理应用状态的库。它的核心是store，围绕着store有state（一个大对象，包含所有状态数据），action（描述一个动作），reducer（根据不同类型的action来得到新的state）。
它的工作流程是：①store.dispatch('ACTION1')-->②自动触发reducer函数，返回新的state-->③render函数订阅state的变化。
#### store
创建store时传入的参数有reducer函数，初始状态值，中间件。
store主要提供了一些函数：
```JS
  // 获取应用的状态数据
  store.getState()
  // 派发一个动作，从而触发reducer
  store.dispatch()
  // 参数里面放state改变后的操作，如重新渲染视图
  store.subscribe()
```
#### action
action是一个普通的javascript对象，它必须包含type属性来区分不同的action。
我们一般不会用字面量的方式去创建action，而是通过一个action生成函数来创建action。这个actionCreator函数可以做一些比较猥琐的事儿。这是后话，暂且不提。
#### reducer
reducer是一个纯函数。里面是判断action的类型，根据原始state和传入的action生成新的state的具体操作。
##### reducer的拆分
当state特别大时，我们需要对reducer进行拆分（拆分的是state的直系属性）。
```JS
  import { combineReducers } from 'redux';

  const totalReducer = combineReducers({
    stateA : reducerA,
    stateB : reducerB,
    stateC : reducerC
  })
  // 等同于
  /*function totalReducer(state = {}, action) {
    return {
      stateA: reducerA(state.a, action),
      stateB: reducerB(state.b, action),
      stateC: reducerC(state.c, action)
    }
  }*/

  export default totalReducer;
```
### redux中间件
#### 为什么需要redux的中间件
redux中间件是用来增强store.dispatch()的。原始的dispatch方法只能接收一个action对象作为参数。但是使用了中间件之后，dispatch方法可以接收函数、promise对象等作为参数。
#### 中间件的用法
```JS
  import { applyMiddleware, createStore } from 'redux';
  import createLogger from 'redux-logger';
  const logger = createLogger();

  const store = createStore(
  reducer,
  applyMiddleware(logger)
  );
```
#### redux-thunk中间件
Action Creator的写法如下：
```JS
  // fetchPosts函数返回了一个函数
  const fetchPosts = postTitle =>
    // 这个就是返回的函数
    (dispatch) => {
      // dispatch一个同步的action，表示操作开始
      dispatch(requestPosts(postTitle));
      // 使用fetch发送异步请求（目测fetch里面可以直接使用then，而且每一次用到fetch时，都是return打头阵），
      return fetch(`/some/API/${postTitle}.json`)
        .then(response => response.json())
        // 派发结束action
        .then(json => dispatch(receivePosts(postTitle, json)));
      };  
    };
```
redux-thunk中间件的用法：①在创建store的时候传入`applyMiddleware(thunk)`-->②派发动作`store.dispatch(fetchPosts('reactjs'))`（dispatch之后还可以继续`.then()`）
#### redux-promise中间件
redux-promise中间件使dispatch支持promise对象作为参数。

Action Creator的写法一：
```JS
const fetchPosts = (dispatch, postTitle) => new Promise(function (resolve, reject) {
   dispatch(requestPosts(postTitle));
   return fetch(`/some/API/${postTitle}.json`)
     .then(response => {
       type: 'FETCH_POSTS',
       payload: response.json()
     });
});
```
Action Creator的写法二：
```JS
  dispatch(createAction(
    'FETCH_POSTS',
    // 这个action的payload为promise对象
    fetch(`/some/API/${postTitle}.json`)
      .then(response => response.json())
  ));
```
### react-redux
#### UI组件（木偶组件）
负责渲染页面，数据全部由props提供，没有自己的state。
#### 容器组件
由react-redux生成，有自己的状态，管理数据和业务逻辑。
#### connect方法
用于根据UI组件生成容器组件。
```JS
  import { connect } from 'react-redux'
  const VisibleTodoList = connect(
    mapStateToProps,      // 建立state到props的映射
    mapDispatchToProps    // 建立dispatch到props的映射
  )(TodoList)             // TodoList为UI组件
```
#### mapStateToProps()
```JS
  const mapStateToProps = (state) => {
    return {
      // todos就是UI组件里面的同名参数，getVisibleTodos根据state算出todos的值
      todos: getVisibleTodos(state.todos, state.visibilityFilter)
    }
  }
```
补充：mapStateToProps的第一个参数总是state对象，还可以使用第二个参数，代表容器组件的props对象。
#### mapDispatchToProps()
写法一（函数形式）
```JS
  const mapDispatchToProps = (
    dispatch,
    ownProps // 容器组件的props对象
  ) => {
    return {
      onClick: () => { // onClick为UI组件里的同名参数
        dispatch({
          type: 'SET_VISIBILITY_FILTER',
          filter: ownProps.filter
        });
      }
    };
  }
```
写法二（对象形式）
```JS
  // 返回的 Action 会由 Redux 自动发出
  const mapDispatchToProps = {
    onClick: (filter) => {
      type: 'SET_VISIBILITY_FILTER',
      filter: filter
    };
  }
```
#### <Provider>组件
在根组件外面包一层<Provider></Provider>，使得所有子组件通过this.context得到store对象。
### 一个真实项目中的应用
#### 入口文件
```JS
  // index.jsx
  import {render} from 'react-dom'
  import {Provider} from 'react-redux'
  // 这里是将store封装了一层，调用configureStore()得到store
  import configureStore from './store/configureStore'
  ...
  const store = configureStore()
  // 这里用了react-router 4.x 版本
  render(
  	<Provider store={store}>
  		<HashRouter basename="/">
  			<AppContainer />
  		</HashRouter>
  	</Provider>,
  	document.body.appendChild(document.createElement('div'))
  )
```
#### 定义action的名字
```JS
// app/contants/userinfo.js
export const USERINFO_UPDATE = "USERINFO_UPDATE"; // 后面还有许多
```
#### 定义action生成函数
```JS
// app/actions/userinfo.js
import * as userTypes from '../constants/userinfo'
export function update(data){
	return {
		type:userTypes.USERINFO_UPDATE,
		data
	}
}
```
#### 定义reducers
```JS
// app/reducers/userinfo.js
import * as userTypes from '../constants/userinfo'
export default function userinfo(state={},action){
	switch(action.type) {
		case userTypes.USERINFO_UPDATE:
				return action.data;
		default:
			return state;
	}
}
```
#### 生成store
```JS
// app/store/configureStore.js
import { createStore } from 'redux'
import rootReducer from '../reducers'

export default function configureStore(initialState){
	const store = createStore(rootReducer,initialState,
    // 目测这里是用来配合浏览器的redux开发者工具的
		window.devToolsExtension ? window.devToolsExtension() : undefined
	)
	return store
}
```
#### 配合react-redux
```JS
// app/appContainer.jsx，导入action生成函数
import { bindActionCreators } from 'redux'
import { connect } from 'react-redux'
import * as userInfoActionsFromOtherFiles from './actions/userinfo.js' // 在这个根组件中，只需要拿到action生成函数就好了
// 调用redux中的action生成函数
componentDidMount(){
  this.props.userInfoActions.update({
    cityName:cityName
  })
}
// 将action生成函数与redux里的state数据映射到组件的props上
function mapStateToProps(state){
	return {
    ...
	}
}
function mapDispatchToProps(dispatch){
	return {
		userInfoActions:bindActionCreators(userInfoActionsFromOtherFiles,dispatch)
	}
}
export default connect(
	mapStateToProps,
	mapDispatchToProps
)(AppContainer)
```
