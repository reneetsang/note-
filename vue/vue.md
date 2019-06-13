## 什么是库？什么是框架

库是将代码集合成一个产品，库是我们调用库中的方法实现自己的功能。

框架则是为解决一类问题二开发的产品，框架是我们在指定的位置编写好代码，框架帮我们调用

框架是库的升级版

## 声明式和命令式

自己写for循环就是命令式，命令按照自己的方式得到结果

声明式就是利用数组的方法forEach，想要循环，让内部帮我做。所以vue比较简单

## VUE

mvvm双向 mvm单向

可以把数据放在ViewModel，数据变了会更新试图

```html
<div id='app'>hello {{info.xxx}}</div>
<script src='node_modules/vue/dist.vue.js'></script>
<!-- 只要模板中使用的数据，必须在实例上声明 -->
<script>
    // vm=>ViewModel
    let vm=new Vue({
        el:'#app',
        //template:'<h1>renee</h1>', //模板，会替换掉上面app的内容
        data:{//存放数据
            msg:'hello'，
            info:{ //即使没有声明xxx写在上面也不会报错，会认为没找到，但是下面赋值是修改不了的
            	xxx:'xxx'
        	} 
        }
    })

    // 把数据都代理给了vm
    vm.msg //就可以获取到数据
    vm.info.xxx='wow'
    vm.$set(vm.info,'address','shenzhen'); //属于vue实例上的方法
    vm.arr.length--;// 没效果 长度监控不了
    vm.arr=[1,2,3]
    
    //vue实例上的方法
    vm.arr=[2,3,4]; // 数据变化后更新试图是异步执行的，其实dom还没有更新，如果需要取修改后内容
    vm.$nextTick(()=>{ // 异步的
        console.log(vm.$el.innerHTML)
        console.log(vm.$options)
    })
    vm.$el // 指当前挂载的元素 console.log(vm.$el.innerHTML)
    vm.$options //选项
    vm.$watch('info.xxx',function(newValue,oldValue){ 
       console.log(newValue,oldValue)
    }) //监控，里面的值变化，会触发这个函数，多次更新只会触发一次
</script>
```

### observer

```java
//数据源
let obj={
    name:'renee',
    age:18,
    info:{
        a:1,
        b:2
    }
        
}
// vue数据劫持 Object.defineProperty
function observer(obj){
    if(typeof obj==='object'){
        for(let key in obj){
            defineReactive(obj,key,obj[key])
        }
    }
}
function defineReactive(obj,key,value){
    observer(value); //递归，可能对象里有对象，判断value是不是一个对象，如果是对象会继续监控
    Object.defineProperty(obj,key,{
        get(){
            return value
        },
        set(val){
            // 用这个好处是这里可以实现别的逻辑
            observer(val); //有可能传入的值也是个对象，比如obj.info={ name:1}，也需要拦截一下 ，需要对这个对象进行监控
            console.log('数据更新了')
            value=val
        }
    })
}
observer(obj);
obj.info.a=3
obj.info={
    name:1
}
obj.info.name=2
// 如果属性不存在，默认后面增加的内容并不会刷新试图
// 数组调用push，是无效的，因为Object.defineProperty不支持数组
// vue源码把数组上所有的方法都重写了    
obj.age=[1,2,3,4];
obj.age.push(5)
    
// 数组重写的原理大概是
let arr=["push","slice","shift","unshift"];
arr.forEach(method=>{
    let oldPush=Array.prototype[method];
    Array.prototype[method]=function(value){
        oldPush.call(this,value)
    }
})
```

### 模板

```HTML
<div id='app'>
    <!-- 取值表达式，可以运算，取值，三元 -->
    {{1+1}}
    {{this.msg}}
    {{flag?'ok':'noOk'}}
    {{ {name:1} }}<!-- 打印对象 -->
</div>
<script src='node_modules/vue/dist.vue.js'></script>
<!-- 只要模板中使用的数据，必须在实例上声明 -->
<script>
    // vm=>ViewModel
    let vm=new Vue({
        el:'#app',
        data:{//存放数据
            msg:'hello'，
            flag:true,
        }
    })
</script>
```

### vue中的指令

```html
<div id='app'>
	<div v-once>{{msg}}</div>
    <div v-html="d"></div>
    <!-- if和else要挨着，中间不要隔着div之类的 -->
    <div v-if="flag">123</div>
    <div v-else>321</div>
    <template></template>
    
    <!-- 循环谁，就把它放在谁身上。vue2.5以上要求，必须在循环时，使用key属性 -->
    <div v-for="(fruit,index) in fruits" :key="index"> <!-- fruit in fruits -->
        {{fruit}}{{index}}
        <div v-if="index%2">{{fruit}}</div>
        <div v-else>{{index}}</div>
    </div>
    <div v-for="(value,key) in info">
        {{value}}
    </div>
    <!-- template不能放key，要放在真实dom里 -->
    <!-- key可以用来区分元素，尽量不要使用idenx作为key，比如倒序，key和内容会变，会重新渲染 -->
    <!-- 如果有唯一标识，尽量使用唯一标识，倒序只需要移动就好 -->
    <template v-for="i in 3">
    	<div :key="i+'_1'"> {{i}}</div>
        <div :key="`${i}_2`"> {{i}}</div>
    </template>
    
    
</div>
<script src='node_modules/vue/dist.vue.js'></script>
<!-- 只要模板中使用的数据，必须在实例上声明 -->
<script>
    // vm=>ViewModel
    let vm=new Vue({
        el:'#app',
        data:{//存放数据
            msg:'hello'，
            d:'<p1>hello</p1>',
            flag:true，
            fruits:['苹果','香蕉','橘子'],
        	info:{name:'renee',age:18}
        }
    })
</script>
```

- v-once

  内部会进行缓存，以后使用的都是缓存里的结果，其实开发时用的比较少

- v-html

  会把内容变成dom元素插入到页面里，innerHTML，但是有安全隐患的，XSS攻击，不能将用户输入的内容展现出来，内容必须是可信任的

  ```html
  <input type="text" v-model="d">
  ```

- v-text

- v-if/v-else

  v-if如果不成立，dom就会消失，控制的是dom

- v-show

  控制的是样式，v-show不支持template

- v-for

  循环谁，就把它放在谁身上。vue2.5以上要求，必须在循环时，使用key属性

### vmodel

```html
<div id='app'>
     <!-- fn加不加括号都可以，方法不能放在data里 -->
    <input type="text" :value="msg" @input="fn($event)">
    <!-- 也可以改写成这样，但还是不够简化 -->
    <input type="text" :value="msg" @input="e => {msg=e.target.value}">
    <!-- v-model是@input + :value的一个语法糖-->
    <input type="text" :value="msg" v-model="msg">
    
    <!-- select,radio,checkbox-->
    <select v-model="seleceValue">
        <option value="0" disabled>请选择</option>
        <option v-for="(list,key) in lists" :value="list.id">{{list.value}}</option>
    </select>
    {{selectValue}}
    
    <!-- radio可以根据v-model进行分组-->
    <input type='radio' v-model="radioValue" value="男">
    <input type='radio' v-model="radioValue" value="女">
    
    <input type="checkbox" v-model="checkValues" value="游泳">
    <input type="checkbox" v-model="checkValues" value="健身">
    
    <!-- 修饰符：用户输入的.number .trim-->
    <input type="text" v-model.number="val">{{typeof val}}
    <!-- 还有键盘修饰符，可以写键盘码，常用的 .ctrl .esc .enter-->
    <input type="text" @keyup.esc="fn">
    
    <!-- 属性绑定： v-bind: -->
    <!-- 绑定样式有两种方式class style，里面是对象或和数组（可以放多个，数组里可以放对象） -->
    <div class="adc" :class="{b:true}">你好</div>
    <div class="adc" :class="['a','b']">你好</div>
    <div class="adc" style="color:red" :style="{background:'blue'}">你好</div>
    <div class="adc" style="color:red" :style="[{background:'blue',color:'blue'}]">你好</div>
</div>
<script src='node_modules/vue/dist.vue.js'></script>
<!-- 单向：数据变化，视图更新 双向：视图更新也会影响数据变化 -->
<script>
    // vm=>ViewModel
	// 配置一个键盘code别名，很少用到
	Vue.config.keyCodes={
        'f1':112
    }
    let vm=new Vue({
        data:{//存放数据
            msg:'hello'，
            radioValue:'男',
            selectValue:2,
            list:[{value:'菜单1',id:1},{value:'菜单2',id:2}],
            checkValues:[]
        },
        methods:{
            // 不要用箭头函数，不然this是window
            fn(e){
                console.log(this);
                this.msg=e.target.value;
            }
        }
    }).$mount('#app'); //挂载到app上，跟上面el:'#app'效果一样。单元测试里经常这么写，因为要挂载到虚拟dom里
</script>
```

### 

```html
<div id='app'>
	{{getFullName()}}
    
</div>
<script src='node_modules/vue/dist.vue.js'></script>
<script>
    let vm=new Vue({
        el:'#app',
        data:{
			fitstName:'珠',
            lastName:'峰',
            msg:'hello',
            fullName2:''
        },
        mounted(){// 在不同阶段会被调用，钩子函数
            
        },
        computed:{// 计算属性，Object.defineProperty来实现的
            fullName(){// get方法，有缓存，如果值没有更改会从缓存中取值，就不会重新执行这个方法
                return this.firstName+this.lastName
            }
        },
        methods:{
            getFullName(){
                this.fullName2=this.firstName+this.lastName
            }
        }
        watch:{ //vm.$watch
            firstName(newValue){
                this.getFullName()
            },
            lastName(newValue){
                this.getFullName() 
            }    
        }
    })
</script>
```

