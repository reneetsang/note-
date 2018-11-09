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

安装antd less less-loader moment prop-types qs react-redux react-router-dom redux redux-logger redux-promise redux-thunk axios blueimp-md5 react-swipe swipe-js-iso react-transition-group

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

            // less
          {
              test: /\.less$/, use:['style-loader','css-loader', 'less-loader' ]
          },
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



