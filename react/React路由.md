## React路由

- 不同路径渲染不同的组件
- 有两种实现方式
  - HashRouter：利用hash实现路由切换
  - BrowserRouter：实现h5 API实现路由的切换

### React-Router-dom的核心组件

- Router
  - Router是一个外层，最后render的是它的子组件，不渲染具体业务组件。
  - 分为HashRouter(通过改变hash)、BrowserRouter(通过改变url)、MemoryRouter
  - Router负责选取哪种方式作为单页应用的方案hash或browser或其他的，把HashRouter换成BrowserRouter，代码可以继续运行。
  - Router的props中有一个history的对象，history是对window.history的封装，history的负责管理与浏览器历史记录的交互和哪种方式的单页应用。history会作为childContext里的一个属性传下去。
- Route
  - 负责渲染具体的业务组件，负责匹配url和对应的组件
  - 有三种渲染的组件的方式：component(对应的组件)、render(是一个函数，函数里渲染组件)、children(无论哪种路由都会渲染)
  - Route的props有一个exact属性。如果是true，匹配时到path结束，要和location.pathname准确匹配。
- Switch
  - 匹配到一个Route子组件就返回不再继续匹配其他组件。
- Link
  - 跳转路由时的组件，调用history.push把改变url，类似A标签。
- Rediect



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
import React,{Component} from 'react';
import {Provider} from './context';
export default class HashRouter extends Component{
    state={
        location: {
            pathname:window.location.hash?window.location.hash.slice(1):'/'
        }
    }
    componentDidMount() {
        //默认hash没有的时候跳转到/
        window.location.hash=window.location.hash||'/';
        //监听hash值变化
        window.addEventListener('hashchange',() => {
            this.setState({
                location: {
                ...this.state.location,
                pathname:window.location.hash?window.location.hash.slice(1):'/'
            }});
        });
    }
    render() {
        let value={
            location:this.state.location
        }
        return (
            <Provider value={value}>
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
        let {path,component: Component}=this.props;
        return (
            <Consumer>
                {
                    value => {
                        let {pathname}=value.location;
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

### 正则匹配路径

#### src/react-router-dom/Route.js

```react
import React,{Component} from 'react';
import {Consumer} from './context';
import pathToRegexp from 'path-to-regexp';
export default class Route extends Component{
    render() {
        let {path,component: Component}=this.props;
+        let regexp=pathToRegexp(path,[],{end:false});
        return (
            <Consumer>
                {
                    value => {
                        let {pathname}=value.location;
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

### exact 精确匹配

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
+        let {path,component: Component,exact=false}=this.props;
+        let regexp=pathToRegexp(path,[],{end:exact});
        return (
            <Consumer>
                {
                    value => {
                        let {pathname}=value.location;
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
            <Consumer>
                {
                    value => {
                        let {history: {push}}=value;
                        return (
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
import {Consumer} from './context';
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
						let children=this.props.children;
						for (let i=0;i<children.length;i++){
							let child=children[i];
							//path的默认值为/ exact默认值为false,非精确匹配 /user/1
							let {path="/",exact=false}=child.props;//:id
							let reg=pathToRegexp(path,[],{end: exact});
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

