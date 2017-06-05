---
title: angular4表单
tags: ["js","angular","表单"]
date: 2017-06-05 12:07:00
categories:
- 前端
- JS
- angular
---
> angular4提供了两种方式来处理表单。模板式表单适合处理比较简单和常规的表单；响应式表单适合应付比较复杂的表单...

<!-- more -->
## 模板式表单
### 麻雀一览
```html
<!-- 表单元素要被包裹在ngForm属性里边，通过模版局部变量，可以拿到表单中的所有数据 -->
<form #myForm="ngForm" (ngSubmit)="onSubmit(myForm.value)">
  <!-- ngModel作为单独的属性放进去，name的属性值将作为表单对象的键名 -->
  <input ngModel name="userName" type="text">
  <!-- 给表单中的元素分组，即给表单对象加一层 -->
  <div ngModelGroup="pwds">
    <input ngModel type="password" name="pwd">
    <input ngModel type="password" name="confirmPwd">
  </div>
</form>
```
## 响应式表单
### 名词解释
FormControl: 表单模版的基本单位
FormGroup: 整个表单或者表单的一个固定的子集
FormArray: 多个同类型的数据组成的数组
### 代码实例
```JS
formModel: FormGroup
// 单独的input
userName: FormControl = new FormControl()
constructor() {
  this.formModel: FormGroup = new FormGroup({
    dateRange: FormGroup = new FormGroup({
      from: new FormControl(),
      to: new FormControl()
    }),
    emails: FormArray = new FormArray([
      new FormControl()
    ])
  })
}

// 增加email输入框
addEmail() {
  let emails = this.fromModel.get('emails') as FormArray
  emails.push(new FormControl())
}
```
```JS
// 使用formBuilder来简化代码('[]'里面可以有三个元素，分别为默认值、校验、异步校验)
constructor(fb: formBuilder) {
  this.fromModel = fb.group({
    dateRange: FormGroup = fb.group({
      from: [""],
      to: [""]
    }),
    emails: FormArray = fb.rray([
      new FormControl()
    ])
  },{}) // 最后的对象里面定义表单组整体的校验规则
}
```
```html
<form [formGroup]="formModel" (ngSubmit)="onSubmit()">
  <div formGroupName="dateRange">
    <input type="date" formControlName="from">
    <input type="date" formControlName="to">
  </div>
  <ul formArrayName="emails">
    <li *ngFor="let e of this.fromModel.get('emails').controls; let i=index;">
      <input [formControlName]="i" type="email">
    </li>
  </ul>
</form>
<!-- fromControl不能在formGroup中使用，要拎出来单独用（在里面的话要用fromControlName="xx"） -->
<input [FormControl]="userName" type="text" name="" value="">
```
### 表单校验
#### angular自带校验器
```JS
constructor(fb: formBuilder) {
  this.fromModel = fb.group({
    userName: ['',[Validators.required,Validators.minLength(6)]]
  })
}
onSubmit() {
  let isValid: boolean = this.formModel.get('userName').Validators
  // 这里拿到的是一个对象（没有错误时返回的null）
  let errors: any = this.formModel.get('userName').errors
}
```
#### 自定义校验
```JS
constructor(fb: formBuilder) {
  this.fromModel = fb.group({
    userName: ['',[Validators.required,Validators.minLength(6)]],
    mobile: ['',[this.mobileValidator]],
    passwordsGroup: fb.group({
      password: [''],
      confirmPwd: ['']
    },{validator: this.equalValidator})
  })
}
mobileValidator(control: FormControl):any {
  var myreg = /^(((13[0-9]){1}) | (15[0-9]{1}) | (18[0-9]{1})) + \d{8})$/
  let valid = myreg.test(control.value)
  return valid ? null : {mobile: true}
}
equalValidator(group: FormGroup):any {
  let password: FormControl = group.get('password') as FormControl
  let confirmPwd: FormControl = group.get('confirmPwd') as FormControl
  let valid: boolean = (password.value === confirmPwd.value)
  return valid ? null : {equal: true}
}
```
#### 表单状态字段
touched和untouched: 有没有获取过焦点
pristine和dirty: 有值被改变，就会变为`dirty`
pending: 正处于异步校验的状态
```html
<!-- 校验不通过时显示的提示信息 -->
<div [hidden]="formModel.get('userName').valid || formModel.get('userName').untouched">校验不通过！</div>
<!-- 注意用error的话要取反的 -->
<div [hidden]="!formModel.hasError('required','userName') || formModel.get('userName').pristine">必填项！</div>
<!-- 异步校验提示信息 -->
<div [hidden]="!formModel.get('mobile').pending">正在校验手机号合法性！</div>
```
#### 表单元素的动态class
初始状态：ng-untouched ng-pristine ng-invalid
其他状态：ng-dirty ng-touched ng-valid
注意：这些class都是全局的，自定义的时候要防止误伤。
```html
<!-- 验证不通过的表单元素加红边框 -->
<input [class.hasError]="formModel.get('userName').invalid && fromModel.get('userName').touched"/>
```
