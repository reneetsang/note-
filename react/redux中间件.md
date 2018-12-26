## redux中间件

安装 

```
yarn add redux-logger redux-thunk redux-promise
```

我们改写了，dispatch方法实现了在更改状态时打印前后的状态,但是这种方案并不好。所以我们可以采用中间的方式。中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能。

```react
let store = createStore(reducer);
let dispatch = store.dispatch;
store.dispatch = function (action) {
  console.log(store.getState().number);
  dispatch(action);
  console.log(store.getState().number)
};
export default store;
```

### applyMiddleware

redux 提供了官方加载中间件的函数 applyMiddleware

```react
function applyMiddleware(middleware) {
	return function (createStore) {
		// reducers就是合并后的reducers
		return function (reducers) {
			let store=createStore(reducers);//这就是原始老的仓库 dispatch 就是原始的dispatch
			let dispatch;//dispatch方法指向新的dispatch方法，在下面重新赋值了
			// 把老仓库传给logger，执行以后middleware2相当于logger第二层，为了获取第三层还要再调用一次
			let middleware2=middleware({
				getState:
				 store.getState,
				dispatch:action=>dispatch(action) // 这样写才会调用新的dispatch方法
			});//调用第一把第一层去掉 得到logger第二层
			// 把原生老的dispatch方法传进去（logger第二层next）
			dispatch=middleware2(store.dispatch);//再调用第二次把第二层去掉 得到logger第三层
			return {
				...store, //老的store 把里面旧的dispatch覆盖
				dispatch
			}
		}
	}
}
```

### redux-logger

redux-logger:能够在控制台清晰的展示出当前redux操作的流程和信息（原有状态、派发信息、修改后的状态信息）

```react
//let logger=store=>next=>action=>{} 
// 每一层都有作用
// 第一层就是为了传入store得到老的状态 
function logger1(store) {
	// 第二层为了进行调用，执行下一个next 就是老的dispatch方法（在下面applyMiddleware应用时传入的 ）
	return function (next) {
		// 这个方法就是dispatch，用它覆盖掉applyMiddleware中的store原始的dispatch方法
		return function (action) {
			console.log(`老值`,store.getState().counter1.number);
			next(action); // 原始老的dispatch方法 相当于调用reducer把状态给改了，再取状态得到新的状态
			console.log(`新值`,store.getState().counter1.number);
		}
	}
```

### redux-thunk

redux-thunk:用来处理异步的dispatch派发

#### 用法

```javascript
//增加客户信息，payload={id,name}
create(payload){
    //=> thunk中间件的语法:在指定执行派发任务的时候，等待3000ms后再派发
    // 原理：
    return dispatch=>{
        //=>dispatch都传递给我们了，我们想什么时候派发，自己搞定即可
        setTimeout(()=>{
            dispatch({
                type:TYPES.CUSTOM_CREATE,
                payload
            })
        },3000)
    }
}
```

#### 原理

判断是否函数，函数就执行，不是就dispatch

```react
// 把dispatch,getState从store解构出来
function thunk1({dispatch,getState}) {
	return function (next) {
		return function (action) {
			//如果发过来的action是一个函数，则让他执行
			if (typeof action == 'function') {
				action(dispatch,getState);
	        //如果说不是一个函数，那么直接传给老的store.dispatch			
			} else {
				next(action);
			}
		}
	} 
}
```

### redux-promise

redux-promise:在dispatch派发时支持promise操作

#### 语法

```javascript
// promise中间件的语法
create(payload){
    return {
        type:TYPES.CUSTOM_CREATE,
        //=>传递给reducer的payload需要等待promise成功，把成功的结果传递过去
        payload:new Promise(resolve=>{
            setTimeout(()=>{
                resolve(payload)
            },3000)
        })            
    }
}
```

#### 原理

这里主要是通过判断对象是否拥有then属性，并且then属性是一个方法，那就默认是要给promise对象

```react
function promise1({dispatch,getState}) {
	return function (next) {
		return function (action) {
			// 如果是promise
			if (action.then && typeof action.then == 'function') {
				action.then(dispatch);
			} else if (action.payload&& action.payload.then && typeof action.payload.then == 'function') {
				action.payload.then(payload => {
					dispatch({...action,payload});
				},payload => {
					dispatch({...action,payload});
				});
			} else {
				// 如果不是promise
				next(action);
			}
		}
	}
}
```

### redux-saga

redux-saga也是实现异步的方式，派发一个动作，发现匹配上了就执行函数

store/index.js

```react
import {creatStore,applyMiddleware} from 'redux';
import reducer from './reducer';
import createSagaMiddleware from 'redux-saga';
import {rootSaga} from '../saga'

// 执行createSagaMiddleware可以得到中间件函数
let sagaMiddleware=createSagaMiddleware();
let store=applyMiddleware(sagaMiddleware)(createStore)(reducer);
// 开始执行rootSaga
sagaMiddleware.run(rootSaga,store)
export default store();
```

store/saga.js

takeEvery监听

put派发动作

all让所有监听函数执行，类似promise.all

call表示告诉saga，清帮我执行以下delay函数，并传入1000作为参数

take表示等待一个动作发生

```react
import {takeEvery,put,all} from 'redux-saga/effexts';
import * as types from './store/action-types';
const delay=ms=>new Promise(function(resolve,reject){
    setTimeout(function(){
        resolve();
    },ms)
})

function* add(dispatch,action){
  
    yield delay(1000);//会等resolve成功后再往下走
    //yield call(delay,1000)也可以这么写，call表示告诉saga，清帮我执行以下delay函数，并传入1000作为参数
    
    // put就是指挥saga中间件向仓库派发一个动作
    yield put({type:types.ADD})
}

function* logger(action){
    console.log(action)
}

//saga分为三类 1.rootSaga 2.监听saga 3.worker干活的saga
function* watchLogger(){
    yield takeEvery('*',logger)
}

function* watchAdd(){
    //takeEvery监听派发给仓库的动作，如果派发的动作是types.ADD，就会执行add生成器
    yield takeEvery(types.ADD_ASYNC,add)
    console.log('rootSaga')
}

export function* rootSaga({getState,dispatch}){
    // 是不能两个yield这样写的，要引入all
	// yield watchAdd();
    // yield watchLogger();
    yield all([watchAll(),watchLogger()]) 
}
```

