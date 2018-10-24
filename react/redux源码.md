redux.js

```react
/**
 * createStore：创建redux容器的
 *  @PARAMS
 *    reducer:函数
 *  @RETURN
 *    store:{
 *      getState,
 *      dispatch,
 *      subscribe
 *    }     
 */

 function createStore(reducer){
    // 创建一个store，state用来存储管理的状态信息，listenAry用来存储事件池中的方法 
    // state不用设置初始值，因为第一次dispatch执行reducer，state没值，走的是reducer中赋值的默认值信息，我们自己会在创建容器的时候就把dispatch执行一次    
    let state,
        listenAry=[];
    // dispatch：基于dispatch实现任务派发
    function dispatch(ation){
        // 1.执行reducer，修改容器中的状态信息（接收reducer的返回值，把返回的信息替换原有的state）,注意的是：我们是把返回值全部替换state，所以要求reducer中在修改状态之前，要先把原始的状态信息克隆一份，再进行单个的修改，如case TYPE.XXX:state={...state,n:100}; 
        state=reducer(state,action)
        // 2.容器中状态信息经过reducer修改后，通知事件池中的方法依次执行
        for(let i=0;i<listenAry.length;i++){
            let item=listenAry[i];
            if(typeof item==='function'){
                item()
            }else{
                listenAry.splice(i,1);
                i--;
            }
        }
    }
    dispatch({type:'_INIT_DEFAULT_STATE'});// 目的为了在创建容器的时候，执行一次dispatch，把reducer的默认状态信息值给redux的state状态

    // getState：获取容器中的状态信息
    function getState(){
        // 1.我们需要保证，返回的状态信息，不能和容器中的state是同一个堆内存（否则外面获取状态信息后，直接就可以修改容器中的状态，这不符合dispatch->reducer才能修改状态的规范）
        //{...state} 这样不行，这是浅克隆
        return JSON.parse(JSON.stringify(state)); // 深度克隆对象（先把对象转换为字符串，在转化为对象）
    }

    // subcribe：向事件池中追加方法
    function subscribe(fn){
        // 1.向容器中追加方法，先要重复验证
        let isExit=listenAry.includes(fn);
        !isExit?listenAry.push(fn):null;
        // 2.返回一个方法：执行返回的方法会把当前绑定的方法在事件池中移除掉
        return function unsubscribe(){
            let index=listenAry.indexOf(fn);
            // listenAry.splice(index,1); 这样子可能会引发数组塌陷问题
            listenAry[index]=null;

        }
    }

    return{
        dispatch,
        getStatem,
        subscribe
    }

 }
// 如果getState直接返回state会有bug，如下可以修改状态
// let obj=store.getState();
// obj.vote.n=1000;

 // 用法
//  let reducer=(state={},action)=>{
//      // state：原有状态信息
//      // action：派发任务时候传递的行为对象
//      switch(action.type){
//          // 根据type执行不同的state修改操作
//          case TYPE.XXX:
//             state={...state,n:100}; //
//      }
//      return state;// 返回的state会替换原有state
//  }
//  let store=createStore(reducer); // create的时候把reducer传递进来，但是此时reducer并没有执行，只有dispatch的时候才执行，通过执行reducer修改容器中的状态
//  // store.dispatch({type:'xxx',...})

//  let unsubscribe=store.subcribe(fn);
//  unsubscribe();

/**
 * combineReducers：Redecer合并的方法
 *   @PARAMS
 *     对象，对象中包含了每一个板块对应的reducer {xxx:function reducer...}
 *   @RETURN
 *     返回的是一个新的reducer函数（把这个值赋值给create-store）
 * 
 * 特殊处理：合并REDUCER之后，redux容器中的state也变为以对应对象管理的模式=>{xxx:{}...}
 */
function combineReducers(reducers){
    // reducers：传递进来的reducer对象集合
    // {
    //     vote:function vote(state={n:0,m:0},action){...return state},
    //     personal:function personal(state={baseInfo=''},action){...return state}
    // }

    // 这里的state就是总容器的state
    return function reducer(state={},action){
        // dispatch派发执行的时候，执行的是返回reducer，这里也要返回一个最终的state对象替换原有的state，而且这个state中需要包含每个模块的状态信息 {vote:...,personal:...}
        // 我们所谓的reducer合并，其实就是dispatch派发的时候，把每一个模块的reducer都单独执行一遍，把每个模块返回的状态最后汇总在一起，替换容器中的状态信息
        let newState={}
        for(let key in reducers){
            if(!reducers.hasOwnProperty(key)) break;
            // reducers[key] 每个模块单独的reducer
            // state[key]:当前模块在redux容器中存储的状态信息
            // 返回值是当前模块最新的状态，把它再放到new-state中
            newState[key]=reducers[key](state[key],action);
        }
        return newState;
    }
}


```

react-redux.js

```react
import React from 'react';
import PropTypes from 'prop-types'

/**
 * Provider：当前项目的“根”组件
 *   1.接手通过属性传递进来的store，把store挂载到上下文中，这样当前项目中任何一个组件中，想要使用redux中的store，直接通过上下文获取即可
 *   2.在组件的render中，把传递给provider的子元素渲染
 */
class Provider extends React.Component{
    // 设置上下文类型
    static childContextTypes={
        store:PropTypes.object
    }
    // 设置上下文信息值
    getChildContext(){
        return{
            store:this.props.store
        }
    }

    constructor(props,context){
        super(props,context)
    }

    render(){
        return this.props.children;
    }
}

/**
 * connect:高阶组件（基于高阶函数：柯理化函数）创建的组件就是高阶组件
 *   @PARAMS
 *     mapStateToProps：回调函数，把redux中的部分状态信息（方法返回的内容）挂载到指定组件的属性上
 *       ```
 *         function mapStateToProps(state){
 *           // state:Redux容器中的状态信息
 *           return {};// return对象中有啥，就把啥挂载到属性上         
 *         }
 *       ```
 * 
 *     mapDispatchToProps：回调函数，把一些需要派发的任务方法也挂载到组件的属性上
 *       ```
 *         function mapDispatchToProps(dispatch){
 *           // dispatch：store中的dispatch
 *           return{
 *             init(){
 *               dispatch({...})
 *             }
 *           } // return啥就把啥挂载到属性上（返回的方法中有执行dispatch派发任务的操作）        
 *     }
 * 
 *   @RETURN
 *     返回一个新的函数connectHOT
 * ====
 *   connectHOT
 *     @PARAMS
 *       传递进来的是要操作的组件，我们需要吧指定的属性和方法都挂载到当前组件的属性上
 * 
 *     @RETURN
 *       返回一个新的组件，在新的组件Proxy（代理组件），在代理组件中，我们要获取provider在上下文中存储的store，紧接着获取store中的state和dispatch，把mapStateToProps和mapDispatchToProps回调函数执行，接收返回的结果，再把这些结果挂载到Component这个要操作组件的属性上
 */ 

 // 大函数执行形成一个不销毁的栈内存，两个回调函数也保存下来了，以后再栈内存里的任何方法就可以用这两个函数了
function connect(mapStateToProps,mapDispatchToProps){
    return function connectHOT(Component){
        return class Proxy extends React.Component{
            // 获取上下文中的store
            static contextTypes={
                store:PropTypes.object
            }

            //获取store中的state和dispatch，把传递的两个回调函数执行，接收返回的结果
            constructor(props,context){
                super(props,context);
                this.state=this.queryMountProps();
            }

            // 渲染Component组件，并且把获取的信息（状态、方法）挂载到组件属性上
            render(){
                // 把this.state对象中的每一项传给Component
                return <Component {...this.state} />
            }

            // 基于Redux中的subscribe向事件池中追加一个方法，当容器中状态改变，我们需要重新获取最新的状态信息，并且重新把Component，把最新的状态信息通过属性传递给Component
            componentDidMount(){
                this.context.store.subscribe(()=>{
                    this.setState(this.queryMountProps());
                })

            }

            // 执行这个方法，就可以从redux中获取最新的信息，基于回调函数筛选，返回的是需要挂载到组件属性上的信息
            queryMountProps=()=>{
                let {store}=this.context,
                    state=store.getState();
                let propsState=typeof mapStateToProps==='function'?mapStateToProps(state):{};
                let propsDispatch=typeof mapDispatchToProps==='function'?mapDispatchToProps(store.dispatch):{};
                return{
                    ...propsState,
                    ...propsDispatch
                }
            }


        }
    }
}

export { 
    Provider
}
```

