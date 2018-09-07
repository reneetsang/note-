## redux中间件

我们改写了，dispatch方法实现了在更改状态时打印前后的状态,但是这种方案并不好。所以我们可以采用中间的方式。中间件就是一个函数，对store.dispatch方法进行了改造，在发出 Action 和执行 Reducer 这两步之间，添加了其他功能

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

### redux-logger

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
			next(action); // 原始老的dispatch方法 相当于调用reducer把状态给改了，再取状态得到新的状
			console.log(`新值`,store.getState().counter1.number);
		}
	}
```

### redux-thunk

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

