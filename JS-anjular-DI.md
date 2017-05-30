---
title: angular4依赖注入
tags: ["js","angular","依赖注入"]
date: 2017-05-29 06:31:00
categories:
- 前端
- JS
- angular
---
> 整理一下angular4中的依赖注入设计模式的相关知识...

<!-- more -->
## 感性认知
### 为什么要用它
依赖注入是为了解决我们在一个组件中要使用某个类的方法时，需要先实例化这个类，这使得这个组件和那个类紧密地耦合在一起的问题。
### 依赖注入是什么
依赖注入是为了解决上述问题的一种设计模式。它是“控制反转”（将实例化这个类的控制权交给外部，从而实现类与组件的解耦）的一体两面。控制反转是目的，依赖注入是手段。
### 怎么用
我们在组件中需要某个服务的实例时，不需要手动地创建这个实例。只需要在构造函数的参数中指定这个实例的变量，以及这个实例的类。然后angular会根据这个类的名字找到providers属性里边指定的同名provide，再找到它对应的useClass，最终自动创建这个服务的实例。
代码一览：
```JS
  // 在模块中声明依赖注入的服务
  @NgModule({
    // 当这两个属性值同名时，可以简写为：ProductService
    providers: [{provide: ProductService, useClass: AnotherProductService}]
  })
  // 组件类中
  export class ProductComponent {
    product: Product
    constructor(productService: ProductService) { // 这一步将ProductService实例化后的对象赋值给了productService变量
      this.product = productService.getProduct() // 这里调用了ProductService实例的方法
    }
  }
```
## angular中如何实现依赖注入
### 两个主要概念
#### 注入器
你需要谁的实例，直接在构造函数的参数里指定变量和类型就好了。
```JS
  constructor(private productService: ProductService) { ... }
```
#### 提供器
angular需要知道怎样来实例化这个服务
```JS
  // 两个属性同名
  providers: [ProductService]
  // 完整写法
  providers: [{provide: ProductService, useClass: ProductService}]
  // 不同命写法
  providers: [{provide: ProductService, useClass: AnotherProductService}]
  // 用工厂方式创建这个实例
  providers: [{provide: ProductService, useFactory: ()=>{...}}]
```
### 实际上手
```JS
  // shared/product.service.ts
  import { Injectable } from '@angular/core'
  // 这里加上注入器，可以在这个服务中注入其他服务
  @Injectable()
  export class ProductService {
    constructor() {}
    // 定义getProduct方法返回Product类型的对象
    getProduct(): Product {
      ... // 这里可以做很多操作，如查询数据库等，这里简化
      return new Product(0,"iphone8",...)
    }
  }
  // 之所以要暴露这个类，是因为在组件中实例化这个类时，需要指定该实例的类型
  export class Product {
    constructor( // 定义Product类中的属性
      public id: number,
      public title: string,
      ...
    ) {}
  }
  // ProductComponent中，略...
```
### 作用域
- 在模块中提供的服务，作用域是它下边的所有组件（绝大多数情况）；
- 在组件中提供的服务，只能在该组件中使用（@Component是@Injectable的实例。所以，组件的注入写在@Component下就好了）；
- 如果在模块中和组件中提供了同名的服务，那么模块中的服务将被组件中的同名服务所覆盖
