## 安装

### 脚手架安装

在全局下安装脚手架，创建一个react项目工程

- $ npm install create-react-app -g
- $ create-react-app <project-name>

进入目录，启动项目

- cd <project-name>
- npm start

### 目录

安装完毕，目录如下

![react1](E:\study\笔记\images\react1.png)

#### public

> 存放的是当前项目的HTML页面（单页面应用放一个index.html即可，多页面根据自己需求放置需要的页面）

在react中，所有的逻辑都是在JS中完成的（包括页面解构的创建），如果想给当前的页面带入一些CSS样式或者IMG图片等内容，我们有两种方式：

- 在JS中基于ES6 Module模块规范，使用import导入，这样webpack在编译合并JS的时候，会把导入的资源文件等插入到页面的结构中(**绝对不能**在JS管控的结构中通过相对目录./或者../，导入资源，因为在webpack编译的时候，地址就不在是之前的相对地址了)
- 如果不想在JS中导入（JS中导入的资源最后都会基于webpack编译），我们也可以把资源手动的在HTML中导入，但是HTML最后也要基于webpack编译，导入的地址也**不建议**写相对地址，而是使用`%PUBLIC_URL%`写成绝对地址`<link rel="manifest" href="%PUBLIC_URL%/manifest.json"> `

#### src

> 项目结构中最主要的目录，因为后期所有的JS、路由、组件等都是放到这里（包括需要编写的CSS或者图片等）

- index.js是当前项目的入口文件

#### .gitignore

> git提交时候的忽略提交文件配置项

用webStorm可以忽略提交这个

```
# webStorm
.idea
```

#### package.json

> 当前项目的配置清单

- npm run start/yarn start： 开发环境下，基于webpack编译处理，最后可以预览当前开发的项目成果（在webpack中安装了dev-server插件，基于这个插件会自动创建一个web服务[端口号默认是3000]，webpack会帮我们自动打开浏览器，展示我们的页面，并且能够监听我们的代码改变，如果代码改变了，webpack会自动重新编译，并且刷新浏览器来完成重新渲染）
- npm run build：项目需要部署到服务器上，我们先执行npm run build，基于webpack.config.prop.js把项目整体编译打包（完成后会在项目中生成一个build文件夹，这个文件夹包含了所有编译后的内容，我们把它上传到服务器，可以说是生成环境）而且在服务上进行部署的时候不需要再安装任何模块
- eject：create-react-app脚手架为了让解构目录清晰，把安装的webpack及配置文件都集成在了react-scripts模块中，放到了node_modules中。



但是真实项目中，我们需要在脚手架默认安装的基础上，额外安装一些我们需要的模块，例如：react-router-dom/axios...再比如:less/less-loader

1. 情况一：如果我们安装其他的组件，但是安装成功后不需要修改webpack的配置项，此时我们直接的安装，并且调取使用即可。

2. 情况二：我们安装的插件是基于webpack处理的，也就是需要安装的模块配置到webpack中（重新修改webpack配置项了）

   首先需要把隐藏到node_modules中的配置项暴露到项目中，再去修改对应的配置项即可

   > **$ npm run ejecct**
   >
   > 会提示确认是否执行eject操作，这个操作是不可逆转的，一旦暴露出来的配置，就无法在隐藏回去了
   >
   > 如果当前的项目基于git管理，在执行eject的时候，如果还有没有提交到历史区的内容，需要先提交到历史区，然后再eject才可以，否则报错This git repository has untracked files or uncommitted changes..

   一旦暴露后，项目目录多了两个文件夹：

   #### config:存放的是webpack的配置文件

   - webpack.config.dev.js 开发环境下的配置项(yarn start)
   - webpack.config.prod.js 生产环境下的配置项(yarn build)

   #### scripts:存放的是可执行脚本的JS文件

   - start.js  yarn start执行的就是这个JS
   - build.js  yarun build执行的就是这个JS

   package.json的内容也改了

   我们预览项目的时候，也是先基于webpack编译，把编译后的内容放到浏览器中运行，说一如果项目中使用了less，我们需要修改webpack配置项，在配置项中加入less的编译工作这样后期预览项目，首先基于webpack把less编译为css，然后呈现在页面中

   $ yarn add less less-loader 

   less是开发（看 webpack.config.dev.js）和生产环境（上线 webpack.config.prod.js）下都需要配置的

   $ set HTTPS=true&&npm start 开启HTTPS协议模式（设置环境变量HTTPS的值）

基于脚手架生成工程目录，默认会自动安装react/react-dom/react-scripts，React由两部分组成,分别是：

- react.js 是 React 的核心库
- react-dom.js 是提供与DOM相关的功能,会在window下增加ReactDOM属性,内部比较重要的方法是render,将react元素或者react组件插入到页面中。
- react-scripts集成了webpack需要的内容
  - babel一套
  - css处理一套
  - eslint一套
  - webpack一套
  - 其他的 
  - 没有less/sass东西的处理（项目中如果使用，需要自己额外的安装）

## react&react-dom

#### 渐进式框架

一种最流行的框架设计思想，一般框架中包含很多内容，这样导致框架的体积过于臃肿，拖慢加载的速度。真实项目中，我们使用一个框架，不一定用到所有的功能，此时我们应该把框架的功能进行拆分，用户想什么，让其自己自由组合即可。

#### 全家桶

即渐进式框架N多部分的组合

vue全家桶：vue-cli+vue+vue-router+vuex+axios(fetch)+vue element(vant)

react全家桶:create-react-app+react+react-dom+react-router+redux+react-redux+axios+ant+dva+saga+mobx

#### 1.react

react框架的核心部分，提供了Compoent类可以供我们进行组件开发，提供了钩子函数（生命周期函数：所有的生命周期函数，都是基于回调函数完成的）

#### 2.react-dom

把JSX语法(REACT独有的语法)渲染为真实DOM(能够放到页面中展示的结构都叫做真实DOM)的组件

## JSX语法

### ReactDOM.render

ReactDOM.render([JSX],[CONTAINER],[CALLBACK]):把JSX元素渲染到页面中

- JSX:REACT虚拟元素
- CONTAINER:容器，我们想把元素放到页面中的哪个容器
- CALLBACK:当把内容放到页面中呈现触发的回调函数（能触发这个函数说明元素已经放在页面中了

### JSX

REACT独有的语法，JAVASCRIPT+XML(HTML)

和我们之前自己拼接的HTML字符串类似，都是把HTML结构代码和JS代码或者数据混合在一起了，但是它不是字符串

1. 不建议我们把JSX直接渲染到BODY中，会报错。而是放在自己创建的一个容器中，一般我们都放在一个ID位ROOT的DIV即可
2. 只能出现一个根元素
3. 在JSX中出现的{}是存放JS的，但是要求JS代码执行完成需要有返回结果（JS表达式）
   - 不能直接放一个对象数据类型的值（对象（除了给style赋值）、数组（数组中如果没有对象，都是基本值或者JSX元素，这样是可以的）、函数都不行）
   - 可以是基本类型的值（布尔类型什么都不显示、null和undefined也是JSX元素，代表是空）
   - 循环判断的语句都不支持，但是支持三元运算符
4. 循环数组创建JSX元素（一般都是基于数组的MAP方法完成迭代），需要给创建的元素设置唯一的KEY值（当前本次循环内唯一即可）
5. 给元素设置样式类用的是className而不是class
6. style中不能直接的写样式字符串，需要基于一个样式`对象`来遍历赋值

## 把JSX（虚拟DOM）变为真实DOM

React是如何把JSX元素转换为真实的DOM元素并且添加到页面中？

```react
import React from 'react';
// React.creatElement
import ReactDOM,{render} from 'react-dom'; //从react-dom中导入一个ReactDOM，逗号后面的内容是把ReactDOM这个对象进行结构=>import {render} from 'react-dom'

let styleObj = { background: 'red' }
ReactDOM.render(<h1 id='titleBox' className='title' style={styleObj}>曾洁莹</h1>,document.getElementById('root'));
```

1. 基于BABEL中的语法解析模块（BABEL-PRESET-REACT）把JSX语法编译为React-createElement(...)结构

   ```
   // 上面写的，会编译成如下结构
   React.createElement('h1',{id:'titleBox',className:'title',style:styleObj},'曾洁莹')
   ```

2. 执行React.creactElement(type,props,children)，把传递进来的参数处理成一个对象（虚拟DOM）

   ```
   // 执行React.createElement，生成的对象里有
   type：'h1'
   props:{
       id:'titleBox',
       className:'title',
       style:...,
       children:'曾洁莹' =>存放的事元素中的内容
   }
   ref:null
   key:null
   ...
   _proto_:Object.prototype
   ```

3. ReactDOM.render(JSX语法最后生成的对象，容器)，基于Render方法把生成的对象动态创建为DOM元素，插入到指定的容器中

### 简单原理

```react
// 1.创建一个对象（默认有四个属性：TYPE/PROPS/REF/KEY），最后要把这个对象返回
// 2.根据传递的值修改这个对象
// TYPE=>传递的TYPE
// PROPS 需要做一些处理：大部分传递PROPS中的属性都赋值给对象的PROPS，有一些比较特殊 
//     ->如果是REF或者KEY，我们需要把传递的PROPS中的这两个属性值，给创建对象的两个属性，而传递的PROPS中把这两个值删除掉
//     ->把传递的CHILDREN作为新创建对象的PROPS中的一个属性	
// 
function createElement(type,props,children){
    props=pros||{}；
    // 创建一个对象，设置一些默认属性值
    let obj={
        type:null,
        props:{
            children:''
        },
        ref:null,
        key:null
    };
    // 用传递的TYPE和PROPS覆盖原有的默认值
	// 简单写法 obj={...obj,type,props}; //=>{type:type,props:props}
    obj={...obj,type,props:{...props,children}}
	// 把ref和key提取出来，并且删除props中的属性
	'key' in obj.props?(obj.key=obj.props.key,obj.props.key=undefined):null;
    'ref' in obj.props?(obj.ref=obj.props.ref,obj.props.ref=undefined):null;
    return obj
}
let objJSX=createElement('h1',{id:'titleBox',className:'title',style:{color:'red'}},'曾洁莹')

// render:把创建的对象生成对应的DOM元素，最后插入到页面中
function render(obj,container,callBack){
    let {type,props}=obj||{},
        newElement=document.createElement(type);
    for(let attr in props){
        if(!props.hasOwnProperty(attr)) break; // 不是私有的，说明找到原型上的，直接结束遍历
        if(!props[attr]) continue; // 如果当前属性没有值，直接不处理即可
        let value=props[attr];
        // CLASS-NAME的处理
        if(attr==='className'){
            newElement.setAttribute('class',value);
            continue
        }
        // STYLE处理
        if(attr==='style'){
            // 如果是空字符串不处理
            if(value==='') continue;
            for(let styKey in value){
                if(value.hasOwnProperty(styKey)){
                    newElement['style'][styEle]=value[styKey]
                }
            }
            continue;
        }
        // CHILDREN处理
        if(attr==='children'){
            if(typeof value=='string'){
                let text=document.createTextNode(value);// 创建文本节点
                newElement.appendChild(text);
            }
            continue;
        }
        newElement.setAttribute(attr,value); // 基于SET-ATTRIBUTE可以让设置的属性表现在HTML的结构上
    }
    container.appendChild(newElement);
    callBack&&callBack();
}
render（objJSX,root,()=>{
    console.log('ok')
})
```

进一步优化：含有多标签

```react
// 创建JSX对象
//   参数：至少两个 type/pros,children这个部分可能没有，也可能有多个
//   
function(type,props,...childrens){// 剩下的参数都是childrens数组里
    let ref,key;
    if('ref' in props){}
    if('key' in props){}
    return{
        type,
        props:{
            ...props,
            children:childrens.length<=1?(childrens[0]||''):childrens
        },
        ref,
        key
    }
}


function createElement(type,props,children){ 
    props=pros||{}；
    // 创建一个对象，设置一些默认属性值
    let obj={
        type:null,
        props:{
            children:''
        },
        ref:null,
        key:null
    };
    // 用传递的TYPE和PROPS覆盖原有的默认值
	// 简单写法 obj={...obj,type,props}; //=>{type:type,props:props}
    obj={...obj,type,props:{...props,children}}
	// 把ref和key提取出来，并且删除props中的属性
	'key' in obj.props?(obj.key=obj.props.key,obj.props.key=undefined):null;
    'ref' in obj.props?(obj.ref=obj.props.ref,obj.props.ref=undefined):null;
    return obj
}
let objJSX=createElement('h1',{id:'titleBox',className:'title',style:{color:'red'}},'曾洁莹')

// render:把创建的对象生成对应的DOM元素，最后插入到页面中
function render(obj,container,callBack){
    let {type,props}=obj||{},
        newElement=document.createElement(type);
    for(let attr in props){
        if(!props.hasOwnProperty(attr)) break; // 不是私有的，说明找到原型上的，直接结束遍历
        if(!props[attr]) continue; // 如果当前属性没有值，直接不处理即可
        let value=props[attr];
        // CLASS-NAME的处理
        if(attr==='className'){
            newElement.setAttribute('class',value);
            continue
        }
        // STYLE处理
        if(attr==='style'){
            // 如果是空字符串不处理
            if(value==='') continue;
            for(let styKey in value){
                if(value.hasOwnProperty(styKey)){
                    newElement['style'][styEle]=value[styKey]
                }
            }
            continue;
        }
        // CHILDREN处理
        // 可能是一个值：可能是字符串也能是一个JSX对象
        // 可能是一个数组：数组中的每一项可能是字符串也可能是JSX对象
        
        // 首先把一个值也变为数组，这样后期统一操作数组即可
        if(attr==='children'){
            if(!(value instanceof Array)){
                value=[value]
            }
            value.forEach((item,index)=>{
                // 验证item是什么类型的：如果是字符串就是创建文本节点。如果是对象，我们需要再次执行render方法，把创建的元素放到最开始创建的大盒子中
                if(typeof value=='string'){
                    let text=document.createTextNode(item);// 创建文本节点
                    newElement.appendChild(text);
                }else{
                    render(item,newElement);
                }
            })
            else{
                // 如果只有一个值，我们创建一个文本节点即可
                 if(typeof value=='string'){
                    let text=document.createTextNode(value);// 创建文本节点
                    newElement.appendChild(text);
                }               
            }

            continue;
        }
        newElement.setAttribute(attr,value); // 基于SET-ATTRIBUTE可以让设置的属性表现在HTML的结构上
    }
    container.appendChild(newElement);
    callBack&&callBack();
}
render（objJSX,root,()=>{
    console.log('ok')
})
```

## 组件

### 1、react组件

不管是vue还是react框架，设计之初都是期望我们按照“组件/模块管理”的方式来构建程序，也就是把一个程序划分为一个个的组件来单独处理

**优势**

1. 有助于多人协作开发
2. 我们开发的组件可以被复用
3. ...

react中创建组件有两种方式：

#### 函数声明式组件

基于集成COMPONENT类来创建组件

src->component:一般会在src里面建一个component文件夹，这个文件夹存放的就是开发的组件

##### src->index.js

```react
import React from 'react'
import ReactDOM from 'react-dom';
import Dialog from './component/Dialog'

ReactDOM.render(<div>
     {/* 注释：JSX调取组件，只需要把组件当作一个标签使用即可（单双闭合都可以） */}
	<Dialog con="哈哈"/>
       {/* 属性值不是字符串，我们需要大括号包起来 */} 
      <Dialog con"嘿嘿" lx={1}>
      	<span>1</span>    
        <span>2</span> 
      <Dialog/>  
</div>,root)
```

##### src->component->Dialog.js

```react
// 每一个组件中都要导入REACT，因为需要基于它的CREAT-ELEMENT把JSX解析渲染
import React from 'react'；

//函数声明组件
// 1、函数返回结果，是一个新的JSX（也就是当前组件的JSX结构）
// 2、props变量存储的值是一个对象，包含了调取组件时候传递的属性值（不传递是一个空对象）
export default function Dialog(props){
    let {con,lx=0,children,style}=props,
        title=lx===0?'系统提示':'系统警告'
    
    // children:可能有可能没有，可能只是一个值，也可能是一个数组，可能每一项是一个字符串，也可能是一个对象等（都代表双闭合组件中的子元素）
    return <session style={style}>
    	<h2>{title}</h2>
        <div>{con}</div>
        { /* 把属性中传递的子元素放到组件中的指定位置 */}       
        {children}
        { /* 也可以基于REACT中提供的专门遍历CHILDREN的方法来遍历操作
        */}  
        {React.Children.map(children,item=>itm)}
    </session>
}
```

#### 函数式组件的渲染机制

知识点：CREATE-ELEMENT在处理的时候，遇到一个组件，返回的对象中，TYPE就不再是字符串标签名了，而是一个函数（类），但是属性还是存在PROPS中

```
{
    type:Dialog,
    props:{
        lx:1,
        con:'xxx',
        children:一个值或者一个数组
    }
}
```

render渲染的时候，我们需要做处理，首先判断TYPE类型，如果是字符串，就创建一个元素标签，如果函数或者类，就把函数执行，把PROPS中的每一项（包含CHILDREN）传递给函数

在执行函数的时候，把函数中的RETURN的JSX转换为新的对象（通过CREATE-ELEMENT），把这个函数返回。紧接着RENDER按照以往的渲染方式，创建DOM元素，插入到制定的容器中即可

#### 简单封装Dialog

##### src->index.js

```react
import React from 'react';
import ReactDOM from 'react-dom';
import './static/css/reset.min.css'
import 'bootstrap/dist/css/bootstrap.css'; //我们一般都把程序中的公用放到Index中导入，这样在其他组件中也可以使用了（webpack会把所有的组件都编译到一起，Index是主入口）
// 导入bootstrap，需要导入的是不经过压缩处理的文件，否则无法编译（真实项目中bootstrap已经是过去式，我们后期项目中使用项目都是antd来做）
//这里面有字体图片，路径是用..识别不了会报错，可以放在src-static中
import Dialog from "./component"

ReactDOM.render(<main>
        <Dialog content='renee wow'}></Dialog>
        <Dialog type={2} content='renee wow2'}/>
        <Dialog type='请登录' content={
			<div>
                    <input type='text' class='from-control' placeholder='请输入用户名' />
                    <input type='password' class='from-control' placeholder='请输入密码' />
             </div>
         }>
            <button typeof="button" className='btn btn-success'>登录</button>
            <button typeof="button" className='btn btn-danger'>取消</button>
            
        </Dialog>

    </main>,root)
```

##### src->component->Dialog.js

```react
import React from 'react'

export default function Dialog(props){
    let {type,content,children}=props;
    
    // 类型的处理
    let typeValue=type||'系统提示'
    if(typeof type==="number"){
        switch(type){
            case 0:
                typeValue='系统提示';
                break;
            case 1:
                typeValue='系统警告';
                break;
            case 2:
                typeValue='系统错误';
                break;
        }
    }
    
    return <section className="panel panel-default" style={{width:50%,margin:'10px auto'}}>
        <div className='panel-heading'>
        	<h3 className='panel-title'>{typeValue}</h3>
        </div>
        <div className='panel-body'>
        	{content}
        </div>
        {/* 如果传递了children，就把内容放到尾部 */}
        {
            children?<div className='panel-footer'>
            	{React.Children.map(children,item=>item)}    
            </div>:null
        }
    </section>
}
```

#### 基于继承component类来创建组件

基于create-element把JSX转换为一个对象，当render渲染这个对象的时候，遇到type是一个函数或者类，不是直接创建元素，而是先把方法执行：

1. 如果是函数式申明的组件，就把它当做普通方法执行（方法中的this是undefined），把函数返回的JSX元素（也就是解析后的对象）进行渲染
2. 如果是类声明式的组件，会把当前类NEW执行，创建类的一个实例（当前本次调取的组件就是它的实例），执行constructor之后，会执行this.render()，把render中返回的JSX拿过来渲染，所以类声明式组件，必须有一个render的方法，方法中需要返回一个'JSX元素'

但是不管是哪一种方式，最后都会把解析出来的props属性对象作为实参传递给对应的函数或者类

```react
import React from 'react';
import ReactDOM from 'react-dom';

funciton Sum(props){
    console.log(this) //undefined
}

class Dialog extens React.Component{
    constructor(props){// props,context,updater
         // ES6中的extends继承，一旦使用了constructor，第一行位置必须设置super执行，相当于React.Component.call(this)，也就是call继承，把父类私有的属性继承过来
        // 传props是把执行类创建实例 传递进来的属性，直接挂载到当前类的实例上
        // 如果只写super(),虽然创建实例的时候把属性传递进来了，但是并没有传递父组件，也就是没有把属性挂载到实例上，使用this.props获取的结果是undefined
        // 如果super(props):在继承父类私有的时候，就把传递的属性挂载到子类的实例上，constructor就可以使用this.props了
        // 不写也没问题
        super(props);
        // props：当render渲染并且类执行创建实例的时候，会把之前jsx解析出来的props对象中的信息（可能会有children）传递给参数props=>'调取组件传递的属性’
        console.log(props)    
        
        // this.props 属性集合
        // this.ref ref集合（非受控组件中用到）
        // this.context 上下文
        // this.updater 
        // 这里的this都是当前Dialog类的实例
        console.log(this.props) // super如果不传就是走的是Component函数中默认的值，undefined
    }
    // render挂载在原型上的
    render(){
        return <section>
        	<h3>系统提示</h3>
        </section>
    }
}


ReactDOM.render(<div>
        曾洁莹
    	<Dialog lx={2} con='哈哈' />
    </div>,root);
// render拿到这个对象，先创建第一层的div，属性往里面加，遇到children循环children每一项。
// 如果这一项是个字符串，通过创建文本节点插入。如果是个对象再回头执行render
let obj={
    type:'div',
    props:{
        children:[
            '曾洁莹',
            {
                type:Dialog,
                props:{
                    lx:2
                }
            }
        ]
    }
}


```

```react
class Dialog extens React.Component{
    constructor(){
        super();
        // 即使在constructor中不设置形参props接收属性，执行super的时候也不传这个属性，除了constructor中不能直接使用this.props，其他生命周期函数中都可以使用（也就是执行完成constructor,react已经帮我们把传递的属性接手，并且挂载到实例上了
    }
    componentWillMount(){
        // 第一次渲染前
        console.log(this.props)
    }
    render(){
        console.log(this.props)
        return <section>
        	<h3>系统提示</h3>
        </section>
    }
}
```

#### 总结

创建组件有两种方式“函数式”，“创建类式”

**函数式**

1. 操作非常简单
2. 能实现的功能也很简单，只是简单的调取和返回JSX而已

**创建类式**

1. 操作相对复杂一些，但是也可以实现更为复杂的业务功能
2. 能够使用生命周期函数操作业务
3. 函数式就是可以理解为静态组件（组件中的内容调取的时候就已经固定了，很难再修改），而类这种方法，可以基于组件内部的状态来动态更新渲染的内容
4. ...

### 2、组件中的属性状态管理

React中的组件有两个非常重要的概念：

1. 组件的属性：[只读的]调取组件的时候传递进来的信息
2. 组件的状态：[可读写的]自己在组件中设定和规划的（只有类声明式组件才有状态的管理，函数式组件声明不存在状态的管理）
   - 组件状态类似于vue中的数据驱动：我们数据绑定的时候是基于状态值绑定，当修改组件状态后对应的JSX元素也会跟着重新渲染（差异渲染：只把数据改变的部分重新渲染，基于DOM-DIFF算法完成的）
   - 当代前端框架最重要的核心思想就是：“数据操控试图（试图影响数据）”，让我们告别JQ手动操作DOM的时代，我们以后只需要改变数据，框架会帮我们重新渲染视图，从而减少操作DOM（提高性能，也有助于开发效率）

#### 属性 [只读的]

```react
import React from 'react';
import ReactDOM from 'react-dom';


class Dialog extens React.Component{
    // this.props是只读的，我们无法在方法中修改它的值，但是可以给其设置默认值或者设置一些规则（例如：设置是否必须传递的以及传递值的类型等）
    static defaultProps={
        lx:'系统提示'
    };
    constructor(props){
        super();
    }
    render(){
        // this.props.con='嘿嘿' 报错
        // 组件中的属性，是调取组件的时候（创建类实例的时候）传递给组件的信息，而这部分信息只能是只读的（只能获取，不能修改）=》组件的属性是只读的
        
        // 这个方法都不许让你用
        // Object.defineProperty(this.props,'con',{ 
        //     writable:true
        // })
        // this.props.con='呵呵'
        
        let {lx,con}=this.props
        return <section>
        	<h3>{lx}</h3>
            <div>{con}</div>
        </section>
    }
    componentDidMount(){
        console.log(this.props.lx)
    }
}

ReactDOM.render(<div>
        曾洁莹
    	<Dialog lx='系统提示' con='哈哈' />
    </div>,root);
```

#### props-types 属性校验

props-types是facebook开发的一个插件，基于这个插件我们可以给组件传递的属性设置规则。

设置的规则不会影响页面的渲染，但是会在控制台抛出警告错误

> 需要安装yarn add prop-types

```react
import React from 'react';
import ReactDOM from 'react-dom';
import PropTypes from 'prop-types'

class Dialog extens React.Component{
    // 其实这样写是不符合ES6语法规范的，但是webpack编译的时候，会把它转换为Dialog.defaultProps这种符合规范的语法
    static defaultProps={
        lx:'系统提示'
    };
    static propTypes={
        //con:PropTypes.string, // 传递的内容必须是字符串
        con:PropTypes.string.isRequired // 不仅传递的内容是字符串，并且还必须传递
    }
    
    constructor(props){
        super();
    }
    render(){
        let {lx,con}=this.props
        return <section>
        	<h3>{lx}</h3>
            <div>{con}</div>
        </section>
    }
    componentDidMount(){
        console.log(this.props.lx)
    }
}

ReactDOM.render(<div>
        曾洁莹
    	<Dialog lx='系统提示' con='哈哈' />
    </div>,root);
```

#### 状态 [可读写的]

**写个例子：用函数声明式组件写个时间**

所谓函数式组件是静态组件，和执行普通函数一样，调取一次组件，就把组件中的内容获取到，插入到页面当中，如果不重新调取组件，显示的内容是不会发生任何改变的。
真实项目中，只有调取组件，组件中的内容不会再次改变的情况下，我们才可能使用函数式组件

```react
import React from 'react';
import ReactDOM from 'react-dom';

function Clock(){
    return <section>
    	<h3>当前北京时间为:</h3>
        <div style={{color:'red'}}>{new Date().toLocaleString()}</div>
    </section>
}
setInterval(()=>{
    //每隔一秒重新调取组件，渲染到页面中
    ReactDOM.render(<Clock></Clock>,root)
},1000)
```

用类声明式组件来写

```react
import React from 'react';
import ReactDOM from 'react-dom';

class Clock extends React.Component{    
    constructor(){
        super();
        // 可以理解为，初始化组件的状态（都是对象类型的），要求我们在constructor中需要把后期使用的状态信息全部初始化一下（约定俗成的语法规范）
        this.state={
            time:new Date().toLocaleString()
        }
    }
    
    ComponentDidMount(){
        // React生命周期函数之一：第一次组件渲染完成后触发(我们在这里只需要间隔1000ms把state状态中的time数据改变，这样react会自动帮我们把组件中的部分内容进行重新的渲染)
        setInterval(()=>{
            // react中虽然下面方式可以修改状态，但是并 不会通知react渲染页面，所以不要这样去操作和修改状态
            // this.state.time=new Date().toLocaleString();
            // console.log(this.state.time)
            
            // Component原型上有个setState方法，所以子类实例也可以使用
            // 修改组件的状态
            // 1.修改部分的状态：会用我们传递的状态和初始化的state进行匹配，只把我们传递的属性进行修改，没有传递的依然保留原始的状态信息（部分状态修改）
            // 2.当状态修改完成，会通知react把组件JSX中的部分元素重新进行渲染
            this.setState({
                time:new Date().toLocaleString()
            },()=>{
                //当通知react把需要重新渲染JSX元素渲染完成后，执行的回调操作(类似于生命周期函数中的componentDidUpdate,不过项目中一般使用钩子函数而不是这个回调)
                // 设置回调函数的原因：set-state是异步操作
            })
        },1000)
    }
    
    render(){
        return <section>
            <h3>当前北京时间为:</h3>
            <div style={{color:'red'}}>
                {/*获取组件的状态信息 */}
                {this.state.time}
            </div>
        </section>
    }
}
ReactDOM.render(<Clock></Clock>,root)
```

####　投票案例

##### this问题，JSX中的事件绑定

```react
render(){
    return <button className='btn btn-success' onClick={this.support}>支持</button>
    support(ev){
        //console.log(this) //undefined 不是我们理解的当前操作的元素
        //ev.target:可以通过事件源获取当前的操作元素，但框架主要是数据驱动DOM改变，要尽量少操作DOM，很少使用
    }
}
```

如果能让方法中的this变成当前类的实例就好了，这样可以操作属性和状态等信息

1. 使用bind，不可以用call和aplly，因为call和apply会立马执行，但这个方法麻烦

   ```react
   render(){
       // render中的this是实例
       return <button className='btn btn-success' onClick={this.support.bind(this)}>支持</button>
       support(ev){
   	
       }
   }
   ```

2. 箭头函数 真实项目中最常用的方式，给JSX元素绑定的事件方法一般都是箭头函数，目的为了保证函数中的this还是实例

   ```react
   render(){
       // render中的this是实例
       return <button className='btn btn-success' onClick={this.support}>支持</button>
       support=ev=>{
   		// this:继承上下文
       }
   }
   ```

##### 1、数据驱动思想

```react
import React from 'react';
import PropTypes from 'prop-types'

export default class Vote extends React.Component{
    // 组件传递的属性是只读的，我们为其设置默认值和相关规则
    static defaultProps={ }
    static propTypes={
        title:PropTypes.string.isRequired
    }
    constructor(props){
        super(props);
        this.state={
            n:0,//支持人数
            m:0 //反对人数
        }
    }
    render(){
        let {n,m}=this.state,
            rate=(n+m)===0?0%||((n/(n+m)*100).toFixed(2)+'%');
        return <section className='panel panel-default' style={{width:'60%',margin:'0 auto'}}>
            <div className='panel-heading'>
                <h3 className='panel-title'>
                    {this.props.title}
                </h3>
            </div>
            <div className='panel-body'>
                支持人数:{n}
                <br /><br />
                反对人数:{m}
                <br /><br />
                支持率:{rate}
            </div>
            <div className='panel-footer'>
                {/**/}
                <button className='btn btn-success' onClick={this.support}>支持</button>
                &nbsp;&nbsp;&nbsp;&nbsp;
                <button className='btn btn-danger' onClick={this.against}>反对</button>
            </div>
        </section>
    }
        
    support=ev=>this.setState({n:this.state.n+1});
	against=ev=>this.setState({m:this.state.m+1});
}

```

##### 2、DOM操作思想

refs:是react中专门提供操作DOM来实现需求的方式，它是一个对象，存储了当前组件中所有设置ref属性的元素（元素ref属性值是啥，refs中存储的元素的属性名就是啥）

```react
import React from 'react';
import PropTypes from 'prop-types'

export default class Vote extends React.Component{
    // 组件传递的属性是只读的，我们为其设置默认值和相关规则
    static defaultProps={ }
    static propTypes={
        title:PropTypes.string.isRequired
    }
    constructor(props){
        super(props);
    }
    render(){
        let {n,m}=this.state,
            rate=(n+m)===0?0%||((n/(n+m)*100).toFixed(2)+'%');
        return <section className='panel panel-default' style={{width:'60%',margin:'0 auto'}}>
            <div className='panel-heading'>
                <h3 className='panel-title'>
                    {this.props.title}
                </h3>
            </div>
            <div className='panel-body'>
                {/* 
                  *	ref='spanLeft'
                  * 是在当前实例上挂载一个属性refs(对象)，存储所有ref元素
                  *
                  * ref={x=>this.spanLeft=x}
                  * x代表当前元素，它的意思是，把当前元素直接挂载到实例上，后期需要用到元素，直接this.spanLeft获取即可
                  */}
                支持人数:<span ref={x=>this.spanLeft=x}>0</span>
                <br /><br />
                反对人数:<span ref={x=>this.spanLeft=x}>0</span>
                <br /><br />
                支持率:<span ref={x=>this.spanLeft=x}>0%</span>
            </div>
            <div className='panel-footer'>
                {/**/}
                <button className='btn btn-success' onClick={this.support}>支持</button>
                &nbsp;&nbsp;&nbsp;&nbsp;
                <button className='btn btn-danger' onClick={this.against}>反对</button>
            </div>
        </section>
    }
        
    support=ev=>{
        console.log(this.refs)
        let {spanLeft}=this;
        spanLeft.innerHTML++;
        this.computed();
    };
    against=ev=>{
        let {spanRight}=this;
        spanRight.innerHTML++;
        this.computed();        
    };
    computed=()=>{
        let {spanLeft,spanRight,spanRate}=this,
            n=pareseFloat(spanLeft.innerHTML),
            m=pareseFloat(spanRight.innerHTML),
            rate=(n+m)===0？'0%':((n/(n+m)*100).toFixed(2)+'%');
    }    
}
```

在React组件中

1. 基于数据驱动(修改状态数据，react帮助我们重新渲染试图)完成的组件叫做**“受控组件（受数据控制的组件）”**
2. 基于ref操作DOM实现试图更新的，叫做“**非受控组件”**

=>真实项目中，建议大家多使用“受控组件”



#### 利用表单元素的ONCHANGE实现MVVM实现双向绑定

vue:[MVVM] 数据更改视图跟着更改，视图更改数据也跟着更改（双向数据绑定）

react:[MVC] 数据更改视图跟着更改（原本是单向数据绑定，但是可以自己构建出双向的效果）

```react
import React from 'react';
import ReactDOM from 'react-dom';
import 'bootstrap/dist/css'
import PropTypes from 'prop-types'

class Temp extends React.Component{
    constructor(){
        super();
        this.state={
            text:'曾洁莹'
        }
    }
    
    componentDidMount(){
        setTimout(()=>{
            this.setState({text:'Renee'})
        },1000)
    }
    
    render(){
       let {text}=this.state 
        return <section className='panel panel-default'>
            <div className='panel-heading'>
                <input type='text' className='form-control' value={text} onChange={ev=>{
                        //在文本框的on-change中修改状态信息：实现的是视图改变数据
                        this.setState({
                            text:ev.target.value
                        })
                    }}/>
            </div>
            <div className='panel-body'>
                {text}
            </div>
    	</section>
    }
}

ReactDOM.render(<Temp/>,root)
```

### 3、生命周期函数（钩子函数）

描述一个组件或者程序从创建到销毁的过程，我们可以在过程中间基于钩子函数完成一些自己的操作（例如：在第一次渲染完成做什么，或者在第二次即将重新渲染之前做什么等...）

**[基本流程]**

- constructor:创建一个组件
- componentWillMount:第一次渲染之前
- render:第一次渲染之前
- componentDidMount:第一次渲染之后

**[修改流程：当组件的状态数据发生改变（set-state）或者传递给组件的属性发生改变（重新调用组件传递不同的属性）都会引发render重新执行渲染（渲染也是差异渲染）]**

- shouldComponentUpdate:是否允许组件重新渲染（允许则执行后面函数，不允许直接结束）
- componentWillUpdate:重新渲染之前
- render:第二次及以后重新渲染
- componentDidUpdate:重新性能渲染之后



- componentWillReceiverProps:父组件把传递给子组件的属性发生改变后触发的钩子函数

**[卸载：原有渲染的内容是不消失的，只不过以后不能基于数据改变视图了]**

- componentWillUnmount：卸载组件之前（一般不用）

```react
import React from 'react';
import ReactDOM from 'react-dom';
import 'bootstrap/dist/css'
import PropTypes from 'prop-types'

class A extends React.Component{
    // 这个是第一个执行的，执行完成后（给属性设置默认值）才向下执行
    // static defaultProps={};
    
    constructor(){
        super();
        console.log('1:constructor');
        this.state={n:1};
    }
    componentWillMount(){
        // 在will-Mount中，如果直接的set-state修改数据，会把状态信息改变后，然后render和did-mount；但如果set-state是放在一个异步操作中完成的（例如定时器或者从服务器获取数据），也是先执行render和did，然后执行这个异步操作修改状态，紧接着走修改的流程（这样和放到did-mount没啥区别），所以一般把数据请求放到Did中处理
        // 真实项目中的数据绑定，一般第一次组件渲染，我们都是绑定的默认数据，第二次才是绑定的从服务器获取（有些需求我们需要根据数据是否存在判断显示隐藏等）
        console.log('2:willMount 第一次渲染之前',this.refs.HH)//->undefined
    }
    componentDidMount(){
        console.log('4:DidMount 第一次渲染之后',this.refs.HH)//->div
        // 真实项目中在这个阶段一般做如下处理：
        // 1.控制状态信息更改的操作
        // 2.从服务器获取数据，然后修改状态信息，完成数据绑定
        // ...
        setInterval(()=>{
            this.setState({n:this.state.n+1});// 状态在改，一直render渲染
        },5000)
    }
    shouldComponentUpdate(nextProps,nextState){
        console.log('5:是否允许更新')
        // return false; 函数返回true就是允许，返回false就是不允许
        console.log(this.state.n);
        // 发现在这个钩子函数中，我们获取的state不是最新修改的，而是上一次的state值
        // 例如：第一次加载完成，5000ms后，我们基于set-state（异步的，改状态前先执行shouldComponentUpdate）把n修改为2，但是此处获取的还是1呢
        
        // 但是这个方法有两个参数
        // nextProps:最新修改的属性信息
        // nextState:最新修改的状态信息
        if(this.nextState.n>3){
            return false;
        }
        return true;
    }
    componentWillUpdate(nextProps,nextState){
        // 这里获取的状态也是更新之前的，和should一样也有两个参数存储最新的信息
        console.log('6:组件更新之前',this.state.n)
    }
    componentWillUpdate(){
        // 这里获取的状态也是更新之后的
        console.log('8:组件更新之后',this.state.n)
    }
    render(){
        console.log('render')
        return<div ref='HH'>
            {this.state.n}
        </div>
    }
}
ReacrDOM.render(<A/>,root)
```

### 4、复合组件和单项数据流传递

父组件把信息传递给子组件：

基于属性传递即可（而且传递是单方向的：只能父亲通过属性把信息给儿子，儿子不能直接把信息作为属性传递给父亲）

后期子组件中的信息需要修改：可以让父组件传递给子组件的信息发生变化（也就是子组件接受的属性发生变化，组件会重新渲染=>componentWillReceiveProps钩子函数）

只要实现，点击子组件BODY中按钮的时候，可以修改父组件panel的状态信息n，panel的状态改变，panel会重新的执行render渲染，而重新执行render的时候，会把最新的状态值作为属性，传递给子组件head，head接受的属性值发生改变，head组件也会重新的渲染

类似于这种“子改父”的操作，我们需要使用一下技巧完成：

1. 把父组件中的一个方法，作为属性传递给子组件
2. 在子组件中，把基于属性传递进来的方法，在合适的时候执行（相当于在执行父组件的方法：而这个方法中完全可以操作父组件中的信息）

```react
import React from 'react';
import ReactDOM from 'react-dom';
import 'bootstrap/dist/css'
import PropTypes from 'prop-types'

class Head extends React.Component{
    constructor(){
        super();
    }
    render(){
        return<div className='panel-heading'>
            {/* 子组件通过属性获取父组件传递的内容 */}
        	<h3 className'panel-title'>点击次数:{this.props.count}</h3>
        </div>
    }
}

class Body extends React.Component{
    constructor(){
        super();
    }
    render(){
        return<div className='panel-body'>
        	<button className='btn btn-success' onClick={this.props.callBack}>点我啊</button>
        </div>
    }
}

class Panel extends React.Component{
    constructor(){
        super();
        thia.state={n:0}
    }
    fn=()=>{
        //修改panel的状态信息
        this.setState({
            n:this.state.n+1
        })
    }
    
    render(){
        return<section className='panel panel-default' style={{width:'50%',margin:'0 auto'}}>
            {/* 父组件中在调取子组件的时候，把信息通过属性传递给子组件 */}
        	<Head count={this.state.n}/>
            {/* 父组件把自己的一个方法基于属性传递给子组件，目的是在子组件中执行这个方法 */}
            <Body callBack={this.fn} />
        </section>
    }
}

ReactDOM.render(<Panel />,root)
```

### 5、轮播图组件

#### src->index.js 

公共的样式在index中导入，组件独有的样式可以在组件内部导入

数据参数一般都通过属性传递过去的

在react的JSX中需要使用图片等资源的时候，资源的地址不能使用./相对地址（因为经过webpack编译后，资源地址的路径已经改变了，原有的相对地址无法找到对应的资源），此时我们需要基于ES6 Module或者CommonJS等模块导入规范，把资源当做模块导入进来（或者使用的图片地址都是网络地址）

```react
import React from 'react';
import ReactDOM from 'react-dom';
import Banner from './component/Banner'
// 公共的样式在index中导入，组件独有的样式可以在组件内部导入
import './static/css/reset.min.css';
let IMG_DATA=[{
    id:1,
    title:'',
    pic:require('./static/imgages/1.png')
},{
    id:2,
    title:'',
    pic:require('./static/imgages/2.png')
},{
    id:3,
    title:'',
    pic:require('./static/imgages/3.png')
},{
    id:4,
    title:'',
    pic:require('./static/imgages/4.png')
},{
    id:5,
    title:'',
    pic:require('./static/imgages/5.png')
}]
//或者这么写
let IMG_DATA=[]
for(let i =1;i<5;i++){
    IMG_DATA.push({
        id:1,
        title:'',
        pic:require(`./static/imgages/${i}.png`)
    })
}

// 调取组件渲染
ReactDOM.render(<main>
     {/*
       * data：轮播图需要绑定的数据
       * interval：自动轮播间隔的时间 默认3000ms
       * step：默认展示图片的索引（记住前后各克隆了一张）默认1
       * speed：每一张切换所需要的运动时间 默认300ms
       */}
    <Banner data={IMG_DATA} interval={} step={} speed />
    <div></div>    
    <Banner data={IMG_DATA.slice(0)} interval={5000} step={} speed /> 
</main>,root)
```

#### src->component->Banner.js

```react
import React from 'react';
import PropTypes from 'prop-types'
import '../static/css/banner.css'

export default class Banner extends React.Component{
    // 设置默认值规则
    static defaultProps={
        data:[],
        interval:3000,
        step:1,
        speed:300
    }
    static propTypes={
        data:PropTypes.array,
        interval:PropTypes.number,
        step:PropTypes.number,
        speed:PropTypes.number	 	
    }
    constructor(props){
        super(props)；
        
        let {step,speed}=this.props
        this.state={
            step,
            speed
        }
    }
	// 实现数据克隆（不要放在render里，因为只需执行一次），
    componentWillMount(){
        let {data}=this.props,
            cloneData=data.slice(0);
        cloneData.push(data[0]);
        cloneData.unshift(data[data.length-1]);
        this.cloneData=cloneData; // 以后不会更改，没必要挂载到state上，挂载到实例上供其他方法调用
    }

	// 自动轮播
    componentDidMount(){
        // 把定时器返回值挂载到实例上，方便后期清除（结束自动轮播）
		this.autoTimer=setInterval(this.autoMove,this.props.interval)
    }

    componentWillUpdate(nextProps,nextState){
		//边界判断:如果最新修改的step索引大于最大索引（说明此时已经是末尾了，不能再向后走了），我们让其立刻回到（无动画）索引为1的位置
        if(nextState.step>(this.cloneData.length-1)){
            this.setState({
                step:1,
                speed:0
            })
        }
    }

    componentDidUpdate(){
		//只有是从克隆的第一张立即切换到真实第一张后，我们才做如下处理：让其从当前第一张运动到第二张即可
        let {step,speed}=this.state;
        if(step===1&&speed===0){
            // 加定时器延迟原因：css3的transition有一个问题（主栈执行的时候，短时间内遇到两次设置transition-duration的代码，以最后一次设置的为主）
            let delayTimer=setTimeout(()=>{
                this.setState({
                    step:step+1,
                    speed:this.props.speed
                })                
            },0)

        }
    }	

    render(){
        // 轮播图数据需要克隆后的，焦点不需要
        let {data}=this.props,
            {cloneData}=this;
        if(data.length==0) return '';
        
        //控制wrapper的样式
        let {step,speed}=this.state
        let wrapperSty={
            left:-step*1000+'px',
            transition:`left ${speed}ms`
        }
        
        return <section className='container'>
            <ul className='wrapper' style={wrapperSty}>
                {cloneData.map((item,index)=>{
                    let {title,pic}=item
                    return <li key={idex}>
                        <img src={pic} alt={title} />
                    </li>
                })}
                <il><img src="" alt="" /></il>
            </ul>
            <ul className='focus'>
                {data.map((item,index)=>{
                    let tempIndex=step-1;
                    step===0?tempIndex=data.length-1:null;
                    step===(cloneData.length-1)?tempIndex=0:null;
                    return <li key={index} className={tempIndex===index?'active:''}></li>
                })}
            </ul>
            <a href='javascript:;' className='arrow arrowLeft'></a>
            <a href='javascript:;' className='arrow arrowRight'></a>
        </section>
    }
    // 实现向右切换
    autoMove=()=>{
        this.setState({
            step:this.state.step+1
        })
    }
}
```

#### src->static->css->banner.css

```css
.container{
    
}
.container:hover .arrow{dispaly:none;}
```

#### react-swipe插件

安装

> yarn add swipe-js-iso react-swipe

```react
import ReactSwipe form 'react-swipe'

ReactDOM.render(<main>
    <ReactSwipe className='container' swierOptions={{auto:2000}}>
        {IMG_DATA.map((item,index)={
            return <div key={index}>
            	<img src={pic} alt={title} />
            </div>
        })}
    </ReactSwipe>
</main>,root)
```

```css
.container{
    margin:20px auto;
}
.container img{}
```

### 复合组件信息传递

#### 复合组件：父组件嵌套子组件

传递信息的方式

- 父组件需要把信息传递给子组件
  - “属性传递”：调取子组件的时候，把信息基于属性的方式传递给子组件（子组件props中存储传递的信息）；这种方式只能父组件传递给子组件，子组件无法直接的把信息传递给父组件，也就是属性传递信息是单向传递的
  - “上下文传递”：父组件先把需要给后代元素（包括孙子元素）使用的信息都设置好（设置在上下文中），后代组件需要用到父组件的信息，主动去父组件调取使用即可
    1. 父组件设置信息

  ```react
  // 在父组件中
  // 需要安装prop-types引入
  // 1.设置子组件上下文属性值类型
  //	 static childContextTypes={}
  // 2.获取子组件的上下文（设置子组件的上下文属性信息）
  //   getChildContext(){ return {}}
  
  static childContextTypes={
      n:PropTypes.number,
      m:PropTypes.number,
      callBack:PropTypes.func
  }
  getChildContext(){
      // return的是啥，相当于给子组件上下文设置为啥
      // 只要render重新渲染，就会执行这个方法，重新更新父组件的上下文信息；如果父组件上下文信息更改了，子组件再重新调取的时候，会使用最新的上下文信息（render->context->子组件调取渲染）
      let {count:{n=0,m=0}}=this.props
      return{
          n,
          m,
          callback:this.updateContext
      }
  }
      
  updateContext=()=>{}
  ```

   	2. 子组件主动获取需要的信息

  ```react
  //在子组件中
  //需要安装prop-types引入
  
  //子组件中设置使用传递进来的上下文类型：设置哪个的类型，子组件上下文中才有哪个属性，不设置的是不允许使用的
  //指定的上下文属性类型需要和父组件中指定的类型保持一致，否则报错
  static contextTypes={
      // 首先类型需要和设置时候类型一样，否则报错；并且你需要啥，写啥即可
      n:PropTypes.number,
      m:PropTypes.number
  }
  constrcutor(props,context){
      // 传递进来的props和context通过super继承，挂在实例上了，通过this.xxx可以用了
      super(props,context)
      console.log(context)
  }
  ```

**属性VS上下文**

1. 属性操作起来简单，子组件是被动接受传递的值（组件内的属性是只读的），只能父传子（子传父不行，父传孙也需要处理：父传子，子再传孙）
2. 上下文操作起来相对复杂一些，子组件是主动获取信息使用的（自组件是可以修改获取到上下文信息的，但是不会影响父组件中的信息，其他组件不受影响），一旦父组件设置了上下文信息，它后代组件都可以直接拿来用，不需要一层层传递

- 自组件也能修改父组件的信息
  - 利用回调函数机制：父组件把一个函数通过属性或者上下文的方式传递给子组件，子组件中只要把这个方法执行即可（也就是子组件执行了父组件方法，还可以传递一些值过去），这样父组件在这个方法中，想把自己的信息改成啥就改成啥

#### 平行组件：兄弟组件或者毫无关系的两个组件

1. 让两个平行组件拥有一个共同的父组件

   父组件中有一些信息，父组件有一个方法传递给A，A中把方法执行（方法执行修改父组件信息值），父组件再把最新的信息传递给B即可，等价于A操作，影响了B

2. 基于redux来进行状态管理，来实现组件之间的信息传输（常用方案）

    redux可以应用在任何项目中（vue/jq/react的都可以），react-redux才是专门给react项目提供的方案