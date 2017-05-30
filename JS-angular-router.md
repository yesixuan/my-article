---
title: angular4路由
tags: ["js","angular","router"]
date: 2017-05-28 18:06:00
categories:
- 前端
- JS
- angular
---
> 听说angular4整了一个超级牛叉的路由模块...

<!-- more -->
## 路由概念一览
### Routes
路由配置，保存着哪个URL对应展示哪个组件，以及在哪个RouterOutlet中显示组件。
### RouterOutlet
在html中标记路由内容呈现位置的占位符指令。
### Router
负责在运行时执行路由的对象，可以通过调用其navigate()和navigateByUrl()方法来导航到一个指定的路由。
### RouterLink
在html中声明路由导航用的指令。
### ActivetedRouter
当前激活的路由对象，保存着当前路由的信息，如路由地址，路由参数等。
## 实际上手
### 创建带有路由模块的项目架构
```shell
  ng new projectName -routing
```
### 路由的配置模块
```JS
  // app-routing.module.ts
  import { NgModule } from '@angular/core'
  import { Routes,RouterModule } from '@angular/router'
  ... // 这里还要导入路由里面切换的组件
  const routes: Routes = [
    {
      path: '',
      redirectTo: '/home', // 注意：这里的home是有'/'的
      pathMatch: 'full'
    },
    {
      path: 'home', // path里边不能用'/'开头
      component: homeComponent,
      children: []
    },
    {
      path: '**',
      component: Code404Component
    }
  ]

  @NgModule({
    imports: [RouterModule.forRoot(routes)], // 在主模块中使用该路由配置就用forRoot，否则forChild
    exports: [RouterModule],
    providers: []
  })
  export class AppRoutingModule { } // 这个模块将要在主模块的imports选项中导入
```
### 路由跳转
#### 声明式
```html
  <!-- 注意点：路径必须用'/开头（导航到跟路由）'，并且要用'[]'包裹起来 -->
  <a [routerLink]="['/',params]"></a>
```
#### 命令式
```JS
  import { Router } from "@angular/router"
  // 在构造函数中注入Router对象
  constructor(private router: Router) { }
  // 点击跳转的函数
  toSomePage() {
    this.router.navigate(['/somePage']) // 这里同样可以传参
  }
```
### 路由传参
#### 查询参数
/product?id=1&name=2 ---> ActivetedRoute.queryParams[id];
代码：
```html
  <!-- id即是要传的参数（这个id将以?的方式拼接，形如：...product?id=1） -->
  <a [routerLink]="['/product']" [queryParams]="{id:1}">商品详情</a>
```
```JS
  // 组件类中
  import { ActivetedRoute } from '@angular/router'
  private productId: number // 定义productId来接受传过来的参数
  constructor(private routeInfo: ActivetedRoute) { } // 将ActivetedRoute注入进来，让别人来使用它实例的方法
  ngOnInit() {
    // 这里的snapshot是生成一次快照，如果组件没被创建，只是参数变化，这个productId不会跟着变化（监听这个参数可以实现）。
    this.productId = this.routeInfo.snapshot.queryParams["id"]
  }
```
#### 路径传参
{path:/product/:id} ---> /product/1 ---> ActivetedRoute.params[id];
代码：
```JS
  // 路由配置中
  {path: product/:id,component:xxComponent}
```
```html
  <!-- 用navigate导航也是一样的 -->
  <a [routerLink]="['/product', 1]">商品详情</a>
```
```JS
  ...
  ngOnInit() {
    this.productId = this.routeInfo.snapshot.params["id"]
    // 用参数订阅的方式响应productId的变化
    this.routeInfo.params.subscribe((params: Params) => { // RxJs的语法
      this.productId = params["id"]
    })
  }
  ...
```
  #### 配置传参
  {path:/product,component:xxComponent,`data:[{isProd:true}]`} ---> ActivetedRoute.data[0][isProd];
  ### 辅助路由
```html
  <router-outlet></router-outlet>
  <router-outlet name="aux"></router-outlet>
```
```JS
  // 这里配置哪些组件能够显示到‘aux’这个插座上，只要有outlet属性，就说明这是辅助路由的配置
  {path: 'xxx', component: XxxComponent, outlet: 'aux'}
  {path: 'yyy', component: YyyComponent, outlet: 'aux'}
```
```html
  <!-- 这里的outlets只是控制辅助路由的切换（独立变化，与主路由链接无关）（辅助路由的URL是用'()'包起来的） -->
  <a [routerLink]="['/home',{outlets: {aux: 'xxx'}}]">Xxx</a>
  <a [routerLink]="['/product',{outlets: {aux: 'yyy'}}]">Yyy</a>
  <!-- 这里是点击切换到开始聊天路由，顺便将主路由切到home路径上去 -->
  <a [routerLink]="[{primary: 'home', outlets: {aux: 'chat'}}]">开始聊天</a>
```
### 路由守卫
#### CanActivate
场景：用户未登陆不允许进入该路由页面
```JS
  // guard/login.guard.ts
  import { CanActivate } from '@angular/router'
  export class LoginGuard implements CanActivate {
    canActivate() { // 这个方法要反回一个布尔值，根据这个布尔值看你是否有权限进到这个路由
      let loggedIn: boolean = Match.random() < 0.5 // 用随机数的方式来模拟看用户是否登陆
      if(!loggedIn) {
        alert('未登录，不能进入该页面')
      }
      return loggedIn
    }
  }
  // 路由配置信息里面加入一个属性
  import { LoginGuard } from "./guard/login.guard"
  {path: 'product/:id',component:ProductComponent,...,canActivatave:[LoginGuard]}
  // 在providers属性中注入LoginGuard以实例化
  @NgModule({
    ...
    providers: [LoginGuard]
  })
```
#### CanDeactivate
场景：用户要离开时，提醒他保存表单信息
```JS
  // guard/unsaved.guard.ts
  import { CanDeactivate } from '@angular/router'
  import { ProductComponent } from '../product/product.component' // 引入你要保护的组件
  export class UnsavedGuard implements CanDeactivate<ProductComponent> { // 这里要指定一个泛型指定你要保护谁
    canActivate(component: ProductComponent) { // 这里我们可以拿到受保护的组件，判断它是否具备离开的条件
      return window.confirm('你还没有保存，确定要离开吗？')
    }
  }
  // 路由配置信息里面加入一个属性
  import { UnsavedGuard } from "./guard/unsaved.guard"
  {path: 'product/:id',component:ProductComponent,...,canDeactivatave:[UnsavedGuard]}
  // 在providers属性中注入LoginGuard以实例化
  @NgModule({
    ...
    providers: [LoginGuard,UnsavedGuard]
  })
```
#### Resolve
在进入路由页面之前，预先从服务器上获取到想要的数据。而后带这这些数据进入路由页面，用户体验更佳
```JS
  // 太难了...
```
