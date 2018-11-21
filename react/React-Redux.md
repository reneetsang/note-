##React-Redux

Redux流程中，每个组建中要把状态映射到组建上，还要自己订阅和更新，很麻烦。所以React-Redux诞生了，可以实现把redux映射到组件里，还可以自动更新。

React-Redux是把redux进一步封装，让redux操作更简单。

- store文件夹中的内容和redux一样
- 只是在组件调取使用的时候，可以优化一些步骤

React-Redux主要有两个组件：

1. Provider根组件

   当前整个项目都在Provider组件下，作用就是把创建的store可以供内部任何后代组件使用（基于上下文完成的）

   Provider组件中只允许出现一个子元素

   把创建的store基于属性传递给Provider(这样后代组件都可以使用这个store)

2. connect高阶组件

### Provider根组件

React-Redux 提供`Provider`组件，用来接受store，再经过他的手通过context api传递给所有的子组件，套在子组件外面（有点像路由的用法），可以让所有子孙组件拿到`state`。

```react
import React from 'react';
import {render} from 'react-dom';
import Cart from  './components/cart'
import {Provider} from 'react-redux';
import store from './store';
render(<Provider store={store}>
    <Cart></Cart>
</Provider>,window.root);
```

### connect() 高阶组件

React-Redux 提供`connect`方法，是个高阶函数，connect实现的是仓库和组件的连接 。

相对于传统的组件，做的优化步骤有：

1. connect方法调用后返回的是新组件。所以导出的不再是我们创建的组件，而是基于connect构造后的高阶组件

   > export default connect([mapStateToProps],[mapDispatchToProps])([自己创建的组件名字])

2. react-redux帮我们做了一件非常重要的事情，以前我们需要自己基于subscribe向事件池追加方法，以达到容器状态信息改变的时候，执行我们追加的方法。但是现在不用了，react-redux帮我们做了这件事：“所有用到redux容器状态信息的组件，都会向事件池追加一个方法，当状态信息改变，通知方法执行，把最新的状态信息作为属性传递给组件，组件的属性值改变了，组件也会重新渲染”

原本没使用react-redux的组件：

```react
import React, { Component } from 'react';
import store from '../store';
import actions from '../store/actions/counter';

export default class Counter extends Component {
    constructor() {
        super();
        this.state = { number: store.getState().c.number }
    }
    componentDidMount() {
        this.unsub = store.subscribe(() => {
            this.setState({ number: store.getState().c.number })
        });
    }
    componentWillUnmount() {
        this.unsub();
    }
    render() {
        return <div>
            计数器 {this.state.number}
            <button onClick={() => {
                store.dispatch(actions.add(2))
            }}>+</button>
            <button onClick={() => {
                store.dispatch(actions.minus(1));
            }}>-</button>
        </div>
    }
}

```

使用react-redux：

```react
import React, { Component } from 'react';
import actions from '../store/actions/counter';
import {connect} from 'react-redux';

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

// // 把REDUX容器中的状态信息遍历，赋值给当前组件的属性（state）
let mapStateToProps = (state)=>{
    // state就是redux容器中的状态信息
	// 我们返回的是啥，就把它挂载到当前组件的属性上（redux存储很多信息状态，我们想用啥就返回啥即可）
    return {n:state.c.number}
}
// 把redux中的dispatch派发行为遍历，也赋值给组件的属性（ActionCreator）
let mapDispatchToProps = (dispatch) =>{
	// dispatch:就是store中存储的dispatch方法
	// 返回的是啥，就相当于把它挂载到当前组件的属性上（一般我们挂载一些方法，这些方法中完成了dispatch派发任务操作）
    return {
        add(n){dispatch(actions.add(n))},
        minus(n){dispatch(actions.minus(n))}
    }
}
export default connect(mapStateToProps,mapDispatchToProps)(Counter);
```

其中上面导出的connect可优化为：

因为react-redux帮我们做了一件事，把action-creator中编写的方法（返回action对象的方法），自动构建成dispatch派发任务的方法，也就是mapDispatchToProps这种格式

```react
export default connect(state=>({n:state.c.number}),action)(Counter);
```

