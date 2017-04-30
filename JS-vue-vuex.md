---
title: vuex
tags: ["vue","vuex","状态管理"]
date: 2017-04-26 09:12:00
categories:
- 前端
- vue
---
> 凌晨四点多惊醒，杂事纷呈，再难入眠。浑浑噩噩坐上地铁，坐地铁的近一个小时时间看了CSDN的老师讲vuex的视频，确实解惑不少...
<!-- more -->
<<<<<<< HEAD

=======
>>>>>>> 64a04020ee5e6bd86d7c17beb1029f0fdfc321fd
### state
#### what?
state是整个应用的数据中心，管理整个应用的状态。也称为状态对象
#### 定义
一般是定义在store/index.js中，就是一个大的对象（或者单独来一个state.js引进来）。
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
1. 直接在组件模板里：{{$store.state.count}}
2. 在计算属性里面使用：
```JS
  computed: {
    count () {
      return this.$store.state.count
    }
  }
```
3. 最高级的用法：
```JS
  computed:{
    ...mapState([
      count
    ])
  }
```
### mutations
#### what?
我把它理解为直接操作状态对象的操作中心
#### 定义
一般是定义在store/mutations.js中,里面暴露了多个函数来改变状态对象
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
getter可以看作是state的计算属性，即通过状态对象衍生出来的数据。从它的名字为getter可以看出,通过读取state得到衍生值
#### 定义
一般是定义在store/getters.js中,里面暴露了许多值，这些值都是通过函数返回值赋值（通过这种方式可以避免在组件内直接使用mapState这种局限性较大方式）
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
action用来派发mutation,一个action中可以触发多个mutation，在不同的时间不同的条件下触发不同的mutation（异步操作在此处执行）
#### 定义
一般是定义在store/actions.js中,里面暴露了很多方法。方法里面触发mutation（函数默认的第一个参数是context，下例使用了对象的结构）
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
用来将上述种种进行模块化
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
实际上就是在state后面多加了一层moduleA（我们那些可爱的映射函数都没法用了）
