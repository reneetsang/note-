## ES6学习笔记 - 创建类的基本语法和继承实现原理

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
        console.log(`my name is ${this.name},i am ${this.age} years old`)
    }
}

//把Person当作一个普通的对象，给对象设置的私有属性
Person.study=function(){
    console.log('good good study day day up')
}
var p1=new Person('renee',18)
```

> ES6中创建类
>
> class的内部是通过`Object.definePropterty`来定义的，把公共方法定义在原型链上。把静态方法定义再类上

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
        console.log(`my name is ${this.name},i am ${this.age} years old`)
    }
    
    //把Person当作普通对象设置属性和方法，只需要在设置的方法前加static即可
    static study(){
        console.log('good good study day day up')
    }
}
let p1=new Person('renee');
//Person(); //报错 ES6中使用class创建的类，天生自带new.target的验证，不允许把创建的类当作普通函数执行
```





```javascript
class Parent{
    constructor(x,y){
        // 给实例设置私有的属性
        this.x=x;
        this.y=y;
    }
    
    // Parent.prototype
    render(){
        // 使用this.render()执行
    }
    
    // 函数也是对象，把Parent当做一个普通的对象，设置的私有属性方法，和实例没有关系
    static ajax(){}
}
Parent.prototype.AA=12; // ES6创建类的大括号中只能写方法（而且不能是箭头函数），不能设置属性，属性需要自己额外拿出来设置
Parent.BB=12; // 把它作为对象设置的私有属性也只能拿到外面设置
new Parent(10,20) // new Parent执行的时候，首先执行constructor

class Children extends Parent{
    constructor(){
        super(10,20)；// Parent.constructor.call(this,10,20)
        //this.x=10
        //this.y=20
        //this.ajax(); 报错，子类只能继承父类原型上的属性和方法，和父类实例私有的属性和方法。对于父类作为普通对象设置的私有属性和方法是无法继承的
    }
    render(){
        
    }
}
console.dir(new Children());
{
    x:10,
    y:20,
    __proto__:Children.prototype
      render
      __proto__:Parent.prototype
        render
        AA:12
        __proto__:Object.
    
}
```



## 类的继承

> Object.create

```javascript
function Parent(){
    this.name = 'parent'
}
Parent.prototype.smoking = function(){
    console.log('smoking')
}
function Child(){}

// 继承公有属性 Object.create是如何实现的
function create(Pproto){
    let Fn = function(){} // 创建一个空函数 没有私有属性和公用属性
    Fn.prototype = Pproto; // 将父类的公有属性放在这个函数上
    return new Fn(); // 产生的实例就只有公有属性了
}
Child.prototype =Object.create(Parent.prototype,{constructor:{value:Child}});
let child = new Child();
console.log(child.__proto__=== Child.prototype);
console.log(Child.prototype.constructor == Child);
```

> extends继承

```javascript
class Person{
    constructor(...arg){
        let [x=0,y=0]=arg;
        this.x=x;
        this.y=y;
    }
    sum(){
        return this.x+this.y;
    }
}

class Child extends Person{ 
    //创建了Child类，并且让Child类继承了Person类
    //1.把Person中的私有属性继承过来设置给了子类实例的私有属性
    //2.让子类实例的原型链上能够找到Person父类的原型（这样子类的实例就可以调用父类原型上的方法了）
    
    //constructor(){}//报错
    //----------------
    //我们可以不写constructor，浏览器会默认创建它，而且默认就把父类私有的属性继承过来了（而且把传给子类的参数值也传递给父类了）
    //constructor(...arg){
        //arg：传递给子类的参数（数组），[剩余运算符]
        //super(...arg) //[展开运算符] 把arg中每一项值展开，分别传递给父类方法super(10，20，30) 
    //} 
    //----------------
    //很多时候我们不仅要继承父类私有的，还需要给子类增加一些额外私有的，此时就必须写constructor，但是一定要在constructor中第一行写上super，否则会报错
    constructor(...arg){
        super(...arg) //super must be first
		let [,,z]=arg;
        this.z=z
    } 
    
    //constructor(x,y,z){
    //    super() //Person.prototype.constructor.call(this)
    //    this.z=z;
    //} 
    
    fn(){
        
    }
}
let c=new Child(10,20,30)

```

### ES6继承的实现原理

new原理：

创建新的空对象

获得构造函数，设置空对象的原型，指向构造函数的原型

把指针绑定为空对象，执行构造函数

确定返回值为对象

不过是New的对象，和字面量创建的，其实都是一个意思。一般推荐使用字面量，便于阅读并且速度更快

```javascript
// 负责将原型的方法和静态方法定义在构造函数上的
function defineProperties(constructor,properties){
    for(let i = 0;i<properties.length;i++){
        let obj = {...properties[i],enumerable:true,writeable:true,configurable:true}
        Object.defineProperty(constructor,properties[i].key,obj);
    }
}
//  对不同的属性做处理 如果是原型上的方法挂载Class.prototype 如果是静态方法放在 Class上
function _createClass(con,protoProperty,staticProperty){
    if(protoProperty){
        defineProperties(con.prototype,protoProperty);
    }
    if(staticProperty){
        defineProperties(con,staticProperty);
    }
}
// 父类
var Parent = function(){
    function Parent(){
        // 类的调用检测
        this.name = 'zfpx'
        _classCallCheck(this,Parent);
        return {a:100}
    }
    // 用来描述这个类的原型方法和静态方法
    _createClass(Parent,[ // 第一个数组表示的是公共方法的描述 descirptor
        {key:'getName',value:function(){
            return 1000
        }}
    ],[ //描述静态的方法
        {key:'fn',value:function(){
            return 100;
        }}
    ])
   return Parent
}()
// 类的调用检测
function _classCallCheck(instance,constructor){ //检查当前类  有没有new出来的，不是new出来的this属于window
    if(!(instance instanceof constructor)) throw Error('without new')
}
// 继承共有方法和静态方法
function _inherits(subClass,superClass){
    // 子类继承父类的公有方法
    subClass.prototype = Object.create(superClass.prototype,{constructor:{value:subClass}});
    // 也要让子类继承父类的静态方法
    subClass.__proto__ = superClass;
}
var Child = function(Parent){ // 表示儿子继承Parent类，要包多一层不然传参会传到Child上
    _inherits(Child,Parent); // 表示继承 儿子继承父亲
    function Child(){ // 类的调用检查
        // 在子类中应该调用父类的构造函数
        // Parent.call(this);
        _classCallCheck(this,Child);
        let that = this;
        let obj =  Object.getPrototypeOf(Child).call(this); // Child.__proto__ = Object.getPrototypeOf(Child) 继承父类的私有方法，为了保险不用Parent.call(this)，因为不一定继承父类
        if(typeof obj === 'object'){ //如果是对象把obj作为实例
            that = obj;
        }
        return that;
    }
    return Child
}(Parent);
let child = new Child;
console.log(child);
```

