## ES6学习笔记 - 箭头函数

**箭头函数的基础语法**

```javascript
let fn=function(x,y){
  return x+y;
}
console.log(fn(10,20)); //30

//改写成箭头函数
let fn=>(x,y)=>x+y;
```

> 箭头函数不支持arguments

```javascript
//传统函数支持arguments
let fn=function(){
  let arg=Array.prototype.slice.call(arguments);
  return eval(arg.join('+'));
}

let fn=(...arg)=>{
  //console.log(arguments); ->报错
  //不支持arguments，但可以使用ES6中的剩余运算符...来获取传递进来的所有参数值（好处：使用剩余运算符接收的结果本身是个数组，不需要再转换了）
  //console.log(arg instanceof Array); //true
  return eval(arg.join('+'));
}
//也可以放fn简写成下面方式
//let fn=(...arg)=>eval(arg.join('+'));
console.log(fn(10,20,30,40))
```

> ES5中this指向问题

```javascript
//普通函数执行，this的指向：看函数执行前面有没有点，有点，点前面是谁this就是谁，没有点this指向window或者undefined(严格模式下)
let obj={
  name:'obj',
  fn(){
    //这样写和下面sum的处理是一样的
    console.log(this)
  }
  sum:function(){}
}
obj.fn(); //->this:obj
document.body.onclick = obj.fn(); //->this:body
setTimeout(obj.fn,1000); //->this:window;
obj.fn.call(12); //->this:12
```

> 箭头函数中没有自己的this指向，用到的this都是所在宿主环境(他的上级作用域)中的this

```javascript
let obj={
  name:'obj',
  fn:()=>{
    console.log(this);
    //不管怎么操作，最后this都指向window：箭头函数中没有自己的this指向，用到的this都是所在宿主环境(他的上级作用域)中的this
  }
}
obj.fn(); 
document.body.onclick = obj.fn(); 
setTimeout(obj.fn,1000); 
obj.fn.call(12); 
```

> 以后项目实战中，不要把所有的函数都改为箭头函数，根据自身需要来修改即可。如：需要让函数中的this是宿主环境中的this，才使用箭头函数；或者不涉及this问题，想让代码写起来简单一些也可以使用箭头函数。

```javascript
let obj={
  name:'obj',
  fn(){
    //this->obj
    
    setTimeout(function(){
      //this->window
    },1000);
    
    setTimeout(function(){
      //this->obj
    }.bind(this),1000);
    
	var _this=this;
    setTimeout(function(){
      //_this->obj
    },1000);
  }
}
obj.fn();
```

> 箭头函数的一些扩充

````javascrpt
let fn=()=>{
  console.log(this);
}

let obj={
  name:'obj',
  sum:function(){
    //->this:obj
    
    fn(); //->this:window
    //宿主环境：不是执行的环境而是定义的环境，fn虽然是在这这行的，但是是在window下定义的，所以它的宿主环境还是window
  }
}
obj.sum();
````

> 层级嵌套的箭头函数

```javascript
let fn=function(i){
  return function(n){
    return n+(++i);
  }
};

let fn=(i)=>(n)=>n+(++i);
```

