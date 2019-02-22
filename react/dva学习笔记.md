## dva

般来说，可以分为主要的三个部分，`models`、`services` 和 `views`。其中，`views`负责页面上的展示，这个不做赘述；`services`里面主要写一些请求后台接口的方法；`models`是其中最重要的概念，这里存放了各种数据，并对数据进行相应的交互。

### models层

model是一个对象，提供了注册model方法app.model({})，取消注册方法app.unmodel(namespace)，需要特别注意的是： 取消 model 注册，清理 reducers, effects 和 subscriptions。subscription 如果没有返回 unlisten 函数，使用 app.unmodel 会给予警告。model里面包括以下五部分：namespace、state、reducers、effects、subscriptions，缺一不可。注意，这里也需要从service层导入相应的方法。

- namespace 命名空间，它就是以前的combineReducers里面的key
- state 相当于原生`React`中的`state`状态，用于存放数据的初始值。
- reducers 一个对象，用于存放能够改变`view`的`action`，里面放react-redux里的dispatch函数，我们触发同步的action，然后经过reducers里对应的函数处理，然后更新store。这里不应该做数据的处理，只是用来`return state`，从而改变`view`层的展示。
- effects 对象，称为副作用。用于和后台交互，是处理异步数据逻辑的地方。当我们在开发的时候如果有异步的操作，会先出发effects里的函数，然后拿到结果以后再出发reducers里的同步函数对store进行CRUD
- subscriptions 对象，订阅监听，比如监听路由，进入页面如何如何，就可以在这里处理。相当于原生`React`中的`componentWillMount`方法。就比如上述代码，监听/project路由，进入该路由页面后，将发起`getAllProjects aciton`，获取页面数据。

```react
import React,{Component} from 'react'
import dva,{connect} from 'dva';
import { combineReducers } from 'redux';
let app =dva();

// combineReducers({
// 	counter
// })
// 状态state是合并后的状态树
// {
// 	counter:{number:0}
// }
app.model({
	// 命名空间，它就是以前的combineReducers里面的key
	namespace:'counter',
	state:{
		current:0,
		highest:0
	},
	//这里可以定义子reducer,它可以而且只能由它来修改状态。用于处理同步操作，唯一可以修改 state 的地方。由 action 触发。
	reducer:{
		// 这个名字是由意义的，如果派发一个counter/add，就会执行此reducer
		// reducer有两个参数，state、action
		add(state,action){
			let newCurrent=state.current+1;
			return {
				...state,
				current:newCurrent,
				highest:newCurrent>state.highest?newCurrent:state.highest
			}
		},
		minus(state,action){
			return {...state, current:state.current-1}
		}
	},
	// 这个对象里放的是副作用，异步的，放的是generator。用于处理异步操作和业务逻辑，不直接修改 state。
	// effects=redux-saga/effects
	effects:{
		*add(action,{call,put}){
			// call表示调用一个异步任务
			yield call(delay,1000);
			// 在model里派发动作的话是不需要加counter（namespace前缀）的
			yield put({type:'minus'})

		}
	},
    //订阅 比如在这里我们监听当URL切换到/todos的时候，当切过来的时候后台接口异步加载数据
    subscriptions:{
        //history操作历史，dispatch派发动作
        setup({history,dispatch}){
            //当浏览器路径发生变化的时候会执行此回调函数并传入location，我们解构出pathname
            history.listen(({pathname})=>{
                if(pathname=='/'){}
            })
        }
    }
})

function delay(ms){
	return new Promise((resolve,reject)=>{
		setTimeout(resolve,ms)
	})
}

// function Counter(props),number,add 是解构出来，而且在函数组件里没有this，类才有
// highest,current来自state.counter里的，add是来自actions里的add
function Counter({highest,current,add}){
	return(
		<div>
			<p>{highest}</p>
			<p>{current}</p>
			{/* onClick={()=>dispatch({type:'counter/add'})}点击加一，要派发对象， 要派发动作要加上命名空间前缀，表示counter这个model的add动作*/}
			{/* 当派发动作的时候，会执行reducers和effects里的add */}
			<button onClick={add}>+</button>
		</div>
	)
}

// 把mapStateToProps和actions通过connect传给组件使用
let mapStateToProps=state=>state.counter;
let actions={
	add(){
		//经过connect后，相当于dispatch({type:'counter/add'})
		return {type:'counter/add'}
	}
}
let ConnectedCounter=connect(mapStateToProps,actions)(Counter)

//定义路由，参数是一个对象，可以解构出history、app，会返回一个组件
app.router((history,app)=>{

})
```

### service层

`Service`层主要负责存放请求后台接口的方法。这里的`request`封装了`fetch`函数，返回的是一个`promise`对象。`request`中传入两个参数，第一个是`url`是请求地址，第二个`options`是请求的参数，看情况传入，比如说这里传入了`method`和`headers`。