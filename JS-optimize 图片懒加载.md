---
title: 细说图片懒加载
tags: ["优化"]
date: 2017-08-22 20:45:00
categories:
- 前端
- JS
---
> 好吧，拾人牙慧的一些优化措施，让老夫再来唠叨一番...

<!-- more -->
## 实现图片懒加载的原理
<img>标签有一个属性是src，用来表示图像的URL，当这个属性的值不为空时，浏览器就会根据这个值发送请求。如果没有src属性，就不会发送请求。  
我们先不给<img>设置src，把图片真正的URL放在另一个属性data-src中，在需要的时候也就是图片进入可视区域的之前，将URL取出放到src中。

## 判断元素是否在可视区
### 方法一
①. 通过document.documentElement.clientHeight获取屏幕可视窗口高度。  
②. 通过element.offsetTop获取元素相对于文档顶部的距离。  
③. 通过document.documentElement.scrollTop获取浏览器窗口顶部与文档顶部之间的距离，也就是滚动条滚动的距离。  
然后判断②-③<①是否成立，如果成立，元素就在可视区域内。

### 方法二
通过`getBoundingClientRect()`方法来获取元素的大小以及位置。  
这个方法返回一个名为ClientRect的DOMRect对象，包含了top、right、botton、left、width、height这些值。  
我们可以通过`el.getBoundingClientRect().top`来获取元素到可视区顶部的距离。  
也就是说，在`el.getBoundingClientRect().top<=clientHeight`时，图片是在可视区域内的。  
```js
function isInSight(el) {
  const bound = el.getBoundingClientRect();
  const clientHeight = window.innerHeight;
  // 如果只考虑向下滚动加载
  // 这里加100是为了提前一点点加载
  return bound.top <= clientHeight + 100;
}
```

### 方法三（[IntersectionObserver](http://www.ruanyifeng.com/blog/2016/11/intersectionobserver_api.html)）
```js
var io = new IntersectionObserver(callback, option);
// 开始观察
io.observe(document.getElementById('example'));
// 停止观察
io.unobserve(element);
// 关闭观察器
io.disconnect();
```
callback的参数是一个数组，每个数组都是一个IntersectionObserverEntry对象，包括以下属性：
- time: 可见性发生变化的时间，单位为毫秒;  
- rootBounds: 与getBoundingClientRect()方法的返回值一样;
- boundingClientRect: 目标元素的矩形区域的信息;  
- intersectionRect: 目标元素与视口（或根元素）的交叉区域的信息;  
- intersectionRatio: 目标元素的可见比例，即intersectionRect占boundingClientRect的比例，完全可见时为1，完全不可见时小于等于0;
- target: 被观察的目标元素，是一个 DOM 节点对象。  
由上可知：当intersectionRatio > 0 && intersectionRatio <= 1即在可视区域内。  

## 加载图片
```js
function checkImgs() {
  const imgs = document.querySelectorAll('.my-photo');
  Array.from(imgs).forEach(el => {
    if (isInSight(el)) {
      loadImg(el);
    }
  })
}
// 加载图片逻辑
function loadImg(el) {
  if (!el.src) {
    const source = el.dataset.src;
    el.src = source;
  }
}
```
这里应该是有一个优化的地方，设一个标识符标识已经加载图片的index，当滚动条滚动时就不需要遍历所有的图片，只需要遍历未加载的图片即可。  

## 函数节流
基本步骤：
1. 获取第一次触发事件的时间戳;
2. 获取第二次触发事件的时间戳;
3. 时间差如果大于某个阈值就执行事件，然后重置第一个时间。  
```js
function throttle(fn, mustRun = 500) {
  const timer = null;
  let previous = null;
  return function() {
    const now = new Date();
    const context = this;
    const args = arguments;
    if (!previous){
      previous = now;
    }
    const remaining = now - previous;
    if (mustRun && remaining >= mustRun) {
      fn.apply(context, args);
      previous = now;
    }
  }
}
```

## 最后一种的实现（比较新的浏览器支持）
```js
function checkImgs() {
  const imgs = Array.from(document.querySelectorAll(".my-photo"));
  imgs.forEach(item => io.observe(item));
}
function loadImg(el) {
  if (!el.src) {
    const source = el.dataset.src;
    el.src = source;
  }
}
const io = new IntersectionObserver(ioes => {
  ioes.forEach(ioe => {
    const el = ioe.target;
    const intersectionRatio = ioe.intersectionRatio;
    if (intersectionRatio > 0 && intersectionRatio <= 1) {
      loadImg(el);
    }
    el.onload = el.onerror = () => io.unobserve(el);
  });
});
```
