---
title: immutable在redux中的应用
tags: ["js","react"]
date: 2017-07-29 14:1542:00
categories:
- 前端
- redux
---
> immutable类型数据对于状态庞大的系统而言是非常有用的一种数据类型，它对于追踪应用状态、减少不必要的试图渲染有着非同寻常的意义...

<!-- more -->
## 一览
### 整合各个子的reducer
redux官方提供的`combineReducers`只支持原生JS的形式，所以这里要用redux-immutable提供的`combineReducers`来代替。  
```js
import {combineReducers} from 'redux-immutable'
import dish from './dish'
import menu from './menu'
import cart from './cart'
// 整合各个子的reducer
const rootReducer = combineReducers({dish, menu, cart})
export default rootReducer
```

### reducer中的initialState肯定也需要初始化成immutable类型
```js
// Map里面传的是一个原生JS对象，初始化reducer中的initialState时建议用Map方法而不是fromJS方法，效率更高
const initialState = Immutable.Map({})
export default function menu(state = initialState, action) {
  switch (action.type) {
    case SET_ERROR:
      return state.set('isError', true)
  }
}
```

### 将immutable的数据映射到组件的props中
state成为了immutable类型，所以`mapStateToProps`也要改写成immutable的取值方法。  
```js
function mapStateToProps(state) {
  return {
    menuList: state.getIn(['dish', 'list']), //使用get或者getIn来获取state中的变量
    CartList: state.getIn(['dish', 'cartList'])
  }
}
```

### immutable数据类型检验
这就需要我们 import 专门针对immutable类型进行校验的库：`react-immutable-proptypes`，使用方法基本上和普通的PropTypes一致：  
```js
propTypes: {
  oldListTypeChecker: React.PropTypes.instanceOf(Immutable.List),
  anotherWay: ImmutablePropTypes.list,
  requiredList: ImmutablePropTypes.list.isRequired,
  mapsToo: ImmutablePropTypes.map,
  evenIterable: ImmutablePropTypes.iterable
}
// 与此同时，产生defaultProps的地方应该为：
fromJS({
  prop1: xxx,
  prop2: xxx,
  prop3: xxx
}).toObject()
```

### 装饰shouldComponentUpdate
减少页面无意义渲染的次数是immutable提升react效率的重中之重。  
```js
import pureRender from "pure-render-immutable-decorator"
// @pureRender会帮你实现shouldComponentUpdate。如果在这个钩子中还有自己的逻辑的话，请参看官方文档
@pureRender
class List extends Component {
  constructor(props, context) {
    super(props, context)
  }
  render() {
    return (
      <div> </div>
    )
  }
}
```
上面用到decorator，在js的babel loader里面，新增plugins: [‘transform-decorators-legacy’]。  
这个模块下面还有一个不用immutable的可以对原生JS对象进行深比较的模块（pure-render-deepCompare-decorator）。  

## 进阶
### immutable.js使用过程中的一些注意点
- fromJS和toJS会深度转换数据，随之带来的开销较大，尽可能避免使用，单层数据转换使用Map()和List()
- js是弱类型，但Map类型的key必须是string！（也就是我们取值是要用get('1')而不是get(1)）
- 所有针对immutable变量的增删改必须左边有赋值，因为所有操作都不会改变原来的值，只是生成一个新的变量
- 获取深层深套对象的值时不需要做每一层级的判空(JS中如果不判空会报错，immutable中只会给undefined)
- immutable对象直接可以转JSON.stringify(),不需要显式手动调用toJS()转原生
- 判断对象是否是空可以直接用size
- 调试过程中要看一个immutable变量中真实的值，可以chrome中加断点，在console中使用.toJS()方法来查看  

### 高阶组件封装
对于使用immutable.js的项目，在应用公共组件的时候，由于公共组件的内部实现一定是原生JS数据，所以我们只能传递原生JS数据到公共组件，但是如果转换成了原生JS数据，就又会出现"React.addons.PureRenderMixin提供的shouldComponentUpdate()是浅比较"问题，对此可以使用下面的高阶组件进行封装。  
```js
/* 定义高阶组件 */
import {React} from 'base'
// 通过Immutable.is 封装过的 shouldComponentUpdate
import {shouldComponentUpdate} from '../immutable-pure-render-decorator'

export default ComposedComponent => {
  return class extends React.Component {
    constructor(props) {
      super(props);
      this.shouldComponentUpdate = shouldComponentUpdate.bind(this)
    }
    render() {
      const props = this.props.toJS ? this.props.toJS() : this.props
      return <ComposedComponent { ...this.props} { ...props} />
    }
  }
}

/* 使用高阶组件 */
import highComponent from '../../../../widgets/libs/utils/highComponent'
// 公共组件
import Dialog from '@alife/dialog'
function mapStateToProps(state) {
}
function mapDispatchToProps(dispatch) {
}
// 通过高阶组件封装
export default connect(mapStateToProps, mapDispatchToProps)(highComponent(Dialog))
```

### immutable常用API
```js
//Map()  原生object转Map对象 (只会转换第一层，注意和fromJS区别)
immutable.Map({name:'danny', age:18})
//List()  原生array转List对象 (只会转换第一层，注意和fromJS区别)
immutable.List([1,2,3,4,5])
//fromJS()   原生js转immutable对象  (深度转换，会将内部嵌套的对象和数组全部转成immutable)
immutable.fromJS([1,2,3,4,5])    //将原生array  --> List
immutable.fromJS({name:'danny', age:18})   //将原生object  --> Map
//toJS()  immutable对象转原生js  (深度转换，会将内部嵌套的Map和List全部转换成原生js)
immutableData.toJS();
//查看List或者map大小  
immutableData.size  或者 immutableData.count()
// is()   判断两个immutable对象是否相等
immutable.is(imA, imB);
//merge()  对象合并
var imA = immutable.fromJS({a:1,b:2});
var imA = immutable.fromJS({c:3});
var imC = imA.merge(imB);
console.log(imC.toJS())  //{a:1,b:2,c:3}
//增删改查（所有操作都会返回新的值，不会修改原来值）
var immutableData = immutable.fromJS({
    a:1,
    b:2，
    c:{
        d:3
    }
});
var data1 = immutableData.get('a') //  data1 = 1  
var data2 = immutableData.getIn(['c', 'd']) // data2 = 3   getIn用于深层结构访问
var data3 = immutableData.set('a' , 2);   // data3中的 a = 2
var data4 = immutableData.setIn(['c', 'd'], 4);   //data4中的 d = 4
var data5 = immutableData.update('a',function(x){return x+4})   //data5中的 a = 5
var data6 = immutableData.updateIn(['c', 'd'],function(x){return x+4})   //data6中的 d = 7
var data7 = immutableData.delete('a')   //data7中的 a 不存在
var data8 = immutableData.deleteIn(['c', 'd'])   //data8中的 d 不存在
```

### 参考资料
[如何用React+Redux+ImmutableJS进行SPA开发](http://yunlaiwu.github.io/blog/2016/12/01/react+redux+immutablejs/)  
[React移动web极致优化](https://github.com/lcxfs1991/blog/issues/8)  
[immutable.js 在React、Redux中的实践以及常用API简介](https://yq.aliyun.com/articles/69516#)  
[Immutable.js 以及在 react+redux 项目中的实践](http://blog.csdn.net/sinat_17775997/article/details/73603797)  
