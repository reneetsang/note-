## vue事件

* @事件.stop

  ```javascript
  stopPropagation,cancelBubble=true;
  ```

* @事件.cap

  ```javascript
  xxx.addEventListener('click',fn)
  ```

* @事件.prevent

  ```javascript
  preventDefault,returnValue=false
  ```

* @事件.once

  ```javascript
  on('click').off('click')
  ```

* 事件.self

  ```javascript
  e.srcElement&&e.target 判断事件源绑定事件
  ```

```html
<div id='app'>
  <div @click='patent'>parent
    <div @click.self='child'>child
      <div @click=grandson>grandson</div>
    </div>
  </div>
  <a href='www.baidu.com' @click.prevent='prevent'>点击</a>
</div>
```

## filters

```
{{'123' | my(1,2,3)}}
filters:{
  my(data,param1,param2,param3){ //第一个参数data就是竖线前面的内容123
	//这里的this指向window
	//return返回什么，显示就是什么
  }
}
```

```html
<div id='app1'>{{'hello' | my}}</div> 
<div id='app2'>{{'hello' | my}}</div>
```

```javascript
//全局过滤器，要放在页面的顶部，放在下面都已经new了，再写没有用
Vue.filter('my',(data)=>{
  return 'renee'+data;
})
new Vue({
  el:'#app1'
})
new Vue({
  el:'#app2'
})
```

## computed计算“属性”，不是方法

> 方法不会有缓存，computed会根据依赖（归vue管理的数据，可以响应式变化的）的属性进行缓存
>
> 两部分组成有get和set（不能只写set），一般情况下通过js赋值影响其他人或者表单元素设置值的时候回调用set方法
>
> computed默认调用get方法，必须要有return，computed不支持异步

```html
<div id='app'>
  <input type='text' v-model='a'>{{msg}}
  <!--根据输入框中的内容 算出了一个错误信息-->
</div>
```

```javascript
let vm=new Vue({
  el:'app',
  data:{a:''},
  computed:{
    msg(){
      if(this.a.length<3){return '少了'}
      if(this.a.length>3){return '多了'}
      return '';
    }
  }
})
```

## watch和computed

* watch

> 使用 `watch` 选项允许我们执行异步操作 (访问一个 API)，限制我们执行该操作的频率，并在我们得到最终结果前，设置中间状态

```html
<div id="app">
    <input type="text" v-model="a">
    {{msg}}
</div>
```

```javascript
var vm = new Vue({
    el:'#app',
    data:{
        a:'',
        msg:''

    },
    watch:{ //只有值变化的时候才会触发，并且支持异步，其他情况我们更善于用computed
        a(newVal,oldVal){ //watch的属性名字要和观察人的名字一致
            setTimeout(()=>{
                this.msg='……';//等待时显示
                if(newVal.length<3){
                    return this.msg='太少' //这里的return是阻止下面执行
                }
                if(newVal.length>6){
                    return this.msg='太多'
                }
                this.msg=''
            },2000)
        }
    }
});
```

> 通常更好的做法是使用计算属性而不是命令式的 `watch` 回调

```javascript
<input type="text" v-model="val">{{fullName}}

//用watch
var vm = new Vue({
    el:'#app',
    data:{
        val:'',
        firstName:'renee',
        lastName:'Tsang',
        fullName:''
    },
    watch:{ //只有值变化的时候才会触发，并且支持异步，其他情况我们更善于用computed
        //watch的属性名字要和观察人的名字一致
        val(newVal){
        	this.fullName=this.firstName+newVal+this.lastName;
        },
        firstName(){},
        lastName(){}
    }
});

//用computed
var vm = new Vue({
    el:'#app',
    data:{
        val:'',
        firstName:'renee',
        lastName:'Tsang',
    },
    computed:{
        fullName(){
            return this.firstName+this.val+this.lastName;
        }
    }
});
```

> 命令式的 vm.$watch，较少用

```javascript
vm.$watch('a',(newVal,oldVal)=>{
    vm.msg='……';
    setTimeout(()=>{
        if(newVal.length<3){
            return vm.msg='太少' //这里的return是阻止下面执行
        }
        if(newVal.length>6){
            return vm.msg='太多'
        }
        vm.msg=''
    },2000)
})
```

## v-if/v-show

- v-if操作额是dom，v-if可以和v-else-if,v-else连写
- v-show操作的是样式

```html
<!-- template标签，是vue提供给我们的，没有任何实际意义的，用来包裹元素用的，最终的渲染结果将不包含 <template> 元素。v-show不支持template -->
<template v-if='true'>
	<div>我很漂亮</div>
    <div>我很漂亮</div>
	<div>我很漂亮</div>    
</template>
<div v-else>我很帅</div>
```

```html
<button @click='cut=!cut'>点击切换</button>
<!--默认情况下在切换dom时，相同的结构会被复用，如果不需要复用，需要加key-->
<template v-if="cut">
    <label>注册</label>
    <input type="text" key="1">
</template>
<template v-else>
    <label>登录</label>
    <input type="text" key="2">
</template>
```

## v-bind 

- 可简写成`:`

- 动态绑定“属性”

  ```html
  <img :src='src' :width='w'>

  <!-- :class绑定的样式和class绑定的不冲突 -->
  <!-- {className:isActive} -->
  <div class='x y' :class="flag?'z':''">张三</div>
  <div class='x' :class="{z:flag,y:true}">李四</div>
  <div class='x' :class="{class1,class2,{z:false}">王五</div>

  <div :class="{true:'x',false:'y'}[true]"></div>
  <div v-for="(a,index) in 10" :class="{x:index%2}">{{a}}</div>

  var vm = new Vue({
      el:'#app',
      data:{
          flag:true,
  		class1:'a',
  		class2:'b'
      }
  });
  ```

  ```html
  <div :style="{backgroundColor:'red',color:'pink'">我很漂亮</div>
  <div :style="[sty1,sty2,{fontSize30}]">我很漂亮</div>
  ```

## 实现单页面开发的方式

- 通过hash记录跳转（可以产生历史管理）
- 浏览器自带的历史管理的方法history（history.pushState()），可能会导致404错误

> 开发时使用hash的方式，上线的话我们会使用history的方式