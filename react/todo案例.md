##todo案例

src->component

	-Head.js

	-Footer.js

	-Body.js

src->static

	-less (要在webpack配置less)

	  -todo.less

src->store

src->index.js

### src->static->todo.less

```less
.panel{
    margin:20px auto;width:500px;
    
    .panel-title{
        .margin-bottom:10px;
        .count{color:#9d0800; font-weight:bold;}
    }
    input{outline:none;}
}

.list-group-item{
    position:relative;
    input{ vertical-align:top;}
    span{
        margin-left:10px;
        &.complete{text-decoration:line-through;}
    }
    a{ display:none; position:absolete; right:10px; top:50%; margin-top:-10px; width:20px; height:20px; line-height:20px; text-align:center;}
    &:hover{
        a{display:block;}
    }
}
```

### src->index.js

```react
import React from 'react';
import ReactDOM,{render} from 'react-dom';
import {Provider} from 'react-redux';
import store from './store';

import 'bootstrap/dist/css/bootstrap.css'
import './static/less/todo.less'

import Head from './component/Head'
import Body from './component/Body'
import Footer from './component/Footer'

render(<Provider store={store}>
    <main className='panel panel-default'>
        <Head></Head>
        <Body></Body>
        <Footer></Footer>
	</main>
</Provider>,root)
```

### src->component->Head.js

```react
import React from 'react';
import ReactDOM,{render} from 'react-dom';
import {connect} from 'react-redux';
import actions from '../store/actions'

class Head extends React.Component{
    constructor(props){
        super(props)
    }
    
    render(){
        return <div className='panel-heading'>
            <h3 className='panel-title'>
                任务列表[当前未完成任务数<span className='count'>0</span>]
            </h3>
            <input type='text' className='form-control' placeholder='请输入任务' onKeyUp={this.keyUP} />
        </div>
    }
    
    // 向redux中追加一条新的任务
    keyUP=ev=>{
        if(ev.keyCode==13){
            let value=ev.target.value.trim();// 获得文本框内容，去除收尾空格
            
        }
    }
}
export default connect(state=>({...state.todo}),actions.todo)(Head);
```

### src->component->Body.js

```react
import React from 'react';
import ReactDOM,{render} from 'react-dom';
import {connect} from 'react-redux';
import actions from '../store/actions'

class Body extends React.Component{
    constructor(props){
        super(props)
    }
    
    render(){
        return <div className='panel-body'>
            <ul className='list-group'>
            	<li className='list-group-item'>
                	<inpit type='checkbox' name='todo'/>
                    <span>吃饭</span>
                    <a hredf='javascript:;' className='btn-danger'>删</a>
                </li>
            </ul>
        </div>
    }
}

export default connect(state=>({...state.todo}),actions.todo)(Body);
```

### src->component->Footer.js

```react
import React from 'react';
import ReactDOM,{render} from 'react-dom';
import {connect} from 'react-redux';
import actions from '../store/actions'

class Footer extends React.Component{
    constructor(props){
        super(props)
    }
    
    render(){
        return <div className='panel-footer'>
            <nav className='nav nav-pills'>
            	<li className='presentation active'><a href='javascript:;'>全部</a></li>
                <li className='presentation'><a href='javascript:;'>已完成</a></li>
                <li className='presentation'><a href='javascript:;'>未完成</a></li>
            </nav>
        </div>
    }
}
export default connect(state=>({...state.todo}),actions.todo)(Footer);
```

### src->store->index.js

```react
import {createStore} from 'redux';
import reducer from './reducer';

let store=createStore(reducer);
export default store;
```



### src->store->action-types.js

```react
export const TODO_ADD='TODO_ADD';
```



### src->store->actions->index.js

```react
import todo from '../todo';

let action={
    todo
}
export default action;
```



### src->store->actions->todo.js

```react
import * as TYPE from '../action-types';

let todo={
    add(){}
}
export default todo;
```



### src->store->reducers->index.js

```react
import {combineReducers} from 'redux';
import todo from './todo';

let reducer=combineReducers({
    todo
})
export default reducer;
```

### src->store->reducers->todo.js

```react
import * as TYPE from '../action-types';

export default function todo(state={
    data:[],
    flag:'all'
},acion){
    switch(action.type){
            
    }
    return state
}
```

