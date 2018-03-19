---
title: vuex
tags: ["vue","vuex","状态管理"]
date: 2017-04-26 09:12:00
categories:
- 前端
- JS
- vue
---
> 凌晨四点多惊醒，杂事纷呈，再难入眠。浑浑噩噩坐上地铁，坐地铁的近一个小时时间看了CSDN的老师讲vuex的视频，确实解惑不少...

<!-- more -->

### state
#### what?
state是整个应用的数据中心，管理整个应用的状态。也称为状态对象。
#### 定义
一般是定义在`store/index.js`中，就是一个大的对象（或者单独来一个`state.js`引进来）。
```JS
  const state = {
    count: 0,
    history: []
  }
  const store = new Vuex.Store({
    state
  })
```
#### 使用
* 直接在组件模板里：`{{$store.state.count}}`。
* 在计算属性里面使用：
```JS
  computed: {
    count () {
      return this.$store.state.count
    }
  }
```
* 最高级的用法：
```JS
  computed:{
    ...mapState([
      count
    ])
  }
```
### mutations
#### what?
我把它理解为直接操作状态对象的操作中心。
#### 定义
一般是定义在`store/mutations.js`中,里面暴露了多个函数来改变状态对象。
```JS
  export const increment = state => {
    state.count++
    state.history.push('increment')
  }
```
#### 使用
```JS
  mothods: {
    ...mapMutations([
      increment
    ])
  }
```
### getters
#### what?
`getter`可以看作是`state`的计算属性，即通过状态对象衍生出来的数据。从它的名字为`getter`可以看出,通过读取`state`得到衍生值。
#### 定义
一般是定义在`store/getters.js`中,里面暴露了许多值，这些值都是通过函数返回值赋值（通过这种方式可以避免在组件内直接使用`mapState`这种局限性较大方式）。
```JS
  export const count = state => state.count
```
#### 使用
```JS
  computed: {
    ...mapGetters([
      count
    ])
  }
```
### actions
#### what?
`action`用来派发`mutation`，一个`action`中可以触发多个`mutation`，在不同的时间不同的条件下触发不同的`mutation`（异步操作在此处执行）。
#### 定义
一般是定义在`store/actions.js`中,里面暴露了很多方法。方法里面触发`mutation`（函数默认的第一个参数是`context`，下例使用了对象的结构）。
```JS
  export const incrementIfOdd = ({ commit, state }) => {
    if ((state.count + 1) % 2 === 0) {
      commit('increment')
    }
  }

  export const incrementAsync = ({ commit }) => {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
```
#### 使用
```JS
  mothods: {
    ...mapActions([
      incrementIfOdd,
      incrementAsync
    ])
  }
```
### module
#### what?
用来将上述种种进行模块化。
#### 定义
```JS
  const moduleA = {
    state,
    getters,
    actions,
    mutations
  }
  export default new Vuex.Store({
    modules: {
      moduleA
    }
  })
```
#### 使用
实际上就是在state后面多加了一层moduleA（我们那些可爱的映射函数都没法用了）。
