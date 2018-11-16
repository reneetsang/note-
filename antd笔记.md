1.$ yarn add antd

2.快速使用

1. 导入antd：需要使用那些组件，就导入那些组件

2. 导入antd的样式：我们自己建立一个css样式表，在样式表中基于@import导入antd.css

   ```css
   /* antd.css */
   @import "~antd/dist/antd.css";
   ```

3. antd提供的组件都是英文国际化的，需要中文显示，我们导入汉化模块



组件模板component-redux

```react
import React from 'react';
import {connect} from 'react-redux'

class Home extends React.Component{
    constructor(props,context){
        super(props,context)
    }

    render(){
        return <div>
            
        </div>
    }
}

export default connect ()(Home)
```



reducer模板

```react
import * as TYPES from '../action-types';

let INIT_STATE={}
export default function person(state=INIT_STATE,action){
    state=JSON.parse(JSON.stringify(state));
    switch(action.type){
        
    }
    return state
}
```

action模板

```react
import * as TYPES from '../action-types';

let course ={

}
export default course;
```

action中的index模板

```react
import {createStore,applyMiddleware} from 'redux';
import reduxLogger from 'redux-logger';
import reduxThunk from 'redux-thunk';
import reduxPromise from 'redux-promise';
import reducer from './reducer/index';

let store=createStore(reducer,applyMiddleware(reduxLogger,reduxThunk,reduxPromise));
export default store;
```





课堂

首先eject

安装antd less less-loader moment prop-types qs readux-redux react-router-dom redux redux-logger redux-promise redux-thunk axios blueimp-md5 react-swipe swipe-js-iso react-transition-group

课堂后台server

express express-session body-parser qs



rem

```less
// rem
html{
    font-size: 100px; /*750:1rem=100px*/
}
#root{
    margin: 0 auto;
    max-width: 750px;
}
```

```js
//    <!-- 把rem计算放到body头部，目的是为了保证css导入后才执行这段js(不写在index中而是写在页面中，就是为了保证这段程序加载速度快一些，没必要等到合并的JS加载，先把样式处理了) -->      
;(function anonymous(){
        function computed(){
          let HTML=document.documentElement,
              winW=HTML.clientWidth,
              desW=750;
          if(winW>=desW){
            HTML.style.fontSize='100px';
            return;
          }
          HTML.style.fontSize=`${winW/750*100}px`    
        }
        computed();
        window.addEventListener('resize',computed,false);
      })()
```

导航条下拉用 react-transition-group组件



webpack less

webpack.config.dev.js 160行

```js
//less
{
    test:/\.(css|less)$/,
        use:[
            ...
            加上去
            {loader:require.resolve('less-loader')}; 
        ]    
}
```

webpack.config.prod.js 169行 208

```javascript
//less
{
    test:/\.(css|less)$/,
    loader:ExtractTextPlugin.extract(
    	Object.assign(
        	{
                ...
                use:[
                	...
                	{loader:require.resolve('less-loader')}
                ]
            }
        )
    )    
}
```



## 项目遇到问题

### 路由校验:比如验证有没有登陆

路由的校验和渲染必须是同步的，因为这样在异步没有完成之前，根本不知道渲染谁。async/await是异步，发现没返回东西会报错。

```react
//路由的校验和渲染必须是同步的，因为这样在异步没有完成之前，根本不知道渲染谁，async/await是异步，发现没返回东西
<Route path='/person/info' render={async()=>{
    // 在info中做权限校验，是否登陆成功
    let result=await checkLogin();
    if(parseFloat(result.code)){
        return <Info/>;
    }
    return <Tip/>;
}}/>
```

正确：

```react
    constructor(props,context){
        super(props,context);

        this.state={
            isLogin:false
        }
    }

    // 验证是否登陆
    async componentWillMount(){
        let result =await checkLogin(),
            isLogin=parseFloat(result.code)===0?true:false;
        this.setState({isLogin})    
    }
    
    render(){
        return <section>
            <Switch>
                {/* 路由的校验和渲染必须是同步的，因为这样在异步没有完成之前，根本不知道渲染谁，async/await是异步，发现没返回东西
                <Route path='/person/info' render={async()=>{
                    // 在info中做权限校验，是否登陆成功
                    let result=await checkLogin();
                    if(parseFloat(result.code)){
                        return <Info/>;
                    }
                    return <Tip/>;
                }}/> */}
                <Route path='/person/info' render={()=>{
                    if(this.state.isLogin){
                        return <Info/>;
                    }
                    return <Tip/>;
                }}/>
             
            </Switch>
        </section>
    }
```

### 基于render返回的组件不是受路由管控的组件

如下是没有用的

```react
<Route path='/person/info' render={()=>{
        // 基于render返回的组件不是受路由管控的组件
        if(this.state.isLogin){
            return <Info/>;
        }
        return <Tip/>;
    }}/>
```

	在TIP和INFO组件里要用withRouter

###  真实项目中，完成数据绑定，我们可以按照以下方案处理：

**方案一**：第一次加载组件之前或者之后（WillMount/DidMount），发送AJAX请求，等待数据请求成功后，把请求回来的数据更新组件内部的状态信息（第一次加载的是空数据，第二次更新的时候加载真实的数据）从而重新渲染组件，展示真实的内容

**弊端**：在路由切换的时候，当前组件很有可能需要重新加载（组件完成了从页面移除到再次展示的过程，这样需要从constructor从头加载组件），这样就导致，只要路由切换到这个组件，都需要重新发送一次AJAX请求，对于一些不是随时更新的组件，这样处理会增加HTTP请求的次数，增重服务器的处理压力

**方案二**：每一次加载组件，我们首先验证redux中是否存储了展示的信息，如果有，直接从redux获取即可，如果没有，发送一个dispatch派发，在派发的action creator中基于AJAX获取数据，把获取的数据传递给reducer，把信息存储到redux中，redux中的信息更改，那么用到它的组件也会重新渲染。

如果有数据请求的思路->刚开始加载组件判断有没有数据，如果有数据就把数据拿到显示。但性能不好，就是每一次当前组件重新渲染（例如每一次路由切换），就要从服务器发请求，服务器压力大，可以把数据存储在redux容器中。

dispatch=>reducer=>改变容器中的状态（初始值baseInfo:null）=>通知所有状态信息的组件重新渲染

每一次加载组件：验证baseInfo的值，如果没有信息，基于dispatch派发一个任务（获取服务器端的数据，发给reducer，并且修改容器中的baseInfo，组件重新渲染）；如果有信息，直接渲染组件，而不重新再重新派发任务（也就是不再重新发送ajax请求）

**弊端**：某些特定的案例中会存在一些问题，需要额外处理，例如（在个人中心，A用户登录成功，我们进入个人信息页面，首先会把A的信息存储到redux中，这样只要进入到信息页，展示的都是A的信息，不会重新从服务器获取最新的信息，即使A的信息已经改变，或者是登录的用户已经变成B，都不会改变），这种情况，我们需要在一些其他操作的时候（例如：重新登录、修改用户信息、退出登录等操作），都需要redux中存储的个人信息更新才可以！

原本

```react
import React from 'react';
import {connect} from 'react-redux'
import {withRouter} from 'react-router-dom'
import {Button} from 'antd'
import {exitLogin,queryInfo} from '../../api/person'

class Info extends React.Component{
    constructor(props,context){
        super(props,context);
        this.state={
            baseInfo:null
        }
    }

    async componentDidMount(){
        let result=queryInfo();
        if(parseFloat(result.code)===0){
            this.setState({baseInfo:result.data})
        }
    }

    render(){
        if(!this.state.baseInfo){
            return ''
        }
        let {name,email,phone}=this.state.baseInfo
        return <div className='personBaseInfo'>

            <p>
                <span>用户名：</span>
                <span>{name}</span>
            </p>
            <p>
                <span>邮箱：</span>
                <span>{email}</span>
            </p>
            <p>
                <span>电话：</span>
                <span>{phone}</span>
            </p>
            <Button type='danger' onClick={async (ev)=>{
                await exitLogin();
                this.props.history.push('/person')
            }}>退出登陆</Button>
        </div>
    }
}

export default withRouter( connect ()(Info))
```

修改：

action-types.js

```javascript
export const PERSON_QUERY_BASE='PERSON_QUERY_BASE';
```

action->person.js

```javascript
import * as TYPES from '../action-types';
import {queryInfo} from '../../api/person';

let person ={
    queryBaseInfo(){
        return{
            type:TYPES.PERSON_QUERY_BASE,
            payload:queryInfo()
        }
    }
}
export default person;
```

reducer->person.js

```javascript
import * as TYPES from '../action-types';

let INIT_STATE={
    baseInfo:null
}
export default function person(state=INIT_STATE,action){
    state=JSON.parse(JSON.stringify(state));
    let payload={};
    switch(action.type){
        case TYPES.PERSON_QUERY_BASE:
            payload=action.payload;
            parseFloat(payload.code)===0?state.baseInfo=payload.data:null;
            break;
    }
    return state
}
```

routes->person->Info.js

```react
import React from 'react';
import {connect} from 'react-redux';
import {withRouter} from 'react-router-dom';
import {Button} from 'antd';
import {exitLogin,queryInfo} from '../../api/person';
import action from '../../store/action'

class Info extends React.Component{
    constructor(props,context){
        super(props,context);
    }

    componentWillMount(){
        // baseInfo是state的数据 queryBaseInfo是action里的方法
        let {baseInfo,queryBaseInfo}=this.props;
        // 如果没数据就派发一次方法获取数据
        !baseInfo?queryBaseInfo():null;
    }

    render(){
        let {baseInfo}=this.props;
        if(!baseInfo) return '';
        let{ name,email,phone}=baseInfo;
        return <div className='personBaseInfo'>
            <p>
                <span>用户名：</span>
                <span>{name}</span>
            </p>
            <p>
                <span>邮箱：</span>
                <span>{email}</span>
            </p>
            <p>
                <span>电话：</span>
                <span>{phone}</span>
            </p>
            <Button type='danger' onClick={async (ev)=>{
                await exitLogin();
                this.props.history.push('/person')
            }}>退出登陆</Button>
        </div>
    }
}

export default withRouter( connect (state=>({...state.person},action.person))(Info))
```

### Expected an assignment or function call and instead saw an expression 报错

这个错误提示的很诡异，我是用如下代码提示的这个错误 index-1 <0 ? index = 0:index = index - 1; 这是一个逗号表达式，但是JSLInt认为这里不应该用表达式，而必须是一个函数，所以，如果非常在乎这个错误，就改为if else 语句吧

### 路由切换跳转页面，组件没有重新渲染

之前讲过，当路由切换的时候，对应的组件会重新的渲染，但是渲染也要分情况

1. 之前渲染其他组件的时候，把当前组件彻底从页面中移除了，再次渲染当前组件，走的是第一次挂载的流程（也就是一切从头开始）
2. 如果当前组件之前没有彻底在页面中移除（本组件内容的子组件在切换），每一次走的是更新的流程，不是重新挂载的流程

```react
// 更新后走这个（就是重新又调取会走这个方法）
async componentWillReceiveProps(){
    let result =await checkLogin(),
        isLogin=parseFloat(result.code)===0?true:false;
    this.setState({isLogin})  
}
```

###　切换帐号，登陆、注册成功后，要把redux中的baseInfo改成最新登录人的值

### 关于样式

描述：最后webpack会把所有板块的CSS合并在一起，如果编写样式的时候命名以及一些处理不够规范，很容易导致样式之间的冲突

解决：使用LESS/SASS预编译语言，每一个板块最外层的样式类名保证唯一性（命令规则：板块名称+BOX等修饰词），把当前板块下的内容都嵌套在这个里面，需要公用的样式写在common中即可，有一些js/weboack板块自动进行了板块区分，目的是为了保证CSS不冲突

###  取消不必要的高阶处理

真实项目中，并不是所有的组件都和redux有关系，所以对于那些和redux没有关系的组件，我们不要再connect高阶处理了（同理对于不需要使用路由中属性的一些组件，也没有必要withRouter...）；也就是尽可能少使用高阶组件，因为高阶组件都是利用柯理化函数思想，形成闭包嵌套，这样导致很多栈内存不销毁，影响性能！