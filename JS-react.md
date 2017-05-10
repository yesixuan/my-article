---
title: react入门
tags: ["react"]
date: 2017-05-09 13:22:00
categories:
- 前端
- JS
- react
---
> 想对react下手已经不是一天两天的事儿了，今日便将心一横...

<!-- more -->
### react的两个核心方法
```JS
  // 渲染方法
  ReactDOM.render(
    <h1>Hello, world!</h1>,
    document.getElementById('root')
  );
  // 定义组件类（ES6的写法）
  class Welcome extends React.Component {
    render() {
      return <h1>Hello, {this.props.name}</h1>;
    }
  }
```
### JSX语法
JSX语法支持html与js混着写。遇见`<`解析为html，遇见`{`解析为javascript。
注：
1. 如果`{}`里面放了javascript数组，JSX会将该数组展开渲染。
2. 给标签设置动态属性值，只需要将属性值用`{}`包起来。
3. 在JSX里面`class`要变为`className`。

### 组件类
#### 组件类的render方法
```JS
  // render方法里面返回一些标签
  render() {
    return (
      <div>
        <h1>Hello, world!</h1>
      </div>
    );
  }
```
#### 组件类的`props`
react里面的`props`是只读的，由父组件传给子组件。子组件通过`this.props.xx`来得到`props`。
#### 组件类的`state`
修改state要用`this.setState({comment: 'Hello'});`（这种方式会自动触发视图渲染）。
#### 组件类的构造函数
```JS
  // 构造函数可以用来初始化props的值
  constructor(props) {
    super(props); // 调一次父类的构造函数
    this.state = {date: new Date()};
  }
```
#### 组件的生命周期
- 三种状态`Mount`、`Update`、`Unmount`。
- 两种特殊状态的处理函数`componentWillReceiveProps(object nextProps)`（已加载组件收到新的参数时调用）；`shouldComponentUpdate(object nextProps, object nextState)`（组件判断是否重新渲染时调用）。

### 事件处理
在ES6写法的组件类中，方法的绑定需要用`bind()`方法绑定`this`。
### 渲染列表
#### 善用数组的`map()`方法
```JS
  const numbers = [1, 2, 3, 4, 5];
  // map()的第二个参数可以用来作为key的属性值
  const listItems = numbers.map((number,index) =>
    <li>{number}</li>
  );
```
#### 列表中的`key`属性
如果循环列表时没有给列表的每一项添加`key`属性，react会有警告。加上`key`属性有利于提高react的性能。
注：`key`并不要求在全局独一无二。
### 表单
#### 获取输入框、文本域、下拉选项中的值
```JS
  handleChange(event) {
    this.setState({value: event.target.value});
  }
```
#### 表单默认值
使用`defaultValue`和`defaultChecked`代替`value`和`checked`。
#### 处理多个表单元素
```JS
  handleInputChange(event) {
    const target = event.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    // 这里的name就是每个表单元素的name属性
    const name = target.name;

    this.setState({
      [name]: value
    });
    // 等价与这种写法
    var partialState = {};
    partialState[name] = value;
    this.setState(partialState);
  }
```
