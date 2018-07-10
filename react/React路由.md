## React路由

- 不同路径渲染不同的组件
- 有两种实现方式
  - HashRouter：利用hash实现路由切换
  - BrowserRouter：实现h5 API实现路由的切换

### 手写路由，实现基本功能

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
// React16.3
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

