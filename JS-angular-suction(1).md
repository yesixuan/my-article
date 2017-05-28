---
title: angular4搭建竞拍网站（一）
tags: ["js","angular"]
date: 2017-05-27 19:46:00
categories:
- 前端
- JS
- angular
---
> 四天的小长假，这是第一天。上午玩了一下react写大众点评app，下午转战angular4。连我自己都有点佩服我自己的忍耐力了...

<!-- more -->
## 使用angular4的准备工作
1. angular-cli命令行工具
```shell
  npm i -g @angular/cli
```
2. sublime安装`type-script`插件
3. 创建项目骨架
```shell
  ng new progectName
```
4. `ng server`启动项目，监听`4200`端口
5. `ng generate component component-name`创建一个完整组件（这里的完整组件指的是组件控制器、HTML片段、样式文件、测试文件。并且这些组件会自动注入到启动模块中去，而后这些组件可以相互引用了）

## angular4的语法规则
### angular4组件的元素
- 必备元素：装饰器（@Component）、模版（template）、控制器（也就是写在装饰器下面的类）
- 可选的输入对象：输入属性（@Input）、提供器（providers，用来作依赖注入）、生命周期钩子（Lifecycle Hooks）
- 可选输出对象：生命周期钩子（Lifecycle Hooks）、样式表（styles，可以是多个）、动画（Animations）、输出属性（@Output）

### 组件控制器实例
```JS
  import { Component, OnInit } from '@angular/core';
  @Component({
    selector: 'app-product', // 使用的时候写成这样即可<app-product></app-product>
    templateUrl: './product.component.html',
    styleUrls: ['./product.component.css']
  })
  export class ProductComponent implements OnInit {
    @Input() // 输入属性吗，值由父组件标签中的属性传递过来
    private stars: boolean[]; // 这个数组元素由基本数据类型布尔值组成的
  	private products: Array<Product>; // 这个Array是由自己创建的类的实例组成的
    private imgSrc = "http://placehold.it/320x150"; // 这是图片占位符的写法（与angular无关）
    constructor() { }
    ngOnInit() {
    	this.products = [
    		new Product(1,"第1个商品",1.99,3.5,"这是一个商品的描述1",["电子产品1","通讯设备1"]),
        ...
    	]
    }
  }
  export class Product { // 这个类是用来描述一个商品的信息的
  	constructor(
  		public id: number,
      ...
  		public categories: Array<string>
  	) {
      ...
  	}
  }
```
### 模块实例
```JS
  import { BrowserModule } from '@angular/platform-browser';
  import { NgModule } from '@angular/core';
  import { FormsModule } from '@angular/forms';
  import { HttpModule } from '@angular/http';

  import { AppComponent } from './app.component';
  import { NavbarComponent } from './navbar/navbar.component';
  ...

  @NgModule({
    declarations: [ // 这个元数据里只能声明组件、指令和管道
      AppComponent, NavbarComponent, ...
    ],
    imports: [ // 声明当前模块依赖的其他模块，引入了这些模块，你就可以使用这些模块提供的组件、指令和服务
      BrowserModule,
      FormsModule,
      HttpModule
    ],
    providers: [], // 声明模块中提供了哪些服务
    bootstrap: [AppComponent] // 声明模块的主组件是谁
  })
  export class AppModule { }
```
### 动态属性绑定
```html
  <img [src]="动态值" alt="">
  <!-- 当布尔值为真的时候，active的class才会被添加，而且之前的container不会被覆盖 -->
	<div class="container" [class.active]="布尔值"></div>
```
### 循环语法
```html
  <!-- 几个注意点：*、let不能丢；of（不是in） -->
	<span *ngFor="let item of items"></span>
```
### 星级评价
1. 拿到评分数值，如果大于5，要做转换；
2. 生成含五个元素的布尔值类型的数组。true代表添加空星样式（默认都是实星样式）；
3. 用*ngFor动态生成5颗星。在循环内使用[class.空星样式]="item"

关键代码
```JS
  let arr = [];
	for(let i = 1; i <= 5; i++) {
		arr.push(星级数 < i)
	}
```
