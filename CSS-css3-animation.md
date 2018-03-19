---
title: 动画与过渡
tags: ["动画","过渡"]
date: 2017-05-15 14:04:00
categories:
- 前端
- CSS
- css3
---
> 如果说css里面有什么是最好玩的，那我觉得毫无疑问该是css里边的动画了...

<!-- more -->
### 关键帧动画
#### 定义@keyframes
```CSS
  @keyframes 动画名称{
    /* 时间点可以用百分比来表示 */
    时间点 {元素状态}
    时间点 {元素状态}
    …
  }
```
#### 使用关键帧动画
```CSS
  div{
    animation: 动画名称 持续时间 ...;
  }
```
#### CSS3动画属性
- `animation`：所有动画属性的简写属性，除了animation-play-state属性（推荐使用这种属性简写方式）。
- `animation-name`：规定@keyframes动画的名称。
- `animation-duration`：规定动画完成一个周期所花费的秒或毫秒。默认是0。
- `animation-timing-function`：规定动画的速度曲线。默认是"ease"。
- `animation-delay`：规定动画何时开始。默认是0。
- `animation-iteration-count`：规定动画被播放的次数。默认是1。
- `animation-direction`：规定动画是否在下一周期逆向地播放。默认是"normal"。
- `animation-play-state`：规定动画是否正在运行或暂停。默认是"running"。
- `animation-fill-mode`：规定对象动画时间之外的状态。

### 过渡
#### 概述
过渡可以让一个元素从一种状态变平滑过渡为为另一种状态。这有点像声明式，我们只需要声明元素的初始状态和结束状态，至于这两个状态的过程，我们不必关心。
#### 场景
1. 通过与用户的交互来改变元素状态，如:hover、:link等。
2. 通过JS的方式来改变元素状态，如：$(.dome).addClass('className')。

#### 定义和用法
```CSS
  /* 这里使用的是简写属性 */
  /* 多个属性要写的话，用逗号隔开 */
  transition: property duration timing-function delay;
```
### animate.css
#### 安装
```shell
  npm install animate.css --save
```
#### 引入
```HTML
  <link rel="stylesheet" href="animate.min.css">
```
#### 使用
1. 添加通用类名：animated（必选）、infinite（可选）。
2. 添加动画类名：[查看不同类名效果](https://daneden.github.io/animate.css/)。
