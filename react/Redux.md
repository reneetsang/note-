# Redux

Redux 是一个 JavaScript 应用状态管理的库，当项目很复杂的时候，属性传递已经达不到我们预期，可以使用Redux 解决数据传递问题，统一状态管理。换句话说，Redux就是用来处理和管理应用的状态/数据。

![redux-wrong](../images/redux-wrong.png)

1. 主要两个或者多个组件之间想要实现信息的共享，都可以基于redux解决，把共享的信息存储到redux容器中进行管理
2. 还可以使用redux做临时存储：页面加载的时候，把从服务器获取的数据信息存储到redux中，组件渲染需要的数据，从redux中获取，这样只要页面不刷新，路由切换的时候，再次渲染组件不需要重新从服务器拉取数据，直接从redux中获取即可。页面刷新，从头开始！（并不一定需要用，只是代替了localStorage本地存储，来实现数据缓存）

##　Redux设计思想

- Redux是将整个应用状态存储到到一个地方，称为store
- 里面保存一棵状态树(state tree)
- 组件可以派发(dispatch)行为(action)给store,而不是直接通知其它组件
- 其它组件可以通过订阅store中的状态(state)来刷新自己的视图

![redux-flow](../images/redux-flow.png)

## 实现简单的Redux

来写一个简单的"redux"吧！

实现把内容渲染到页面上

### 1.渲染状态

这时候appState数据是全局变量，所有人都可以更改，很不安全

```jsx
//数据源
let appState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
// 渲染标题
function renderTitle(title) {
    let titleEle=document.querySelector('#title');
    titleEle.innerHTML=title.text;
    titleEle.style.color=title.color;
}
// 渲染内容
function renderContent(content) {
    let contentEle=document.querySelector('#content');
    contentEle.innerHTML=content.text;
    contentEle.style.color=content.color;
}
// 执行渲染的方法
function render(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}
render(appState);
```

### 2.提高数据修改的门槛

- 状态不应该是全局的，也不应该哪个方法里直接可以更改（操作危险）
- 一旦数据可以任意修改，所有对共享状态的操作都是不可预料的
- 模块之间需要共享数据和数据可能被任意修改导致不可预料的结果之间有矛盾
- 所以提供一个修改状态的`dispatch`方法，不要去直接更改状态，对数据的操作修改必须通过这个方法

规定 如果想要修改appState只能通过调用dispatch方法

action是一个动作，用来描述你想怎么修改

```jsx
let appState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
function renderTitle(title) {
    let titleEle=document.querySelector('#title');
    titleEle.innerHTML=title.text;
    titleEle.style.color=title.color;
}
function renderContent(content) {
    let contentEle=document.querySelector('#content');
    contentEle.innerHTML=content.text;
    contentEle.style.color=content.color;
}
function render(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}
//先定义好要做那些事情（常量） 也叫宏
// action是一个动作 {type:UPDATE_TITLE_COLOR,color:'orange}
const UPDATE_TITLE_COLOR = 'UPDATE_TITLE_COLOR'; // 更新标题颜色
const UPDATE_CONTENT_CONTENT = 'UPDATE_CONTENT_CONTENT'; // 更新内容文本

// 保安
// 派发的方法，用来更改状态
// 派发时应该将修改的动作action提交过来，是个对象，对象里的type属性是固定必须的。
function dispatch(action) {
    switch (action.type) {
        case UPDATE_TITLE_COLOR:
            appState.title.color=action.color;    
            break;    
        case UPDATE_CONTENT_CONTENT:
            appState.content.text=action.text;
            break;
        default:
            break;    
    }
}
// action是一个动作，是个对象{type:UPDATE_TITLE_COLOR,color:'purple'}
dispatch({type:UPDATE_TITLE_COLOR,color:'purple'});
dispatch({type:UPDATE_CONTENT_CONTENT,text:'新标题'});

render(appState);
```

### 3.分装仓库

上面虽然定义了一个dispatch来修改，但是写个appState=null还是可以修改，因为它还是全局变量。

需要把状态放进一个容器里，保护起来，外面拿不到（限制它的作用域，放在函数里），将定义状态和规则的部分抽离到容器外面

```jsx
function renderTitle(title) {
    let titleEle=document.querySelector('#title');
    titleEle.innerHTML=title.text;
    titleEle.style.color=title.color;
}
function renderContent(content) {
    let contentEle=document.querySelector('#content');
    contentEle.innerHTML=content.text;
    contentEle.style.color=content.color;
}
function render(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}
// 创建容器/仓库，这个应该是个可以复用的方法（每个保安的工作业务逻辑应该是不一样的），所以dispatch和state都不应该写死在里面，要传进去
function createStore(reducer) {
    let state; 
    // 保护起来，这时候是私有变量，又可以让外面可以获取状态
    function getState() {
        return state;
    }
	
    // 拿到动作，根据老状态，算出新状态。
    // 为什么要知道老状态呢，比如要写计数器，每次加一，那要知道原来旧状态是多少才可以
    function dispatch(action) { 
        state=reducer(state,action); // 返回新的state状态，把新状态覆盖
    }
    // 刚开始state是undefined，调用reducer可以获取，所以需要派发一次动作
    // 在创建仓库的时候派发一次动作，为了走下reducer获取初始值，写个空对象是undefined走默认值（返回初始状态 ）
    dispatch({});
    
    // 将方法暴露给外面使用(store.getState()),将状态放到了容器中外部无法在进行更改了
    return { getState , dispatch }
}

// 容器一般会封装成库
// 将定义状态和规则的部分抽离到容器外面，再传进去
// 初始状态
let initState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
const UPDATE_TITLE_COLOR = 'UPDATE_TITLE_COLOR';
const UPDATE_CONTENT_CONTENT = 'UPDATE_CONTENT_CONTENT';

// 用户自己定义的规则，我们叫它reducer，也就是所谓的管理员
// 处理器 reducer要有两个参数，传入老的状态和新传递的动作算出新的状态
// 如果想获取默认状态，有一种方式，就是调用reducer，让每一个规则都不匹配将默认值返回
// 在reducer中，reducer是一个纯函数，每次需要返回一个新的状态，只承担计算 State 的功能
let reducer=function (state=initState,action) {
    switch (action.type) {
        case UPDATE_TITLE_COLOR:
            return {...state,title: {...state.title,color:action.color}};
        case UPDATE_CONTENT_CONTENT:
        return {...state,content: {...state.content,text:action.text}};    
            break;
        default:
            return state;    
    }
}

// 把reducer传进去，因为每个管理员处理的事情不一样
let store=createStore(reducer);
render(store.getState()); //store.getState() 状态对象
setTimeout(function () {
    store.dispatch({type:UPDATE_TITLE_COLOR,color:'purple'});
    store.dispatch({type:UPDATE_CONTENT_CONTENT,text:'新标题'});
    render(store.getState());
},2000);
```

### 4.监控数据变化 发布订阅

上面每派发一次动作，就要重新渲染一次，很麻烦

把渲染逻辑放在dispatch里面，订阅这个状态变化，通知我刷新

```jsx
function renderTitle(title) {
    let titleEle=document.querySelector('#title');
    titleEle.innerHTML=title.text;
    titleEle.style.color=title.color;
}
function renderContent(content) {
    let contentEle=document.querySelector('#content');
    contentEle.innerHTML=content.text;
    contentEle.style.color=content.color;
}
function render() {
    renderTitle(store.getState().title);
    renderContent(store.getState().content);
}
function createStore(reducer) {
    let state;
    let listeners=[]; // 订阅者
    function getState() {
        return state;
    }
	// 发布订阅模式，先将render方法订阅好，每次dispatch时都调用订阅好的方法
    function dispatch(action) { // 发布
        state=reducer(state,action);
        listeners.forEach(l=>l()); // 当派发后，状态变化时让所有的订阅函数执行
    }

    function subscribe(listener) { // 订阅的方法
        listeners.push(listener);
        return () => {
            // 再次调用时 移除监听函数，取消订阅
            listeners = listeners.filter(item => item!=listener);
            console.log(listeners);
        }
    }
    dispatch({});
    return { getState,dispatch,subscribe }
}
let initState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
let reducer=function (state=initState,action) {
    switch (action.type) {
        case UPDATE_TITLE_COLOR:
            return {...state,title: {...state.title,color:action.color}};
        case UPDATE_CONTENT_CONTENT:
        return {...state,content: {...state.content,text:action.text}};    
            break;
        default:
            return state;    
    }
}
let store=createStore(reducer);
render();
// 向仓库进行订阅，订阅一个函数，监控状态，更改执行render方法
let unsubscribe = store.subscribe(render);
setTimeout(function () {
    store.dispatch({type:'UPDATE_TITLE_COLOR',color:'purple'});
    unsubscribe();
    store.dispatch({type:'UPDATE_CONTENT_CONTENT',text:'新标题'});
},2000);
```

## Redux概念解析

![redux](../images/redux.png)

### Store

Redux的核心是一个`store` ，就是保存数据的地方，可以看出是一个容器，整个应用就只能有一个store。Redux提供`createStore()`函数来生成store。

```javascript
import { createStore } from 'redux';
let store = createStore(fn);
```

上面代码中，createStore函数接受另一个函数作为参数，返回新生成的Store对象。

### State

store 某个节点对应的数据集合就是state。`state` 是被托管的数据，也就是每次触发监听事件，我们要操作的数据。可以通过`store.getState()`获得。

Redux 规定，一个state对应一个View。State相同，则View相同。

```jsx
let store = createStore(fn);
let state = store.getState();
```

### Action

State 的变化，会导致 View 的变化。但是，用户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。

Action 是一个对象。其中的`type`属性是必须的，表示 Action 的名称。其他属性可以自由设置。

```javascript
{ type: types.ADD_TODO , text: '读书' }
```

### Actor Creator

action creator 顾名思义就是用来创建 action 的，action creator 只简单的返回 action。

每个版块单独的action-creator:就是dispatch派发时候需要传递的action对象进一步统一进行处理（react-redux中会体验到它的好处）

```javascript
const ADD_TODO = 'ADD_TODO'; //import * as TYPE from '../action-types';
let actions = {
    addTodo(todo){
        // dispatch派发的时候需要传递啥就返回啥即可
        return { type: ADD_TODO, todo}
    }
}
```

###　store.dispath(action)

store.dispatch()是 View 发出 Action 的唯一方法。store.dispatch接受一个 Action 对象作为参数，将它发送出去

通过派发任务，告知reducer执行，需要把state和action传递进去。reducer执行，把容器中的状态改变   

```javascript
let store=createStore(reducer);
store.dispatch({type:'ADD_TODO',text:'读书'});
```

结合 Action Creator，这段代码可以改写如下。

```javascript
 store.dispatch(actions.addTodo(e.target.value))
```

## Redux 核心API

### createStore

使用方法

> let store=createStore(reducer);

- reducer

  在Redux中，负责响应`action`并修改数据的角色就是`reducer`。 reducer要有两个参数 ，要根据老的状态和新传递的动作算出新的状态。`reducer`本质上是一个纯函数，每次需要返回一个新的状态对象。

  纯函数很严格，也就是说你几乎除了计算数据以外什么都不能干，计算的时候还不能依赖除了函数参数以外的数据。

  ```javascript
  //以下为reducer的格式
  const todo = (state = initialState, action) => {
    switch(action.type) {
        case 'XXX':
            return //具体的业务逻辑;
        case 'XXX':
            return //具体的业务逻辑;   
        default:
            return state;
    }
  }
  ```

- getState()

  将状态放到了容器中外部无法在进行更改了，使用获取`store`中的状态。

- dispatch(action)

  store.dispatch()是 View 发出 Action 的唯一方法。store.dispatch接受一个 Action 对象作为参数，将它发送出去

- subscribe(listener)

### bindActionCreator

```react
import actions from '../store/actions/counter';
import {bindActionCreator} from '../../redux';
let newActions=bindActionCreator(actions,store.dispatch);

<button onClick={()=>newActions.add()}>+</button>
<button onClick={()=>newActions.minus()}>-</button>
```

原理

```react
export default function (actions,dispatch) {
    let newActions={};
    for (let key in actions) {
        newActions[key]=() => dispatch(actions[key].apply(null,arguments));
    }
    return newActions;
}
```



###combineReducers

因为redux只有一个状态树，即一个reducer，组件都是不同的状态，只能把这些动作都发给统一的仓库，统一的reducer来处理 

可以使用combineReducers把多个reducer合并成一个reducer导出 

key是新状态的命名空间，值是reducer，执行后会返回一个新的reducer。

```javascript
// 合并reducers时，为了保证每一个板块管理的状态信息不冲突，在redux中，按照指定的名称单独划分板块的状态
//{c:{},todo:{}}
let reducer = combineReducers({
    c: counter,
    todo
});
export default reducer;
```

原理：

```javascript
function combineReducers(reducers) {
    // 第二次调用reducer ，内部会自动的把第一次的状态传递给reducer
    return (state = {}, action) => {
        let newState = {}
        // reducer默认要返回一个状态，要获取counter的初始值和todo的初始值
        for (let key in reducers) {
            // 默认reducer俩参数 一个叫state，一个叫action
            // 俩参数初始值是undefined, {}
            let s = reducers[key](state[key], action);
            newState[key] = s;
        }
        return newState;
    }
}

//因为redux应用只能有一个仓库，只能有一个reducer
//把多个reducer函数合并成一个
export default function (reducers) {
	//返回的这个函数就是合并后的reducer
	// state合并后老的状态 acion新的状态
	return function (state={},action) {
		let newState={};
		for (let key in reducers) {
			// 第一次循环
			// key=counter1
			// reducers[counter1]=counter那个reducer 
			// state合并后的状态树 
			// if (key===action.name) {
				newState[key]=reducers[key](state[key],action);
			//}
		}
		return newState;
	}
}
```

### context

react提供一个context API，可以解决跨组件的数据传递。16.3版本以前的context和现在最新版context用法有区别。在16.3官方不推荐使用，如果某个组件shouldComponentUpdate返回了false后面的组件就不会更新了。都是有关系的组件才可以，类似祖父传给子孙。

contextAPI 新的方法非常简便。

```jsx
import React from 'react';
import {render} from 'react-dom';
// 创建一个上下文,有两个属性 一个叫Provider提供者（传递数据） 还有个叫Consume消费者（接受消费数据），可以跨级别传递数据（父->子 孙）
// createContext中的对象是默认参数
let { Consumer,Provider} = React.createContext();
// context 可以创建多个 这时候就不要解构了，不同的context是不能交互的
class Title extends React.Component{
    render(){
        // 子类通过Consumer进行消费 内部必须是一个函数 函数的参数是Provider的value属性
        return <Consumer>
            {({s,h})=>{
                return <div style={s} onClick={()=>{
                    h('red');
                }}>hello</div>
            }}
        </Consumer>
    }
}
class Head extends React.Component{
    render() {
        return <div>
            <Title></Title>
        </div>
    }
}
//  Provider使用在父组件上
class App extends React.Component{
    constructor(){
        super();
        this.state = {color:'green'}
    }
    handleClick = (newColor) =>{
        this.setState({ color: newColor})
    }
    render(){
        return <Provider value={{ s: this.state,h:this.handleClick}}>
            <Head></Head>
        </Provider>
    }
}
render(<App></App>,window.root)
```

##　例子：简单的加减数量

```jsx
import React,{Component} from 'react';
import {createStore} from '../redux';

let initState = {number:0};
// 创建需要的方法 ation-type
const INCREMENT = 'INCREMENT';
const DECREMENT = 'DECREMENT';
// 创建规则
function reducer(state = initState,action){ //{type:'IN...',count:2}
    switch (action.type) {
        case INCREMENT:
            return { number:state.number + action.count};
        case DECREMENT:
            return { number: state.number - action.count}
    }
    return state;
}
// 创建容器
let store = createStore(reducer);
export default class Counter extends Component{
    constructor(){
        super();
        this.state = { number: store.getState().number}
    }
    componentDidMount(){
        // 组件挂载完成后 希望订阅一个更新状态的方法，只要状态发生变化，就setState更新视图
        this.unsub = store.subscribe(()=>{
            this.setState({ number: store.getState().number })
        });
    }
    componentWillUnmount(){
        // 移除订阅
        this.unsub();
    }
    render(){
        return <div>
            {/* 属性和状态变了，视图才会更新，所以要放在状态里 */}
            计数器 {this.state.number}
            <button onClick={()=>{
                store.dispatch({type:INCREMENT,count:2})
            }}>+</button>
            <button onClick={()=>{
                store.dispatch({ type: DECREMENT, count: 1 })
            }}>-</button>
        </div>
    }
}
```

## 优化结构 

`index.js`中的代码逐渐变得冗杂。我把所有的代码都写在`index.js`中是为了起步时的简单易懂。接下来，我们来看一下如何组织Redux项目。首先，在`src`文件夹中创建一下文件和文件夹：

一般项目里，会有一个store的文件夹，专门管理的redux的

- actions 里放actorCreator的

  - 因为每个组件都有它自己不同的动作

  - Actor Creator 用来创建action动作，不需要手动拼action了 ，组件里引用这个就行，不用再引用action-types了。就是封装几个方法（都是需要dispatch派发任务修改状态时候执行的方法），方法返回的是当前派发任务时候传递的action对象。 

- reducers 里放reducer的

  - 希望每个组件都只维护自己的状态

  - redux只有一个状态树，一个reducer，组件都是不同的状态，只能把这些动作都发给统一的仓库，统一的reducer来处理 

  - 一般情况下，如果用combineReducers库合并成一个reducer，一般会在reducers文件夹下在新建一个单独的文件(index.js)，把合并的reducer导出来再用

- action-types.js 放常量的（想要实现的功能）

- index.js 创建容器stores

```javascript
├── components
│   └── Counter.js
├── index.js // 主入口
└── store
    ├── action-types.js // 所有派发任务的行为标识都在这里进行宏观管理
    ├── actions // 存放每一个模块需要进行的派发任务(ActionCreator)
        ├── index.js // 合并所有的action-creator,类似reducer合并，为了防止冲突，合并后的对象是以板块名称单独划分管理
    │   └── counter.js
    ├── index.js // 创建store(redux容器)
    └── reducers // 存放每一个模块的reducer,专门处理自己的动作
        ├── index.js // 使用combineReducers合并所有reducer
        └── counter.js
```

## React-Redux

Redux流程中，每个组建中要把状态映射到组建上，还要自己订阅和更新，很麻烦。所以React-Redux诞生了，可以实现把redux映射到组件里，还可以自动更新。

react-redux是把redux进一步封装，是被redux项目，让redux操作更简单

- store文件夹中的内容和redux一样
- 只是在组件调取使用的时候，可以优化一些步骤

React-Redux主要有两个组件：

1. Provider根组件

   当前整个项目都在Provider组件下，作用就是把创建的store可以供内部任何后代组件使用（基于上下文完成的）

   Provider组件中只允许出现一个子元素

   把创建的store基于属性传递给Provider(这样后代组件都可以使用这个store)

2. connect高阶组件

#### connect() 高阶组件

React-Redux 提供`connect`方法，是个高阶函数，用于从 UI 组件生成容器组件。connect方法调用后返回的是新组件。

connect实现的是仓库和组件的连接 。

其中`connect`方法接受两个参数：`mapStateToProps`和`mapDispatchToProps`

- mapStateToProps

  是一个函数 把状态映射为一个属性对象 

  映射state状态到props属性上。mapStateToProps会订阅 Store，每当state更新的时候，就会自动执行，重新计算 UI 组件的参数，从而触发 UI 组件的重新渲染。

  ```javascript
  let mapStateToProps = (state)=>{ // state就是store.getState()
      return {n:state.c.number} // 相当于以前的store.getState().c.number 返回的结果会作为Counter的属性
  }
  ```

- mapDispatchToProps

  也是一个函数 把dispatch方法映射为一个属性对象 

  将dispatch方法的返回值作为属性对象

```jsx
import React, { Component } from 'react';
import actions from '../store/actions/counter';
import {connect} from 'react-redux';
// connect方法是个高阶函数，是实现redux和组件的连接

class Counter extends Component {
    constructor() {
        super();
    }
    render() {
        return <div>
            计数器 {this.props.n}
            <button onClick={() => {
                this.props.add(2)
            }}>+</button>
            <button onClick={() => {
              this.props.minus(1);
            }}>-</button>
        </div>
    }
}

// connect方法接受两个参数：mapStateToProps和mapDispatchToProps
// mapStateToProps映射状态到属性
// 将状态的返回值作为属性
let mapStateToProps = (state)=>{ // state就是store.getState()
    return {n:state.c.number} // 相当于以前的store.getState().c.number 返回的结果会作为Counter的属性
}
// mapDispatchToProps将dispatch方法 返回值作为属性
let mapDispatchToProps = (dispatch) =>{ // dispatch是store.dispatch
    return {
        add(n){dispatch(actions.add(n))},
        minus(n){dispatch(actions.minus(n))}
    }
}
// connect方法调用后返回的是新组件，导出连接后的新组件给外面用
export default connect(mapStateToProps,mapDispatchToProps)(Counter);
// mapDispatchToProps很像action，可以直接用
export default connect(mapStateToProps,action)(Counter);
```

##### 原理

```react
import {Consumer} from './context';
import React,{Component} from 'react';
import {bindActionCreators} from '../redux';
/**
 * connect实现的是仓库和组件的连接
 * 把仓库中的数据拿到，加工以后传给了组件
 * mapStateToProps 是一个函数 把状态映射为一个属性对象
 * mapDispatchToProps 也是一个函数 把dispatch方法映射为一个属性对象
 */
export default function (mapStateToProps,mapDispatchToProps) {
	// 返回一个函数，接收参数是一个组件，返回的还是个组件
	return function (Com) {
		//在Proxy这个组件里实现仓库和组件的连接
		class Proxy extends Component{
			// 这个state传给了Com
			state=mapStateToProps(this.props.store.getState())
			componentDidMount() {
				this.unsubscribe = this.props.store.subscribe(() => {
					this.setState(mapStateToProps(this.props.store.getState()));
				});
			}
			componentWillUnmount = () => {
				this.unsubscribe();
			}
			
			render() {
				// 先声明一个空的actions对象
				// mapDispatchToProps可以是函数，也可以是actionCreator对象
				let actions={};
				// 如果说mapDispatchToProps是一个函数，执行后得到属性对象
				if (typeof mapDispatchToProps === 'function') {
					actions = mapDispatchToProps(this.props.store.dispatch);
				//如果说mapDispatchToProps是一个对象的话，我们需要手工绑定自动派发
				} else {
					actions=bindActionCreators(mapDispatchToProps,this.props.store.dispatch);
				}
				return <Com {...this.state} {...actions}/>
			}
		}
		
		// 返回一个组件，要接收provider的仓库
		return () => (
			<Consumer>
				{
					// value =>{let store=value.store}
					// 渲染Consumer其实是渲染value =>{let store=value.store}的返回值，就是Proxy
					value => <Proxy store={value.store}/>
				}
			</Consumer>
		);
	}
}
```

#### Provider 根组件

React-Redux 提供`Provider`组件，用来接受store，再经过他的手通过context api传递给所有的子组件，套在子组件外面（有点像路由的用法），可以让子孙组件拿到`state`。

connect方法返回的是新组件容器，需要让容器组件拿到`state`对象，才能生成 UI 组件的参数。

```jsx
import React from 'react';
import {render} from 'react-dom';
import Cart from  './components/cart'
import 'bootstrap/dist/css/bootstrap.css';
import {Provider} from 'react-redux';
import store from './store';
render(<Provider store={store}>
    <Cart></Cart>
</Provider>,window.root);
```

`Provider`在根组件外面包了一层，这样一来，`App`的所有子组件就默认都可以拿到`state`了。

##### 原理

```react
/**
 * 是一个组件，用来接受store,再经过它的手通过context api传递给所有的子组件
 */
import React,{Component} from 'react'
import {Provider as StoreProvider} from './context';

import PropTypes from 'prop-types';
export default class Provider extends Component{
	//规定如果有人想使用这个组件，必须提供一个redux仓库属性
	static propTypes={
		store:PropTypes.object.isRequired
	}
	render() {
		let value={store:this.props.store};
		return (
			<StoreProvider value={value}>
				{this.props.children}
			</StoreProvider>
		)
	}
}
```

#### 例子

```react
// react-redux
// VoteBase.js
import {connect} from 'react-redux'
import action from '../../store/action'
// 相对于传统的组件，做的优化步骤有：
// 1.导出的不再是我们创建的组件，而是基于connect构造后的高阶组件
//	export default connect([mapStateToProps],[mapDispatchToProps])([自己创建的组件名字])
// 2.react-redux帮我们做了一件非常重要的事情，以前我们需要自己基于subscribe向事件池追加方法，以达到容器状态信息改变的时候，执行我们追加的方法。但是现在不用了，react-redux帮我们做了这件事：“所有用到redux容器状态信息的组件，都会向事件池追加一个方法，当状态信息改变，通知方法执行，把最新的状态信息作为属性传递给组件，组件的属性值改变了，组件也会重新渲染”
class VoteBase extends React.Component{
	constructor(props){
		super(props);
	}

	componentWillMount(){
		this.props.init({
			title:'我长得帅不帅',
			n:0,
			m:100
		})
	}

	render(){
		let {title,n,m}=this.props;
		return <div className='panel panel-default'>
			<div ckassName='panel-heading'>
				<h3 className='panel-title'>{title}</h3>
			</div>
		</div>
	}
}
// 把REDUX容器中的状态信息遍历，赋值给当前组件的属性（state）
let mapStateToProps=state=>{
	// state就是redux容器中的状态信息
	// 我们返回的是啥，就把它挂载到当前组件的属性上（redux存储很多信息状态，我们想用啥就返回啥即可），console.log(this.props)可查看到
	return{
		...state.vote
	}
};
// 把redux中的dispatch派发行为遍历，也赋值给组件的属性（ActionCreator）
// let mapDispatchToProps=dispatch=>{
// 	// dispatch:就是store中存储的dispatch方法
// 	// 返回的是啥，就相当于把它挂载到当前组件的属性上（一般我们挂载一些方法，这些方法中完成了dispatch派发任务操作）
// 	return{
// 		init(initData){
// 			dispatch(action.vote.init(initData));
// 		}
// 	}
// };
// export default connect(mapStateToProps,mapDispatchToProps)(VoteBase);

// 可优化为:
export default connect(state=>({...state.vote}),action)(VoteBase);
// 因为react-redux帮我们做了一件事，把action-creator中编写的方法（返回action对象的方法），自动构建成dispatch派发任务的方法，也就是mapDispatchToProps这种格式
```

