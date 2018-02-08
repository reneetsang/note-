## JS中常用的设计模式

> 单例设计模式、构造原型设计模式、发布订阅设计模式、promise设计模式

#### 单例模式

```javascript
//单例模式：把实现当前这个模块所有的属性和方法汇总到同一个命名空间下（分组作用，避免了全局变量的污染）
let exampleRender=(function(){
    //实现当前模块具体业务逻辑的方法全部存放在闭包中
    let fn=function(){
        //...
    }
    return{
        init:function(){
            //入口方法：控制当前模块具体的业务逻辑顺序
        }
    }
})();
exampleRender.init();
```

> 真实项目中，如果我们想要实现具体的业务逻辑需求，都可以依托于单例模式构建；我们把项目划分成各大板块或者模块，把实现同一个板块的方法放在一个独立的命名空间下，方便团队协作开发

#### 构造原型模式：最贴近oop面向对象编程思想的

> 以后真实项目中，不管是封装类库还是插件或者UI组件，基本上都是构造原型模式来开发的

```javascript
class Tool{
    constructor(){
        this.isCompatible='addEventListener' in document; //如果不兼容返回false(IE6~8)
    }
    //挂在到原型的方法
    css(){}
    //挂在到普通对象的方法
    static distinct(){}
}
class Banner extends Tool{
    constructor(...arg){
        super();
        thix.xxx=xxx;
    }
    //挂在到子类原型的方法
    bindData(){
        this.css(); //把父类原型上的方法执行（子类继承了父类，那么子类的实例就可以调取父类原型上的方法了）
        this.distinct===undefined; //子类的实例只能调取父类原型上的方法，以及父类给实例提供的私有属性方法，但是父类作为普通对象加入的静态方法，子类的实例是无法调取的（只有这样才可以调取使用：Tool.distinct()）
    }
}
```

> 有三个类A/B/C，想让C继承A和B

```javascript
class A{}
class B extends A{}
class C extends B{}
```

#### 发布订阅设计模式：观察者模式

> 不同于单例和构造，发布订阅是小型设计模式，应用到某一个具体的需求中：凡是当到达某个条件之后要执行N个方法，我们都可以依托于发布订阅设计模式管理和规划我们的JS代码

> 我们经常把发布订阅设计模式嵌套到其他的设计模式中

#### promise设计模式

> 解决AJAX异步请求层级嵌套的问题

> 它也是小型设计模式，目的是为了解决层级嵌套问题的，我们也经常会把它嵌套在其他的设计模式中运行

```javascript
$.ajax({
    url:'/A',
    async:true, //异步
    success:function(result){
        $.ajax({
            url:'/B',
            async:true, //异步
            success:function(result){
				//还会有后续嵌套
            }
        })
    }
})
```

