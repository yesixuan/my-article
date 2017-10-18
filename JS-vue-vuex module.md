---
title: 再谈vuex之模块化
tags: ["vue","vuex","状态管理"]
date: 2017-10-18 09:38:00
categories:
- 前端
- vue
---
> 很多人都知道，使用redux时的模块化是很方便的。利用不同的reducer函数管理不同模块中的状态。vuex也是支持模块化的，今天来侃侃...

<!-- more -->
## 前导
- 模块包含什么  
- 模块如何组织  
- 模块如何访问外部信息  
- 组件中如何操作模块  

## 模块是什么
不同于redux中的模块，vuex中的模块相当于是一个小的store，包含state、mutations、actions、getters。  
```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```

## 模块的局部状态
### 模块内的mutations
```js
const moduleA = {
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  }
}
```

### 模块内的getters
```js
getters: {
  // 接收到的第一个参数是模块局部的state
  doubleCount (state) {
    return state.count * 2
  }
}

/* 如果getters需要用到模块外部的state数据，就要接受后面的参数 */
getters: {
  // 对于模块内部的 getter，根节点状态会作为第三个参数暴露出来
  sumWithRootCount (state, getters, rootState) {
    return state.count + rootState.count
  }
}
```  

### 模块内的actions  
对于模块内部的 action，局部状态通过 context.state 暴露出来，根节点状态则为 context.rootState.  
```js
actions: {
  // 将state, commit, rootState解构出来
  incrementIfOddOnRootSum ({ state, commit, rootState }) {
    if ((state.count + rootState.count) % 2 === 1) {
      commit('increment')
    }
  }
}
```  

## 命名空间
默认情况下，模块内部的 action、mutation 和 getter 是注册在全局命名空间的——这样使得多个模块能够对同一 mutation 或 action 作出响应。  
但我们通常是希望每个模块拥有各自的命名空间，以免“误伤”。  

### 给予模块各自不同的命名空间  
你可以通过添加 namespaced: true 的方式使其成为命名空间模块。当模块被注册后，它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名。  
```js
const store = new Vuex.Store({
  modules: {
    account: {
      /* 开启该模块自己的命名空间 */
      namespaced: true,
      // ...
    }
  }
})
```  

### 在命名空间模块内访问全局内容
如果你希望使用全局 state 和 getter，rootState 和 rootGetter 会作为第三和第四参数传入 getter，也会通过 context 对象的属性传入 action。  
- getters  
```js
getters: {
  // 在这个模块的 getter 中，`getters` 被局部化了
  // 你可以使用 getter 的第四个参数来调用 `rootGetters`
  someGetter (state, getters, rootState, rootGetters) {
    getters.someOtherGetter // -> 'foo/someOtherGetter'
    rootGetters.someOtherGetter // -> 'someOtherGetter'
  },
  someOtherGetter: state => { ... }
},
```  

- actions  
```js
actions: {
  // 在这个模块中， dispatch 和 commit 也被局部化了
  // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
  someAction ({ dispatch, commit, getters, rootGetters }) {
    getters.someGetter // -> 'foo/someGetter'
    rootGetters.someGetter // -> 'someGetter'

    dispatch('someOtherAction') // -> 'foo/someOtherAction'
    dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

    commit('someMutation') // -> 'foo/someMutation'
    commit('someMutation', null, { root: true }) // -> 'someMutation'
  },
  someOtherAction (ctx, payload) { ... }
}
``` 

### 组件内使用绑定函数  
当使用 mapState, mapGetters, mapActions 和 mapMutations 这些函数来绑定命名空间模块时，写起来可能比较繁琐：  
```js
/* 原始写法 */
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo',
    'some/nested/module/bar'
  ])
}
```  

对于这种情况，你可以将模块的空间名称字符串作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。于是上面的例子可以简化为：  
```js
/* 推荐写法 */
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo',
    'bar'
  ])
}
```  

还可以更精简一点：
```js
/* 鼎力推荐 */
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

// 使用 createNamespacedHelpers 对组件的侵入最小
export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```  

## 写在最后  

虽然vuex赋予我们在模块内部访问全局的信息，但是不推荐这般使用。这样会使得模块与模块之间相互耦合，不利于复用。  
如果你发现自己不得不从模块内部访问全局的信息，那你就该思考现在的模块划分是不是有问题，有没有更好的模块划分方式。
