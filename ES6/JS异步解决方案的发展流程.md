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

这里我们发现手动迭代很复杂，所以有一个库co可以配合Generator使用。

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

应用co库

