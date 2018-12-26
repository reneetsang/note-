## JS异步解决方案的发展流程

同步：同步连续执行。

异步：先干一件事，中间去干其他的事，最终在回来干这件事。

例如，你去烧水，一直不做别的事情等水烧开的过程叫同步，如果在等水烧开的过程中去洗碗了，这个是异步。

异步发展流程：callback ->  promise -> generator + co -> async+await(语法糖)

### callback

异步执行可以用回调函数实现：

```javascript
let fs = require('fs');
fs.readFile('./2.promise/1.txt','utf8',function(err,data){
    fs.readFile(data,'utf8',function(err,data){
        console.log(data);
    });
});
```

有时候回调嵌套很多时，代码就会非常繁琐，难以维护，造成“回调地狱”。

还有问题是，无法在同一时刻合并两节异步的结果，异步方案结果不支持return。

```javascript
let fs = require('fs');
fs.readFile('./2.promise/1.txt','utf8',function(err,data){ // error-first
    console.log(data);
});
fs.readFile('./2.promise/2.txt','utf8',function(err,data){ // error-first
    console.log(data);
});
```

### promise

为了解决上面的问题，则出现了promise。

Promise本意是承诺，在程序中的意思就是承诺我过一段时间后会给你一个结果。 什么时候会用到过一段时间？答案是异步操作，异步是指可能比较长时间才有结果的才做，例如网络请求、读取本地文件等。

```javascript
let fs = require('fs');
function read(file){
    return new Promise(function(resolve,reject){
        fs.readFile(file,'utf8',function(err,data){
            if(err) return reject(err);
            resolve(data);
        })
    })
}
//当第一个then中返回一个promise，会将返回的promise的结果,传递到下一个then中。这就是比较著名的链式调用了。
read('./1.txt').then(function(data){
    return read(data);
}).then(function(data){
    console.log(data)
}).catch(function(err){
    console.log(err)
});
```

### generator + co 

迭代器就是一个有next方法的对象，每次调用next都会返回一个对象，对象里有done、value，for of必须拥有迭代器的元素才能使用

```javascript
let likeArray={0:1,1:2,2:3,length:3,[Symbol.iterator](){
    let flag=false;
    let index=0;
    let that=this;
    return{
        next(){
            return{done:index===that.length,value:that[index++]}
        }
    }
}}
let arr=[...likeArray];
console.log(arr)
```

*表示是一个生成器函数，一般可以配合yield使用

默认我用...likeArray会让迭代器执行

```javascript
let likeArray={0:1,1:2,2:3,length:3,[Symbol.iterator]:function*(){
    let index=0;
    yield this[this++]
	yield this[this++]
}}
let arr=[...likeArray];
console.log(arr)

let likeArray={0:1,1:2,2:3,length:3,[Symbol.iterator]:function*(){
    let index=0;
    while(index!==this.length){
        yield this[index++]
    }
}}
let arr=[...likeArray];
console.log(arr)
```

generactor的好处就是，遇到yield就会暂停，调用Next会继续往下执行

```javascript
function *gen(){
    yield 1;
}
let it=gen();
let flag=false
do{
    let {value,done}=it.next();
    flag=done;
    console.log(value)
}while(!flag)
```

```javascript
function* gen(){
    let a=yield 1;
    console.log(a);
    let b=yield 2;
    console.log(b);
    let c=yield 3;
    console.log(3)
}
let it=gen();
it.next();//因为遇到yield会暂停，第一次调用next函数时传递的参数是无效的
it.next('hello');//第二次的next执行时，传的参会返回给第一个yield的返回值，即a
```

```javascript
let fs=require('fs');
let bluebird=require('bluebird');
let read=bluebird.promisify(fs.readFile);
function * r(){
    let age=yield read('name.txt','utf8');
    let address=yeid read(age,'utf8');
    let r=yield read(adress,'utf8');
    return r
}
let it =r();
let {value,done}=it.next();
value.then((data)=>{
    let {value,done}=it.next(data);
    value.then((data)=>{
        let {value,done}=it.next(data);
        value.then((data)=>{
            console.log(data)
        })
    })
})
```

----

这里我们发现手动迭代很复杂，所以有一个库co可以配合Generator使用。

```javascript
let fs=require('fs');
let bluebird=require('bluebird');
let read=bluebird.promisify(fs.readFile);
function * r(){
    let age=yield read('name.txt','utf8');
    let address=yeid read(age,'utf8');
    let r=yield read(adress,'utf8');
    return r
}
let co=require('co');
co(r()).then(data=>{
    console.log(data)
})
```

```javascript
let fs = require('fs');
function read(file){
    return new Promise(function(resolve,reject){
        fs.readFile(file,'utf8',function(err,data){
            if(err) return reject(err);
            resolve(data);
        })
    })
}
function *gen(filename){
    // 第一次it.next().value 就是yield后面的promise
    let a = yield read(filename);
    let b = yield read(a);
    return b;
}
let it = gen('./1.txt');
// 这里返回迭代器(迭代器返回一个对象拥有value和done属性,当迭代完成后done的属性为true)
it.next().value.then(function(data){
    it.next(data).value.then(function(value){
        console.log(it.next(value).value);
    })
})
```

