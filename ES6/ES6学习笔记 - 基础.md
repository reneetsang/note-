## ES6学习笔记-基础

#### let基础语法

> let 变量名 = 变量值

#### 使用let创建变量和使用var创建变量的区别

1. let不存在变量提升机制

   ES6中只提供了创建变量的新语法标准（let），创建函数还是沿用ES5中的function（还会存在变量提升），如果想让函数也不存在变量提升，都使用函数表达式赋值的方式操作：let fun = function(){}

   创建变量：let xxx=xxx;

   创建函数：let xxx=function(){}

   自执行函数：(function(){})()

   好处：此时代码不用再考虑变量提升了

2. 使用let定义的变量不允许在 `同一个作用域中` 重复声明

   ```javascript
   var num=12;
   var num=13;
   console.log(num) //13
   
   let str='声明';
   let str='声明2';
   console.log(str) //报错，上面代码也不会执行。说明在JS代码执行之前就已经知道有重复声明的了，也就是浏览器依然存在类似于变量提升的机制：在JS代码执行之前先把所有let声明的变量过一遍，发现重复直接报错
   
   let num2=12,
       fn=function(){
         let num=13;
       }
   console.log(num); //12 当前作用域下别重复即可，不同作用域中的变量是自己私有的，名字可重复
   
   let num3=12;
   num3=13
   console.log(13) //13 let不允许重复声明，但允许重复赋值
   
   var num4=200;
   let num4=200; //不管你之前使用什么方式在当前作用域中声明的变量，再使用let声明都会报错
   ```

3. 关于暂时性死区：使用typeof检测一个未被声明过的变量

   > ES5中返回的结果是undefined但不会报错
   >
   > ES6中直接报错

   当前作用域内已经绑定好一个变量，这个变量在这个作用域去取的时候只会在当前作用域寻找。

   ``` javascript
   "use strict";
   console.log(typeof num); //undefined 当前变量不存在，但使用typeof检测的时候，不会提示错误
   
   console.log(typeof num); //报错 ES6中检测一个没有被声明过的变量直接报错
   let num;
   
   let num;
   console.log(typeof num); //undefined 只声明没有定义（赋值），默认值是undefined
   ```

4. ES6语法创建的变量(let)存在块级作用域，ES5语法创建变量（var/function）没有块级作用域

   **ES5**

   > window全局作用域
   >
   > 函数执行形成的私有作用域

   **ES6**

   >  除了ES5中的两个作用域，ES6中新增加块级作用域（可以把块级作用域理解为之前学习的私有作用域：存在私有变量和作用域链的一些机制）` ES6语法中把大部分括号包起来的都称之为块级作用域`

   ```javascript
   var num=12,str='';
   let fn=function(str){
     str='hello';
     //console.log(argument[0]); ->'hello'当前js并没有开启严格模式，所以形参变量和arg存在映射机制（但以后尽量不要这样处理，因为把ES6编译成ES5后，会默认开启严格模式，映射机制中断，导致ES6和ES5结果不一样）
     //console.log(num); 报错
     let num =13;
     console.log(num,str) // 13 'hello'
   }
   fn('学习')
   console.log(num,str) // 12 ''
   ```

   > 我们遇到大部分的大括号操作都是块级作用域

   ```javascript
   if(10>=10){ let total = 100;}
   console.log(total) //报错 判断体也是一个块级作用域，在这个作用域中声明的变量是私有变量，在块级作用域外无法使用（每一次循环都会形成一个新的块级作用域。当前案例形成五个块级作用域，每一个块级作用域中都有一个私有变量i，分别存储的是0~4）
   
   for(let i = 0;i<5;i++){
     console.log(i)
   }
   console.log(i) //报错 循环体也是一个块级作用域
   
   { let i =12}
   console.log(i) //报错 这是ES6中标准的块级作用域
   
   let i=10;
   {
     let i=20;
     {
       {
         console.log(i) //报错 虽然没有变量提升，但每一次进入当前作用域都会把let定义的变量找一遍，说明当前作用域是有这个变量的，提前使用会报错
       }
       let i =30;   
     }
   }
   
   try{
     let i=100;
   }catch(e){
     let k =200;
   }
   consoele.log(i)//报错
   consoele.log(k)//报错
   ```

   > 块级作用域的增加有什么用？

   ```javascript
   //使用ES6的块级作用域
   for(let i=0;i<div.length;i++){
     div[i].onclick=function(){
       console.log(i)
     }
   }
   ```


#### const的基础语法

const的细节知识点和let一样，和let的主要区别在于：let是创建变量，const是创建常量

const 声明的 本身不可以修改 但是如果是对象的话 属性允许修改

因为对象是引用类型的，P中保存的仅是对象的指针，这就意味着，const仅保证指针不发生改变，修改对象的属性不会改变对象的指针，所以是被允许的。也就是说const定义的引用类型只要指针不发生改变，其他的不论如何改变都是允许的。

> 变量：值可以修改
>
> 常量：值不可修改

```javascript
let num=12;
num =13;
console.log(num); // 13

const str='学习'；
str='笔记'； //使用babel如果遇到了const设置的常量再进行修改，就会无法进行编译了
console.log(str); //报错
```

#### JS中创建变量方式汇总

> ` var`：ES5中创建变量
>
> `function`：ES中创建函数
>
> ES5中创建变量或者函数存在变量提升、重复声明等特征，但没有块级作用域的机制

> `let`：ES6中创建变量
>
> `const`：ES6创建常量
>
> ES6中创建的变量或者常量都不可以变量提升，也不可以重复声明，还存在块级作用域

> `class`：ES6中创建类的方式
>
> `import`：ES6中模块导入的方式

```javascript
class Parent{
  constructor(){
    //->this.xxx=xxx;
  }
  
  //Parent.prototype
  aa(){}
  
  //Parent own property
  static bb(){}
}
```

