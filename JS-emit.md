---
title: JS观察者模式
tags: ["js","设计模式"]
date: 2017-07-11 09:52:00
categories:
- 前端
- JS
- 常用函数
---
> 观察者模式是一种非常经典的设计模式，本文是JS方式的实现...

<!-- more -->
## 自定义事件对象
```js
  // 用于创建事件对象的类
  function Person(name) {
    this.name = name
    // 私有属性，用于管理事件名称对应的回调列表
    this._events = {}
  }
  /* 如果有这个事件名，则将传进来的回调放至回调数组尾部；如果没有，则创建 */
  Person.prototype.on = function(eventName, callback) {
    if(this._events[eventName]) {
      this._events[eventName].push(callback)
    }else {
      this._events[eventName] = [callback]
    }
  }
  /* 拿到事件名称对应的回调列表，依次执行这些回调 */
  Person.prototype.emit = function(eventName) {
    // 拿到除事件名以外的其他参数，传至每一个回调
    var args = Array.prototype.slice.call(arguments, 1)
    var callbacks = this._events[eventName]
    var self = this
    callbacks.forEach(function(callback) {
      callback.apply(self, args)
    })
  }
  /* 解除订阅，这里的回调需要是引用外部的函数，而不是直接写函数体 */
  Person.prototype.off = function(eventName, callback) {
    this._events[eventName] = this._events[eventName].filter(function(item) {
      return item != callback
    })
  }
  var girl = new Person()
  function test(he) {
    console.log('记得撩起',he)
  }
  girl.on('待你长发及腰', function(he) {
    console.log('娶你可好？',he)
  })
  girl.on('待你长发及腰', test)
  girl.emit('待你长发及腰','hehe')
  girl.off('待你长发及腰', test)
  girl.emit('待你长发及腰','hehe')
```

## 节流函数
```js
  function debounce(func, delay) {
    var timer = null
    return function() {
      // 返回的函数中还是能够拿到调用者的this
      var context = this
      // 函数参数虽然没有显式地声明出来，但是可以通过arguments拿到，apply中也是可以直接传入arguments的
      var args = arguments
      clearTimeout(timer)
      timer = setTimeout(function() {
        func.apply(context, args)
      }, delay || 200)
    }
  }
```

## 防抖函数
```js
  function throttle(func, delay) {
    var last = 0
    return function() {
      var curr = +new Date()
      if(curr - last > delay) {
        func.apply(this, arguments)
        last = curr
      }
    }
  }
```

## 封装jsonp
```js
  function jsonp(config) {
    var options = config || {}
    var callbackName = ('jsonp_' + Math.random()).replace(".", "")
    var oHead = document.getElementByTagName('head')[0]
    var oScript = document.creatElement('script')
    oHead.appendChild(oScript)
    window[callbackName] = function(json) {
      oHead.removeChild(oScript)
      clearTimeout(oScript.timer)
      window[callbackName] = null
      options.success && options.success(json)
    }
    oScript.src = option.url + '?' + callbackName
    if(options.time) {
      oScript.timer = setTimeout(function() {
        window[callbackName] = null
        oHead.removeChild(oScript)
        options.fail && options.fail({message: '超时！'})
      }, options.time)
    }
  }
  /* 使用 */
  jsonp({
    url: '/b.com/b.json',
    time: 5000,
    success: function(res) {},
    fail: function() {}
  })
```

## 封装ajax
```js
  // json对象转成URL
  function json2url(json) {
    var arr = []
    for (var name in json) {
      arr.push(name + '=' + json[name])
    }
    return arr.join('&')
  }

  function ajax(json) {
    json = json || {}
    if (!json.url) return
    json.data = json.data || {}
    json.type = json.type || 'get'
    var timer = null
    if (window.XMLHttpRequest) {
      var oAjax = new XMLHttpRequest()
    } else {
      var oAjax = new ActiveXObject('Microsoft.XMLHTTP')
    }
    switch (json.type) {
      case 'get':
        oAjax.open('GET', json.url + '?' + json2url(json.data), true)
        oAjax.send()
        break
      case 'post':
        oAjax.open('POST', json.url, true)
        oAjax.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded')
        oAjax.send(json2url(json.data))
        break
    }
    oAjax.onreadystatechange = function() {
      if (oAjax.readyState == 4) {
        clearTimeout(timer)
        if (oAjax.status >= 200 && oAjax.status < 300 || oAjax.status == 304) {
          json.success && json.success(oAjax.responseText)
        } else {
          json.error && json.error(oAjax.status)
        }
      }
    }
  }

  // ... 使用
  ajax({
    url: '/user',
    data: {},
    type: 'get',
    success: function(res) {},
    error: function() {}
  })
```

## 深克隆对象
```js
  function deepCopy(p, c) {
    var c = c || {}
    for (var i in p) {
      if (typeof p[i] === 'object') { // 判断是否为广义对象
        c[i] = (p[i].constructor === Array) ? [] : {} // 判断是否为狭义对象
        deepCopy(p[i], c[i])
      } else {　　　　　　　　　
        c[i] = p[i]
      }　　
    }　　　　
    return c
  }
```
