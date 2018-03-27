## ES6学习笔记 - 解构赋值

**按照原有值的解构，把原有值中的某一部分内容快速获取到（快速赋值给一个变量）**

#### 1.数组的解构赋值

> 解构赋值本身是ES6的语法规范，使用什么关键字来声明这些变量是无所谓的

```javascript
let [a,b,c]=[12,23,34];
//a:12 b:23 c:34

var [d,e,f]=[1,2,3]
//d:2 e:2 f:3

[g,h,i]=[12,13,14]
//相当于给window增加的全局属性 g:12 h:13 i:14
//但是这种操作在JS的严格模式下是不允许的，因为严格模式下不能出现非使用var/let等声明的变量

```

> 多维数组的解构赋值，可以快速获取到需要的结果

```javascript
let [,[,A],[,B,[,C]]]=[12,[23,34],[45,56,[66,88]]]
//A:34 B:56 C:88
```

> 如果只想获取数组中前面某几项的内容，后面解构不需要补全

```javascript
let [D]=[1,2,3];
//D:1

let [,E]=[12,13,14];
//E:13
```

> 在解构赋值中，可以给某一项设置默认值

```javascript
let [,,,A]=[12,13,14]
//A:undefined

let [,,,B=0]=[12,13,14]
//B:0
```

> 在解构赋值中，支持 ` ...XXX` 的拓展运算符

```javascript
let [A,...B]=[1,2,3,4];
//A:12 B:[2，3，4]

let [...C]=[1,2,3,4,5];
//C:[1,2,3,4,5] 数组克隆

let [D,...E,F]=[1,2,3,4,5];
console.log(D,E,F);//报错 拓展运算符只能出现在解构赋值中的结构末尾位置
```

#### 2.对象的解构赋值

```javascript
let {name,age}={name:'renee',age:18};
console.log(name,age); //'renee' 18

let {A,B}={name:'renee',age:18};
console.log(A,B); //undefined undefined
//在对象的解构赋值中，赋值的变量需要和对象中的属性温和，否则无法获取对应的属性值
 
let {C=0}={name:'renee',age:18};
console.log(C); //0   
//和数组的解构赋值一样，可以把后面不需要获取的解构省略掉，也可以给当前的变量设置默认值

let {name}={name:'renee',age:18};
console.log(name); //'renee'
let {，age}={name:'renee',age:18};
console.log(age); //报错
//和数组的解构赋值不一样的地方在于，对象前面不允许出现空来占位（因为对象获取需要通过具体的属性名来获取，写成空的话，浏览器不知道怎么识别）
let {age}={name:'renee',age:18};
console.log(age); //18 可省略，但不能用空占位置

let {name，...arg}={name:'renee',age:18,teacher:'周老师'};
console.log(name,arg); //'renee' {age:18,teacher:'周老师'}
//支持拓展运算符的
```

> 可以使用解构赋值克隆，但只是把对象进行浅克隆（只把第一级克隆了）

```javascript
let obj={name:'renee',age:18,teacher:'周老师',score:[99,98,97]};
let {...arg}=obj;
console.log(arg===obj);// false
console.log(arg.score===obj.score);// ture
```

> 深拷贝-使用JSON中的方法

```javascript
let school = {
    name:{name:'zfpx'},
    age:9,
    address:'珠峰',
    arr:['1','2','3']
}
let newSchool = JSON.parse(JSON.stringify(school));
school.name.name = 'zf';
console.log(newSchool);
//但遇上对象里的值是函数就不行了
```

> 深拷贝-拷贝递归

```javascript
let school = {
    name:{name:'zfpx'},
    age:9,
    address:'珠峰',
    arr:[{name:1},'2','3']
}
function deepClone(parent,c){ // parent是要拷贝的对象
    let child = c||{};
    for(let key in parent){
        if(parent.hasOwnProperty(key)){
            let val = parent[key];
            if(typeof val === 'object'){
                child[key] = Object.prototype.toString.call(val)==='[object Array]'?[]:{}
                deepClone(parent[key],child[key]); // [1,2,3] // []
            }else{
               child[key] = parent[key]; // 处理普通属性的
            }
        }
    }
    return child;
}
console.log(deepClone(school));

```



```javascript
let {name:A,age:B}={name:'renee',age:18};
//A:'renee' B:18
//在对象的解构赋值中，可以把对象的属性名起一个小名（A和B相当于小名或者别名，用冒号）
```

```javascript
let data=[
  {
    "name":"renee",
    "age":18,
    "score":{
      "english":[100,99,98],
      "math":[97,96,95],
      "chinese":[60,61,62] 
    }
  }
];
let [{score:{english:[A],math:[,B],chinese:[,,C]}}]=data;
console.log(A,B,C);//100 96 62
```

#### 2.解构赋值的作用

> 快速交换两个变量的值

```javascript
let a=12;
let b=13;
[a,b]=[b,a];
console.log(a,b); //13 12
```

> 接收函数返回的多个值

```javascript
let fn=function(){
    let a=12,
        b=13,
        c=14;
    return [a,b,c];
}
let [a,b,c]=fn();
console.log(a,b,c) //12 13 14
```

> 可以快速接收到传递的多个值（我传递的是一个数组或者对象）

```javascript
let fn=function([A,B,C,D=0]){
    console.log(A,B,C,D); //12 13 34 0
}
fn([12,13,34]);
//console.log(A) 报错 函数中的ABC是私有变量
```

> 快速处理options参数的初始化操作

```javascript
let animate=function({curEle=null,target={},duration=1000,callBack=null}={}){
    console.log(curEle,target,duration,callBack); //body {opacity:1} 1000 null
};
animate({
    curEle:document.body,
    target:{opacity:1}
});
```

> 在ES6中支持给函数设置默认值，用等号

```javascript
let fn=function(x)｛
	console.log(x); //undefined;
	x=x||0;
	console.log(x); //0
｝
fn();

let fn2=function(x=0){
    console.log(x); //0
}
fn2();
```