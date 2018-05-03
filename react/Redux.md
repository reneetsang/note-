# Redux

Redux 是一个 JavaScript 应用状态管理的库，换句话说，Redux就是用来处理和管理应用的状态/数据。

## 实现简单的Redux

先来写一个简单的"redux"把

### 1.渲染状态

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
function renderApp(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}
renderApp(appState);
```

### 2.提高数据修改的门槛

- 状态不应该是全局的，也不应该哪个方法里直接可以更改（操作危险）
- 一旦数据可以任意修改，所有对共享状态的操作都是不可预料的
- 模块之间需要共享数据和数据可能被任意修改导致不可预料的结果之间有矛盾
- 所以所有对数据的操作修改必须通过`dispatch`函数

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
function renderApp(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}
function dispatch(action) {
    switch (action.type) {
        case 'UPDATE_TITLE_COLOR':
            appState.title.color=action.color;    
            break;    
        case 'UPDATE_CONTENT_CONTENT':
            appState.content.text=action.text;
            break;
        default:
            break;    
    }
}
dispatch({type:'UPDATE_TITLE_COLOR',color:'purple'});
dispatch({type:'UPDATE_CONTENT_CONTENT',text:'新标题'});

renderApp(appState);
```

### 3.分装仓库

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
function renderApp(appState) {
    renderTitle(appState.title);
    renderContent(appState.content);
}


function createStore(reducer) {
    let state;
    function getState() {
        return state;
    }

    function dispatch(action) { 
        state=reducer(state,action);
    }
    dispatch({});
    return {
        getState,
        dispatch
    }

}
let initState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
// 用户自己定义的规则，我们叫它reducer，也就是所谓的管理员
// reducer要有两个参数， 要根据老的状态和新传递的动作算出新的状态
// 如果想获取默认状态，有一种方式，就是调用reducer，让每一个规则都不匹配将默认值返回
// 在reducer中，reducer是一个纯函数，每次需要返回一个新的状态
let reducer=function (state=initState,action) {
    switch (action.type) {
        case 'UPDATE_TITLE_COLOR':
            return {...state,title: {...state.title,color:action.color}};
        case 'UPDATE_CONTENT_CONTENT':
        return {...state,content: {...state.content,text:action.text}};    
            break;
        default:
            return state;    
    }
}
let store=createStore(reducer);
renderApp(store.getState());
setTimeout(function () {
    store.dispatch({type:'UPDATE_TITLE_COLOR',color:'purple'});
    store.dispatch({type:'UPDATE_CONTENT_CONTENT',text:'新标题'});
    renderApp(store.getState());
},2000);
```

### 4.监控数据变化

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
    let listeners=[];
    function getState() {
        return state;
    }
	// 发布订阅模式，先将render方法订阅好，每次dispatch时都调用订阅好的方法
    function dispatch(action) { // 发布
        state=reducer(state,action);
        listeners.forEach(l=>l());
    }

    function subscribe(listener) { // 订阅
        listeners.push(listener);
        return () => {
            // 再次调用时 移除监听函数
            listeners = listeners.filter(item => item!=listener);
            console.log(listeners);
        }
    }
    dispatch({});
    return {
        getState,
        dispatch,
        subscribe
    }

}
let initState={
    title: {color: 'red',text: '标题'},
    content:{color:'green',text:'内容'}
}
let reducer=function (state=initState,action) {
    switch (action.type) {
        case 'UPDATE_TITLE_COLOR':
            return {...state,title: {...state.title,color:action.color}};
        case 'UPDATE_CONTENT_CONTENT':
        return {...state,content: {...state.content,text:action.text}};    
            break;
        default:
            return state;    
    }
}
let store=createStore(reducer);
render();
let unsubscribe = store.subscribe(render);
setTimeout(function () {
    store.dispatch({type:'UPDATE_TITLE_COLOR',color:'purple'});
    unsubscribe();
    store.dispatch({type:'UPDATE_CONTENT_CONTENT',text:'新标题'});
},2000);
```

### 基本概念

![redux](../images/redux.png)

#### Store

Redux的核心是一个`store` ，就是保存数据的地方，可以看出是一个容器，整个应用就只能有一个store。Redux提供`createStore()`函数来生成store。

```javascript
import { createStore } from 'redux';
const store = createStore(fn);
```

#### State

store 某个节点对应的数据集合就是state。`state` 是被托管的数据，也就是每次触发监听事件，我们要操作的数据。可以通过`store.getState()`获得。

Redux 规定，一个state对应一个View。State相同，则View相同。

#### Action

State 的变化，会导致 View 的变化。但是，用户接触不到 State，只能接触到 View。所以，State 的变化必须是 View 导致的。Action 就是 View 发出的通知，表示 State 应该要发生变化了。

Action 是一个对象。其中的`type`属性是必须的，表示 Action 的名称。其他属性可以自由设置。

```javascript
{ type: types.ADD_TODO , text: '读书' }
```

####　Actor Creator

action creator 顾名思义就是用来创建 action 的，action creator 只简单的返回 action。

```javascript
const ADD_TODO = 'ADD_TODO';
let actions = {
    addTodo(todo){
        return { type: ADD_TODO, todo}
    }
}
```

#### store.dispath(action)

store.dispatch()是 View 发出 Action 的唯一方法。store.dispatch接受一个 Action 对象作为参数，将它发送出去

```javascript
let store=createStore(reducer);
store.dispatch({type:'ADD_TODO',text:'读书'});
```

结合 Action Creator，这段代码可以改写如下。

```javascript
 store.dispatch(actions.addTodo(e.target.value))
```

### Redux 核心API

#### createStore

使用方法

> let store=createStore(reducer);

- reducer

  在Redux中，负责响应`action`并修改数据的角色就是`reducer`。 reducer要有两个参数 ，要根据老的状态和新传递的动作算出新的状态。`reducer`本质上是一个纯函数，每次需要返回一个新的状态对象。

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

- subscribe(listener)

- combineReducers