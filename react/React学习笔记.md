# React学习笔记

## 安装

在全局下安装脚手架，创建一个react项目工程

- $ npm install create-react-app -g
- $ create-react-app <project-name>

进入目录，启动项目

- cd <project-name>
- npm start

安装完毕，目录如下

![react1](E:\study\笔记\images\react1.png)

默认会自动安装React，React由两部分组成,分别是：

- react.js 是 React 的核心库
- react-dom.js 是提供与DOM相关的功能,会在window下增加ReactDOM属性,内部比较重要的方法是render,将react元素或者react组件插入到页面中。

## JSX

jsx语法是facebook自己发明的，是javascript和xml语法的集合体，即可以像平常一样使用HTML，也可以在里面嵌套JavaScript语法。将组件的结构、数据甚至样式都聚合在一起定义组件，运行时，Babel等工具会将JSX编译成JavaScript语法。

```jsx
let ele = (
    <h1 className="red">
        hello!
        <span>ReneeTsang!</span>
    </h1>
);
```

### ReactDOM.render()

ReactDOM.render 是 React 的最基本方法，用于将模板转为 HTML 语言，并插入指定的 DOM 节点。

JSX其实只是一种语法糖，最终会通过[babel](https://babeljs.io/repl/)转译成createElement语法，以下代码等价

```javascript
ReactDOM.render(ele);
ReactDOM.render(React.createElement("h1",{className: "red" },"hello!",React.createElement("span",null,"ReneeTsang!")));
```

### JSX表达式的用法

jsx和html的写法不完全一样。

- 遇到 HTML 标签（以 `<` 开头），就用 HTML 规则解析；遇到代码块（以 `{` 开头），就用 JavaScript 规则解析


- 如果换行需要用`()`包裹jsx代码
- 复数同级标签，必须加一层标签包裹，如不想使用div等包裹，可以使用`<React.Fragment>`


- 可以放JS的执行结果，用`{}`包裹
- 可以把JSX元素当作函数的返回值

```jsx
import React from 'react';
// React.creatElement
import ReactDOM from 'react-dom';

let sty = { background: 'red' }
let ele = (
    <React.Fragment>
        <h1style={{background: 'red'}}>{/*可以这样子写注释*/} title</h1>
        <h1 style={sty}>hello</h1>
    </React.Fragment>
)

function toResult({name,age}) {
    return <span>今年{name},{age}岁了!</span>
}
let arrs =  [{name:'renee',age:18},{name:'洁莹',age:18}];
ReactDOM.render(<div>
    {arrs.map(((item,index)=>(
        typeof item==='object'?<li key={index}>{toResult(item)}</li>:null
    )))}
</div>,document.getElementById('root'));
```

- JSX 允许直接在模板插入 JavaScript 变量。如果变量是数组，数组可以直接渲染到页面上。

```jsx
let dinner = ['汉堡', '可乐', '薯条'];
// 渲染列表要用map
let eleObj = dinner.map((item, index) => (
    <li key={index}>{item}</li>
));
render(eleObj, window.root);
```

### JSX属性

- 在JSX中分为普通属性和特殊属性，像class要写成className，for要写成htmlFor，因为class和for是js关键字

```jsx
let ele = (
    <label htmlFor="a" className="focus">输入焦点</label>
    <input type="text" id="a"/>
)
```

- style属性要采用对象的形式来写
- key 使用驼峰写法，value值是字符串

```jsx
let sty = { background: 'red',color: 'blue'}
let ele = (
    <h1 style={sty}>hello</h1>
)
```

- 使用dangerouslyInnerHTML属性插入html，但会导致xss攻击

```jsx
let ele = (
	<div dangerouslySetInnerHTML={{__html:'<h1>hello world</h1>'}}></div>
)
```

### JSX组件

react元素是是组件组成的基本单位，组件名需要**开头大写**，react组件当作jsx来进行使用

- 首字母必须大写，目的是为了和JSX元素进行区分
- 组件也是JSX元素，定义后可以像JSX元素一样进行使用
- 每个组件必须返回唯一的顶级JSX标签元素，也可以返回null，null也是合法的
- 可以通过render方法将组件渲染成真实DOM，但是只会渲染一次
- render方法重复渲染只会局部更新

#### 组件分为两种 ，函数组件(函数)function和类组件class

- 第一种方式是函数声明
  - 函数组件中**没有**自己的状态、this和生命周期

```jsx
function Build(props) {
  return <p>{props.name} {props.age}</p>
}
render(<div>
  <Build name={school1.name} age={school1.age}/>
  <Build {...school2} />
</div>,window.root);
```

- 第二种方式是类声明，类声明**有**状态，this，和生命周期，所以这个用的多

  - 类组件，需要有自己的状态，需要继承父类组件extends Component
  - 如果用类组件需要提供一个render方法，必须要返回一个 JSX 元素，会自动将render方法的返回值作为结果进行渲染


```jsx
import React, { Component } from 'react';
import ReactDOM,{ render } from 'react-dom';

//我们的组件要继承React组件，因为react组件封装了很多方法，如this.setState()
class Build extends Component{
    constructor(props){
        
    }  
  render(){
      let {name,age} = this.props;
      return <p>{name} {age}</p>
  }
}
render(<Bulid name='renee'></Bulid>,window.root)
```

#### 组件中属性和状态的区别

- 组件的数据来源有两个地方
  - props 外界传递过来的(默认属性，属性校验)，在render方法中可以通过this.props获取属性
  - React.js 也提供了一种方式 `defaultProps`，可以方便的做到默认配置。 
  - `props` 一旦传入进来就不能改变 
  - state 状态是自己的,改变状态唯一的方式就是setState
  - setState是批量更新的，不一定是同步的

> 属性和状态的变化都会影响视图更新

```jsx
import React, { Component } from 'react';
import ReactDOM,{ render } from 'react-dom';

class Clock extends Component{
    constructor(){
        super();
        this.state = {date:new Date().toLocaleString(),name:'zfpx'}
    }
    // 如果用类组件需要提供一个render方法
    // 组件渲染完成后会调用这个生命周期
    // 什么时候放在this上 什么时候放在this.state上
    componentDidMount(){ // 组件渲染完成，当渲染后会自动触发此函数
        this.timer = setInterval(()=>{
            // setState可以更新页面
            // 会将这个对象和原有的状态进行合并，合并后重新渲染页面
            this.setState({ date: new Date().toLocaleString() });
        },1000)
    }
    componentWillUnmount(){ // 组件将要卸载，当组件移除时会调用
        clearInterval(this.timer); // 一般卸载组件后要移除定时器 和绑定的方法
    }
    // 绑定方法有几种方式 方法中可能会用this
    // 1.箭头函数
    // 2.bind
    // 3.再构造函数中绑定this  this.handleClick = this.handleClick.bind(this)
    // 4.es7语法 可以解决this指向
    handleClick=()=>{
        ReactDOM.unmountComponentAtNode(document.querySelector('#root'));
    }
    render(){
        return (
            <div onClick={this.handleClick}>
                时间是:{this.state.date} 名字:{this.state.name}
            </div>
        )
    }
}
render(<div>
    <Clock /> 
</div>, window.root);

// 组件有两个数据源 一个是属性 外界传递的  还有一个叫状态是自己的
// 你传递的属性可能不是我预期的
```

#### PropTypes属性校验

> npm install prop-types

组件的属性可以接受任意值，字符串、对象、函数等等都可以。有时，我们需要一种机制，验证别人使用组件时，提供的参数是否符合要求。

propTypes和defaultProps名字不能更改，这是react规定好的名称

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import PropTypes from 'prop-types'; //引入属性校验的模块
class School extends React.Component{ // 类上的属性就叫静态属性
    // ES7方法，ES6不支持静态属性，只有静态方法
    // 校验属性类型
    static propTypes = { // 校验属性的类型和是否必填
        name: PropTypes.string.isRequired,// 支持的类型可以参考prop-types的readme文件
        age:PropTypes.number.isRequired,
        gender: PropTypes.oneOf(['male','famale'])
    };
	// 默认属性值
    static defaultProps = { // 先默认调用defaultProps
        name:'renee', // 没传就用renee
        age:18,
        gender:'女'
    }; // 默认属性
    constructor(props){ //如果想在构造函数中拿到属性需要通过参数的方式
         //不能在组件中更改属性
        super();
    }
    render(){
        return <h1>{this.props.name} {this.props.age} {this.props.gender}</h1>
    }
}
```

#### 绑定事件

给元素绑定事件，事件绑定方式

- React.js 中的`event` 对象并不是浏览器提供的，而是它自己内部所构建的。

- 给jsx元素绑定事件要注意事件中的this指向，事件名采用 on+"开头大写事件名"的方式

bind解决this问题，不会产生新的函数，只执行一次。但一般还是用ES7箭头函数

```react
class Clock extends Component {
    constructor(){
        super();
        this.state = {num:0};
        this.fn=this.fn.bind(this)
    }
    fn(){
        console.log(this)
    }
    render(){
        return <h1 onClick={this.fn}>{this.state.date}</h1>
    }
}
ReactDOM.render(<Clock/>,window.root);
```

```react
class Clock extends Component {
    constructor(){
        super();
        this.state = {num:0};
    }
    fn=()=>{(
        // 发现this.state都是同一个，都一样，这样写只会执行一个
        // this.setState({num:this.state.num+1})
        // this.setState({num:this.state.num+1})
        // this.setState({num:this.state.num+1})   
        
        // 想获取更改后的值，再累加，这种方式不建议
        this.state({num:this.state.num+1},()=>{
            this.state({num:this.state.num+1})
        })
        // 可以这么写
        // 会把上一个 setState 的结果传入这个函数，你就可以使用该结果进行运算、操作，然后返回一个对象作为更新 state 的对象
        this.setState((prevState)=>({num:prevState.num+1}));        		    this.setState((prevState)=>({num:prevState.num+1}));
    )}
    render(){
        return (<div>
        	{this.state.num}
             <button onClick={this.fn}>+</button>   
             <button onClick={this.remove}>删除组件</button>   
        </div>)
    }
}
ReactDOM.render(<Clock/>,window.root);
```

```jsx
class Clock extends Component {
    constructor(){
        super();
        this.state = {date:new Date().toLocaleString()}
    }
    componentDidMount(){ //组件渲染完成，当渲染后会自动触发此函数
        this.timer = setInterval(()=>{ // 箭头函数 否则this 指向的是window
            this.setState({date:new Date().toLocaleString()})
        },1000);
    }
    componentWillUnmount(){ //组件将要卸载，当组件移除时会调用
        clearInterval(this.timer); //一般在这个方法中 清除定时器和绑定的事件
    }
    destroy=()=>{ //es7 箭头函数
        // 删除某个组件
        ReactDOM.unmountComponentAtNode(window.root);
    }
    render(){
        // 给react元素绑定事件默认this是undefined,可以用bind方式（会产生新函数）、箭头函数
        return <h1 onClick={this.destroy}>{this.state.date}</h1>
    }
}
// 执行顺序 constructor -> render -> componentDidMount -> setState-> render - onClick-> unmountComponentAtNode -> componentWillUnmount -> clearInterval
ReactDOM.render(<Clock/>,window.root);
```

#### 组件的通信方式

组件间的通信，第一种方法就是通过属性传递，父->子->孙子

是单向数据流，数据方向是单向的，儿子不能更改父亲的属性



#### 受控组件和非受控组件

受控组件和非受控组件（state），指的都是表单元素

- 受控组件，可以对输入进行监控，而且对表单进行默认值操作

- 非受控组件
  - 可以操作dom，获取真实dom
  - 可以和第三方库结合，如JQ
  - 不需要对当前输入的内容进行校验，也不需要默认值

##### 受控组件

> React.js 认为所有的状态都应该由 React.js 的 state 控制，只要类似于 `<input />`、`<textarea />`、`<select />` 这样的输入控件被设置了 `value`值，那么它们的值永远以被设置的值为准。值不变，`value` 就不会变化。 

```javascript
{/* 写了value 要写onChange事件更改状态或者用defaltValue，不然不会更改 */}
<input type="text" required={true} value={this.state.a} onChange={this.handleChange} name="a"/>
```

##### 非受控组件





###  生命周期lifeCycle

有图

#### componentWillMount 组件挂载之前

在组件挂载之前调用且全局只调用一次。如果在这个钩子里可以setState，render后可以看到更新后的state，不会触发重复渲染。该生命周期可以发起异步请求，并setState。（*React v16.3后废弃该生命周期，没什么用处，如果想要在render之前调取某些属性，可以在constructor中完成设置state*）

#### componentDidMount 组件挂在完成后

#### shouldComponentUpdate

#### componentDidUpdate

```react

```









