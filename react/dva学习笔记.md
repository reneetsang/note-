## dva

般来说，可以分为主要的三个部分，`models`、`services` 和 `views`。其中，`views`负责页面上的展示，这个不做赘述；`services`里面主要写一些请求后台接口的方法；`models`是其中最重要的概念，这里存放了各种数据，并对数据进行相应的交互。

### models层

model是一个对象，提供了注册model方法app.model({})，取消注册方法app.unmodel(namespace)，需要特别注意的是： 取消 model 注册，清理 reducers, effects 和 subscriptions。subscription 如果没有返回 unlisten 函数，使用 app.unmodel 会给予警告。model里面包括以下五部分：namespace、state、reducers、effects、subscriptions，缺一不可。注意，这里也需要从service层导入相应的方法。

- namespace 命名空间，它就是以前的combineReducers里面的key

- state 相当于原生`React`中的`state`状态，用于存放数据的初始值。

- reducers 一个对象，用于存放能够改变`view`的`action`，里面放react-redux里的dispatch函数，我们触发同步的action，然后经过reducers里对应的函数处理，然后更新store。这里不应该做数据的处理，只是用来`return state`，从而改变`view`层的展示。

- effects 对象，称为副作用。用于和后台交互，是处理异步数据逻辑的地方。当我们在开发的时候如果有异步的操作，会先出发effects里的函数，然后拿到结果以后再出发reducers里的同步函数对store进行CRUD

- subscriptions 对象，订阅监听，比如监听路由，进入页面如何如何，就可以在这里处理。相当于原生`React`中的`componentWillMount`方法。就比如上述代码，监听/project路由，进入该路由页面后，将发起`getAllProjects aciton`，获取页面数据。

  query、update 前面的 * 号，表示这个方法是一个 Generator函数

  subscriptions：用于添加一个监听器

  effects：异步执行 dispatch，用于发起异步请求 

  - call 是调用执行一个函数 (调用service里面的方法)

  - put 则是相当于 dispatch 执行一个 action

  - select 则可以用来访问其它 model

    ```javascript
    const num = yield select(state => state.num) //这里就获取到了当前state中的数据num
    //方式二： const num = yield select(({num}) =>num)
    //方式三： const num = yield select(_ =>_.num)
    ```

  reducers：同步请求，主要用来改变state

  

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



model

- Namespace : ‘userDate’ // 相当于整个store的state上的命名空间  例如 state.userDate

- State: 初始化state 相当于 每个命名空间上的初始化state  

  State:{

  Name:’123’, 

  Age:19

  }

  对应着store上的

  state.userDate:{

  Name:’123’, 

  Age:19

  }

- subscriptions // key:value格式的对象,value是函数,这个其实是当store的dispatch触发时,会自动触发subscriptions 内的函数,subscriptions 相当于发布订阅模式中的订阅事件

- effects // redux-saga处理函数 使用dispatch action:{type:xxx,payload:{}}触发 副作用 key:value形式的对象,但是里面都是genenator函数,专门处理异步请求的,所有的副作用都在这里面处理,除了异步请求,其他诸如 new Date()等等这些操作都放在effects中, 在effects中,我们不会更改状态,需要更改状态时,我们将dispatch新的action,交给reducer去处理,并且改变store中的state.userDate

- reducers 处理同步操作,里面的函数全部都是纯函数,不会改变传入的值,不会有副作用,输入相同时,无论什么情况下,输出也想同,他的返回值是新的state.userDate

services

Services是专门用来处理数据的模块,每个文件里面都是导出一个个方法(一般是异步请求的api方法),专门提供给 models里面的effects使用

### service层

`Service`层主要负责存放请求后台接口的方法。这里的`request`封装了`fetch`函数，返回的是一个`promise`对象。`request`中传入两个参数，第一个是`url`是请求地址，第二个`options`是请求的参数，看情况传入，比如说这里传入了`method`和`headers`。