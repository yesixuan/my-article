---
title: CSS3样式书写规范
tags: ["CSS","规范"]
date: 2017-07-26 12:12:00
categories:
- 前端
- CSS
---
> css一直是一门被人诟病的语言。有人说它甚至不能算是一门语言。正因如此，规范的css才更显得至关重要...  

<!-- more -->
## 编码设置
采用UTF-8编码，在css代码头部使用：  
```css
@charset "UTF-8"
```
注意：必须要定义在CSS文件所有的字符前面（包括注释），编码申明才会生效。  

## 命名空间规范
- 布局：以`g`为命名空间。例如：g-wrap、g-header、g-content
- 状态：以`s`为命名空间，表示动态的、具有交互性质的状态。例如：s-current、s-selected
- 工具：以`u`为命名空间，表示不耦合业务逻辑的、可复用的工具。例如：u-clearfix、u-ellipsis
- 组件：以`m`为命名空间，表示可复用、移植的组件模块。例如：j-request、j-open

## 样式属性顺序
按照功能来对属性进行分组：position model --> box model --> typographic --> visual。  
- 如果包含content属性，应放在最前面
- position model布局方式、位置。相关属性：position、top、z-index、display、float
- box model盒模型。相关属性：width、padding、margin、border、overflow
- typographic文本排版。相关属性：font、line-height、text-align、word-wrap
- visual视觉外观。相关属性：color、background、list-style、transform、animation

## css方言的使用建议
### 嵌套层级规定
嵌套层级不建议超过3层。  
### 共用类的使用方法
共用类使用%xxx定义，@extend引用，如：  
```css
%clearfix {
  overflow: auto;
  zoom: 1;
}
.g-header {
  @extend %clearfix;
}
```
这样编译出来的文件中会将公共样式放到一个类中，不会造成冗余代码。
