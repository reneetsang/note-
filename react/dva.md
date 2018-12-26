## dva

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
	//这里可以定义子reducer,它可以而且只能由它来修改状态
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
	// 这个对象里放的是副作用，异步的，放的是generator
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
            //当浏览器路径发生变化的时候会执行此回调函数并传入location，我们结构出pathname
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

