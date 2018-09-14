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
- npm run build：项目需要部署到服务器上，我们先执行npm run build，把项目整体编译打包（完成后会在项目中生成一个build文件夹，这个文件夹包含了所有编译后的内容，我们把它上传到服务器，可以说是生成环境）而且在服务上进行部署的时候不需要再安装任何模块
- eject：create-react-app脚手架为了让解构目录清晰，把安装的webpack及配置文件都集成在了react-scripts模块中，放到了node_modules中。

但是真实项目中，我们需要在脚手架默认安装的基础上，额外安装一些我们需要的模块，例如：react-router-dom/axios...再比如:less/less-loader

1. 情况一：如果我们安装其他的组件，但是安装成功后不需要修改webpack的配置项，此时我们直接的安装，并且调取使用即可。

2. 情况二：我们安装的插件是基于webpack处理的，也就是需要安装的模块配置到webpack中（重新修改webpack配置项了）

   首先需要把隐藏到node_modules中的配置项暴露到项目中，再去修改对应的配置项即可

   > $ npm run ejecct
   >
   > 会提示确认是否执行eject操作，这个操作是不可逆转的，一旦暴露出来的配置，就无法在隐藏回去了
   >
   > 如果当前的项目基于git管理，在执行eject的是偶，如果还有没有提交到历史区的内容，需要先提交到历史区，然后再eject才可以，否则报错This git repository has untracked files or uncommitted changes..

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

##### ReactDOM.render

ReactDOM.render([JSX],[CONTAINER],[CALLBACK]):把JSX元素渲染到页面中

- JSX:REACT虚拟元素
- CONTAINER:容器，我们想把元素放到页面中的哪个容器
- CALLBACK:当把内容放到页面中呈现触发的回调函数（能触发这个函数说明元素已经放在页面中了

##### JSX

REACT独有的语法，JAVASCRIPT+XML(HTML)

和我们之前自己拼接的HTML字符串类似，都是把HTML结构代码和JS代码或者数据混合在一起了，但是它不是字符串

1. 不建议我们把JSX直接渲染到BODY中，而是放在自己创建的一个容器中，一般我们都放在一个ID位ROOT的DIV即可
2. 在JSX中出现的{}是存放JS的，但是要求JS代码执行完成需要有返回结果（JS表达式）
   - 不能直接放一个对象数据类型的值（对象（除了给style赋值）、数组（数组中如果没有对象，都是基本值或者JSX元素，这样是可以的）、函数都不行）
   - 可以是基本类型的值（布尔类型什么都不显示、null和undefined也是JSX元素，代表是空）
   - 循环判断的语句都不支持，但是支持三元运算符
3. 循环数组创建JSX元素（一般都是基于数组的MAP方法完成迭代），需要给创建的元素设置唯一的KEY值（当前本次循环内唯一即可）
4. 只能出现一个根元素
5. 给元素设置样式类用的是className而不是class
6. style中不能直接的写样式字符串，需要基于一个样式对象来遍历赋值

## 把JSX（虚拟DOM）变为真实DOM

JSX渲染机制

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

2. 执行React.creactElement(type,props,children)，创建一个对象（虚拟DOM）

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

投票案例

```react
import React from 'react';

export default class Vote extends React.Component{
    constructor(){
        super()
    }
    render(){
        return <section className='panel panel-default' style={{width:'60%',margin:'0 auto'}}>
            <div className='panel-deading'>
                <h3 className='panel-title'>
                    标题:{this.props.title}
                </h3>
            </div>
            <div className='panel-body'>
                支持人数
                <br /><br />
                反对人数
                <br /><br />
                支持率
            </div>
            <div className='panel-footer'>
                <button className='btn btn-success'>支持</button>
                &nbsp;&nbsp;&nbsp;&nbsp;
                <button className='btn btn-danger'>反对</button>
            </div>
        </section>
    }
}

```

