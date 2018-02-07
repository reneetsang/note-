## ES6学习笔记 - 创建类的基本语法

> ES5中创建类的实例，以及如何禁止用户把类当作普通函数执行（ `new target`）

```javascript
function Person(name,age){
    //console.log(new.target); //ES6增加的语法，如果是通过new执行的，返回的结果是当前创建的类；如果是当作普通函执行（Person()），返回的是undefined
    if(typeof new.target === 'undefined'){
        throw new SyntaxError('当前Person不能作为一个普通函数执行')
    }
        
    //new执行的时候，this是当前类的实例，this.xxx=xxx是给当前实例增加的私有属性
    this.name=name;
    this.age=age;
}

//原型上存放的是公有的属性和方法：给创建的实例使用
Person.prototype={
    constructor:Person,
    say:function(){
        console.log('my name is ${this.name},i am ${this.age} years old')
    }
}

//把Person当作一个普通的对象，给对象设置的私有属性
Person.study=function(){
    console.log('good good study day day up')
}
var p1=new Person('renee',18)
```

> ES6中创建类

```javascript
console.log(Person); //报错 不存在变量提示
class Person{
    constructor(name='无名氏',age=18){
        //给实例设置的私有属性
        this.name=name;
        this.age=age;
    }
    
    //直接在大括号中编写的方法都设置在类的原型上，ES6默认把constructor的问题解决了，此时原型上的constructor指向的就是Person
    say(){
        console.log('my name is ${this.name},i am ${this.age} years old')
    }
    
    //把Person当作普通对象设置属性和方法，只需要在设置的方法前加statuc即可
    static study(){
        console.log('good good study day day up')
    }
}
let p1=new Person('renee');
//Person(); //报错 ES6中使用class创建的类，天生自带new.target的验证，不允许把创建的类当作普通函数执行
```

