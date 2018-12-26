## JS中的同步异步编程

浏览器是多线程的，JS是单线程的（浏览器只分配一个线程来执行JS）

进程大线程小：一个进程中包含多个线程，例如在浏览器中打开一个HTML页面就占用了一个进程，加载页面的时候，浏览器分配一个线程去计算DOM树，分配其他的线程加载对应的资源文件，再分配一个线程自上而下执行JS

同步：在一个线程上（主栈/主任务队列）同一个时间只能做一件事情，当事情完成才能进行下一个事情（先把一个任务进栈执行，执行完成，再把下一个任务进栈，上一个任务出栈...）

异步：在主栈中执行一个任务，但是发现这个任务是一个异步的操作，我们会把它移除主栈，放到等待任务队列中（此时浏览器会分配其他线程监听异步任务是否到达指定的执行时间），如果主栈执行完成，监听者会把到达时间的异步任务重新放到主栈中执行...

### 宏任务和微任务

异步编程分宏任务和微任务

【宏任务:macro task】

- 定时器
- 时间绑定
- AJAX
- 回调函数
- node中fs可以进行异步的I/O操作等

【微任务:micro task】

- Promise(async/await) 

  - Promise并不是完全的同步，当在Excutor中执行resolve或者reject的时候，此时是异步操作，会先执行then/catch等，当主栈完成后，才会调用resolve/reject存放的方法执行

  - async:把一个函数直接变成promise实例，await:等待一个promise的结果

- process.nextTick

执行顺序：SYNC=>MICRO=>MACRO

所以JS中的异步编程，仅仅是根据某些机制来管控任务的执行顺序，不存在同时执行两个任务这个说法。

```javascript
setTimeout(()=>{console.log(1)},20)

setTimeout(()=>{console.log(2)},0); //=>默认会有最小的等待时间（V8一般是5~6ms）

console.time('WHILE');
let i=0;
while(i<=99999999){i++}
console.timeEND('WHILE')

setTimeout(()=>{console.log(3)},10)

console.log(4)
```

```javascript
//=>AJAX任务开始：send
//=>AJAX任务结束：状态为4
let xhr=new XMLHttpRequest();
xhr.open('GET','XXX.txt',false);
// 放到等待区的时候，此时状态是1
xhr.onreadystatechange=()=>{// 当状态改变时
    console.log(xhr.readyState);//=>主栈空闲时输出4
}
xhr.send();// 当状态未4时主栈空闲

//以下一次都不会输出
let xhr=new XMLHttpRequest();
xhr.open('GET','XXX.txt',false);
xhr.send();
// 此时状态已经为4了
xhr.onreadystatechange=()=>{// 状态改变才会触发，放到等待区的时候状态已经为4了，不会再改变了，所以不会执行这个方法
    console.log(xhr.readyState);
}

//异步的情况
let xhr=new XMLHttpRequest();
xhr.open('GET','XXX.txt');
xhr.send();//=>	异步操作：执行send后，有一个线程是去请求数据，主栈会空闲下来
// 此方法进栈又出栈，放到等待区，放等待区之前状态是1
xhr.onreadystatechange=()=>{
    console.log(xhr.readyState)//输出2、3、4
}
//主栈又空闲了
//状态为2 函数执行
//状态为3 函数执行
//状态为4 函数执行
```

await有暂时跳出线程队列的含义

```javascript
console.log(1)
new Promise((resolve,reject)=>{
    //=>new Promise的时候，会立即把Excutor函数（也就是传递的回调函数）执行，所以Promise本身可以理解为同步的
    console.log(2)
    setTimeout(()=>{
        resolve()；//=>Promise内部机制：执行resolve会把之前基于then存放的方法按照规律执行
    },10)
}).then(()=>{//=>执行完成Excutor，紧接着执行then，执行then方法，会把传递的回调函数放到执行的容器中，等待触发执行（Promise内部的机制）
    console.log(3)
})
console.log(4)
// 1 2 4 3

//then相当于把容器方法，resolve执行容器的方法，但promise有自己的机制，执行resolve之后，他会等，等容器方法放完了再执行
console.log(1)
new Promise((resolve,reject)=>{
    console.log(2)
	resolve()；//执行resolve的时候还没执行then，此时容器没有方法
}).then(()=>{
    console.log(3)
})
console.log(4)
// 1 2 4 3


//=>ES7中新增加对Promise操作的新语法：aysnc/await（使用await必须保证当前方法是基于async修饰的）
function AA(){
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            Math.random()<0.5?reject(100):resolve(200);
        },0)
    })
}
async function fn(){
    let res=await AA();//=>先把AA执行，等待AA中的PROMISE完成（不论成功和失败），把最后的处理结果获取到赋值给res，拿到后再执行后面的代码（有人说：await是异步的操作同步化是错的）
}

//下一道
function AA(){
    console.log(1)
    return new Promise((resolve,reject)=>{
        setTimeout(()=>{
            resolve(200);
        },0)
    })
}
async function fn(){
    console.log(2)
    let res=await AA()
    //1.先把AA执行，返回一个promise实例
    //2.它会暂时跳出当前正在执行的函数(fn)，也就是await后面的代码先不执行（把后面代码从主栈移除，放到等待区域中）
    //3.此时主栈暂时空闲，会执行console.log(4)
    //4.当主栈中的其它任务完成（主栈空闲），并且AA中的promise也已经计算完成最后的结果，再把之前第二步移到等到区域的内容，重新拿回到主栈中执行
    console.log(3)
}
fn();
console.log(4)
//2 1 4 3 =>await并不是同步的
```

```javascript
async function async1(){
    console.log('async1 start');
    await async2();
    console.log('async1 end')
}
async function async2(){console.log('async2')}
console.log('script start');
setTimeout(function(){console.log('setTimeout');},0)
async1();
new Promise(function(resolve){
    console.log('promise1');
    resolve();
}).then(function(){
    console.log('promise2');
})
console.log('script end');
//=>script start async1 start async2 promise1 script end (promise2或者async1 end 顺序根据不同的v8版本，是不一样的) setTimeout
```

## node中独有的异步操作API

setImmediate：它也是定时器，但它不设置时间，但是它也是异步编程（宏任务），它会在所有定时器之前执行

process.nextTick：把当前任务放到主栈最后（等待区之前）执行（当主栈执行完，先执行nextTick，再到等待队列中找）

```javascript
//在node执行下面代码，发现每次执行先后顺序不一样，因为node需要启动时间，执行过程中setTimeout可能到时间了也可能没到时间，所以这个先后顺序取决于node的执行时间。
setTimeout(()=>{
 console.log(1)
},0)
setImmediate(()=>{
 console.log(2)
})

```

```javascript
process.nextTick(()=>{
    console.log(2);
})
setTimeout(()=>{
 console.log(1)
},10)
let i=0;
while(i<999999){
    i++
}
console.log(3)
//3 2 1
```

process.nextTick可以这么用

```javascript
function computed(){
    let 1=0;
    while(i<999999){
        i++
    }
}
let http=require('http');
http.createServer((req,res)=>{
    res.end('ok');
}).listen(8888,()=>{
    console.log('success')
});
process.nextTick(computed);
```

process.env.NODE_ENV：全局环境变量

用途：真实项目中，我们项目基于webpack打包配置的时候，往往需要区分不同环境下的不同操作，例如有：开发环境、测试环境、生产环境……，而我们一般都是基于环境变量来区分打包配置的！

yarn add cross-env需要提前安装这个模块，否则window不兼容。

```javascript
//package.json
{
    "script":{
        "dev":"cross-env NODE_ENV=dev node index.js", //执行Index.js文件
        "pro":"cross-env NODE_ENV=pro node index.js",
        "test":"cross-env NODE_ENV=test node index.js",  
    }
}
```

在Index.js中

```javascript
let ENV=process.env.NODE_ENV;
if(ENV==='dev'){
    console.log('我是开发环境')
}
if(ENV==='pro'){
    console.log('我是生产环境')
}
```

然后yarn dev，会执行cross-env（设置兼容） NODE_ENV=dev（设置环境变量） node index.js（执行文件）

