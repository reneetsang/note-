## React路由

- 不同路径渲染不同的组件
- 有两种实现方式
  - HashRouter：利用hash实现路由切换，通过监听#后面hash值的变化来监听页面
  - BrowserRouter：实现h5 API实现路由的切换

### React-Router-dom的核心组件

- Router
  - Router是一个外层，最后render的是它的子组件，不渲染具体业务组件。
  - 分为HashRouter(通过改变hash)、BrowserRouter(通过改变url)、MemoryRouter
  - Router负责选取哪种方式作为单页应用的方案hash或browser或其他的，把HashRouter换成BrowserRouter，代码可以继续运行。
  - Router的props中有一个history的对象，history是对window.history的封装，history的负责管理与浏览器历史记录的交互和哪种方式的单页应用。history会作为childContext里的一个属性传下去。

- Route

  - `Route` 是用于声明路由映射到应用程序的组件层。 

  - 负责渲染具体的业务组件，负责匹配url和对应的组件
  - 有三种渲染的组件的方式：component(对应的组件)、render(是一个函数，函数里渲染组件)、children(无论哪种路由都会渲染)
  - Route的props有一个exact属性。如果是true，匹配时到path结束，要和location.pathname准确匹配。

- Switch

  - 匹配到一个Route子组件就返回不再继续匹配其他组件。

- Link

  - 我们应用需要在各个页面间切换。如果使用锚点元素（就是）实现，在每次点击时页面将被重新加载。React Router提供了<Link>组件用来避免这种状况的发生。当你点击<Link>时，URL会更新，组件会被重新渲染，但是页面不会重新加载。 

  - 跳转路由时的组件，调用history.push把改变url，类似A标签。

- Rediect

  - 在应用中 `<Redirect>` 可以设置重定向到其他 route 而不改变旧的 URL。 
  - 重定向，如果都匹配不上就返回这个组件

### this.props

如果通过Router渲染的组件，那么会有三个属性

> <http://localhost:3000/#/user?name=zfpx#top>

```javascript
{
    "match": {
        "path": "/user/:id",   //匹配路径
        "url": "/user/1",      //地址栏中的url
        "isExact": true,       //是否精确匹配
        "params": {"id": "1"}  // 路径参数对象
    },
    "location": {
        "pathname": "/user/1",  //路径名
        "search": "?name=zfpx", //查询字符串
        "hash": "#top",         //hash值
        "state":undefined       //可以记录这个路径从哪里跳转过来的
    },
    "history": {
        "length": 6,            //历史长度
        "action": "POP",        //动作
        "location": {           //当前应用所处的位置
            "pathname": "/user/1",
            "search": "?name=zfpx",
            "hash": "#top",
            "state":undefined  //location可以拥有与之相关的状态。这是一些固定的数据，并且不存在于URL之中
        },
        "go":f go(n),//是一个强大的方法，并包含了goForward与goBack的功能。传入负数则退后，传入正数则向前
        "goBack":f goBack(),//返回一层页面。实际上是将history的索引值减1
        "goForward":f goForward(),//与goBack相对。向前一层页面
        "listen":f listen(listener),//采用观察者模式，在location改变时，history会发出通知
        "push":f push(path,state),//方法使能你跳转到新的location
        "replace":f replace(path,state)//replace方法与push相似，但它并非添加location，而是替换当前索引上的位置,重定向时要使用replace,
        "createHref":f createHref(location)
    }
}
```



### Path-to-RegExp

把一个路径转换成正则表达式 

```react
var pathToRegexp = require('path-to-regexp')
// 该对象上挂载了 3 个方法
pathToRegexp(path, keys, options)
pathToRegexp.parse(path)
pathToRegexp.compile(path)
```

- pathToRegexp(path, keys, options)`
  - path: 自定义的匹配路由
  - keys: 通过分组获得的相关值，路径参数等。
  - options: 基本匹配选项 
    - sensitive: 大小写精确匹配（default: false，`不是`）
    - strict: 末尾斜杠是否精确匹配（default: false，`不是`）
    - end: 是否是全局匹配，相当于，带不带 `/g` 标识符。(defualt: true，带 `/g`，精确匹配) 
    - delimiter: 设置重复参数的分割符，默认为 `/`。对于 pathname 匹配时，使用默认就好。

```javascript
var keys = []
var re = pathToRegexp('/foo/:bar', keys)
// re = /^\/foo\/([^\/]+?)\/?$/i
// keys = [{ name: 'bar', prefix: '/', delimiter: '/', optional: false, repeat: false, pattern: '[^\\/]+?' }]
```

```javascript
let pathToRegexp=require('path-to-regexp');
let keys=[];
//end = false 不必须结束
//end = true 必须结束
let reg1=pathToRegexp('/user/:id/:name',keys,{end:true});
let names=keys.map(key => key.name);//[id,name]
let result='/user/1/zfpx'.match(reg1); //[ '/user/1/zfpx', '1', 'zfpx', index: 0, input: '/user/1/zfpx' ]
let params=names.reduce((memo,name,idx) => {
	memo[name]=result[idx+1];
	return memo;
},{});
console.log(params);// parmas = {id:1,name:'zfpx'}
```



### 一步步开始手写路由，先实现基本功能

#### src/index.js

```react
import React from 'react';
import ReactDOM from 'react-dom';
import {HashRouter as Router,Route} from './react-router-dom';
import Home from './components/Home';
import User from './components/User';
import Profile from './components/Profile';
ReactDOM.render(
    <Router>
        <div>
          <Route path="/" component={Home} />
          <Route path="/user" component={User} />
          <Route path="/user/:id" component={User} />
          <Route path="/profile" component={Profile}/>
        </div>
    </Router>
,document.getElementById('root'));
```

#### src/react-router-dom/index.js 

暴露方法

```react
import HashRouter from './HashRouter';
import Route from './Route';

export {
    HashRouter,
    Route
}
```

#### src/react-router-dom/context.js 

自带API，下次再写源码

```react
import React from 'react';
// React16.3以上版本才可以用
let {Provider,Consumer}=React.createContext();
export {Provider,Consumer};
```

#### src/react-router-dom/HashRouter.js 

```react
// 把数据（地址等）在router中传给route，用context
import React,{Component} from 'react';
import {Provider} from './context';

// 每当地址栏里锚点发生改变的时候，都需要重新匹配
export default class HashRouter extends Component{
    state={
        location: {
            // #/ #/user 要把#截掉
            pathname:window.location.hash?window.location.hash.slice(1):'/'  //如果没有哈希值就给个'/'默认的路径
        }
    }
    componentDidMount() {
        //默认hash没有的时候跳转到/
        window.location.hash=window.location.hash||'/';
        //监听hash值变化
        window.addEventListener('hashchange',() => {
            // hash变化后setState，改变location，重新走render
            this.setState({
                location: {
                ...this.state.location,// 里面还有很多属性，不要写死，先取回来解构
                pathname:window.location.hash?window.location.hash.slice(1):'/' // 取新的锚点赋给pathname
            }});
        });
    }
    render() {
        let value={
            location:this.state.location
        }
        return (
            // 提供传递数据
            <Provider value={value}>
                {/* 作为Provider的子组件，provider里的组件就可以拿到value 对这个例子而言，这里面就是那三个route */}
                {this.props.children}
            </Provider>
        )
    }
}
```

#### src/react-router-dom/Route.js 

```react
import React,{Component} from 'react';
import {Consumer} from './context';
export default class Route extends Component{
    render() {
        return (
            // Consumer里放一个函数
            <Consumer>
                {
                    // 参数value就是Provider中的value
                    value => {
                        // 电脑输入的地址
                        let {location:{pathname}}=value; // /user
                        // 跟Route中的属性比较，先解构一下
                        // component:Component起个别名，重命名一下，因为类组件需要大写
                        let {path='/',component:Component,exact=false}=this.props;
                        if (path == pathname) {
                            return <Component/>
                        } else {
                            return null;
                        }
                    }
                }
            </Consumer>
        )
    }
}
```

### exact 精确匹配

```react
<Route path='/roster'/>
// 当路径名为'/'时, path不匹配
// 当路径名为'/roster'或'/roster/2'时, path匹配
// 当你只想匹配'/roster'时，你需要使用"exact"参数来精确匹配
// 则路由仅匹配'/roster'而不会匹配'/roster/2'
<Route exact path='/roster'/>
```

#### src/index.js

```react
ReactDOM.render(
    <Router>
        <div>
+            <Route exact={true} path="/" component={Home} />
            <Route path="/user" component={User} />
          <Route path="/profile" component={Profile}/>
        </div>
    </Router>
,document.getElementById('root'));
```

#### src/react-router-dom/Route.js

```react
export default class Route extends Component{
    render() {
        return (
            <Consumer>
                {
                    value => {
                        let {pathname}=value.location;
+        			   let {path='/',component:Component,exact=false}=this.props;
+        			   let regexp=pathToRegexp(path,[],{end:exact});
                        if (regexp.test(pathname)) {
                            return <Component/>
                        } else {
                            return null;
                        }
                    }
                }
            </Consumer>
        )
    }
}
```

### 正则匹配路径

路径参数，可支持匹配/user/:id

          <Route path="/user" component={User} />
          <Route path="/user/:id" component={User} />
#### src/react-router-dom/Route.js

```react
import React,{Component} from 'react';
import {Consumer} from './context';
import pathToRegexp from 'path-to-regexp';
export default class Route extends Component{
    render() {
        return (
            <Consumer>
                {
                    value=>{
                        let {location:{pathname}}=value; // /user
                        let {path='/',component:Component,exact=false}=this.props;
                        // 把path编译成正则
                        let keys=[]
                        let regexp=pathToRegexp(path,keys,{end:exact})
                        let result=pathname.math(regexp);
                        let props={
                            location:value.location,
                            history:value.history
                        }
                        // 匹配上就渲染
                        if(result){
                            // 这样子Component就可以使用location和history了
                            return <Component {...props}/>
                        }else{
                            return null;
                        }
                    }
                }
            </Consumer>
        )
    }
}
```

### Link

#### src/index.js 

写一个简单导航

```react
import 'bootstrap/dist/css/bootstrap.css'
ReactDOM.render(
    <Router>
        <div>
            <nav className="navbar navbar-inverse">
                <div className="container-fluid">
                    <div className="navbar-header">
                        <a className="navbar-brand" >学生管理系统</a>
                    </div>
                    <div id="navbar" className="collapse navbar-collapse">
                        <ul className="nav navbar-nav">
                            <li><Link to="/">Home</Link></li>
                            <li><Link to="/user">User</Link></li>
                            <li><Link to="/profile">Profile</Link></li>
                        </ul>
                    </div>
                </div>
            </nav>
            <div className="container">
                <Route exact={true} path="/" component={Home} />
                <Route path="/user" component={User} />
                <Route path="/profile" component={Profile}/>
            </div>
        </div>
    </Router>
,document.getElementById('root'));
```

#### src/react-router-dom/HashRouter.js 

增加push方法

```react
    render() {
        let value={
            location: this.state.location,
+            history: {
+                // 参数to是要跳转的路径
+                push(to) {
+                    window.location.hash=to;
+                }
+            }
        }
        return (
            <Provider value={value}>
                {this.props.children}
            </Provider>
        )
    }
```

#### src/react-router-dom/Link.js 

```react
import React,{Component} from 'react';
import {Consumer} from './context';
export default class Link extends Component{
    render() {
        return (
            // 返回一个A标签
            // <Link to="/user/list">用户列表</Link> 标签是Link组件的children            			   // 用户列表是Link组件的children 可以{this.props.children}获取
            <Consumer>
                {
                    value => {
                        // push方法是从Provider的value拿到的 value.history.push
                        // 解构一下，下面不用写那么长
                        let {history: {push}}=value;
                        return (
                            // {this.props.children}
                            <a onClick={()=>push(this.props.to)}>{this.props.children}</a>
                        )
                    }
                }
            </Consumer>
        )
    }
}
```

### Rediect&Switch

#### src/index.js

在导航中增加Switch和Redirect组件

```react
<div className="container">
+    <Switch>
        <Route exact={true} path="/" component={Home} />
        <Route path="/user" component={User} />
        <Route path="/profile" component={Profile} />
 +      <Redirect to="/"/>
 +   </Switch>
</div>
```

#### src/react-router-dom/Redirect.js 

```react
import React,{Component} from 'react';
import {Consumer} from './context'; // 要用history，在上下文中
export default class Redirect extends Component{
	render() {
		return (
			<Consumer>
				{
					value => {
						value.history.push(this.props.to);
						return null;
					}
				}
		   </Consumer>
	   )
   }
}
```

#### src/react-router-dom/Switch.js 

```react
// Switch是Route的一个父组件
import React,{Component} from 'react'
import {Consumer} from './context';
import pathToRegexp from 'path-to-regexp';
export default class Switch extends Component{
	render() {
		return (
			<Consumer>
				{
					value => {
						let {location: {pathname}}=value;
                          // 拿到子组件
						let children=this.props.children;
						for (let i=0;i<children.length;i++){
                           	  // 拿到每一个子组件开始对比
							let child=children[i];
							//path的默认值为/ exact默认值为false,非精确匹配 /user/1也可以匹配上
                               // 从属性对象中拿到path和exact，并给上默认值 
							let {path="/",exact=false}=child.props;//:id
	                          // 转正则
							let reg=pathToRegexp(path,[],{end: exact});
                               // 如果匹配上就返回这个子组件，不再循环
							if (reg.test(pathname)) {
								return child;
							}
						}
						return null;
					}
				}
		   </Consumer>
	   )
   }
}
```

### 页面跳转

#### src/components/User.js

```react
import React,{Component} from 'react';
import {Link,Route} from '../react-router-dom';
import UserAdd from './UserAdd';
import UserList from './UserList';
import UserDetail from './UserDetail';
export default class User extends Component{
	componentWillMount() {
		console.log('User componentWillMount');
	}
	componentDidMount() {
		console.log('User componentDidMount');
	}
	render() {
		return (
			<div className="row">
				<div className="col-md-2">
					<ul className="nav nav-stacked">
						<li><Link to="/user/add">新增用户</Link></li>
						<li><Link to="/user/list">用户列表</Link></li>
					</ul>
				</div>
				<div className="col-md-10">
					<Route path="/user/add" component={UserAdd} />
					<Route path="/user/list" component={UserList} />
					<Route path="/user/detail/:id" component={UserDetail}/>
				</div>
			</div>
		)
	}
}
```

#### src/components/UserAdd.js

```react
import React,{Component} from 'react';
export default class UserAdd extends Component{
    handleSubmit=(event) => {
        event.preventDefault();
        let username=this.username.value;
        let email=this.email.value;
        let user={username,email};
        // 从缓存中读取用户列表字符串
        let usersStr=localStorage.getItem('users');
        // 将字符串转成对象数组
        let users=usersStr? JSON.parse(usersStr):[];
        // 给每个对象增加一个ID号
        user.id = users.length>0? users[users.length-1].id+1:1;
        // 向此数组中加入新的对象
        users.push(user);
        // localStorage只能放字符串，需要把数组从新数列化，再把数组保存到缓存中
        localStorage.setItem('users',JSON.stringify(users));
        console.log(this.props); // 每个组件都有一个对象，里面是location、match、history那些东西
        // 是在HashRouter.js里的传给route.js
        // 再通过history对象的push方法跳转到用户列表页面
        this.props.history.push('/user/list');
    }
    render() {
        return (
            <div className="row">
                <div className="col-md-12">
                    <form onSubmit={this.handleSubmit}>
                        <div className="form-group">
                            <label htmlFor="username">用户名</label>
                            {/* ref引用的是真实DOM元素 input框的真实DOM元素挂到当前组件内部属性上去了，this.name指向input的真实DOM元素 */}
                            {/* ref=>this.name=ref这个函数在虚拟DOM元素渲染成真DOM插入页面后才执行 */}
                            <input type="text" className="form-control" ref={input=>this.username = input}/>
                        </div>
                        <div className="form-group">
                            <label htmlFor="email">邮箱 </label>
                            <input type="email" className="form-control" ref={input=>this.email = input}/>
                        </div>
                        <div className="form-group">
                            <input type="submit" className="btn btn-primary"/>
                        </div>
                    </form>
                </div>
            </div>
        )
    }
}
```

#### src/components/UserList.js

```react
import React,{Component} from 'react';
import {Link} from '../react-router-dom';
export default class UserList extends Component{
    state={
        users:[]
    }
    componentDidMount() {
        let usersStr=localStorage.getItem('users');
        let users=usersStr? JSON.parse(usersStr):[];
        this.setState({users});
    }
    render() {
        return (
            <div className="row">
                <div className="col-md-12">
                    <ul className="list-group">
                        {
                            this.state.users.map(user => (
                                <li className="list-group-item" key={user.id}>
                                    <Link to={`/user/detail/${user.id}`}>{user.username}</Link>
                                </li>
                            ))
                        }
                    </ul>
                </div>
            </div>
        )
    }
}
```

